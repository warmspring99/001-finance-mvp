# Feature Specification: Financp0 Personal Finance MVP

**Feature Branch**: `001-finance-mvp` (not created; repository has no Git branch configured)

**Created**: 2026-10-19

**Status**: Draft

**Input**: User description: Deliver Financp0's initial personal finance experience: import credit-card transaction emails from Gmail on a schedule or manually, parse and deduplicate transactions, categorize them with user-ordered rules, track user-defined budgets, flag exceptions for confirmation, and present results in a configurable mobile-friendly dashboard.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Import Credit-Card Transactions (Priority: P1)

As a young professional, I want Financp0 to retrieve transaction information from my Gmail so that my spending is tracked without manually entering each purchase or connecting directly to my bank.

**Why this priority**: Transaction intake supplies the data required for useful budgets and spending summaries.

**Independent Test**: Authorize email access, trigger an import against representative supported credit-card messages, and verify that extracted transactions appear once in the transaction history or are explicitly flagged for confirmation.

**Acceptance Scenarios**:

1. **Given** a user has authorized Gmail access and has supported credit-card transaction emails, **When** the user starts an import, **Then** Financp0 extracts transaction details and presents the import result.
2. **Given** scheduled import is enabled, **When** the configured schedule runs, **Then** Financp0 retrieves and processes eligible new messages and reports the outcome.
3. **Given** a transaction has already been processed and its source identifier is seen again, **When** another import processes it, **Then** Financp0 does not create a second transaction.
4. **Given** a message cannot be parsed reliably or lacks required transaction details, **When** it is processed, **Then** Financp0 flags it for user review rather than silently dropping it or inventing values.
5. **Given** an import completes, **When** transaction processing is finished, **Then** raw email content is not retained and only normalized transaction data and required metadata remain.

---

### User Story 2 - Categorize Transactions and Set Budgets (Priority: P1)

As a user, I want to create categories, ordered categorization rules, and budgets for time periods I choose so that transactions are consistently organized and budget usage reflects my preferences.

**Why this priority**: Categorization and budgets turn imported transactions into useful, personalized financial information.

**Independent Test**: Create categories, budgets, and overlapping rules; process a known transaction; verify the first matching rule in user-defined order is applied, its explanation is shown, and its amount contributes to no more than one budget.

**Acceptance Scenarios**:

1. **Given** the user has configured ordered rules, **When** a transaction matches one or more rules, **Then** the first matching rule in the user's order determines the categorization action.
2. **Given** a rule matches a transaction, **When** the transaction is displayed, **Then** the user can see which rule was applied.
3. **Given** the user creates a budget, **When** they configure its amount, category, and date window, **Then** Financp0 tracks spending and remaining amount within that configured window.
4. **Given** a transaction is categorized, **When** budget totals are calculated, **Then** the transaction contributes to no more than one budget.
5. **Given** a budget has an expiry date, **When** that date passes, **Then** the budget is no longer treated as active for later spending.

---

### User Story 3 - Review Exceptions and Refunds (Priority: P1)

As a user, I want potentially non-standard transactions and refunds to be brought to my attention so that I can validate exceptions and understand changes to my spending.

**Why this priority**: Financial automation must make exceptions visible so users can trust the resulting records and budget totals.

**Independent Test**: Submit examples of an ambiguous transaction, a non-standard transaction, and a refund; verify that exceptions appear in the confirmation area and notifications are presented where required.

**Acceptance Scenarios**:

1. **Given** a transaction is identified as ambiguous or non-standard, **When** processing finishes, **Then** it appears in a confirmation area and the user receives a notification.
2. **Given** a transaction is awaiting confirmation, **When** the user confirms or corrects it, **Then** the review state and resulting categorization are updated and visible.
3. **Given** an incoming transaction is identified as a refund, **When** it is processed, **Then** the user is notified and the refund is reflected in spending and budget totals without silently erasing the original transaction history.
4. **Given** a refund cannot be confidently associated with an earlier purchase, **When** processing finishes, **Then** it is flagged for user confirmation rather than silently changing another transaction.

---

### User Story 4 - Monitor and Configure the Dashboard (Priority: P2)

