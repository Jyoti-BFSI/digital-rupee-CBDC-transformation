# ABC Bank – Digital Rupee (e₹) Retail CBDC Transformation Program

## API Design Document

### 1. Document Control

| Field | Details |
|---|---|
| Document ID | API-001 |
| Version | 1.0 — Portfolio Draft |
| Project | ABC Bank – Digital Rupee Retail CBDC Transformation |
| Base Path | `/api/v1` |
| Proposed Backend | Python and FastAPI |
| Status | Proposed API contract for an educational simulation |

### 2. Purpose

This document defines the proposed REST API endpoints for the simulated Digital Rupee application.

The APIs will support wallet creation, simulated funding, P2P and P2M payments, transaction history, merchants, payment rules, illustrative risk alerts and reconciliation.

**Disclaimer:** This is a fictional educational application. It does not connect to the RBI or a live CBDC or payment system, and it does not move real money.

### 3. API Design Principles

- Use consistent resource names and URL patterns.
- Use HTTP methods according to the intended operation.
- Validate requests on the backend.
- Return predictable response structures and appropriate HTTP status codes.
- Prevent duplicate payment processing through idempotency controls.
- Enforce authorization on protected operations.
- Avoid returning unnecessary personal or sensitive data.
- Use synthetic customer and transaction data during development.

### 4. Proposed API Endpoints

| ID | Method | Endpoint | Purpose |
|---|---|---|---|
| API-001 | POST | `/api/v1/customers` | Create a synthetic customer profile |
| API-002 | GET | `/api/v1/customers/{customer_id}` | Retrieve customer details |
| API-003 | POST | `/api/v1/wallets` | Create a simulated wallet |
| API-004 | GET | `/api/v1/wallets/{wallet_id}` | Retrieve wallet details and balance |
| API-005 | POST | `/api/v1/wallets/{wallet_id}/funding` | Add simulated funds under configured rules |
| API-006 | POST | `/api/v1/payments` | Initiate a simulated payment |
| API-007 | GET | `/api/v1/transactions/{transaction_id}` | Retrieve transaction details |
| API-008 | GET | `/api/v1/wallets/{wallet_id}/transactions` | Retrieve wallet transaction history |
| API-009 | POST | `/api/v1/merchants` | Register a synthetic merchant |
| API-010 | GET | `/api/v1/merchants/{merchant_id}` | Retrieve merchant details |
| API-011 | GET | `/api/v1/merchants/{merchant_id}/transactions` | Retrieve merchant payment history |
| API-012 | GET | `/api/v1/payment-rules` | List configured demonstration rules |
| API-013 | GET | `/api/v1/risk-alerts` | List illustrative risk alerts |
| API-014 | GET | `/api/v1/reconciliation/runs` | List reconciliation runs |
| API-015 | POST | `/api/v1/reconciliation/runs` | Start a simulated reconciliation run |
| API-016 | GET | `/api/v1/operations/summary` | Retrieve operational summary metrics |

These endpoints are proposed interfaces; they will be implemented incrementally.

### 5. Example: Create a Wallet

**Method:** `POST`

**Endpoint:** `/api/v1/wallets`

**Example request**

```json
{
  "customer_id": "11111111-1111-4111-8111-111111111111",
  "currency_code": "INR"
}
```

**Example successful response — HTTP 201**

```json
{
  "wallet_id": "22222222-2222-4222-8222-222222222222",
  "customer_id": "11111111-1111-4111-8111-111111111111",
  "balance": "0.00",
  "currency_code": "INR",
  "wallet_status": "ACTIVE"
}
```

The IDs are illustrative examples, not actual customer or wallet records.

### 6. Example: Initiate a Simulated Payment

**Method:** `POST`

**Endpoint:** `/api/v1/payments`

**Example request**

```json
{
  "sender_wallet_id": "22222222-2222-4222-8222-222222222222",
  "receiver_wallet_id": "33333333-3333-4333-8333-333333333333",
  "amount": "250.00",
  "transaction_type": "P2P"
}
```

