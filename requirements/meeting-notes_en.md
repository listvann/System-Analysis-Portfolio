# Meeting Minutes

**Project:** Next-Gen Laboratory Information System (LIS) Development  
**Date:** October 26, 2026  
**Attendees:** 
*   Li-Iu-Kun Anna (System Analyst)
*   Dr. John Smith (Lab Manager)

## 📌 Agenda
*   Review of frequent Turnaround Time (TAT) violations in PCR testing.
*   Identification of bottlenecks during the pre-analytical phase (specimen routing) and the analytical phase (reagent consumption tracking).
*   Discussion on integration requirements for robotic liquid handling stations with the new LIS.

## 🎯 Key Decisions
1.  **Deprecate Visual Sorting:** Confirmed that manually sorting specimens by cap color leads to routing errors and delays, as cap colors (e.g., yellow) are shared across multiple laboratory departments.
2.  **New Barcode Algorithm:** Approved the transition from the legacy barcode format to a 12-digit structure: **Order Root / INZ (9 digits) + Container Code (3 digits)**.
3.  **Automated Routing:** The 3-digit Container Code will act as the primary lookup key in the LIS database to unambiguously identify the specimen type and its destination (e.g., PCR Genetics, ELISA). Routing will be system-enforced via barcode scanning rather than visual inspection.
4.  **Implement Automated Lot Tracking:** Manual well counting and reagent deduction by lab technicians are deprecated. Approved the solution to automatically deduct tests from reagent lots upon virtual rack generation.
5. **Hard Stop on Mismatched Routing:** Scanning a tube whose container code belongs to another department triggers a hard block, prevents virtual rack assignment and reagent deduction, and displays an alert with the target department.
6. **Morning Peak Capacity:** The workflow and integration pipeline are sized for morning accessioning peak hours without scanner UI lag or dropped events.
7. **Offline Fallback Procedure (for Urgent/STAT Specimens):** During temporary LIS unavailability, operations rely on the 12-digit barcode (`INZ + Container Code`) and a shift log for urgent specimens, syncing data once connectivity is restored.

## 📝 Action Items

| Task | Owner | Due Date | Status |
| :--- | :--- | :--- | :--- |
| Design the logical data model (ER Diagram) for the new barcode logic and lot tracking system | Li-Iu-Kun Anna | Oct 28, 2026 | Completed |
| Provide the current container master data export and Accessioning SOPs | Dr. John Smith | Oct 29, 2026 | Completed |
| Draft the API integration specification for result delivery (incorporating the Clinical Pathologist validation gateway) | Li-Iu-Kun Anna | Nov 02, 2026 | Completed |

**Next Sync:** October 30, 2026 (Database Architecture Review).