As a user, I want to review budget usage, remaining amounts, and spending trends in a configurable mobile-friendly dashboard so that I can understand my financial position at a glance.

**Why this priority**: The dashboard is the main way users consume the imported and categorized financial data.

**Independent Test**: Load a set of categorized transactions and active budgets, verify displayed totals and remaining amounts, rearrange dashboard widgets, and confirm the layout remains after reopening the app on a mobile-sized screen.

**Acceptance Scenarios**:

1. **Given** transactions and budgets exist, **When** the user opens the dashboard, **Then** it shows budget usage, remaining amounts, and spending trends for the relevant user-defined periods.
2. **Given** the user rearranges or resizes dashboard widgets, **When** the dashboard is reopened, **Then** the chosen layout is preserved.
3. **Given** the user opens Financp0 on a mobile device, **When** they review budgets, transaction history, or flagged items, **Then** the primary tasks remain usable without a desktop layout.

---

### User Story 5 - Control Import, Notifications, and Retention (Priority: P2)

As a user, I want to control when email imports run and how long normalized financial records are kept so that automation and data retention match my preferences.

**Why this priority**: User control over email access, notifications, and retention is necessary for a privacy-sensitive finance product.

**Independent Test**: Configure an import schedule, manually run an import, change retention, and revoke Gmail access; verify each setting takes effect and the user receives a clear status.

**Acceptance Scenarios**:

1. **Given** Gmail access is authorized, **When** the user changes the import schedule or starts a manual import, **Then** the requested import behavior is reflected.
2. **Given** the user selects a retention period, **When** normalized transaction records exceed that period, **Then** they are removed according to the selected policy.
3. **Given** Gmail access is connected, **When** the user revokes that access, **Then** future imports stop and the connection state is shown.
4. **Given** an import or notification is displayed, **When** the user reviews it, **Then** Gmail credentials, access tokens, and private financial details are not exposed in unintended user-facing information.

### Edge Cases

- A source identifier is missing or reused for different transaction details.
- The same transaction arrives through more than one message or import run.
- A message is malformed, incomplete, unsupported, or contains multiple transaction records.
- Two or more rules match one transaction, or no rule matches.
- A transaction does not match any budget, or configured budget date windows overlap.
- A refund arrives before its original purchase, has a different reference identifier, or cannot be confidently matched to a purchase.
- An import is interrupted, Gmail access expires, or a scheduled run cannot reach Gmail.
- A retention setting changes while older records already exist.
- A user revokes Gmail access while an import is running.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Financp0 MUST let an individual user authorize and revoke access to Gmail for importing financial email.
- **FR-002**: Financp0 MUST support both user-triggered and user-scheduled imports.
- **FR-003**: The initial release MUST support credit-card transaction emails; bank-statement ingestion and correction are outside the initial release.
- **FR-004**: Financp0 MUST use provider-specific parsing methods that can be extended for additional financial email formats.
- **FR-005**: Financp0 MUST extract and normalize transaction details needed for history, categorization, budgets, and duplicate detection.
- **FR-006**: Financp0 MUST retain normalized transaction data and necessary processing metadata, and MUST NOT retain raw email content after processing.
- **FR-007**: Financp0 MUST detect duplicate transactions using source-provided transaction or reference identifiers and MUST NOT create a second transaction for a recognized duplicate.
- **FR-008**: Financp0 MUST preserve already processed transaction records as the source of truth and MUST NOT silently overwrite conflicts discovered in later imports.
- **FR-009**: Financp0 MUST let the user create and manage categories and ordered rules using trigger type, trigger, action, and target building blocks with preset options.
- **FR-010**: Financp0 MUST evaluate matching rules in user-defined order and make the applied rule visible on the transaction.
- **FR-011**: Financp0 MUST allow a user to create budgets with an amount, category, and daily, weekly, monthly, yearly, custom, or expiry-based date window.
- **FR-012**: Financp0 MUST assign no more than one budget to a transaction and MUST show budget usage and remaining amount for the configured period.
- **FR-013**: Financp0 MUST identify potentially non-standard or ambiguous transactions and place them in a user confirmation area.
- **FR-014**: Financp0 MUST notify the user about transactions requiring confirmation and detected refunds.
- **FR-015**: Financp0 MUST reflect detected refunds in spending and budget totals while retaining the original transaction history; an unmatched or uncertain refund MUST be flagged for confirmation.
- **FR-016**: Financp0 MUST let users confirm or correct flagged transactions and display the resulting state.
- **FR-017**: Financp0 MUST provide a dashboard showing budget usage, remaining amounts, and spending trends.
- **FR-018**: Financp0 MUST let users rearrange dashboard widgets and preserve their dashboard configuration.
- **FR-019**: Financp0 MUST support dashboard, budget, transaction, import, and review tasks in a mobile-friendly experience that can be installed from a supported browser.
- **FR-020**: Financp0 MUST let users choose a transaction data-retention period of unlimited, 3 years, 2 years, 1 year, 6 months, 3 months, or 1 month, and apply that policy to normalized records.
- **FR-021**: Financp0 MUST protect Gmail credentials, access tokens, and personal financial data from unauthorized access or disclosure.
- **FR-022**: Financp0 MUST provide clear import status, including success, partial processing, and actionable failures.

