# ABC Bank – Digital Rupee (e₹) Transformation Program
## Functional Requirements Document (FRD)

### 1. Document Control

| Field | Details |
|---|---|
| Project Name | ABC Bank – Digital Rupee (e₹) Retail CBDC Transformation Program |
| Document ID | FRD-001 |
| Version | 1.0 |
| Status | Draft – Portfolio Simulation |
| Prepared By | BFSI / Digital Banking Transformation Consultant |
| Date | 09 October 2026 |
| Parent Document | BRD-001 – Business Requirements Document |

### 2. Purpose

This document defines the functional behavior expected from the simulated ABC Bank Digital Rupee application.

It translates approved business needs into specific system functions, validations, outcomes and exception-handling requirements.

Developers will use these requirements to understand what to build. Testers will use them to prepare test cases. Business stakeholders will use them to verify whether the solution meets the intended requirements.

### 3. Solution Overview

The simulated solution will provide the following capabilities:

1. Customer and eligibility management.
2. Wallet creation and status management.
3. Simulated wallet funding and balance enquiry.
4. P2P payments.
5. Merchant registration and P2M payments.
6. Transaction history.
7. Configurable payment rules.
8. Illustrative risk monitoring.
9. Offline-payment simulation.
10. Transaction reconciliation.
11. Operations monitoring.
12. Business analytics.

All balances and payments are simulated. The solution will not connect to real bank accounts or production CBDC infrastructure.

### 4. Users and Permissions

| User | Functional Access |
|---|---|
| Customer | Create and view own wallet, simulate funding, make payments and view own transactions |
| Merchant | View own registration status and relevant incoming payments |
| Operations User | View permitted transaction records and reconciliation exceptions |
| Risk Reviewer | View illustrative risk alerts and record review outcomes |
| Administrator | Manage permitted simulation settings and test data |

Access must be restricted by role. Customers must not be able to view or change another customer's private wallet information.

### 5. Functional Requirements

Priority definitions:

- P0: Required for the initial working demonstration.
- P1: Planned enhancement after core functionality is working.

## 5.1 Customer Eligibility and Wallet Management

### FR-WAL-001 — Customer Eligibility Validation

**Linked business requirement:** BR-001

The system shall validate that the customer exists, has an eligible status and satisfies the configured eligibility rules before allowing wallet creation.

**Inputs:** Customer identifier.

**Processing:**
1. Receive the wallet-creation request.
2. Retrieve the simulated customer record.
3. Check customer status and configured eligibility conditions.
4. Return an appropriate result.

**Successful outcome:** The customer is eligible to proceed with wallet creation.

**Failure outcomes:**
- Customer does not exist.
- Customer is inactive or ineligible.
- Required customer information is unavailable.

**Priority:** P0.

### FR-WAL-002 — Create e₹ Wallet

**Linked business requirement:** BR-001

The system shall create a wallet for an eligible customer.

**Processing:**
1. Receive an eligible customer's wallet-creation request.
2. Check whether an active wallet already exists, according to the project's wallet policy.
3. Generate a unique wallet identifier.
4. Create the wallet record.
5. Set its initial status to ACTIVE.
6. Set its initial simulated balance to ₹0.
7. Return the wallet identifier and creation outcome.

**Successful outcome:** One wallet record is created and can be retrieved by its identifier.

**Failure outcomes:**
- Customer is ineligible.
- An active wallet already exists and duplicate creation is not permitted.
- Wallet creation fails.

**Priority:** P0.

### FR-WAL-003 — View Wallet Details

The system shall allow an authorized customer to view their wallet identifier, status and available simulated balance.

**Failure outcome:** An unauthorized request must not disclose another customer's wallet information.

**Priority:** P0.

### FR-WAL-004 — Wallet Status Management

The system shall maintain a wallet status, initially including ACTIVE and BLOCKED.

A BLOCKED wallet shall not initiate payments. The handling of incoming payments to a blocked wallet must follow a separately configured business rule.

**Priority:** P0.

## 5.2 Simulated Wallet Funding

### FR-FND-001 — Submit Funding Request

**Linked business requirement:** BR-002

The system shall allow an authorized customer to submit a simulated funding request for an active wallet.

**Inputs:** Wallet identifier and amount.

**Validations:**
- The wallet exists.
- The wallet is eligible for the funding operation.
- The amount is greater than zero.
- The amount meets configured limits.
- The request is not a duplicate of an already processed request.

