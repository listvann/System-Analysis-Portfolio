### Text Structure of the "As-Is" Business Process (Prior to New LIS Implementation)
![PCR Testing Business Process As-Is](pcr-process-as-is-en.png)  
📄 [Download high-resolution schema (PDF)](pcr-process-as-is-en.pdf)

**1. Zone: Collection Site and Courier (External Contour)**
* **Action 1:** Patient registration, biological specimen (BS) collection, printing of the paper requisition form.
* **Action 2:** Courier picks up the cooler bag with manifests and transports it to the laboratory.
* **⚠️ Risks (Bottlenecks):** Registration errors (namesakes, incorrect tests), temperature violations during transit (critical for CSF), loss of the bag or delivery to the wrong address.

**2. Zone: Laboratory Reception (Sorting)**
* **Action 1:** Unpacking bags, prioritization (urgent/distant cities), reconciliation with the manifest.
* **Action 2:** Scanning the primary barcode (Sample ID) into the LIS.
* **Action 3 (Manual):** Sorting tubes into racks by department. The receptionist relies on the paper form and the color of the tube cap.
* **⚠️ Risks:** Loss of the form, loss of the tube, human factor — receptionist placed the tube in the wrong rack (mixed up departments).

**3. Zone: PCR Department — Lab Technician (Clean Zone / Extraction and Aliquoting)**
* **Action 1:** Lab technician retrieves the racks from reception. Inside the department, an additional manual sorting by extraction type (automated/manual) is performed based on codes and cap color.
* **Action 2 (Double work):** Lab technician scans the Sample ID into the internal program ("Analytical Software"). The program assigns a secondary (internal) number to the tube. The patient's full name is not in this program (only on the tube itself).
* **Action 3 (BS Pre-processing):** Centrifugation, as well as the addition of specific reagents for chemical treatment, if strictly required by the preparation methodology for a specific type of biological specimen.
* **Action 4 (Extraction and Aliquoting):** Staging is strictly observed: first BS isolation (extraction), then aliquoting the isolated BS into microplates with dry reagent. Three scenarios are possible:
  * *Scenario A (Automated method):* Lab technician loads all BS, reagents, and plastics into the analyzer station and starts the process. The device fully automatically extracts the BS and aliquots it into the target microplates.
  * *Scenario B (Semi-automated method):* Lab technician places an empty microplate, reagents, and the initial BS into the device. After automated extraction, the lab technician takes the microplate and manually aliquots the BS into microplates with dry reagent using light tables.
  * *Scenario C (Manual method):* Samples are passed through the sorter software. Extraction is done by the lab technician manually (or manual aliquoting into a microplate followed by extraction in the device). Next, the lab technician manually aliquots the extracted BS at the light tables.
* **Action 5 (Double check of aliquoting):** When working at the light tables (scenarios B and C), the lab technician must scan the barcode of each microplate with reagents before aliquoting.
* **Action 6:** Lab technician tightly seals the microplate for transfer to the "dirty" zone.
* **⚠️ Risks:** Cross-contamination of samples, contamination with foreign DNA. During automated aliquoting, "visual fatigue" occurs at the stage of placing tubes in the rack. Incorrect reagents selected.

**4. Zone: PCR Department — CDL Doctor (Dirty Zone / Amplification and Validation)**
* **Action 1:** The doctor places the sealed microplate into the thermocycler and scans the microplate barcode — the program with the required melting temperature is loaded. Amplification is initiated.
* **Action 2:** After amplification is complete, the results (graphs) are automatically exported to the "Analytical Software".
* **Action 3 (Complex verification):** The doctor begins validation. Opens the LIS (age, sex, history) and the "Analytical Software" (checks expiration dates/series of kits, evaluates melting curves, peaks, and controls).
* **Action 4:** If the result is satisfactory, the doctor manually transfers the decision ("Detected / Not detected") from the Analytical Software to the LIS, generating a signed report.
* **⚠️ Risks:** The doctor may miss information about a change in the series of controls/reagents, incorrectly interpret peaks on the curve due to lack of experience, or carelessly make an error during the manual transfer of the result.

### Text Structure of the "To-Be" Business Process (Post-LIS Implementation & Integration)

**1. Zone: Collection Site and Courier (External Contour)**
* **Action 1:** Order registration in the HIS (Hospital Information System). Printing of the paper requisition form is solely for data verification and patient signature (the paper remains at the clinic). A barcode (Sample ID) is affixed to the tube.
* **Action 2:** Courier delivers the cooler bag with the biological specimen (BS). The manifest is transmitted electronically as a registry barcode.
* ✔️ **Mitigated Risk:** No risk of losing the paper form, sending unmarked tubes, or misreading handwriting.

