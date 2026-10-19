# Financp0 Product Discovery Brief

## 1. Product Summary

Financp0 is a single-user personal finance application designed to help young professionals track budgets, spending, and financial behavior without needing a direct bank connection on every update. The product ingests emails from Gmail, extracts financial transactions, applies user-defined rules, auto-categorizes spending, surfaces flagged edge cases for review, and presents the result in a mobile-friendly, customizable dashboard.

## 2. Problem Being Solved

Users want a simpler, lower-friction way to understand where their money is going without manually entering transactions or reconciling spreadsheets. They want transactions to be automatically generated from email statements and receipts, categorized consistently, and reflected in budgets and spending summaries.

## 3. Primary User

- Single-user personal finance consumer
- Young professionals
- Wants near-real-time visibility into spending and category budgets
- Prefers low-friction automation over manual data entry

## 4. Confirmed Requirements

### Core Workflow
- Gmail-based transaction ingestion
- Scheduled sync and manual trigger
- Initial support for credit-card statements only
- Future bank statements to be used as a correction layer later
- Transaction parsing from emailed financial statements
- Auto-categorization using user-defined rules
- Budget tracking based on user-defined timelines
- Review queue for transactions that need confirmation
- Dashboard overview of budgets, remaining amounts, and trends

### Rules and Automation
- Rule engine similar to a Trello-style automation builder
- Structure: trigger type -> trigger -> action -> target
- Rule order matters and is user-controlled
- One budget per transaction
- Each transaction should show breadcrumbs explaining the rule used

### Budget Model
- User-defined budgets by timeline
- Supported durations: daily, weekly, monthly, yearly
- Expiring budgets also supported (e.g., vacation, medical, travel)
- No automatic budget calculation beyond user-defined periods and expiration behavior

### Review and Trust Model
- Transactions are assumed valid by default unless flagged
- Edge-case transactions are surfaced for user review
- Detection of refunds and notifications
- Flagging of non-standard transactions
- Deduplication using bank-provided transaction/reference IDs
- User can confirm or adjust edge-case entries

### Data and Privacy
- Store only normalized transaction data and metadata; do not retain raw emails in v1
- User-selectable retention options:
  - unlimited
  - 3 years
  - 2 years
  - 1 year
  - 6 months
  - 3 months
  - 1 month
- Sensitive financial data handled with least-privilege, secure token storage, and minimal exposure

### Dashboard and Experience
- Drag-and-drop dashboard widgets
- Mobile first experience through PWA
- User-customizable dashboard layout

## 5. Technical Context

### Frontend
- React + TypeScript
- PWA for mobile support
- Customizable dashboard UI

### Backend
- Python + FastAPI
- API endpoints for email ingestion, transaction processing, rules, budgets, and review flow
- Background scheduled jobs for import processing

### Database
- PostgreSQL
- Storage for transactions, categories, budgets, rules, dashboard configuration, and review state

### Email Integration
- Google Cloud Gmail API
- OAuth-based integration with least-privilege permissions

### Deployment Model
- Hosted SaaS for one user

## 6. Assumptions

- The app is not a direct bank aggregation product in v1
- The app is not a multi-user or household finance product
- “Real time” means near real-time processing after Gmail sync, not bank API polling
- Users are comfortable with email-driven financial ingestion
- Rule-based categorization is the primary automation model for v1

## 7. Decisions and Clarifications from Discovery

### Parsing Strategy
- Different bank email formats will require different parsing methods
- Parsing logic should be extensible by bank/provider

### Rule Precedence
- User-defined rule ordering is the desired precedence mechanism
- Rules are processed in the order defined by the user

### Duplicate Handling
- Duplicate detection uses the bank’s unique transaction/reference ID
- Duplicate records are not treated as new transactions

### Refund Handling
- Refunds are detected automatically
- Refunds trigger a notification and require the transaction to be handled appropriately
- Original transaction should be removed or corrected according to the detected refund logic

### Data Integrity Model
- Source of truth for new records is the email content at the time of ingestion
- Database is the source of truth for already processed records
- Duplicate detection prevents re-processing the same item
- Review workflow catches anomalies before final confirmation

### Budget Semantics
- Budgets are user-defined and do not require automatic calculation by the app
- Budgets can be custom date windows and expiry windows

### Review UX
- Notifications for edge cases
- Separate review/confirmation section for flagged transactions

### User Trust
- Breadcrumbs show why a rule applied to a transaction
- Potentially non-standard transactions are flagged for visibility
- Only one budget is assigned to each transaction

## 8. Risks and Gaps

- Email parsing variability is the biggest technical risk due to inconsistent bank statement formats
- Different email types may require separate parse strategies and validation rules
- Rule precedence and matching need to be clear and predictable for users
- Transaction normalization must handle refunds, reversals, and duplicates carefully
- Budget semantics may become complex if users create overlapping or nested timelines
- Review queue design must remain lightweight and low-friction
- Without a clear rule syntax specification, the app may become confusing or inconsistent to configure

## 9. Open Questions and Decisions Still Needed

1. What is the exact rule syntax for categorization?
   - Proposed: trigger type -> trigger -> action -> target
2. What email types should be supported first after launch?
   - Current answer: credit-card statements first; bank statements later as correction
3. What should be considered a flagged transaction?
   - Current answer: only edge-case/low-confidence items for now
4. Should refund transactions automatically remove the original transaction or require confirmation first?
   - Current answer: automatic detection with notification; workflow still needs clarification
5. What is the minimum detail required for a custom budget model in v1?
   - Current answer: custom date windows and expiry windows, not deeply automated logic
6. What is the exact UX for the review queue in the first release?
   - Current answer: notification + confirm transactions section

## 10. Recommended MVP Scope

### MVP Feature Set
- Gmail email ingestion
- credit-card statement parsing
- transaction extraction and normalization
- duplicate detection using unique reference IDs
- user-defined category rules with ordered precedence
- transaction categorization
- custom date-window budgets and expiry windows
- transaction review queue for flagged cases
- dashboard overview with drag-and-drop widgets
- PWA mobile support

### Out of Scope for MVP
- Direct bank account integrations
- Household/shared-user account management
- Investment tracking
- Complex monthly reconciliation workflows
- Raw email retention
- Full AI-based categorization

## 11. Product Recommendation

The most viable initial product is a focused email-driven budgeting assistant that turns credit-card transaction emails into categorized, budget-aware financial summaries. The product should prioritize parsing reliability, duplicate detection, and a clear review workflow before expanding into broader financial features.

## 12. Decision Summary

This project should start as a single-user, email-first, rule-based budgeting tool with a strong emphasis on trust, auditability, and user control. The first release should focus on credit-card statements, custom category rules, budget timelines, and a mobile dashboard, with bank statement reconciliation added later as a correction layer.
