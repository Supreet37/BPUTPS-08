# MEIL ESG Reporting Platform

A centralized, web-based ESG (Environmental, Social, Governance) reporting system built for Megha Engineering and Infrastructures Limited (MEIL) and its group of companies, designed to meet SEBI's BRSR (Business Responsibility and Sustainability Reporting) requirements across a large, multi-entity organizational structure.

---

## 1. Problem Statement

**Development of an online portal for BRSR reporting of Corporate ESG Reports.**

The UN has set out a series of Sustainable Development Goals (SDGs), and ESG (Environmental, Social, and Governance) has emerged as the standard framework for evaluating how organizations perform against sustainability and ethical-impact expectations in that context. In India, the Securities and Exchange Board of India (SEBI) has mandated a standardized disclosure format — **BRSR (Business Responsibility and Sustainability Reporting)** — for large corporate entities to report their ESG performance in a uniform, comparable way.

**Megha Engineering and Infrastructures Limited (MEIL)** is a large infrastructure group with multiple subsidiaries and approximately **250 projects** spread across India and abroad, spanning sectors such as hydrocarbons, power, transport, water management, irrigation, defense, and telecom.

The objective of this project is to design and build a web-based software solution that allows the MEIL Group of Companies to manage its ESG reporting in a comprehensive, detailed, and SEBI-compliant manner. Because of MEIL's scale and organizational complexity, the system must support reporting at multiple levels of granularity — from individual **projects**, up through **subsidiary entities**, and finally consolidated at the **group** level — rather than treating MEIL as a single flat reporting entity.

---

## 2. Requirements

### 2.1 Functional Requirements

**A. Multi-Level Reporting Hierarchy**
- Support data entry, validation, and roll-up across four levels: **Project → Business Unit → Subsidiary → Group (MEIL Central)**.
- Each project is assigned to exactly one subsidiary; each subsidiary rolls up into the overall MEIL group total.
- The system must scale comfortably to ~250 concurrent projects and multiple subsidiaries without breaking the reporting workflow.

**B. SEBI BRSR Compliance**
- All data fields, sections, and the final generated report must conform exactly to SEBI's officially published BRSR format, which consists of:
  - **Section A — General Disclosures**: entity details, products/services, operations, employee/worker details, CSR, complaints and grievances.
  - **Section B — Management and Process Disclosures**: policies, governance structures, and board-level oversight of ESG matters.
  - **Section C — Principle-wise Performance Disclosures**: structured around the **9 National Guidelines on Responsible Business Conduct (NGRBC) Principles**, each containing mandatory **Essential Indicators** and voluntary **Leadership Indicators**.
- Support for **BRSR Core** — the subset of indicators that require third-party assurance — with separate tagging/handling from standard indicators.
- Support for both **standalone** and **consolidated** reporting boundaries, with the chosen boundary locked for consistency across reporting periods.

**C. Granular ESG Data Collection**
- **Environmental**: fuel consumption (type, quantity, unit), electricity consumption (source, quantity), water usage, waste generation and disposal, and raw activity data needed to compute emissions (no manually entered CO₂e values — these are calculated, not typed in).
- **Social**: employee and worker headcounts (with clear distinction between the two categories, and between permanent/non-permanent roles), training hours, health and safety metrics, welfare and working conditions, diversity metrics, grievance redressal, and human rights disclosures.
- **Governance**: policies, compliance records, audit trails, risk management disclosures, ethics and anti-corruption measures, and whistleblower mechanisms.
- **Value Chain Partners**: capture of relevant ESG data from upstream/downstream partners (suppliers, contractors, sub-contractors) where required by BRSR.
- **Evidence-backed data entry**: every data point must be supportable with uploaded evidence (fuel invoices, electricity bills, water records, waste documentation, HR/safety records, audit reports).

