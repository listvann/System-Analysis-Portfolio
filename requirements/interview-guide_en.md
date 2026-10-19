# Stakeholder Interview Guide

**Stakeholder:** Lab Manager / CMO

**Topic:** Specimen routing issues and Turnaround Time (TAT) violations.

**Objective:** Identify root causes of pre-analytical delays and gather business requirements for the new LIS.

---

## 1. Introduction and Context
*   State the meeting objective: designing labeling and routing logic for the new LIS.
*   Confirm time constraints.

## 2. As-Is Process Discovery
1.  How exactly does the workflow operate from the moment the courier delivers the transport cooler with specimens to the Accessioning department?
2.  How does the Accessioning Technician know whether to route a specific specimen to PCR Genetics or ELISA?
3.  How does the lab technician at the analyzer record that a specimen is in progress? (Barcode scanner, manual log entry?)

## 3. Root Cause Analysis: 5 Whys Technique
*Problem Statement: Frequent TAT violations for PCR assays.*

*   **Why is result delivery delayed?** 
    *   *Answer:* Technicians waste time searching for the correct tubes in the racks before loading the analyzer.
*   **Why are they wasting time searching?**
    *   *Answer:* The racks arrive from Accessioning with mixed tubes meant for different PCR assays.
*   **Why are the tubes mixed?**
    *   *Answer:* Sorting is done manually by cap color (e.g., yellow), which is shared across multiple departments.
*   **Why do they rely on cap color instead of the system?**
    *   *Answer:* The current barcode (INZ / Order Root) displays the entire patient order. It lacks routing info for the specific tube, leaving the accessioner blind.
*   **Why is routing info missing from the barcode?**
    *   *Answer (Root Cause):* The legacy barcode generation logic does not support linking to a specific department and specimen type.
    *   **Solution:** Implement a 12-digit barcode (INZ + Container Code) for automated routing of each physical tube.

## 4. Expectations and Metrics
1.  What metrics will you, as the Lab Manager, want to see on your dashboard once the new labeling system is deployed?
2.  How should the system react if an ELISA technician scans a "wrong" tube (intended for PCR)? Do we need a hard stop or just a UI warning?
3.  What mandatory compliance reports must be generated for the State Department of Health (DOH) / CDC at the end of the shift?

## 5. Wrap-up and Follow-up
*   Summarize key points. Meeting Minutes will be sent by EOD.
*   Request access to current Accessioning SOPs and specimen barcodes (strictly test data or HIPAA-compliant de-identified records) for further analysis.
