# ABC Bank – Digital Rupee (e₹) Retail CBDC Transformation Program

## Database Design Document

### 1. Document Control

| Field | Details |
|---|---|
| Document ID | DBD-001 |
| Version | 1.0 — Portfolio Draft |
| Project | ABC Bank – Digital Rupee Retail CBDC Transformation |
| Status | Proposed logical database design |
| Prepared By | BFSI / Digital Banking Transformation Consultant — Project Simulation |

### 2. Purpose

This document defines the proposed data structures required for the simulated Digital Rupee wallet and payment application.

The design supports customer and merchant records, simulated wallets, payment transactions, configurable rules, illustrative risk alerts, reconciliation and auditability.

**Disclaimer:** This is an educational simulation. It does not connect to the RBI or any live CBDC or payment system. It uses synthetic data and does not move real money.

### 3. Database Technology

**Proposed database:** PostgreSQL

PostgreSQL is a relational database that stores information in tables and supports relationships, constraints and database transactions.

The design uses a relational model because the application needs to maintain consistent relationships between wallets, customers and payment records.

### 4. Proposed Tables

#### 4.1 Customers

**Purpose:** Stores synthetic customer profiles.

| Column | Proposed Type | Description |
|---|---|---|
| customer_id | UUID | Primary key |
| full_name | VARCHAR(100) | Fictional customer name |
| email | VARCHAR(255) | Synthetic email address |
| customer_status | VARCHAR(20) | ACTIVE or INACTIVE |
| created_at | TIMESTAMPTZ | Record creation timestamp |

#### 4.2 Wallets

**Purpose:** Stores simulated wallet details and available balance.

| Column | Proposed Type | Description |
|---|---|---|
| wallet_id | UUID | Primary key |
| customer_id | UUID | Foreign key to Customers |
| wallet_status | VARCHAR(20) | ACTIVE, BLOCKED or CLOSED |
| balance | NUMERIC(18,2) | Simulated balance |
| currency_code | CHAR(3) | INR for the demonstration |
| created_at | TIMESTAMPTZ | Wallet creation timestamp |

Business rule: A wallet cannot have a negative balance as a result of a successful simulated payment.

#### 4.3 Merchants

**Purpose:** Stores synthetic merchant information.

| Column | Proposed Type | Description |
|---|---|---|
| merchant_id | UUID | Primary key |
| merchant_name | VARCHAR(150) | Fictional merchant name |
| merchant_status | VARCHAR(20) | ACTIVE, INACTIVE or SUSPENDED |
| created_at | TIMESTAMPTZ | Record creation timestamp |

#### 4.4 Transactions

**Purpose:** Stores each simulated funding or payment event.

| Column | Proposed Type | Description |
|---|---|---|
| transaction_id | UUID | Primary key |
| sender_wallet_id | UUID, nullable | Source wallet where applicable |
| receiver_wallet_id | UUID, nullable | Destination wallet where applicable |
| merchant_id | UUID, nullable | Merchant involved in a merchant payment |
| transaction_type | VARCHAR(20) | FUNDING, P2P or P2M |
| amount | NUMERIC(18,2) | Positive transaction amount |
| transaction_status | VARCHAR(20) | PENDING, SUCCESS or DECLINED |
| idempotency_key | VARCHAR(100), nullable | Duplicate-request prevention key |
| created_at | TIMESTAMPTZ | Request timestamp |
| completed_at | TIMESTAMPTZ, nullable | Completion timestamp |

Design note: Funding records may not have a sender wallet. Merchant payments may identify a merchant as well as the paying wallet. The precise constraints will be defined in the implementation schema.

#### 4.5 Payment Rules

**Purpose:** Stores configurable demonstration rules.

| Column | Proposed Type | Description |
|---|---|---|
| rule_id | UUID | Primary key |
| rule_name | VARCHAR(100) | Rule description |
| rule_type | VARCHAR(30) | Example: PAYMENT_LIMIT |
| rule_value | VARCHAR(100) | Configured value |
| is_active | BOOLEAN | Whether the rule is enabled |
| created_at | TIMESTAMPTZ | Record creation timestamp |

#### 4.6 Risk Alerts

**Purpose:** Records illustrative risk indicators for synthetic transactions.

| Column | Proposed Type | Description |
|---|---|---|
| alert_id | UUID | Primary key |
| transaction_id | UUID | Related transaction |
| alert_type | VARCHAR(50) | Demonstration alert category |
| severity | VARCHAR(20) | LOW, MEDIUM or HIGH |
| review_status | VARCHAR(20) | OPEN, UNDER_REVIEW or CLOSED |
| created_at | TIMESTAMPTZ | Alert creation timestamp |

#### 4.7 Audit Events

**Purpose:** Maintains a record of significant application activities.

| Column | Proposed Type | Description |
|---|---|---|
| audit_event_id | UUID | Primary key |
| event_type | VARCHAR(50) | Type of event |
| entity_type | VARCHAR(50) | Entity involved |
| entity_id | UUID, nullable | Related record identifier |
| event_timestamp | TIMESTAMPTZ | Time of the event |
| details | JSONB | Non-sensitive event metadata |

Audit records should not contain passwords, authentication tokens or other secrets.

### 5. Relationships

- A customer may have one or more wallets, subject to the project's wallet-eligibility rules.
- Each wallet belongs to one customer.
- A wallet can be the source or destination of multiple transactions.
- A merchant may be associated with multiple P2M transactions.
- A transaction may generate zero or more illustrative risk alerts.
- Audit events can reference relevant application records.

### 6. Data Integrity and Control Rules

1. Primary keys must uniquely identify records.
2. Foreign keys must reference valid records.
3. Transaction amounts must be positive.
4. Wallet balances must not become negative through a successful payment.
5. Duplicate payment requests must be handled using an idempotency mechanism.
6. A successful simulated transfer must update the related wallet balances and transaction record consistently.
7. Declined transactions must not debit or credit wallets.
8. Synthetic email addresses and fictional customer information must be used in development.
9. Transaction timestamps should use timezone-aware database types.
10. Status values must be validated against defined application rules.

### 7. Transaction Consistency

The backend should use a database transaction for a simulated wallet transfer.

Conceptual sequence:

1. Validate the request and idempotency key.
2. Verify wallet eligibility and available balance.
3. Evaluate applicable payment rules.
4. Update the sender and recipient balances consistently.
5. Record the transaction outcome.
6. Commit the database transaction only when the required updates succeed.

If a required update fails, the database transaction should roll back the changes.

Concurrent payment requests will require appropriate locking or equivalent consistency controls to prevent spending the same available balance twice.

### 8. Reporting Use Cases

The proposed database should support reports for:

- Successful and declined transaction volumes.
- Total simulated payment values.
- Payment types and transaction statuses.
- Risk alerts by category and review status.
- Reconciliation exceptions and resolution status.

### 9. Open Design Decisions

The following details will be finalized before database implementation:

- Whether each customer may have multiple wallets.
- The exact merchant-payment relationship and validation rules.
- The approach for maintaining a complete transaction ledger.
- The required indexes and query-performance considerations.
- The handling of declined requests and idempotency-key retention.
- The reconciliation data model and exception lifecycle.

### 10. Next Steps

1. Review the proposed tables and relationships.
2. Create the Entity Relationship Diagram (ERD).
3. Finalize field constraints and indexes.
4. Write PostgreSQL table-creation scripts.
5. Populate the database with synthetic test records.
6. Test data integrity and simulated payment consistency.

**Document status:** Proposed logical database design for a portfolio simulation.