**Successful outcome:** The simulated wallet balance increases by the accepted funding amount, and a transaction record is created.

**Failure outcome:** Invalid funding requests are rejected without changing the wallet balance.

**Priority:** P0.

**Important limitation:** This operation simulates funding. It does not debit a real bank account or represent actual issuance of Digital Rupee.

### FR-FND-002 — View Available Balance

The system shall return the latest available simulated wallet balance to an authorized customer.

The balance shown must reflect completed simulated transactions and must not include rejected transactions.

**Priority:** P0.

## 5.3 Person-to-Person Payments

### FR-PAY-001 — Submit P2P Payment

**Linked business requirement:** BR-003

The system shall accept a payment request from one wallet to another.

**Inputs:**
- Payer wallet identifier.
- Recipient wallet identifier.
- Payment amount.
- Client request reference or idempotency key.

**Validations:**
1. The payer wallet exists and is ACTIVE.
2. The recipient wallet exists and is eligible to receive the payment under the configured rules.
3. The payer and recipient are not the same wallet.
4. The amount is greater than zero.
5. The payer has sufficient available balance.
6. The payment complies with applicable transaction limits.
7. The request has not already been processed.
8. Applicable payment restrictions are satisfied.

**Successful outcome:**
- A unique transaction identifier is assigned.
- The payer is debited.
- The recipient is credited.
- The transaction is recorded as successful.
- The outcome can be retrieved by the authorized users.

**Failure outcome:** The payment is rejected with a meaningful reason, and no partial balance transfer is left behind.

**Priority:** P0.

### FR-PAY-002 — Maintain Consistent Payment Updates

The system shall process a successful P2P payment so that the debit, credit and transaction record remain consistent.

If the payment cannot be completed safely, the system shall not leave the simulated ledger in a partially updated state.

**Priority:** P0.

### FR-PAY-003 — Prevent Duplicate Payment Processing

The system shall support a unique request reference or idempotency key for payment requests.

If the same request is submitted again, the system shall return the existing outcome or otherwise prevent an unintended second debit and credit.

**Priority:** P0.

### FR-PAY-004 — Reject Invalid Payments

The system shall reject a payment if any mandatory validation fails.

The response shall contain a transaction outcome and a reason code suitable for the application to display.

Example reason codes:
- INSUFFICIENT_BALANCE
- WALLET_INACTIVE
- INVALID_AMOUNT
- LIMIT_EXCEEDED
- RECIPIENT_INELIGIBLE
- DUPLICATE_REQUEST

The exact code list may be expanded during API design.

**Priority:** P0.

## 5.4 Merchant Management and P2M Payments

### FR-MER-001 — Register Merchant

**Linked business requirement:** BR-005

The system shall allow an authorized administrator or simulated merchant-onboarding user to create a merchant record.

The record shall include a unique merchant identifier, merchant name, category and status.

**Successful outcome:** An eligible merchant is recorded with a status that determines whether it may accept payments.

**Priority:** P0.

### FR-MER-002 — Validate Merchant Eligibility

The system shall validate the merchant's existence and configured status before processing a merchant payment.

A merchant that is not eligible to accept payments shall not receive a successful P2M transaction.

**Priority:** P0.

### FR-MER-003 — Submit P2M Payment

**Linked business requirement:** BR-004

The system shall accept a simulated payment from a customer wallet to an eligible merchant.

**Inputs:** Payer wallet identifier, merchant identifier, amount and request reference.

**Validations:**
- Payer wallet is ACTIVE.
- Merchant is eligible to accept payments.
- Amount is greater than zero.
- Payer has sufficient available balance.
- Applicable limits and payment rules are satisfied.
- Request has not already been processed.

**Successful outcome:** The payer's simulated balance is reduced, the payment is recorded, and the merchant can view the payment outcome.

**Priority:** P0.

### FR-MER-004 — View Merchant Payments

The system shall allow an authorized merchant user to view relevant payment records associated with that merchant.

A merchant must not be able to access another merchant's records.

**Priority:** P0.

## 5.5 Transaction History

### FR-HIS-001 — Retrieve Transaction History

**Linked business requirement:** BR-006

The system shall display transactions associated with the authorized customer's wallet.

Transaction records should include:
- Transaction identifier.
- Transaction type.
- Amount.
- Date and time.
- Counterparty or merchant reference, where applicable.
- Transaction status.
- Rejection reason, where applicable.

**Priority:** P0.

### FR-HIS-002 — Filter Transactions

The system should allow transaction history to be filtered by date range, transaction type and status.

