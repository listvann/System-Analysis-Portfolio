# Stakeholder Register & Power/Interest Grid (LIS)

This document outlines the Stakeholder Register for the Laboratory Information System (LIS) and categorizes them using Mendelow's Power/Interest Grid to define communication strategies.

## 1. Stakeholder Register

| Group | Role | Responsibilities & Pain Points | Expectations / Needs |
| :--- | :--- | :--- | :--- |
| **Internal** | **Lab Manager / CMO** (Project Sponsor) | Monitors Turnaround Time (TAT), reagent usage, and rejection rates. | Analytical dashboards, transparent analyzer workload metrics. |
| **Internal** | **Lab Technician** (End User) | Validates results, processes specimens. Pain point: manual data entry errors. | Automated routing via barcodes, direct integration with lab analyzers. |
| **Internal** | **Phlebotomist / Nurse** (End User) | Collects and labels specimens. Pain point: confusing tube cap colors. | Clear barcode generation, UI prompts for correct specimen containers. |
| **External** | **Patient** (End Customer) | Provides specimens, waits for diagnosis. Pain point: delays and data privacy. | Fast PDF report delivery, strict PHI (Protected Health Information) security. |
| **External** | **State Health Registry** (Regulator) | Epidemiological control (e.g., infectious disease tracking). | Reliable, secure API integration (e.g., SOAP/XML) for mandatory reporting. |
| **Technical**| **IT Support / DevOps** (Support) | Maintains servers and DB uptime. Pain point: system crashes during peak load. | High availability, comprehensive error logging, and monitoring tools. |

## 2. Power/Interest Grid

Stakeholders are mapped into four quadrants to determine the optimal engagement strategy:

*   **Manage Closely (High Power, High Interest):**
    *   *Lab Manager:* Controls the budget and final sign-off. Requires regular sprint reviews and architectural alignment.
    *   *State Health Registry (Regulator):* Dictates strict legal and technical requirements for data exchange (API contracts).
*   **Keep Satisfied (High Power, Low Interest):**
    *   *IT Support:* They do not use the LIS daily but can block the deployment if the architecture violates security policies. Must be involved in infrastructure reviews.
*   **Keep Informed (Low Power, High Interest):**
    *   *Lab Technicians & Phlebotomists:* The primary end-users. While their decision-making power is low, the system's success depends on their UX. Essential to involve them during UAT (User Acceptance Testing).
*   **Monitor (Low Power, Low Interest):**
    *   *Patient:* Interacts only with the final output (test result report). Engagement is limited to one-way notifications (SMS/Email).
