<!--
Sync Impact Report
- Version change: 1.0.0 -> 2.0.0 (major: project-specific financial data and automation
  requirements materially redefine the existing principles)
- Modified principles:
  - I. Accuracy and Auditability -> I. Financial Data Integrity and Traceability
  - II. Privacy, Security, and Consent by Default -> II. Privacy and User Control
  - III. Test-First Quality and Safe Change -> III. Correctness Through Testing
  - IV. Transparent, Explainable Decisions -> IV. Explainable Automation
  - V. Simplicity, Observability, and Maintainability -> V. Reliable, Extensible Integrations
- Added sections: none
- Removed sections: none
- Follow-up TODOs: none
-->

# Financp0 Constitution

## Core Principles

### I. Financial Data Integrity and Traceability
The system MUST represent each imported transaction accurately and retain normalized transaction
data and the metadata needed to trace its source and processing. It MUST deduplicate using the
transaction or reference identifier provided by the source, and MUST NOT silently overwrite
conflicting processed records. Each transaction MUST have no more than one assigned budget.
Refunds MUST notify the user and be reflected in budget totals without silently erasing the
transaction history. These requirements protect user trust in financial summaries.

### II. Privacy and User Control
The system MUST request only the Gmail permissions necessary for the user-approved import
workflow, protect OAuth credentials and financial data, and provide a way to revoke Gmail access.
It MUST NOT retain raw emails after processing; only normalized transactions and required metadata
may be stored. Users MUST be able to select one of the supported retention periods: unlimited,
three years, two years, one year, six months, three months, or one month. Secrets and personal
financial data MUST NOT appear in application logs. These controls limit exposure of sensitive
information.

### III. Correctness Through Testing
Changes to transaction extraction, provider-specific parsing, deduplication, refunds, rule
ordering, budget periods, retention, or financial calculations MUST include automated tests for
the affected behavior. Integrations between Gmail ingestion, transaction processing, persistence,
and user review MUST be tested at their boundaries. A release MUST NOT proceed with known
regressions in financial correctness, data integrity, or access control.

### IV. Explainable Automation
Categorization rules MUST be user-configurable using clear trigger, condition, action, and target
building blocks, with preset options where applicable. Rules MUST be applied in the order chosen
by the user, and the applied rule MUST be visible as transaction-level breadcrumbs. Transactions
identified as non-standard or requiring validation MUST be surfaced in a confirmation area and
notify the user. Automation MUST NOT obscure why a transaction was categorized or assigned to a
budget.

### V. Reliable, Extensible Integrations
Email parsing MUST support distinct, extensible methods for different financial providers. The
initial release MUST ingest credit-card transaction emails through the Gmail API; bank statements
are deferred to a later correction workflow. Import MUST support scheduled and user-triggered
runs. The user experience MUST provide a responsive PWA, including a configurable dashboard with
drag-and-drop widgets. The implementation MUST favor clear, maintainable solutions over
unnecessary complexity.

## Product and Technical Constraints

The project uses React with TypeScript for the frontend, Python with FastAPI for the backend, and
PostgreSQL for persistent application data. Gmail integration uses the Google Cloud Gmail API and
user-authorized access. The product is a hosted SaaS for individual personal finance users; shared
household accounts and direct bank connections are outside the initial release.

Budgets MUST support user-defined daily, weekly, monthly, yearly, and custom date windows, as well
as expiring budgets. The system MUST calculate budget usage consistently from assigned
transactions and the configured period. It MUST NOT assign a transaction to multiple budgets.
Financial periods, currency precision, and date handling MUST be explicit and consistently applied.

## Development and Quality Gates

Work MUST be grounded in a clear requirement and acceptance criteria before implementation.
Plans and reviews MUST assess compliance with this constitution, especially privacy, data integrity,
and explainability. Provider parsing changes MUST be validated against representative message
formats without retaining raw email content as application data. Changes to financial logic MUST
include focused automated tests and evidence that those tests pass. Breaking data or API changes
MUST include a documented compatibility or migration plan.

## Governance

This constitution governs product and engineering decisions for Financp0. Amendments require a
documented rationale and project-owner review. Version numbers follow semantic versioning:
MAJOR for backward-incompatible governance changes or material principle redefinitions, MINOR for
new principles or materially expanded requirements, and PATCH for clarifications that do not change
obligations.

Contributors MUST assess compliance during planning, implementation, review, and final validation.
Exceptions MUST be documented with their rationale, impact, and approval before adoption. If a
proposal conflicts with a MUST requirement, the design MUST change or receive an explicit
constitution amendment before the conflicting work proceeds.

**Version**: 2.0.0 | **Ratified**: 2026-10-18 | **Last Amended**: 2026-10-19
