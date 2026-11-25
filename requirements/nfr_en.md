# Non-Functional Requirements (NFR)

**Author:** Li-Iu-Kun Anna  
**Scope:** International (LIS → EHR)  
**Related Docs:** [Meeting Notes](meeting-notes_en.md) | [Results API Spec](../project-lis/api-results_en.md) | [Message Broker](../project-lis/message-broker_en.md) | [Database Schema](../project-lis/database_en.md)

---

## 1. Performance and Load

* **Morning accessioning peak:** When specimens arrive in bulk at the start of the shift, scanning the 12-digit barcode (`INZ + Container Code`) and checking routing should feel responsive to lab technicians without UI lag (target — under `500 ms` per scan under normal network conditions).
* **Immediate routing block (`Hard Stop`):** If a technician scans a specimen whose container code belongs to another department, the system stops adding it to the virtual rack right away, skips reagent deduction, and shows a clear alert with the target department before sample prep starts.
* **Batch validation without UI blocking:** When a clinical pathologist validates results in batches, the UI should not freeze waiting on the EHR — delivery runs asynchronously via `Transactional Outbox` and `RabbitMQ`.

---

## 2. Reliability and Failure Handling

* **No lost results during EHR issues:** Updating the result in `Results` and writing the event to `outbox_messages` happen in one database transaction. If the EHR is down, slow, or returns `5xx`, the message is not lost — it goes through `lis.results.retry.q` with backoff delays, and if retries run out, lands in `lis.results.dlq` with an alert for investigation.
* **No duplicate entries in the patient chart:** Retries of the same event keep `X-Idempotency-Key` equal to `message_id`, while `X-Request-ID` is generated fresh on each attempt — so the EHR does not create duplicate reports in the patient chart.
* **Offline fallback for urgent specimens:** If the LIS is temporarily unavailable, urgent specimen handling does not stop — staff rely on the 12-digit barcode (`INZ + Container Code`) and a shift log, then record and sync everything once connectivity returns.

---

## 3. Security and Data Compliance

* **Health and personal data protection:** Data handling and transfer follow **HIPAA / GDPR** principles and organizational security rules, using encrypted transport (`TLS 1.2+`) and authentication between the LIS and EHR.
* **Internal vs. outbound data boundaries:** The internal reagent lot identifier (`lot_id`) stays inside the LIS for reagent traceability and quality control and is not sent in the outbound EHR webhook.
* **Audit and incident tracing:** Logs record `message_id`, `X-Request-ID`, `iln`, timestamps, and EHR response codes so any item in `DLQ` can be traced and investigated quickly.

---

## 4. Maintainability and Growth

* **Single contract source:** The result payload structure follows the OpenAPI spec (`api-results-swagger_en.yaml`, `LabResultPayload`), keeping docs, backend work, and tests aligned on one schema.
* **Adding new integrations:** With RabbitMQ queues and a standard REST webhook in place, connecting additional facilities or external EHR endpoints does not require changing core technician or pathologist workflows.
