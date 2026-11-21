# Feature: Manage Invoices

***

**Feature ID:** 7  
**Branch pattern:** `feature/7-manage-invoices`  
**Status:** Draft  
**Created:** 2026-11-20  
**Input:** Invoicing System requirements and project notes  
**Depends on:** Manage_Users_Roles, Manage_Clients, Manage_Projects, Manage_Time, Manage_Rates_Services, Manage_Expenses  
**Related:** Manage_Clients, Manage_Projects, Manage_Time, Manage_Rates_Services, Manage_Expenses

---

## User Stories

### US-7.1: Create and Generate Invoices

**As an** administrator  
**I want to** generate invoices for clients  
**So that** the company can bill clients for completed work and approved expenses.

**Priority:** P1  
**Independent test:** Select a client and billing period with approved billable work, generate an invoice, and verify that the correct items are included.  
**Acceptance scenarios:**
- An invoice is generated using approved billable time and expenses.
- An invoice cannot be generated without a valid client.
- An invoice receives a unique invoice number.

### US-7.2: Manage Invoice Payment Terms

**As an** administrator  
**I want to** select payment terms for each invoice  
**So that** clients know when their payments are due.

**Priority:** P1  
**Independent test:** Create an invoice using Net 15 and another using Net 30, then verify that their due dates are calculated correctly.  
**Acceptance scenarios:**
- Net 15 calculates a due date 15 days after the invoice date.
- Net 30 calculates a due date 30 days after the invoice date.
- Invalid payment terms are rejected.

### US-7.3: Review and Finalize Invoices

**As a** manager  
**I want to** review and finalize invoices  
**So that** billing information is accurate before invoices are issued to clients.

**Priority:** P1  
**Independent test:** Generate an invoice, review its details, and finalize it. Verify that its status changes to Finalized.  
**Acceptance scenarios:**
- An authorized user can finalize a valid invoice.
- An invoice with missing required information cannot be finalized.

### US-7.4: Track Invoice Payments

**As an** administrator  
**I want to** track invoice payment status  
**So that** the company can identify paid, unpaid, and overdue invoices.

**Priority:** P1  
**Independent test:** Record a payment against an invoice and verify that the invoice payment status updates correctly.  
**Acceptance scenarios:**
- A payment can be recorded against an invoice.
- An overdue unpaid invoice is identified.
- A paid invoice is marked as Paid.

---

## Requirements

### Functional Requirements

- **FR-001:** The system MUST allow authorized users to create invoices for clients.
- **FR-002:** The system MUST assign each invoice a unique InvoiceID and invoice number.
- **FR-003:** Each invoice MUST be associated with a valid client.
- **FR-004:** The system MUST allow users to select a billing period.
- **FR-005:** The system MUST support biweekly billing periods.
- **FR-006:** The system MUST retrieve approved billable time entries associated with the selected client and billing period.
- **FR-007:** The system MUST retrieve approved billable expenses associated with the selected client and billing period.
- **FR-008:** The system MUST calculate labor charges using applicable employee billing rates.
- **FR-009:** The system MUST calculate invoice line totals, subtotal, and total.
- **FR-010:** The system MUST support Net 15 and Net 30 payment terms.
- **FR-011:** The system MUST calculate the invoice due date based on the invoice date and selected payment terms.
- **FR-012:** The system MUST allow authorized users to review and finalize invoices.
- **FR-013:** The system MUST prevent time entries and expenses from being billed more than once.
- **FR-014:** The system MUST allow users to view and search invoices by client, invoice number, date, and status.
- **FR-015:** The system MUST track invoice statuses, including Draft, Finalized, Paid, and Overdue.
- **FR-016:** The system MUST preserve finalized invoice details, including the original rates and amounts.
- **FR-017:** The system MUST allow authorized users to record payments against invoices.
- **FR-018:** The system MUST update invoice payment status when payments are recorded.

---

## Assumptions

- Each invoice belongs to one client.
- Invoices are generated using approved billable time and expenses.
- Billing occurs every two weeks.
- The system supports Net 15 and Net 30 payment terms.
- Net 15 means payment is due 15 days after the invoice date.
- Net 30 means payment is due 30 days after the invoice date.
- Each invoice has a unique invoice number.
- Only authorized users can finalize invoices.
- Finalized invoices retain their original billing details.
- The company records payments through the invoicing system.
- Tax calculation requirements must be confirmed separately if applicable.