**Priority:** P1.

### FR-HIS-003 — Maintain Transaction Traceability

The system shall retain transaction identifiers and relevant status history sufficient to support the project's testing, customer-history and reconciliation scenarios.

**Priority:** P0.

## 5.6 Programmable Payment Rules

### FR-RUL-001 — Configure Payment Restrictions

**Linked business requirement:** BR-007

The system shall support illustrative, configurable payment restrictions such as:
- Maximum permitted amount.
- Permitted merchant categories.
- Permitted purpose.
- Expiry date or time.

These restrictions are educational examples, not official CBDC policy.

**Priority:** P1.

### FR-RUL-002 — Evaluate Payment Rules

Before completing a payment subject to restrictions, the system shall evaluate the applicable rules.

If a mandatory restriction is violated, the payment shall be rejected with a suitable reason code.

**Priority:** P1.

### FR-RUL-003 — Handle Expired Rules or Instruments

The system shall reject a payment that uses a simulated restricted instrument or rule that has expired, where expiry is applicable.

**Priority:** P1.

## 5.7 Risk Monitoring and Alerts

### FR-RSK-001 — Evaluate Transaction Risk

**Linked business requirement:** BR-008

The system shall evaluate simulated transactions against configurable illustrative risk rules.

Example rules may include:
- An amount exceeds a configured threshold.
- Multiple transactions occur within a defined time window.
- A transaction conflicts with a configured payment restriction.
- A test scenario matches a known risk indicator.

**Priority:** P1.

### FR-RSK-002 — Generate Risk Alert

When a transaction matches an alert rule, the system shall create a risk-alert record containing an alert identifier, related transaction, rule identifier, timestamp and review status.

An alert indicates a potential concern, not a confirmed finding of fraud or money laundering.

**Priority:** P1.

### FR-RSK-003 — Review Risk Alert

An authorized risk reviewer shall be able to view an alert and record a disposition such as OPEN, REVIEWED or ESCALATED.

The system shall record the disposition and relevant review metadata.

**Priority:** P1.

## 5.8 Offline-Payment Simulation

### FR-OFF-001 — Simulate Offline Payment

**Linked business requirement:** BR-009

The system shall support a controlled simulation of payment requests created while a device or participant is offline.

The simulation must clearly distinguish provisional offline activity from a transaction accepted by the central simulated ledger.

**Priority:** P1.

### FR-OFF-002 — Synchronize Offline Transactions

The system shall process pending offline records during simulated synchronization and record the synchronization outcome.

Possible outcomes include accepted, rejected, duplicate or requiring investigation.

**Priority:** P1.

### FR-OFF-003 — Detect Duplicate or Conflicting Spend

The system shall identify offline records that may conflict with previously accepted transactions or exceed the permitted simulated offline allocation.

Conflicting records shall be flagged for the defined resolution process rather than silently accepted.

**Priority:** P1.

## 5.9 Reconciliation

### FR-REC-001 — Compare Transaction Records

**Linked business requirement:** BR-010

The system shall compare two sets of synthetic transaction records using agreed matching fields, such as transaction reference, amount and transaction date.

**Priority:** P1.

### FR-REC-002 — Identify Reconciliation Exceptions

The system shall identify configured exception types, including:
- Transaction present in one source but missing in another.
- Amount mismatch.
- Duplicate reference.
- Status mismatch.

The exception record shall identify the mismatch type and relevant transaction reference.

**Priority:** P1.

### FR-REC-003 — Track Exception Resolution

An authorized operations user shall be able to view an exception and update its status to OPEN, UNDER_INVESTIGATION or RESOLVED.

The system shall retain the status and relevant review details.

**Priority:** P1.

## 5.10 Operations and Analytics

### FR-OPS-001 — View Operational Transactions

**Linked business requirement:** BR-012

An authorized operations user shall be able to view simulated transactions with their identifiers, status, amount, type and timestamps.

**Priority:** P1.

### FR-OPS-002 — View Risk and Reconciliation Exceptions

The operations interface shall provide authorized users with visibility of relevant risk alerts and reconciliation exceptions.

Access to risk review functions shall be restricted to the appropriate role.

**Priority:** P1.

### FR-ANA-001 — Calculate Business Metrics

**Linked business requirement:** BR-011

The system shall provide sample data or aggregations to calculate:
- Total payment attempts.
- Successful and rejected payments.
- Payment success and rejection rates.
- Risk-alert volumes.
- Open and resolved reconciliation exceptions.
- Wallet creation outcomes.