**D. Validation and Data Quality**
- Multi-step validation of all submitted data, including input completeness checks, value/anomaly checks, reporting-period checks, evidence-attachment checks, cross-field consistency checks, and final submission checks.
- Anomalous or incomplete data must be blocked from consolidation until it is corrected or independently verified.

**E. Review, Approval, and Calculation Workflow**
- A defined record lifecycle: **Draft → Submitted → Under Review → Returned/Approved → Calculated → Locked → Consolidated → Reported.**
- Subsidiary-level reviewers must be able to validate submitted project data against evidence, query or return incorrect entries, and approve/"stamp" verified data.
- Emissions (Scope 1 and Scope 2) must be system-calculated from raw activity data using centrally maintained, versioned emission factors — never manually entered or edited by the person submitting the activity data.
- Scope 3 (value-chain) emissions must be handled at the central/group level across categories such as purchased goods, transport, business travel, and leased assets.

**F. Consolidation and Reporting**
- Automatic roll-up of approved data from Project → Subsidiary → Group level, covering emissions (Scope 1/2/3, total GHG), energy, water, waste, workforce, safety, and governance metrics.
- Generation of the final BRSR-compliant report (exportable as PDF/Excel), auto-mapped from approved, consolidated data.
- Mapping of disclosed metrics to relevant UN SDGs.

**G. Auditability and Traceability**
- Full **data lineage** from any top-level group metric down to the originating project-level activity record and its supporting evidence document.
- An **immutable audit log** capturing user identity, timestamps, original vs. edited values, the emission factor version applied, and approver sign-off, at every step.

**H. Access Control**
- Role-based access aligned to the reporting hierarchy: a Project Handler can only access their own assigned project's data; a Subsidiary Handler can only access the projects assigned to their subsidiary; Group/Central users can view consolidated, approved data with full drill-down rights.
- Strict data privacy between unrelated projects and unrelated subsidiaries.

**I. Dashboards and Monitoring**
- Executive-level dashboards summarizing Environmental, Social, and Governance performance across the group.
- Subsidiary-to-subsidiary comparison and drill-down views.
- Historical trend tracking and progress-against-target tracking.
- An exception/data-quality view highlighting critical issues, anomalies, pending reviews, and missing entries, with the ability to raise a one-click audit query back to the relevant subsidiary.

### 2.2 Non-Functional Requirements

- **Scalability**: must handle data entry and reporting across ~250 projects and multiple subsidiaries simultaneously.
- **Data integrity**: calculations (especially emissions) must be reproducible and tamper-evident.
- **Security**: strict, hierarchy-aware access control; sensitive documents (evidence) must be securely stored and access-logged.
- **Auditability**: every number in the final report must be traceable back to its source evidence.
- **Usability**: data-entry forms must be simple enough for non-technical project-level staff (e.g., site engineers) to use accurately.
- **Compliance-accuracy**: the system must stay aligned with SEBI's BRSR format, including future regulatory updates.

---

## 3. System Architecture

The platform is designed around **three reporting levels**, connected by a structured data flow, validation pipeline, and consolidation engine.

### 3.1 Level 1 — Project (Operational Data Entry)

- A **Project Handler**, assigned to exactly one project, enters raw **activity data only** — never manually computed emissions.
  - Environmental: fuel type/quantity/unit, electricity (kWh, source), water, waste, emissions-related activity data.
  - Social: employee/worker counts, training, health & safety, welfare, diversity.
  - Governance: policies, compliance, audits, risk management, ethics/anti-corruption, grievance handling.
- Every entry must be backed by **evidence upload** (invoices, bills, records, audit reports).
- A **6-step Validation Engine** checks: input completeness, value/anomaly thresholds, reporting period correctness, evidence presence, cross-field consistency, and submission readiness.
- On passing validation, the record status moves from **Draft → Submitted**, becoming visible only to that project and its assigned Subsidiary Handler.
- Failed validation returns the record for correction and resubmission.