---

## Edge Cases

- A client has no approved billable time or expenses.
- An invoice is generated for an invalid billing period.
- A time entry has already been included in another invoice.
- An expense has already been included in another invoice.
- An employee has no applicable billing rate.
- A client is inactive.
- An invoice is finalized with missing information.
- A payment is entered with an invalid or negative amount.
- A payment is recorded against an already paid invoice.
- An invoice passes its due date without being paid.
- An administrator attempts to change a finalized invoice.
- A client makes a partial payment.

---

## Success Criteria

- **SC-001:** Every invoice has a unique InvoiceID and invoice number.
- **SC-002:** Authorized users can create invoices for valid clients.
- **SC-003:** Invoices include approved billable time and expenses.
- **SC-004:** Biweekly billing periods are supported.
- **SC-005:** Net 15 and Net 30 due dates are calculated correctly.
- **SC-006:** Invoice totals are calculated accurately.
- **SC-007:** Duplicate billing is prevented.
- **SC-008:** Finalized invoices preserve their original billing details.
- **SC-009:** Invoice statuses are tracked accurately.
- **SC-010:** Payments can be recorded against invoices.

---

## Key Entities

- **Invoice:** Represents a bill sent to a client for services and expenses.
- **InvoiceLineItem:** Represents an individual charge included on an invoice.
- **Client:** Represents the customer receiving the invoice.
- **Project:** Represents the project associated with the billed work.
- **TimeEntry:** Represents approved employee hours included in an invoice.
- **Expense:** Represents approved billable project expenses.
- **EmployeeRate:** Determines the hourly billing rate for employee work.
- **Payment:** Represents money received toward an invoice.

---

## Initial Data Model

### `invoices` table

| Field | Type | Rules |
|---|---|---|
| InvoiceID | INTEGER PK | Unique, auto-increment |
| InvoiceNumber | VARCHAR(50) | Required, unique |
| ClientID | INTEGER FK | Required |
| InvoiceDate | DATE | Required |
| BillingPeriodStart | DATE | Required |
| BillingPeriodEnd | DATE | Required |
| PaymentTerms | VARCHAR(20) | Required; Net 15 or Net 30 |
| DueDate | DATE | Required |
| Subtotal | DECIMAL(12,2) | Required |
| Total | DECIMAL(12,2) | Required |
| Status | VARCHAR(30) | Required |

### `invoice_line_items` table

| Field | Type | Rules |
|---|---|---|
| LineItemID | INTEGER PK | Unique, auto-increment |
| InvoiceID | INTEGER FK | Required |
| Description | VARCHAR(255) | Required |
| Quantity | DECIMAL(10,2) | Required |
| UnitRate | DECIMAL(10,2) | Required |
| LineTotal | DECIMAL(12,2) | Required |
| TimeEntryID | INTEGER FK | Nullable |
| ExpenseID | INTEGER FK | Nullable |

### `payments` table

| Field | Type | Rules |
|---|---|---|
| PaymentID | INTEGER PK | Unique, auto-increment |
| InvoiceID | INTEGER FK | Required |
| PaymentDate | DATE | Required |
| Amount | DECIMAL(12,2) | Required, greater than zero |
| PaymentMethod | VARCHAR(50) | Optional |
| ReferenceNumber | VARCHAR(100) | Optional |

---

## Acceptance Criteria

### US-7.1 — Create and Generate Invoices

#### Scenario: Generate a biweekly invoice

* **Given** a client has approved billable time and expenses
* **When** an authorized user selects a valid biweekly billing period and generates an invoice
* **Then** the system creates an invoice
* **And** includes the approved billable items for that client and period

#### Scenario: Generate an invoice number

* **Given** an authorized user creates a new invoice
* **When** the system saves the invoice
* **Then** the system assigns a unique invoice number
* **And** prevents another invoice from using the same number

#### Scenario: Prevent invoice generation without billable items

* **Given** a client has no approved billable time or expenses
* **When** an authorized user attempts to generate an invoice
* **Then** the system prevents an empty invoice from being generated
* **And** displays an appropriate message

### US-7.2 — Manage Invoice Payment Terms