### Key Entities *(include if data involved)*

- **User**: An individual account owner with email authorization, preferences, and retention settings.
- **Connected Email Account**: A user-authorized Gmail connection with connection status and import configuration.
- **Import Run**: A scheduled or user-triggered attempt to retrieve and process eligible messages, with status and result metadata.
- **Normalized Transaction**: A financial record with source identifier, transaction date, merchant or description, amount, currency, category, budget association, review state, and processing provenance.
- **Category**: A user-defined classification used by categorization rules and budgets.
- **Categorization Rule**: An ordered user-defined condition and action that determines how matching transactions are categorized.
- **Budget**: A user-defined spending limit associated with a category and date window, optionally with an expiry.
- **Refund**: A transaction event representing returned funds and its association, when known, with an earlier purchase.
- **Review Item**: A flagged transaction or refund requiring user confirmation or correction.
- **Dashboard Layout**: The user's configured arrangement of dashboard widgets.
- **Notification**: A user-facing alert for imports, refunds, and transactions requiring attention.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: In a representative test set of supported credit-card transaction messages, at least 95% are either extracted into complete normalized transactions or explicitly surfaced for review; none are silently discarded.
- **SC-002**: In duplicate-import tests, 100% of transactions with recognized source identifiers appear only once in the transaction history.
- **SC-003**: For every tested budget period, displayed spending and remaining amounts match the transactions assigned to that budget.
- **SC-004**: In usability testing, at least 90% of participants can connect email, run an import, and locate an imported transaction without assistance.
- **SC-005**: In usability testing, at least 90% of participants can create a category budget and identify its usage and remaining amount from the dashboard without assistance.
- **SC-006**: All transactions flagged for confirmation and all detected refunds in the test suite produce a visible review item or notification as required.
- **SC-007**: A user's dashboard layout and selected retention period remain in effect after the user leaves and returns to the application.

## Assumptions

- Financp0 is an individual-user hosted service; shared household accounts are outside the initial release.
- “Real time” means processing after scheduled or manually triggered email synchronization, not direct bank synchronization.
- Initial supported source messages are credit-card transaction emails; full bank statements are deferred as a later correction workflow.
- A recognized source transaction/reference identifier is sufficient to deduplicate records; where identifiers are absent or conflict, the item is flagged rather than guessed.
- A detected refund offsets the original purchase in spending totals while preserving its history; uncertain associations require confirmation.
- Unmatched transactions can remain in transaction history without a budget assignment until the user configures a matching category or budget.
- Retention periods apply to normalized transaction records and associated metadata; the product does not retain raw message content after processing.
- The product is intended for a modern mobile or desktop browser with an internet connection.
- Initial rule behavior is deterministic and user-ordered; machine-learning categorization is outside MVP scope.
- The exact supported credit-card message formats will be selected during planning and validated with representative examples.
- The numeric parsing and usability thresholds in Success Criteria are proposed MVP targets and must be validated during planning with representative messages and users.
