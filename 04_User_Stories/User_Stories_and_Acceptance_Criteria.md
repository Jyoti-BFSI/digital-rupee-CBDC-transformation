# ABC Bank – Digital Rupee (e₹) Transformation Program
## User Stories and Acceptance Criteria

### 1. Document Control

| Field | Details |
|---|---|
| Project | ABC Bank – Digital Rupee (e₹) Transformation Program |
| Document ID | US-001 |
| Version | 1.0 |
| Status | Draft – Portfolio Simulation |
| Prepared By | BFSI / Digital Banking Transformation Consultant |
| Parent Documents | BRD-001, FRD-001 |
| Date | 09 October 2026 |

### 2. Purpose

This document converts business and functional requirements into user stories with testable acceptance criteria.

The stories describe the capabilities users need. The acceptance criteria specify the expected behavior that must be demonstrated before a story is accepted.

All customers, wallets, merchants, balances and transactions in this project are simulated.

### 3. User Story Format

Each user story follows this structure:

**As a** [type of user],  
**I want** [capability],  
**so that** [business benefit].

Acceptance criteria use the Given–When–Then format:

- **Given:** The starting condition.
- **When:** The user performs an action.
- **Then:** The expected outcome.

Example:

**Given** an eligible customer without an existing active wallet,  
**when** the customer submits a wallet-creation request,  
**then** the system creates a unique wallet, activates it and initializes the simulated balance to ₹0.

### 4. User Stories and Acceptance Criteria

## Epic 1 — Customer Wallet Management

### US-WAL-001: Create an e₹ Wallet

**Linked requirements:** BR-001, FR-WAL-001, FR-WAL-002  
**Priority:** P0

**User story**

As an eligible ABC Bank customer, I want to create an e₹ wallet so that I can access the simulated Digital Rupee service.

**Acceptance criteria**

**AC-01 — Successful wallet creation**
- Given an eligible customer with no existing active wallet,
- When the customer submits a valid wallet-creation request,
- Then the system creates a unique wallet ID, sets the status to ACTIVE and initializes the balance to ₹0.

**AC-02 — Ineligible customer**
- Given a customer who fails the configured eligibility checks,
- When the customer requests a wallet,
- Then the system rejects the request and returns an appropriate reason.

**AC-03 — Unknown customer**
- Given a customer ID that does not exist,
- When the customer submits a wallet-creation request,
- Then the system rejects the request without creating a wallet.

**AC-04 — Duplicate wallet request**
- Given a customer who already has an active wallet,
- When the customer submits another wallet-creation request,
- Then the system follows the configured duplicate-wallet policy and does not create an unintended duplicate.

**AC-05 — Invalid request**
- Given a request with a missing or malformed customer ID,
- When the request is submitted,
- Then the system returns a validation error and creates no wallet.

### US-WAL-002: View Wallet Balance

**Linked requirements:** BR-002, FR-WAL-003, FR-FND-002  
**Priority:** P0

**User story**

As a customer, I want to view my wallet balance so that I know how much simulated value is available.

**Acceptance criteria**

- An authorized customer can view their wallet ID, status and current balance.
- The displayed balance reflects completed simulated transactions.
- A customer cannot retrieve another customer's wallet information.
- A wallet that cannot be found produces a controlled error rather than an incorrect balance.

### US-WAL-003: Simulate Wallet Funding

**Linked requirements:** BR-002, FR-FND-001  
**Priority:** P0

**User story**

As an eligible customer, I want to add simulated funds to my wallet so that I can test payment journeys.

**Acceptance criteria**

- Given an active wallet and a valid positive amount, when a funding request is accepted, then the simulated balance increases by that amount and a transaction record is created.
- Given an invalid amount of zero or less, when funding is requested, then the request is rejected and the balance remains unchanged.
- Given a blocked wallet, when funding is requested, then the configured wallet policy is applied and the outcome is recorded.
- Given a repeated funding request with the same idempotency key, when it is submitted again, then the system does not apply the same funding twice.
- The interface clearly identifies this feature as simulated funding.

