# Meeting Minutes

**Project:** Next-Gen Laboratory Information System (LIS) Development  
**Date:** October 26, 2026  
**Attendees:** 
*   Anna Li-Yu-Kun (System Analyst)
*   Dr. John Smith (Lab Manager)

## 📌 Agenda
*   Review of frequent Turnaround Time (TAT) violations in PCR testing.
*   Identification of bottlenecks during the pre-analytical phase (Accessioning department) and the analytical phase (manual reagent consumption tracking).

## 🎯 Key Decisions
1.  **Deprecate Visual Sorting:** Confirmed that manually sorting specimens by cap color leads to routing errors and delays, as cap colors (e.g., yellow) are shared across multiple laboratory departments.
2.  **New Barcode Algorithm:** Approved the transition from the legacy barcode format to a 12-digit structure: **Order Root / INZ (9 digits) + Container Code (3 digits)**.
3.  **Automated Routing:** The 3-digit Container Code will act as the primary lookup key in the LIS database to unambiguously identify the specimen type and its destination (e.g., PCR Genetics, ELISA). Routing will be system-enforced via barcode scanning rather than visual inspection.
4.  **Implement Automated Lot Tracking:** Manual well counting and reagent deduction by lab technicians are deprecated. Approved the solution to automatically deduct tests from reagent lots upon virtual rack generation.

## 📝 Action Items

| Task | Owner | Due Date | Status |
| :--- | :--- | :--- | :--- |
| Design the conceptual ER Diagram for the new routing database logic and reagent lot tracking | Anna Li-Yu-Kun | Oct 28, 2026 | Completed |
| Provide HIPAA-compliant (de-identified) specimen barcodes and current Accessioning SOPs | Dr. John Smith | Oct 29, 2026 | To Do |
| Draft the API integration specification for result delivery (incorporating the Clinical Pathologist validation gateway) | Anna Li-Yu-Kun | Nov 02, 2026 | Completed |

**Next Sync:** October 30, 2026 (Database Architecture Review).