A client should provide an idempotency key, for example in an `Idempotency-Key` request header, to help prevent the same payment request from being processed more than once.

**Example successful response — HTTP 201**

```json
{
  "transaction_id": "44444444-4444-4444-8444-444444444444",
  "transaction_type": "P2P",
  "amount": "250.00",
  "transaction_status": "SUCCESS",
  "message": "Simulated payment completed"
}
```

A success response should only be returned after the required database updates have completed consistently. Actual processing will depend on the implemented business rules.

### 7. Example: Retrieve Transaction History

**Method:** `GET`

**Endpoint:** `/api/v1/wallets/{wallet_id}/transactions`

Example request:

`GET /api/v1/wallets/22222222-2222-4222-8222-222222222222/transactions`

**Example response**

```json
{
  "wallet_id": "22222222-2222-4222-8222-222222222222",
  "transactions": [
    {
      "transaction_id": "44444444-4444-4444-8444-444444444444",
      "transaction_type": "P2P",
      "amount": "250.00",
      "transaction_status": "SUCCESS",
      "created_at": "2026-10-09T10:00:00Z"
    }
  ]
}
```

The timestamp and transaction record are illustrative sample data.

### 8. Error Handling

The API should return consistent error responses.

| HTTP Status | Meaning | Example |
|---|---|---|
| 400 | Invalid request | Amount is missing or invalid |
| 401 | Authentication required | No valid authentication |
| 403 | Access forbidden | User cannot access the requested wallet |
| 404 | Resource not found | Wallet does not exist |
| 409 | Conflict | Idempotency key reused with different payment details |
| 422 | Request validation failed | Invalid field format |
| 500 | Unexpected server error | Unhandled backend failure |

**Example error response**

```json
{
  "error": {
    "code": "INSUFFICIENT_BALANCE",
    "message": "The simulated wallet has insufficient available balance."
  }
}
```

Error responses must not expose passwords, access tokens, database credentials or internal stack traces.

### 9. Payment API Business Controls

Before processing a payment, the backend should:

1. Validate the request format and positive amount.
2. Verify that the sender and receiver wallets exist and are eligible.
3. Verify that the sender has sufficient available simulated balance.
4. Validate the idempotency key and detect duplicate requests.
5. Evaluate configured payment rules and illustrative risk scenarios.
6. Record the transaction outcome and apply balance changes consistently.
7. Return the final transaction status.

Rejected payments must not create unintended balance changes.

### 10. Security and Access Control

The proposed implementation should demonstrate:

- Authentication for protected endpoints.
- Authorization checks on wallet, merchant and operations data.
- Server-side request validation.
- Restricted access to operations and reconciliation functions.
- Safe handling of application secrets.
- Audit records for significant actions.
- Synthetic data only.

The initial portfolio application must not be presented as production-secure or regulator-approved.

### 11. Testing Strategy

Use Postman for manual API testing and Pytest for automated tests.

Test scenarios should include:

- Successful wallet creation.
- Wallet creation for a nonexistent customer.
- Successful simulated funding.
- Successful P2P payment.
- Insufficient balance.
- Inactive or blocked wallet.
- Invalid or negative payment amount.
- Duplicate payment request.
- Unauthorized access to another user's wallet.
- Transaction-history retrieval.
- Reconciliation with an illustrative mismatch.

### 12. Traceability

The API contract implements capabilities identified in the Business Requirements Document, Functional Requirements Document, User Stories and Acceptance Criteria, Solution Architecture and Database Design Document.

### 13. Next Steps

1. Review and finalize the endpoint list.
2. Implement the FastAPI project structure.
3. Build the wallet endpoints first.
4. Test the endpoints in Postman.
5. Add payment processing with consistency and idempotency controls.
6. Implement the remaining endpoints incrementally.

**Document status:** Proposed API contract for an educational portfolio simulation.
