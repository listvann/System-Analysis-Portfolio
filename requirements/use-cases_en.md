# User Stories and Use Cases

**Author:** Li-Iu-Kun Anna  
**Scope:** International (LIS → EHR)  
**Related Docs:** [Stakeholders](../project-lis/stakeholders_en.md) | [Business Processes](../project-lis/business-processes_en.md) | [Results API](../project-lis/api-results_en.md) | [Message Broker](../project-lis/message-broker_en.md) | [NFR](nfr_en.md)

---

## Part 1. User Stories and Acceptance Criteria

### US-01. Specimen Accessioning and Department Routing Check

**As a** lab technician,  
**I want** to see immediately whether a tube belongs to the PCR department when scanning its 12-digit barcode (`INZ + Container Code`),  
**so that** I do not waste time or reagents on specimens meant for another lab section.

**Acceptance Criteria:**

* **Tube belongs to the PCR department**
  * **Given** a lab technician is logged into the LIS on the accessioning and sorting screen,
  * **When** they scan a 12-digit barcode with a PCR container code,
  * **Then** the tube is added to the active virtual rack and the order moves to `IN_PROGRESS`.

* **Tube belongs to another department**
  * **Given** a lab technician is working in the PCR module,
  * **When** they scan a tube with a container code for another department (such as biochemistry or ELISA),
  * **Then** the system blocks adding the tube to the rack, skips reagent deduction, and alerts the technician with the target department.

---

### US-02. Result Validation and Sending to the EHR

**As a** clinical pathologist,  
**I want** to validate reviewed test results in the LIS and know they will be sent to the EHR without slowing down my screen,  
**so that** the clinician and patient get timely updates in the patient chart.

**Acceptance Criteria:**

* **Partial order completion**
  * **Given** an order under `iln` has two tests, where the first is validated (`VALIDATED`) and the second is still running (`PRELIMINARY`),
  * **When** the clinical pathologist validates the first test,
  * **Then** a `result.validated` event is written to `outbox_messages`, only the validated test is sent to the EHR with order status `IN_PROGRESS`, and the preliminary result stays internal.

* **All tests in the order are final**
  * **Given** all tests under `iln` have reached a final status (`VALIDATED` or `REJECTED`),
  * **When** the clinical pathologist validates the last remaining test,
  * **Then** the outbound webhook sends order status `COMPLETED` along with all final results.

* **Invalid specimen or redraw needed**
  * **Given** PCR amplification shows inhibition or insufficient sample volume,
  * **When** the clinical pathologist marks the result as `REJECTED`,
  * **Then** the rejection status is sent to the EHR so the ordering provider knows a redraw is needed.

---

### US-03. Reliable Delivery During EHR Outages

**As a** lab manager,  
**I want** validated results to neither get lost nor create duplicates in the patient chart when the external EHR has issues,  
**so that** we meet turnaround times without cleaning up duplicate reports manually.

**Acceptance Criteria:**

* **Temporary EHR or network outage**
  * **Given** the worker sends `POST /api/v1/webhook/lab-results` to the EHR,
  * **When** the EHR returns `5xx` or times out,
  * **Then** the message moves to `lis.results.retry.q` and retries with the same `X-Idempotency-Key` (`message_id`) and a new `X-Request-ID`.

* **Retry of an already processed payload (`409 Conflict`)**
  * **Given** the EHR already processed the result for that `X-Idempotency-Key`, but the response was lost on the network,
  * **When** the worker retries with the same `X-Idempotency-Key`,
  * **Then** the EHR returns `409 Conflict` without creating a duplicate in the patient chart, and the worker sends `ACK` to clear the message from the queue.

* **Payload error or unknown `iln` (`400` / `404`)**
  * **Given** the EHR responds with `400 Bad Request` or `404 Not Found` (for example, the `iln` order does not exist in the EHR),
  * **When** the worker receives the response,
  * **Then** the message goes straight to `lis.results.dlq` without pointless retries and triggers an alert for review.

---

## Part 2. End-to-End Use Case: From Tube Scan to EHR Delivery

**UC-01. PCR Specimen Processing, Validation, and EHR Result Delivery**

* **Actors:** Lab Technician, Clinical Pathologist, LIS, RabbitMQ, External EHR.
* **Preconditions:**
  * The order is registered in the EHR and synced to the LIS with a unique `iln`.
  * The specimen tube carries a 12-digit barcode (`INZ + Container Code`).
  * An active reagent lot (`lot_id`) is configured in the LIS.
* **Main Flow:**
  1. The lab technician scans the tube barcode upon arrival in the PCR section.
  2. The LIS checks the container code, assigns the specimen to a virtual rack, links the reagent lot (`lot_id` for internal traceability), and sets the order status to `IN_PROGRESS`.
  3. After nucleic acid extraction and amplification, analyzer readings enter the LIS as `PRELIMINARY`.
  4. The clinical pathologist reviews amplification curves and controls, then validates the result (`VALIDATED`).
  5. Inside one database transaction, the LIS updates `Results` and inserts a row into `outbox_messages`.
  6. `Outbox Publisher` pushes the `result.validated` event to RabbitMQ (`lis.results.exchange`).
  7. `Webhook Worker` picks up the message from `lis.results.validated.q` and sends `POST /api/v1/webhook/lab-results` to the EHR over `TLS 1.2+` (keeping internal `lot_id` out of the payload).
  8. The EHR saves the result to the patient chart and returns `200 OK`.
  9. The worker sends `ACK` to RabbitMQ, completing delivery.
* **Exceptions and Alternate Flows:**
  * **Wrong department tube:** The LIS triggers a `Hard Stop`, blocks rack assignment, and displays the correct department.
  * **Sample rejection / inhibition:** The clinical pathologist sets `REJECTED`, and the flow continues so the rejection reaches the EHR.
  * **EHR unreachable or returning `5xx`:** The message routes through `lis.results.retry.q` for retry using the original `X-Idempotency-Key`.
  * **`400/401/404` error or retries exhausted:** The message lands in `lis.results.dlq` and raises an alert.
  * **LIS outage:** Urgent specimens are logged via their 12-digit barcode in a shift log and entered once the LIS is back online.
