# Integration Specification: Result Delivery (REST API)

**Architectural Style:** REST (Webhook / Push Model)  
**Trigger Event:** Result validation by the Clinical Pathologist in the LIS interface.  
**Target System:** External EHR (Electronic Health Record) system (for patient report generation).  

## Business Rules
1. **Identification:** The EHR expects the **ILN** (12-digit Order Number) rather than the physical specimen barcode (INZ + Container Code). The ILN serves as the primary key linking the test result to the financial encounter and the patient's EHR profile.
2. **Dynamic Reference Ranges:** Reference ranges are calculated dynamically by the LIS during test execution, based on the patient's demographic data (gender, DOB). The EHR receives pre-calculated textual reference values.
3. **Legal Compliance & E-Signature:** To generate a legally binding report with a digital signature, the payload strictly includes the executing laboratory's legal entity details and the authorizing Clinical Pathologist's full name.

---

## Data Contract
**Endpoint:** `POST /api/v1/webhook/lab-results`  
**Content-Type:** `application/json`

### Example Request Body

```json
{
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
    "department": "Molecular Pathology (PCR)"
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
    "validated_datetime": "2026-10-19T14:45:00Z",
    "head_of_lab_stamp_required": true
  }
}
```

### Data Dictionary

| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `iln` | String | Yes | Identification Personal Number (Order Number). |
| `collection_datetime` | DateTime | Yes | Actual date and time of specimen collection (ISO 8601). |
| `patient.gender` | String | Yes | Patient's gender (M/F). Critical for defining the `reference_range`. |
| `results[].value` | String | Yes | The actual test result value. |
| `results[].test_status` | String | Yes | Status of the specific test (e.g., VALIDATED, REJECTED). |
| `validation.doctor_full_name` | String | Yes | Full name of the Clinical Pathologist who interpreted and validated the result. |
