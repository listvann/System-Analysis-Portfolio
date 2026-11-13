# Stakeholder Register & Power/Interest Grid (LIS)

This document outlines the Stakeholder Register for the Laboratory Information System (LIS) and categorizes them using Mendelow's Power/Interest Grid to define communication strategies.

## 1. Stakeholder Register

| Group | Role | Responsibilities & Pain Points | Expectations / Needs |
| :--- | :--- | :--- | :--- |
| **Internal** | **Lab Manager / CMO** | Monitors Turnaround Time (TAT), reagent usage, and rejection rates. | Analytical dashboards, transparent analyzer workload metrics. |
| **Internal** | **Clinical Pathologist / Lab Physician** | Validates amplification graphs, verifies reagent lot changes. Pain point: manual result transfer to LIS, risk of validating with incorrect controls. | Single-window UI for graphs and patient history, automated lot change alerts, one-click result transmission. |
| **Internal** | **Lab Technician** | Sample preparation, extraction, loading analyzers. Pain point: double specimen registration, manual well counting, manual reagent deduction. | Automated routing via barcode, virtual plate/rack generation, automated Lot Tracking. |
| **Internal** | **Phlebotomist / Nurse** | Collects and labels specimens. Pain point: confusing tube cap colors. | Clear barcode generation, UI prompts for correct specimen containers. |
| **External** | **Patient** | Provides specimens, waits for diagnosis. Pain point: delays and data privacy. | Fast PDF report delivery, strict Protected Health Information (PHI) security. |
| **External** | **State Health Registry / CDC** | Epidemiological control (e.g., infectious disease tracking). | Reliable, secure API integration (e.g., SOAP/XML) for mandatory reporting. |
| **Technical**| **IT Support / DevOps** | Maintains servers and DB uptime. Pain point: system crashes during peak load. | High availability, comprehensive error logging, and monitoring tools. |

## 2. Power/Interest Grid

Stakeholders are mapped into four quadrants to determine the optimal engagement strategy:

*   **Manage Closely (High Power, High Interest):**
    *   *Lab Manager:* Controls the budget and final sign-off. Requires regular sprint reviews and architectural alignment.
    *   *State Health Registry:* Dictates strict legal and technical requirements for data exchange (API contracts).
*   **Keep Satisfied (High Power, Low Interest):**
    *   *IT Support:* They do not use the LIS daily but can block the deployment if the architecture violates security policies. Must be involved in infrastructure reviews.
*   **Keep Informed (Low Power, High Interest):**
    *   *Clinical Pathologists, Lab Technicians & Phlebotomists:* The primary end-users. While their decision-making power is low, the system's success depends on their UX. Essential to involve them during User Acceptance Testing (UAT).
*   **Monitor (Low Power, Low Interest):**
    *   *Patient:* Interacts only with the final output (test result report). Engagement is limited to one-way notifications (SMS/Email).
