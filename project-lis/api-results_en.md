# Integration Specification: Result Delivery (REST API)

**Architectural Style:** REST (Webhook / Push Model)  
**Trigger Event:** Result validation by the Clinical Pathologist in the LIS interface.  
**Target System:** External EHR (Electronic Health Record) system.
**Security:** Authorization via `Bearer Token` (OAuth 2.0 / JWT) + `X-Request-ID` header for distributed tracing.

## Business Rules
1. **Identification & Idempotency:** The EHR expects the **ILN** (12-digit Order Number) rather than the physical specimen barcode. The ILN serves as the primary key linking the result to the patient's EHR profile. To prevent duplicate report generation during retries, the LIS sends `message_id` in the payload and the `X-Idempotency-Key` header. On initial delivery of an event, `X-Idempotency-Key` equals `message_id` and remains unchanged across all retries for that message, while `X-Request-ID` is generated fresh per HTTP attempt for distributed tracing.
2. **Dynamic Reference Ranges:** Reference ranges are calculated dynamically by the LIS based on the patient's gender and DOB. The EHR receives pre-calculated textual values.
3. **Legal Compliance & E-Signature:** The payload strictly includes the executing laboratory's legal entity details and the authorizing Clinical Pathologist's full name.
4. **Partial Order Delivery:** The webhook is triggered as individual assays are validated, without waiting for every test in the order to finish. Only items with `VALIDATED` or `REJECTED` status are included in `results`. While `PRELIMINARY` assays remain in the order, `order_status` is sent as `IN_PROGRESS`; once all ordered tests are finalized, it is sent as `COMPLETED`.

## Data Contract
**Endpoint:** `POST /api/v1/webhook/lab-results`  
**Content-Type:** `application/json`
**Headers:**
* `Authorization: Bearer <token>`
* `X-Idempotency-Key: <uuid>`
* `X-Request-ID: <uuid>`

### Example Request Body

```json
{
  "message_id": "a1b2c3d4-e5f6-7890-abcd-1234567890ab",
  "iln": "123456789101",
  "order_status": "COMPLETED",
  "collection_datetime": "2026-10-19T08:30:00Z",
  "patient": {
    "full_name": "Ivan Ivanov",
    "dob": "1990-05-15",
    "gender": "M",
    "phone": "+15551234567"
  },
  "laboratory": {
    "legal_name": "National Diagnostics Network LLC",
    "department": "Molecular Diagnostics (PCR)"
  },
  "results": [
    {
      "test_code": "PCR-COV-19",
      "test_name": "SARS-CoV-2 RNA (COVID-19)",
      "test_status": "VALIDATED",
      "value": "Not Detected",
      "reference_range": "Not Detected",
      "unit": ""
    }
  ],
  "validation": {
    "doctor_full_name": "Dr. Alice Smith",
    "validated_datetime": "2026-10-19T14:45:00Z"
  }
}
```
### Response Codes

| Code | Description | Scenario |
| :--- | :--- | :--- |
| `200 OK` | Success | EHR successfully received and linked the data. |
| `400 Bad Request` | Validation Error | Missing required fields (e.g., Doctor's Name). |
| `401 Unauthorized` | Auth Error | Invalid or expired Bearer Token. |
| `404 Not Found` | Business Error | Order ILN not found in the EHR database. |
| `409 Conflict` | Idempotency Error | Request with this `message_id` / `X-Idempotency-Key` has already been processed. |
| `500 Internal Error` | Server Error | Internal Server Error on the EHR side. |

### Key Parameters (Data Dictionary)
> **Note:** The table below lists only the core contract parameters critical for business logic and routing. For the complete request and response schema, refer to the OpenAPI specification in [api-results-swagger_en.yaml](api-results-swagger_en.yaml) (`LabResultPayload`).

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `message_id` | UUID (String) | Yes | Unique message identifier to ensure idempotency. |
| `iln` | String | Yes | Identification Personal Number (Order Number). |
| `collection_datetime` | DateTime | Yes | Actual date and time of specimen collection (ISO 8601). |
| `patient.gender` | String | Yes | Patient's gender (M/F). Critical for defining the `reference_range`. |
| `results[].value` | String | Yes | The actual test result value. |
| `results[].test_status` | String | Yes | Status of the specific test (e.g., VALIDATED, REJECTED). |
| `validation.doctor_full_name` | String | Yes | Full name of the Clinical Pathologist who interpreted and validated the result. |

## Sequence Diagram

```mermaid
sequenceDiagram
    autonumber
    actor Doc as Pathologist
    participant LIS as LIS
    participant MQ as RabbitMQ
    participant W as Worker
    participant EHR as EHR

    Doc->>LIS: Validate result
    LIS->>LIS: Save to DB, generate message_id
    LIS->>MQ: Publish result.validated (JSON)
    LIS-->>Doc: UI: "Queued for delivery"

    MQ->>W: Consume message
    W->>EHR: POST /api/v1/webhook/lab-results<br/>(JWT, X-Idempotency-Key, X-Request-ID)

    alt 200 OK or 409 Conflict (duplicate)
        EHR-->>W: 200 OK / 409 Conflict
        W->>MQ: ACK (remove from queue)
    else 400 / 404 (validation or ILN error)
        EHR-->>W: 400 / 404 ErrorResponse
        W->>MQ: NACK → route to DLQ + Alert
    else 500 / Timeout (EHR failure)
        EHR-->>W: 500 / Timeout
        W->>MQ: NACK → Retry (up to 3x, exp. backoff)<br/>On failure → DLQ + Alert
    end
```

## 📑 OpenAPI (Swagger) Specification

The complete API contract is designed according to the OpenAPI 3.0 standard. 
📄 **[View the source contract code (api-results-swagger_en.yaml)](api-results-swagger_en.yaml)**

### Contract Visualization (Swagger UI)

**1. Endpoint Description and Overview**
![Swagger Endpoint](assets/swagger-endpoint_en.png)

**2. Request Headers (Parameters)**
![Swagger Parameters](assets/swagger-parameters_en.png)

**3. Request Body Structure**
![Swagger Request](assets/swagger-request_en.png)

**4. Response Codes and Scenarios**
![Swagger Responses 200 & 400](assets/swagger-responses_en-1.png)
![Swagger Responses 401 & 404](assets/swagger-responses_en-2.png)
![Swagger Responses 409 & 500](assets/swagger-responses_en-3.png)
