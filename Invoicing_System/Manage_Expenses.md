# Feature: Manage Expenses

***

**Feature ID:** 6  
**Branch pattern:** `feature/6-manage-expenses`  
**Status:** Draft  
**Created:** 2026-11-20  
**Input:** Invoicing System requirements and project notes  
**Depends on:** Manage_Users_Roles, Manage_Projects  
**Related:** Manage_Time, Manage_Rates_Services, Manage_Invoices

---

## User Stories

### US-6.1: Record Project Expenses

**As an** employee  
**I want to** enter expenses related to my assigned projects  
**So that** project costs can be tracked and billed to the correct client when appropriate.

**Priority:** P1  
**Independent test:** Enter an expense for an assigned project and verify that the expense appears in the expense list.  
**Acceptance scenarios:**
- A valid expense is saved successfully.
- Invalid expense amounts or project assignments are rejected.

### US-6.2: Review and Approve Expenses

**As a** manager  
**I want to** review and approve employee expenses  
**So that** only valid expenses are included in client invoices.

**Priority:** P1  
**Independent test:** Submit an expense and approve it as a manager, then verify its approval status.  
**Acceptance scenarios:**
- A manager can approve a valid expense.
- A manager can reject an invalid expense.
- Unauthorized users cannot approve expenses.

### US-6.3: View and Track Expenses

**As an** administrator  
**I want to** view expenses by employee, project, and date  
**So that** I can review expenses before invoices are generated.

**Priority:** P1  
**Independent test:** Filter expenses by project and verify that only matching records are displayed.  
**Acceptance scenarios:**
- Expenses can be filtered using available criteria.
- Expense records display their current approval and billing status.

---

## Requirements

### Functional Requirements

- **FR-001:** The system MUST allow employees to create expense records.
- **FR-002:** The system MUST assign each expense a unique ExpenseID.
- **FR-003:** Each expense MUST be associated with an employee and project.
- **FR-004:** The system MUST record the expense date, amount, description, and category.
- **FR-005:** The system MUST identify whether an expense is billable.
- **FR-006:** The system MUST allow employees to update eligible expense records.
- **FR-007:** The system MUST allow authorized managers to approve or reject expenses.
- **FR-008:** The system MUST prevent negative expense amounts.
- **FR-009:** The system MUST allow authorized users to view expenses by employee, project, and date.
- **FR-010:** The system MUST make approved billable expenses available for invoicing.
- **FR-011:** The system MUST prevent an expense from being billed more than once.
- **FR-012:** The system MUST preserve expense records associated with finalized invoices.

---

## Assumptions

- Employees enter expenses related to their assigned projects.
- Managers review and approve expenses before invoicing.
- Expenses may include travel, materials, supplies, or other project-related costs.
- Expenses are stored as monetary amounts.
- Only approved billable expenses can be included on client invoices.
- Non-billable expenses may be tracked without being charged to clients.

---

## Edge Cases

- An employee enters a negative expense amount.
- An expense is entered without a project.
- An employee submits the same expense more than once.
- A manager rejects an expense.
- An employee attempts to edit an approved expense.
- An expense has already been included in an invoice.
- An expense is associated with an inactive project.

---

## Success Criteria

- **SC-001:** Every expense has a unique ExpenseID.
- **SC-002:** Employees can record expenses for assigned projects.
- **SC-003:** Invalid expense amounts are rejected.
- **SC-004:** Managers can approve or reject submitted expenses.
- **SC-005:** Approved billable expenses are available for invoicing.
- **SC-006:** Expenses cannot be billed more than once.
- **SC-007:** Historical expense records remain available.

---

## Key Entities

- **Expense:** Represents a cost associated with a project.
- **User:** Represents the employee who submitted the expense.
- **Project:** Represents the project associated with the expense.
- **Invoice:** Represents the invoice that may contain the billable expense.

---

## Initial Data Model

### `expenses` table

| Field | Type | Rules |
|---|---|---|
| ExpenseID | INTEGER PK | Unique, auto-increment |
| UserID | INTEGER FK | Required |
| ProjectID | INTEGER FK | Required |
| ExpenseDate | DATE | Required |
| Amount | DECIMAL(10,2) | Required, greater than zero |
| Description | TEXT | Required |
| Category | VARCHAR(50) | Required |
| IsBillable | BOOLEAN | Required |
| ApprovalStatus | VARCHAR(30) | Required |
| InvoiceID | INTEGER FK | Nullable, references invoices |

---

## Acceptance Criteria

### US-6.1 — Record Project Expenses

#### Scenario: Submit a valid expense

* **Given** an employee is assigned to an active project
* **When** the employee submits a valid expense amount, date, and description
* **Then** the system saves the expense
* **And** associates it with the employee and project

#### Scenario: Reject a negative expense

* **Given** an employee is entering an expense
* **When** the employee enters a negative amount
* **Then** the system rejects the expense
* **And** displays a validation message

#### Scenario: Prevent unauthorized project expenses

* **Given** an employee is not assigned to a project
* **When** the employee attempts to submit an expense for that project
* **Then** the system rejects the expense

### US-6.2 — Review and Approve Expenses

#### Scenario: Approve an expense

* **Given** an employee has submitted a valid expense
* **When** an authorized manager approves the expense
* **Then** the system updates its approval status
* **And** makes it available for invoicing if it is billable

#### Scenario: Reject an expense

* **Given** an expense contains invalid or unacceptable information
* **When** an authorized manager rejects the expense
* **Then** the system marks the expense as rejected
* **And** prevents it from being included in an invoice

### US-6.3 — View and Track Expenses

#### Scenario: Filter expenses by project

* **Given** expenses exist for multiple projects
* **When** an authorized user selects a project filter
* **Then** the system displays only expenses associated with that project

#### Scenario: Prevent duplicate expense billing

* **Given** an expense has already been included in a finalized invoice
* **When** another invoice is generated
* **Then** the system prevents that expense from being billed again

---

## Data Ownership

- Employees own expense submissions.
- Managers own expense approval.
- The system tracks expense approval and billing status.
- Projects identify the client associated with an expense.
- Invoice records identify which expenses have already been billed.

---

## API Requirements

- `GET /api/expenses` — Retrieve expenses.
- `GET /api/expenses/{id}` — Retrieve one expense.
- `POST /api/expenses` — Create an expense.
- `PUT /api/expenses/{id}` — Update an eligible expense.
- `PATCH /api/expenses/{id}/approve` — Approve an expense.
- `PATCH /api/expenses/{id}/reject` — Reject an expense.
- `GET /api/projects/{id}/expenses` — Retrieve project expenses.
- `GET /api/users/{id}/expenses` — Retrieve employee expenses.

---

## Screen Requirements

- **Expense Entry Screen:** Enter expense date, project, amount, category, and description.
- **My Expenses Screen:** Display an employee's submitted expenses.
- **Expense Approval Screen:** Allow managers to approve or reject expenses.
- **Expense Report Screen:** Filter expenses by employee, project, and date.
- **Expense Details Screen:** Display expense information and approval status.

---

## Definition of Done

- Employees can create and update eligible expenses.
- Expenses are associated with valid projects and employees.
- Invalid amounts are rejected.
- Managers can approve or reject expenses.
- Approved billable expenses can be included in invoices.
- Expenses cannot be billed more than once.
- Historical expense information is preserved.
- Required tests pass.

---

## Out of Scope

- Employee reimbursement payments.
- Company credit card integration.
- Automatic receipt scanning.
- Bank account integration.
- Payroll deductions.