#### Scenario: Calculate Net 15 due date

* **Given** an invoice has an invoice date
* **When** an authorized user selects Net 15 payment terms
* **Then** the system sets the due date to 15 days after the invoice date

#### Scenario: Calculate Net 30 due date

* **Given** an invoice has an invoice date
* **When** an authorized user selects Net 30 payment terms
* **Then** the system sets the due date to 30 days after the invoice date

#### Scenario: Reject invalid payment terms

* **Given** an authorized user is creating an invoice
* **When** the user selects an unsupported payment term
* **Then** the system rejects the selection
* **And** requires a supported payment term

### US-7.3 — Review and Finalize Invoices

#### Scenario: Finalize a valid invoice

* **Given** an invoice contains valid client and billing information
* **When** an authorized user finalizes the invoice
* **Then** the system changes the invoice status to Finalized
* **And** preserves the invoice's billing details

#### Scenario: Prevent finalization of incomplete invoice

* **Given** an invoice is missing required information
* **When** an authorized user attempts to finalize the invoice
* **Then** the system prevents finalization
* **And** identifies the missing information

#### Scenario: Prevent duplicate billing

* **Given** a time entry or expense has already been included in a finalized invoice
* **When** another invoice is generated
* **Then** the system prevents the same item from being billed again

### US-7.4 — Track Invoice Payments

#### Scenario: Record a payment

* **Given** an invoice has been finalized
* **When** an authorized user records a valid payment
* **Then** the system saves the payment
* **And** updates the invoice's payment status when appropriate

#### Scenario: Identify an overdue invoice

* **Given** an invoice has not been fully paid
* **And** its due date has passed
* **When** the system evaluates the invoice status
* **Then** the system identifies the invoice as Overdue

#### Scenario: Record a partial payment

* **Given** an invoice has an outstanding balance
* **When** an authorized user records a payment smaller than the outstanding balance
* **Then** the system records the payment
* **And** maintains the remaining outstanding balance

---

## Data Ownership

- Administrators and authorized managers own invoice creation and finalization.
- Client records provide billing information.
- Project records identify the work associated with a client.
- Approved time entries provide labor charges.
- Approved expenses provide additional billable charges.
- Employee rates determine labor billing amounts.
- Invoice records own finalized line items, totals, payment terms, and due dates.
- Payment records track money received toward invoices.

---

## API Requirements

- `GET /api/invoices` — Retrieve invoices.
- `GET /api/invoices/{id}` — Retrieve one invoice.
- `POST /api/invoices` — Create an invoice.
- `PUT /api/invoices/{id}` — Update an eligible draft invoice.
- `POST /api/invoices/{id}/generate` — Generate invoice line items from approved billable records.
- `PATCH /api/invoices/{id}/finalize` — Finalize an invoice.
- `GET /api/clients/{id}/invoices` — Retrieve invoices for a client.
- `POST /api/invoices/{id}/payments` — Record a payment.
- `GET /api/invoices/{id}/payments` — Retrieve invoice payments.
- `GET /api/invoices/{id}/balance` — Retrieve the outstanding balance.

---

## Screen Requirements

- **Invoice List Screen:** Display invoice number, client, invoice date, due date, total, and status.
- **Invoice Creation Screen:** Select a client and billing period.
- **Invoice Details Screen:** Display invoice line items, subtotal, total, and payment terms.
- **Invoice Review Screen:** Allow authorized users to review an invoice before finalizing it.
- **Payment Screen:** Record payments associated with an invoice.
- **Invoice History Screen:** Display previous invoices for a client.

---

## Definition of Done

- Authorized users can create and view invoices.
- Invoices include approved billable time and expenses.
- Biweekly billing periods work correctly.
- Net 15 and Net 30 due dates are calculated correctly.
- Invoice totals are accurate.
- Each invoice receives a unique invoice number.
- Duplicate billing is prevented.
- Finalized invoices preserve their original details.
- Invoice statuses are tracked correctly.
- Payments can be recorded against invoices.
- Required tests pass.

---

## Out of Scope

- Automatic payment processing through banks.
- Credit card payment gateway integration.
- Automatic tax filing.
- Payroll processing.
- Automatic collection calls or debt collection.
- Client self-service payment portal.