### 3.2 Level 2 — Subsidiary (Verification, Approval, and Calculation)

- A **Subsidiary Handler** receives submitted project data in a **Submission Inbox**, scoped only to the projects assigned to their subsidiary.
- Reviews and validates entries against uploaded evidence, runs local anomaly checks, and can query or return data to the Project Handler.
- On approval, the **Emission Calculation Engine** computes:
  - **Scope 1** = Fuel quantity × Approved (centrally versioned) Emission Factor
  - **Scope 2** = Electricity consumption (kWh) × Approved grid Emission Factor
- Every calculation record stores the activity data, unit, emission factor and its version, the resulting CO₂e, and the user/timestamp.
- Approved project data is aggregated into a **Subsidiary Total**, digitally stamped, and the record status progresses: **Approved → Calculated → Locked.**

### 3.3 Level 3 — MEIL Central (Consolidation and Intelligence)

- **Central Intake & Lock**: only approved, locked subsidiary data is accepted; once received it is frozen and indexed for audit.
- **Consolidation Engine**: rolls up Project → Subsidiary → MEIL-wide totals across Scope 1, Scope 2, and Scope 3 (value-chain) emissions, plus energy, water, waste, workforce, safety, and governance metrics.
- **Exception Management**: surfaces critical issues, anomalies, pending reviews, and missing entries, with one-click audit queries routed back to the relevant subsidiary.
- **Data Quality Center**: tracks each record's state as Verified / Under Review / Missing / Anomaly; anomalous data is blocked from consolidation until verified.
- **ESG Data Lineage**: every consolidated metric can be traced back through subsidiary → project → activity record → evidence → the emission factor engine/version used → the approval stamp.
- **Executive ESG Dashboard**: covers Environmental (Scope 1/2/3, total GHG, energy, renewable %, water, waste), Social (workforce, training, safety/LTIFR, turnover, women %, CSR), and Governance (ethics, whistleblower cases, compliance training, pending approvals, anomaly flags).
- **Subsidiary Comparison & Drill-Down**, **Target & Progress Tracking**, **Historical Trends**, and **SDG Mapping** views.
- **BRSR/ESG Report Generator**: converts approved, consolidated data into the final BRSR-mapped report, exportable as PDF/Excel.

### 3.4 Shared Platform Services (used across all three levels)

- **Authentication & Role-Based Access Control**: enforces project/subsidiary-scoped visibility; blocks cross-project and cross-subsidiary access by default.
- **Validation Rules Configuration**: centrally defines required fields/units, anomaly thresholds, required evidence per metric, and reporting-period rules.
- **Emission Factor Master**: a centrally maintained, versioned source of truth for fuel and grid-electricity emission factors — not editable by Project Handlers.
- **Evidence Storage**: securely stores and links bills, invoices, receipts, meter records, and HR/safety/audit documents to their corresponding data entries.
- **Immutable Audit Log**: append-only log of user identity, timestamps, original vs. edited values, emission factor versions used, approver stamps, and source document links.
- **Core Database**: stores subsidiary/project master data, raw activity data (E/S/G), calculations, approvals, and consolidated totals.
- **Workflow & Notifications**: manages the record status lifecycle, return/query routing, pending-approval alerts, and reminders.

### 3.5 End-to-End Data Flow (Summary)

```
Project Handler
   → Collect records → Enter E/S/G activity data → Attach evidence
   → Validate (6 checks) → Submit (status: SUBMITTED)

Subsidiary Handler
   → Receive in Submission Inbox → Review & validate vs evidence
   → (if issues) Return/Query to Project Handler
   → (if OK) Emission Calculation (Scope 1 & 2) → Approve & Stamp
     → Subsidiary Total (status: APPROVED → CALCULATED → LOCKED)

MEIL Central
   → Central Intake & Lock (approved data only)
   → Data-Quality Check & Anomaly Gate
   → Consolidation (Project → Subsidiary → MEIL; Scope 1/2/3)
   → Lineage & Audit Indexing
   → Executive Dashboard, Subsidiary Comparison, Targets/Trends, SDG Mapping
   → BRSR Mapping → PDF/Excel Report Export
   → Delivered to Board / Auditors / Regulators
```

