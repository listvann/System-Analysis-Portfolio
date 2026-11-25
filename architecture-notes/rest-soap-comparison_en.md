# REST vs SOAP Architectural Styles: Applicability Analysis

## 1. SOAP (Simple Object Access Protocol)
This is a strict messaging protocol. It operates under rigid rules: the structure of both the request and the response is strictly regulated.

*   **Format:** XML only.
*   **Transport:** Usually HTTP/HTTPS, but supports SMTP, TCP.
*   **Contract:** Defined via WSDL (a rigid schema; any deviation causes an error).
*   **Pros:** Maximum reliability and built-in cryptography (WS-Security).

**Envelope Example (Requesting patient data):**

    <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/">
       <soapenv:Header/>
       <soapenv:Body>
          <getPatientInfo>
             <patientId>12345</patientId>
          </getPatientInfo>
       </soapenv:Body>
    </soapenv:Envelope>

## 2. REST (Representational State Transfer)
This is an architectural style, not a protocol. It is more flexible, lightweight, and uses standard web mechanisms (HTTP).

*   **Format:** Almost any. JSON is the most common due to readability.
*   **Transport:** HTTP/HTTPS only.
*   **Contract:** Flexible (usually described in Swagger / OpenAPI).
*   **Pros:** High speed, easy parsing on the frontend. Uses clear methods (GET - retrieve, POST - create, PUT/PATCH - update).

**Request Example (Retrieving patient data):**

    // GET /api/v1/patients/12345
    
    {
      "id": 12345,
      "name": "Ivan Ivanov",
      "status": "active"
    }

---

## 3. Practical Use Case: Choosing a Format for a PCR Laboratory

Often, a single Laboratory Information System (LIS) needs to use both approaches. Let's look at the "Delivering Test Results" process.

**Case 1: Mobile App for Patients (Choice: REST)**
A patient wants to view their PCR test result on their smartphone. 
Speed and traffic economy are critical for a mobile client. We don't need heavy XML envelopes. The mobile app calls the API via a simple `GET` request and instantly receives a lightweight JSON file with the result and reference values.

**Case 2: LIS Integration with State Health Registries (Choice: SOAP)**
The laboratory is required to report notifiable infectious disease results to a state health registry. 
Public health gateways demand strict delivery guarantees, rigid contract validation, and strong message-level security. Dropped payloads are unacceptable. SOAP fits here because of strict WSDL schema enforcement and built-in security standards, even though XML parsing is heavier than JSON.

## Summary
If you need speed, a lightweight footprint, and web/mobile frontend integration — choose **REST + JSON**. 
If you need robust transactions and integration with rigid Enterprise or government systems — choose **SOAP + XML**.