**2. Zone: Laboratory Reception (Sorting)**
* **Action 1:** Unpacking bags.
* **Action 2 (Digital Form):** The receptionist scans the Sample ID. The LIS screen opens the complete electronic order card (patient name, tests, clinic comments). All discrepancies are flagged by the clinic in the system, and the laboratory sees them on screen in real time.
* **Action 3 (On-screen Routing):** Upon scanning the Sample ID, the LIS instantly displays a large color/text prompt on the monitor (e.g., a large window saying "PCR — RED RACK"). The receptionist simply places the tube in the specified rack guided solely by the screen prompt, without needing to recall complex distribution rules.
* ✔️ **Mitigated Risk:** Reduced cognitive load on the receptionist. Routing error risk ("human factor") is eliminated, as the distribution logic is embedded under the hood of the LIS. The risk of form loss is completely eliminated.

**3. Zone: PCR Department — Lab Technician (Clean Zone / Extraction and Aliquoting)**
* **Action 1:** Lab technician scans the rack upon receiving it in the department.
* **Action 2 (Seamless Integration):** Upon scanning the Sample ID, the Analytical Software automatically requests data from the LIS via API. Secondary internal numbers are no longer created. The program immediately "knows" which tests are required for this Sample ID.
* **Action 3:** BS Pre-processing (centrifugation, chemical treatment).
* **Action 4 (Smart Aliquoting):** LIS/Analytical Software automatically generates a virtual rack and microplate map. The extraction and aliquoting process follows the selected scenario. The sorter and light tables control every movement.
* **Action 5:** Lab technician tightly seals the microplate for transfer to the "dirty" zone.
* ✔️ **Mitigated Risk:** Double registration of the BS is eliminated. Cognitive load on the lab technician during well calculation is reduced.

**4. Zone: PCR Department — CDL Doctor (Dirty Zone / Amplification and Validation)**
* **Action 1:** The doctor places the sealed microplate into the thermocycler and scans the microplate barcode. The required program is initiated.
* **Action 2 (Smart Lot Tracking):** The LIS automatically calculates test consumption from the current kit (based on data scanned upon receipt from the warehouse: test quantity, reagent type, kit lot, and control lot). Upon reaching the calculated limit and a potential lot change, the LIS displays a prompt window to the doctor: "Has the control/reagent kit lot changed?" for mandatory confirmation.
* **Action 3:** Amplification results are exported to the internal program ("Analytical Software"), where the doctor performs the validation process.
* **Action 4 (Data Verification):** The doctor opens the Analytical Software to evaluate the result (graphs, peaks, controls) and simultaneously uses the LIS as an additional source of patient information (history of studies, age).
* **Action 5 (Automated Dispatch):** If the result is correct (readable, does not require a re-run), the doctor presses the send button in the internal program. The result is automatically transmitted via API to the LIS and is strictly linked to the patient card via the Sample ID - Patient ID mapping.
* **Action 6 (Result Delivery):** LIS automatically generates the result report. The report is sent to the patient via email, saved in the virtual patient portal, or issued in printed form upon personal request at the medical office.
* ✔️ **Mitigated Risk:** Manual text entry of the "Detected/Not detected" status is eliminated. Data transmission to the LIS via a single button click eliminates the risk of typos. The risk of performing validation with outdated control lot data is eliminated (the system prevents the doctor from forgetting a reagent batch changeover).

### Conclusion: Business Value of Implementation

The transformation of the process from the As-Is to the To-Be model solves the key operational problems of the clinical diagnostic laboratory:

1. **Minimization of the Human Factor (Zero Tolerance to Error):** Manual routing decisions based on memory and cap color, double manual data entry, and manual text transfer of validation results are completely eliminated.
2. **Seamless Data Integration:** The API connection between the LIS and the Analytical Software eliminates the need for secondary (internal) numbers. The system itself manages the routing of the biological specimen.
3. **Single Window Workstation:** The CDL doctor receives all the necessary information for validation (patient history from the LIS + melting curves and controls from the Analytical Software) in a single interface. This significantly reduces the Turnaround Time (TAT) for delivering results.
4. **Traceability:** The transition from paper routing slips to a digital form twin and software-based routing (LIS on-screen prompts, automated sorters) ensures 100% transparency of biological specimen movement at every stage.
5. **Quality Assurance & Compliance:** Proactive tracking of reagent lots and automated calculation of test consumption safeguard the laboratory against issuing invalid results. The system relieves the doctor of the cognitive load of monitoring controls and prevents errors before they occur.