## Epic 2 — Customer Payments

### US-PAY-001: Make a P2P Payment

**Linked requirements:** BR-003, FR-PAY-001, FR-PAY-002  
**Priority:** P0

**User story**

As a customer, I want to send simulated Digital Rupee to another eligible wallet so that I can demonstrate person-to-person payments.

**Acceptance criteria**

**AC-01 — Successful payment**
- Given an active payer wallet with sufficient balance and an eligible recipient wallet,
- When the payer submits a valid payment within the configured limits,
- Then the system records the payment, debits the payer and credits the recipient consistently.

**AC-02 — Insufficient balance**
- Given a payer with a simulated balance of ₹100,
- When the payer attempts to send ₹150,
- Then the system rejects the payment and neither wallet balance is changed.

**AC-03 — Inactive payer**
- Given a payer wallet that is BLOCKED,
- When the payer attempts a payment,
- Then the system rejects the request without debiting the wallet.

**AC-04 — Invalid amount**
- Given a payment amount of zero or less,
- When the request is submitted,
- Then the system rejects the request without changing either balance.

**AC-05 — Same payer and recipient**
- Given the same wallet ID is supplied as both payer and recipient,
- When a payment is requested,
- Then the system rejects the payment.

**AC-06 — Duplicate payment request**
- Given a payment request that has already been processed,
- When the same idempotency key and request are submitted again,
- Then the system returns the existing outcome or otherwise prevents a second transfer.

**AC-07 — Traceable outcome**
- Given a payment attempt,
- When processing completes or fails,
- Then the system records a traceable outcome and an appropriate status or reason.

### US-PAY-002: Pay a Merchant

**Linked requirements:** BR-004, BR-005, FR-MER-002, FR-MER-003  
**Priority:** P0

**User story**

As a customer, I want to pay a registered merchant from my simulated wallet so that I can demonstrate merchant payments.

**Acceptance criteria**

- Given an active payer wallet, an eligible merchant and sufficient balance, when a valid payment is submitted, then the system records a successful payment and applies the configured simulated balance updates.
- Given an inactive or ineligible merchant, when payment is attempted, then the payment is rejected.
- Given insufficient balance, when payment is attempted, then the payment is rejected without an unintended debit.
- Given an invalid amount or exceeded limit, when payment is attempted, then the system returns an appropriate validation outcome.
- The merchant can view the payment record associated with its own merchant ID.
- One merchant cannot view another merchant's restricted payment records.

### US-PAY-003: View Transaction History

**Linked requirements:** BR-006, FR-HIS-001, FR-HIS-002  
**Priority:** P0 for basic history; P1 for filtering

**User story**

As a customer, I want to view my payment history so that I can check previous payment outcomes.

**Acceptance criteria**

- The customer can view transactions associated with their authorized wallet.
- Each transaction displays its ID, type, amount, date/time and status.
- Where relevant, the record includes the counterparty or merchant reference.
- Rejected transactions display an appropriate reason where available.
- A customer cannot view another customer's restricted transaction history.
- When filtering is implemented, the results match the selected date range, type and status.

## Epic 3 — Merchant Management

### US-MER-001: Register a Merchant

**Linked requirements:** BR-005, FR-MER-001  
**Priority:** P0

**User story**

As an authorized onboarding user, I want to register a merchant so that the merchant can accept simulated payments.

**Acceptance criteria**

- Given valid merchant details, when an authorized user submits a registration, then the system creates a merchant record with a unique merchant ID.
- Given missing mandatory fields, when registration is submitted, then the system rejects the request and identifies the validation issue.
- Given an existing merchant ID or prohibited duplicate record, when a duplicate registration is attempted, then the system applies the configured duplicate policy.
- Only an eligible merchant may accept payments.

## Epic 4 — Programmable Payment Rules

### US-RUL-001: Apply Payment Restrictions

**Linked requirements:** BR-007, FR-RUL-001, FR-RUL-002  
**Priority:** P1