The calculation definitions shall be documented, including the reporting period and denominator used for each rate.

**Priority:** P1.

### FR-ANA-002 — Display Analytics

The solution shall display agreed sample metrics in an operations dashboard or analytics report.

Metrics must be calculated from the project's synthetic data rather than manually invented results.

**Priority:** P1.

## 5.11 Access Control and Auditability

### FR-SEC-001 — Enforce Role-Based Access

The system shall restrict customer, merchant, operations, risk-review and administrative functions according to the user's assigned role.

**Priority:** P0 for functions exposed in the initial application; additional operational roles are required before their P1 functions are released.

### FR-SEC-002 — Record Important Actions

The system shall record relevant events such as wallet creation, payment outcomes, risk-alert review and reconciliation status changes.

**Priority:** P1.

### FR-SEC-003 — Protect Sensitive Information

The system shall use synthetic customer data and avoid exposing unnecessary personal information in API responses, logs and reports.

**Priority:** P0.

### FR-SEC-004 — Validate API Inputs

The system shall validate required fields, data types, amount formats and permitted values before processing requests.

Invalid requests shall receive a controlled error response.

**Priority:** P0.

### 6. Cross-Functional Processing Rules

The following rules apply across relevant functions:

1. Monetary amounts must use a defined precision and consistent representation.
2. Failed or rejected payments must not alter balances.
3. Successful transfers must maintain consistent debit and credit outcomes.
4. Repeated requests must not create unintended duplicate transactions.
5. All transactions must have traceable identifiers.
6. Access to customer and operational data must be authorized.
7. Errors must be recorded without exposing sensitive implementation details.
8. Transaction and reconciliation statuses must have documented meanings.

### 7. API Behavior Expectations

The API design will be documented separately. The following examples illustrate the intended functional interface.

**Create wallet**

`POST /wallets`

Example request:

    {
      "customer_id": "C10001"
    }

Illustrative successful response:

    {
      "wallet_id": "EW100001",
      "status": "ACTIVE",
      "balance": "0.00"
    }

**Submit P2P payment**

`POST /payments/p2p`

Example request:

    {
      "payer_wallet_id": "EW100001",
      "recipient_wallet_id": "EW100002",
      "amount": "500.00",
      "idempotency_key": "REQ-10001"
    }

Illustrative response:

    {
      "transaction_id": "TXN100001",
      "status": "SUCCESS"
    }

These are proposed example contracts. Exact field names, response schemas, error formats and authentication requirements will be finalized in the API specification. The examples do not indicate that these APIs have already been implemented.

### 8. Dependencies and Open Design Decisions

The following decisions must be addressed before or during detailed design:

- How will customer eligibility be represented in the simulation?
- Is one active wallet permitted per customer, or can a customer have multiple wallets?
- What are the configurable wallet and transaction limits?
- How will simultaneous payment requests be handled?
- Which transaction states will be supported?
- How will failed or reversed transactions be represented?
- What will the simulated offline allocation and synchronization rules be?
- Which source records will be used for reconciliation?
- Which roles may view or resolve risk alerts?
- What are the API authentication and authorization requirements?

These questions will be resolved through business-rule clarification and technical design. They must not be treated as already-approved bank policy.

### 9. Functional Acceptance Approach

Each functional requirement will be verified using one or more test cases.

Testing will include:
- Positive scenarios: valid requests complete successfully.
- Negative scenarios: invalid requests are rejected.
- Boundary scenarios: values at, below and above configured limits are tested.
- Duplicate scenarios: repeated requests do not create unintended effects.
- Consistency scenarios: balances and transaction records remain aligned.
- Access-control scenarios: unauthorized actions are blocked.
- Exception scenarios: failures, risk alerts and reconciliation mismatches are traceable.

Detailed test steps, test data and expected results will be maintained in the test-case document.

### 10. Requirement Traceability

Each FRD requirement includes a reference to its corresponding business requirement where applicable.

The next requirements and testing artifacts will extend this traceability chain:

Business Requirement → Functional Requirement → User Story / Acceptance Criteria → Test Case → Test Result.

Any change to a business requirement should trigger a review of the affected functional requirements, design, tests and documentation.

### 11. Disclaimer

This is a simulated educational and portfolio reference implementation. It is not affiliated with or endorsed by the Reserve Bank of India or any actual bank.

It does not connect to production CBDC infrastructure, process real financial transactions or establish regulatory compliance.
