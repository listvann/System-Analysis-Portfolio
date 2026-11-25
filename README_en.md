*[🇷🇺 Русский](README.md) ∙ [🇬🇧 English](README_en.md)*

# Li-Iu-Kun Anna – System Analyst | HealthTech & API Integrations

System Analyst with deep domain expertise in MedTech/HealthTech.

My background includes 7 years of hands-on experience in PCR diagnostics laboratories. I have a comprehensive, inside understanding of clinical workflows, biological specimen routing, and laboratory equipment operations. In the IT sector, I translate these physical processes into system logic: designing architecture, API contracts, and data exchange workflows between medical systems (LIS, EHR, Analytical Software).

**Open to Full-time Opportunities:** System Analyst, Integration Analyst.

## 🛠 Technical Skills

* **Domain Expertise:** HealthTech, Laboratory Information Systems (LIS/LIMS), Electronic Health Records (EHR), Medical Equipment Integration.
* **Systems & Business Analysis:** Requirements elicitation, business process modeling (BPMN 2.0), system interaction design (UML Sequence, Use Cases).
* **Integrations & APIs:** REST/SOAP API design, contract specification (OpenAPI 3.0 / Swagger), API testing (Postman), data formats (JSON, XML).
* **Architecture & Databases:** SQL (PostgreSQL), relational database design (ERD), message brokers understanding (RabbitMQ / Kafka).
* **Tools:** Draw.io, PlantUML, Markdown, Git/GitHub, Jira/Confluence.

## 📁 Architectural Case Study: Laboratory Information System (LIS)

A comprehensive "To-Be" system design case focused on transitioning to a paperless workflow, automated reagent Lot Tracking, and seamless integration with liquid handling stations.

**Business Analysis & Requirements:**
* [Stakeholder Map & Power/Interest Grid](project-lis/stakeholders_en.md) — roles, interests, and responsibilities across the lab workflow.
* [Stakeholder Deep-Dive Interview (5 Whys)](requirements/interview-guide_en.md) — root cause analysis of bottlenecks and manual errors at accessioning.
* [Business Requirements Meeting Notes](requirements/meeting-notes_en.md) — agreed business rules, constraints, and lab scope.
* [As-Is & To-Be Process Modeling (BPMN 2.0)](project-lis/business-processes_en.md) — shift from paper logs to barcode scanning and reagent lot tracking.
* [User Stories & Use Cases](requirements/use-cases_en.md) — specimen accessioning, result validation flows, and acceptance criteria.
* [Non-Functional Requirements (NFR)](requirements/nfr_en.md) — morning peak performance, EHR outage resilience, and HIPAA / GDPR data safeguards.

**System Architecture & Integration Design:**
* [Database Architecture (ER Diagram & SQL Scripts)](project-lis/database_en.md) — schema for orders, specimens, results, reagent lots, and `outbox_messages`.
* [Result Delivery API Spec (OpenAPI / Swagger) & Sequence Diagram](project-lis/api-results_en.md) — `POST /api/v1/webhook/lab-results` contract, response codes, and idempotency.
* [REST vs. SOAP Comparison](architecture-notes/rest-soap-comparison_en.md) — rationale for choosing REST/Webhook + RabbitMQ for EHR integration.
* [Asynchronous Messaging via Message Broker (RabbitMQ)](project-lis/message-broker_en.md) — Transactional Outbox, retry queues, and DLQ handling.