**User story**

As an authorized product administrator, I want to configure illustrative payment restrictions so that I can demonstrate rule-controlled payment scenarios.

**Acceptance criteria**

- An authorized administrator can configure a permitted rule type and its parameters.
- A payment that satisfies all applicable mandatory rules can proceed to the remaining validations.
- A payment that violates a mandatory restriction is rejected with a meaningful reason.
- The system applies configured amount, merchant-category, purpose or expiry rules where applicable.
- An unauthorized user cannot change payment rules.
- Rule changes and their effective status can be inspected during testing.

These are simulated business rules, not official RBI CBDC restrictions.

## Epic 5 — Risk Monitoring

### US-RSK-001: Generate a Risk Alert

**Linked requirements:** BR-008, FR-RSK-001, FR-RSK-002  
**Priority:** P1

**User story**

As a risk reviewer, I want transactions matching configured risk rules to generate alerts so that I can identify activity requiring investigation.

**Acceptance criteria**

- Given a transaction matches an enabled risk rule, when the rule is evaluated, then the system creates a unique alert.
- The alert includes the relevant transaction reference, rule identifier, timestamp and review status.
- Given a transaction does not match any enabled alert rule, when it is evaluated, then no alert is created by those rules.
- An alert is treated as an indicator requiring review, not as proof of fraud.
- Authorized risk users can access the alert; unauthorized users cannot access restricted review functions.

### US-RSK-002: Review a Risk Alert

**Linked requirements:** BR-008, FR-RSK-003  
**Priority:** P1

**User story**

As an authorized risk reviewer, I want to record an alert review outcome so that risk cases can be tracked.

**Acceptance criteria**

- The reviewer can open an alert and view its relevant details.
- The reviewer can select a permitted disposition, such as REVIEWED or ESCALATED.
- The system records the updated status and review metadata.
- Unauthorized users cannot change the alert's review outcome.

## Epic 6 — Offline-Payment Simulation

### US-OFF-001: Synchronize Offline Transactions

**Linked requirements:** BR-009, FR-OFF-001, FR-OFF-002, FR-OFF-003  
**Priority:** P1

**User story**

As an operations user, I want to review the synchronization of simulated offline transactions so that I can understand how conflicting records may be handled.

**Acceptance criteria**

- An offline transaction is identified as provisional until it completes the defined synchronization process.
- When a valid pending record is synchronized, the system records its outcome.
- A repeated record is identified as a duplicate and does not cause an unintended second transfer.
- A conflicting or over-limit record is rejected or flagged according to the configured simulation rules.
- The synchronization outcome is traceable and available for review.

Offline payment acceptance, allocation and conflict resolution are simulation rules, not claims about an actual production CBDC design.

## Epic 7 — Reconciliation and Operations

### US-REC-001: Identify Reconciliation Exceptions

**Linked requirements:** BR-010, FR-REC-001, FR-REC-002  
**Priority:** P1

**User story**

As a reconciliation analyst, I want to compare simulated transaction records so that missing or mismatched records can be investigated.

**Acceptance criteria**

- Given two sets of matching transaction records, when reconciliation runs, then the records are identified as matched according to the configured matching rules.
- Given a record exists in only one source, when reconciliation runs, then a missing-record exception is created.
- Given the transaction references match but amounts differ, when reconciliation runs, then an amount-mismatch exception is created.
- Duplicate references are identified according to the configured reconciliation rules.
- Each exception includes an identifier, exception type and relevant record references.

### US-REC-002: Track an Exception

**Linked requirements:** BR-010, FR-REC-003  
**Priority:** P1

**User story**

As an operations user, I want to update a reconciliation exception's status so that its investigation can be tracked.

**Acceptance criteria**

- An authorized user can view an exception.
- The user can change the status to OPEN, UNDER_INVESTIGATION or RESOLVED, subject to configured workflow rules.
- The system records the updated status and relevant review metadata.
- An unauthorized user cannot change exception status.