---

## 4. Features to Be Included

### 4.1 Project Handler Portal (Level 1)
- Role-restricted login showing only the handler's assigned project.
- Structured data-entry forms for Environmental, Social, and Governance activity data.
- Evidence upload against each data entry (invoices, bills, HR/safety records, audit reports).
- Real-time validation feedback before submission (missing fields, out-of-range values, missing evidence).
- Draft-save and resume capability.
- Submission status tracker (Draft / Submitted / Returned / Approved).
- Notifications for returned/queried entries requiring correction.

### 4.2 Subsidiary Portal (Level 2)
- Submission inbox listing all pending project submissions under that subsidiary.
- Side-by-side review of submitted data against uploaded evidence.
- Ability to query or return specific entries to the Project Handler with comments.
- Automated Scope 1 and Scope 2 emission calculation using the central Emission Factor Master.
- Subsidiary-level totals and approval/"digital stamp" action.
- Local anomaly and consistency checks before approval.
- View of subsidiary-wide historical submissions.

### 4.3 MEIL Central Command Center (Level 3)
- Executive ESG dashboard (Environmental / Social / Governance summary views).
- Group-wide and Scope 1/2/3 GHG consolidation view.
- Subsidiary comparison and drill-down (down to the original project record and evidence).
- Data Quality Center showing Verified / Under Review / Missing / Anomaly status across the group.
- Exception management queue with one-click audit queries to subsidiaries.
- Target-setting and progress-tracking against ESG goals.
- Historical trend analysis across reporting periods.
- SDG mapping view linking disclosed metrics to relevant UN Sustainable Development Goals.
- BRSR/ESG Report Generator with PDF and Excel export.
- Full audit trail and data lineage viewer (metric → subsidiary → project → activity record → evidence → emission factor version → approval).

### 4.4 Admin Console
- Emission Factor Master management (add/update/version fuel and grid-electricity factors).
- Validation rules configuration (required fields, units, anomaly thresholds, required evidence per metric, reporting-period rules).
- User, role, and project-to-subsidiary mapping management.
- BRSR field-mapping configuration (so the system can be kept aligned with future SEBI format updates).

### 4.5 Cross-Cutting Platform Features
- Role-based access control strictly scoped to project/subsidiary boundaries.
- Immutable, append-only audit logging across every create/update/approve action.
- Notification and reminder system for pending approvals and returned entries.
- Value Chain Partner data tracking (for relevant upstream/downstream BRSR disclosures).
- BRSR Core indicator tagging (to flag assurance-critical KPIs separately from standard ones).
- Employee vs. Worker and permanent vs. non-permanent workforce classification.
- Standalone vs. consolidated reporting-boundary configuration, locked per reporting cycle.
- Multi-year/multi-period data retention for year-over-year trend comparisons.

---

## 5. Supporting Reference Materials

The following official and reference documents were used to define the above requirements and should be referred to for exact field-level BRSR specifications:

- SEBI BRSR Format — **Annexure I** (the exact reporting template/fields)
- SEBI BRSR Guidance Note — **Annexure II** (clause-by-clause explanation of Annexure I)
- BRSR FAQs (practical clarifications on applicability, BRSR Core, value chain partners, etc.)
- ICAI Background Material on BRSR (detailed concept/calculation reference)
- A real, filed BRSR report from a comparable large infrastructure group (used as a format/sample-data reference)
- SEBI's original note on the rationale and intent behind introducing BRSR

---

## 6. Status

This repository contains the design and development of the MEIL ESG Reporting Platform as part of a hackathon submission. The requirements and architecture above reflect the current design scope and may evolve as development progresses.