### US-OPS-001: View Operational Dashboard

**Linked requirements:** BR-011, BR-012, FR-OPS-001, FR-OPS-002, FR-ANA-001, FR-ANA-002  
**Priority:** P1

**User story**

As an operations manager, I want to view summary transaction, risk and reconciliation metrics so that I can monitor the simulated service.

**Acceptance criteria**

- The dashboard displays the agreed transaction-volume and transaction-status metrics.
- The dashboard displays risk-alert and reconciliation-exception counts.
- Each displayed metric is calculated from the underlying synthetic data using a documented definition.
- The dashboard identifies the reporting period used.
- Users can access only the data and functions permitted by their roles.
- Dashboard values are consistent with the corresponding underlying records.

## Epic 8 — Access Control and Auditability

### US-SEC-001: Restrict Access by Role

**Linked requirements:** FR-SEC-001, FR-SEC-003, FR-SEC-004  
**Priority:** P0

**User story**

As a system administrator, I want the application to enforce role-based access so that users can access only the functions and information permitted to them.

**Acceptance criteria**

- A customer can access only their own authorized wallet and transaction information.
- A merchant can access only permitted merchant information.
- Risk-review functions are restricted to authorized risk users.
- Operational functions are restricted to authorized operations users.
- Invalid API inputs are rejected with a controlled error response.
- Sensitive personal information is not unnecessarily exposed in responses or logs.

### US-SEC-002: Record Important Actions

**Linked requirements:** FR-SEC-002  
**Priority:** P1

**User story**

As an authorized reviewer, I want important simulated business actions to be traceable so that I can understand what happened during a transaction or review.

**Acceptance criteria**

- Relevant events such as wallet creation, payment outcomes, risk-review actions and exception status changes are recorded.
- Each recorded event includes appropriate identifying information and a timestamp.
- Records are available only to authorized users.
- The audit record does not unnecessarily expose sensitive data.

### 5. Story Prioritization

The initial build should prioritize the following sequence:

1. US-WAL-001 — Create a wallet.
2. US-WAL-002 — View wallet balance.
3. US-WAL-003 — Simulate funding.
4. US-PAY-001 — Make a P2P payment.
5. US-MER-001 — Register a merchant.
6. US-PAY-002 — Pay a merchant.
7. US-PAY-003 — View transaction history.
8. US-SEC-001 — Restrict access and validate requests.
9. US-RUL-001 — Apply payment restrictions.
10. US-RSK-001 and US-RSK-002 — Generate and review risk alerts.
11. US-OFF-001 — Synchronize offline transactions.
12. US-REC-001 and US-REC-002 — Reconcile records and track exceptions.
13. US-OPS-001 — View operational metrics.

Security and transaction integrity must be considered from the start, even when advanced operational features are introduced later.

### 6. Definition of Done

A user story will be considered complete for this portfolio project when:

1. Its scope and acceptance criteria are understood.
2. The required functionality has been implemented.
3. Relevant positive and negative test cases have been executed.
4. Failed scenarios do not produce unintended balance or data changes.
5. Identified defects are resolved or explicitly documented.
6. The outcome can be demonstrated using synthetic data.
7. Relevant requirements and test results are traceable.
8. The project documentation is updated.

### 7. Open Questions for Later Refinement

The following decisions will be refined during business-rule and technical design:

- Whether the simulation permits more than one wallet per customer.
- Exact transaction limits and boundary conditions.
- Treatment of incoming payments to blocked wallets.
- Payment reversal and refund behavior.
- Merchant-category and payment-purpose rule formats.
- Offline allocation, synchronization and conflict-resolution policy.
- Detailed risk-alert review workflow.
- API authentication and role enforcement design.

These decisions must be documented before the related functionality is considered complete.

### 8. Disclaimer

This is a simulated educational and portfolio reference implementation. It is not affiliated with or endorsed by the Reserve Bank of India or any actual bank.

It does not connect to production CBDC infrastructure, process real financial transactions or establish regulatory compliance.
