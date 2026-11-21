# Feature: Manage Rates & Services

***

**Feature ID:** 5  
**Branch pattern:** `feature/5-manage-rates-services`  
**Status:** Draft  
**Created:** 2026-11-20  
**Input:** Invoicing System requirements and project notes  
**Depends on:** Manage_Users_Roles  
**Related:** Manage_Projects, Manage_Time, Manage_Expenses, Manage_Invoices

---

## User Stories

### US-5.1: Manage Employee Billing Rates

**As an** administrator  
**I want to** create and update employee billing rates  
**So that** the system can calculate the correct charges for employee work.

**Priority:** P1  
**Independent test:** Assign an hourly billing rate to an employee and verify that the rate is saved.  
**Acceptance scenarios:**
- An administrator can create a valid employee rate.
- Invalid or negative rates are rejected.

### US-5.2: Manage Services

**As an** administrator  
**I want to** create, view, and update services  
**So that** the company can identify the types of work being billed to clients.

**Priority:** P1  
**Independent test:** Create a service and verify that it appears in the service list.  
**Acceptance scenarios:**
- A valid service is created successfully.
- A service with missing required information is rejected.

### US-5.3: Apply Billing Rates to Work

**As a** manager  
**I want to** use the correct employee billing rates when calculating charges  
**So that** invoices reflect the appropriate cost of completed work.

**Priority:** P1  
**Independent test:** Calculate charges using an employee's hourly rate and approved time entry, then verify the total.  
**Acceptance scenarios:**
- The correct rate is used when calculating billable hours.
- Previously finalized invoices retain their original rates.

---

## Requirements

### Functional Requirements

- **FR-001:** The system MUST allow authorized administrators to create employee billing rates.
- **FR-002:** The system MUST assign each billing rate a unique RateID.
- **FR-003:** The system MUST associate each employee rate with a valid UserID.
- **FR-004:** The system MUST store an hourly billing rate and effective date.
- **FR-005:** The system MUST allow authorized administrators to update billing rates.
- **FR-006:** The system MUST allow authorized users to create, view, and update services.
- **FR-007:** Each service MUST have a unique ServiceID.
- **FR-008:** The system MUST store a service name and description.
- **FR-009:** The system MUST use the applicable employee billing rate when calculating billable time.
- **FR-010:** The system MUST prevent negative billing rates.
- **FR-011:** The system MUST preserve the rates used on finalized invoices.
- **FR-012:** The system MUST prevent the removal of services that are required by historical invoice records.

---

## Assumptions

- Employees may have different hourly billing rates.
- Billing rates are stored as monetary amounts per hour.
- Rates may change over time.
- An effective date determines when a billing rate applies.
- Administrators control rate and service maintenance.
- Historical invoices retain the rates originally applied.
- Services may be associated with invoice line items.

---

## Edge Cases

- An administrator enters a negative billing rate.
- An employee does not have an assigned billing rate.
- A rate is changed after time has been recorded.
- Two rates have overlapping effective dates.
- A manager attempts to calculate charges without an applicable rate.
- An administrator attempts to remove a service used by an existing invoice.

---

## Success Criteria

- **SC-001:** Every billing rate has a unique RateID.
- **SC-002:** Administrators can create and update employee rates.
- **SC-003:** Invalid or negative rates are rejected.
- **SC-004:** Services can be created and maintained.
- **SC-005:** Billable time uses the applicable employee rate.
- **SC-006:** Finalized invoices retain their original rates.

---

## Key Entities

- **EmployeeRate:** Represents the hourly billing rate assigned to an employee.
- **User:** Represents the employee associated with a billing rate.
- **Service:** Represents a type of work provided to a client.
- **TimeEntry:** Represents the employee hours used to calculate labor charges.
- **InvoiceLineItem:** Represents an individual charge included on an invoice.

---

## Initial Data Model

### `employee_rates` table

| Field | Type | Rules |
|---|---|---|
| RateID | INTEGER PK | Unique, auto-increment |
| UserID | INTEGER FK | Required |
| HourlyRate | DECIMAL(10,2) | Required, zero or greater |
| EffectiveDate | DATE | Required |
| EndDate | DATE | Optional |
| IsActive | BOOLEAN | Required |

### `services` table

| Field | Type | Rules |
|---|---|---|
| ServiceID | INTEGER PK | Unique, auto-increment |
| ServiceName | VARCHAR(100) | Required |
| Description | TEXT | Optional |
| IsActive | BOOLEAN | Required |

---

## Acceptance Criteria

### US-5.1 — Manage Employee Billing Rates

#### Scenario: Create an employee billing rate

* **Given** an administrator is managing employee billing rates
* **When** the administrator enters a valid employee and hourly rate
* **Then** the system creates the billing rate
* **And** assigns it a unique RateID

#### Scenario: Reject an invalid billing rate

* **Given** an administrator is creating a billing rate
* **When** the administrator enters a negative amount
* **Then** the system rejects the rate
* **And** displays a validation message

### US-5.2 — Manage Services

#### Scenario: Create a service

* **Given** an authorized administrator is creating a service
* **When** the administrator submits a valid service name and description
* **Then** the system creates the service
* **And** assigns it a unique ServiceID

#### Scenario: Reject incomplete service information

* **Given** an administrator is creating a service
* **When** the administrator leaves the required service name empty
* **Then** the system rejects the service
* **And** identifies the missing information

### US-5.3 — Apply Billing Rates to Work

#### Scenario: Calculate billable time

* **Given** an employee has an applicable hourly billing rate
* **And** the employee has approved billable time
* **When** the system calculates the labor charge
* **Then** the system multiplies the hours worked by the applicable rate

#### Scenario: Preserve historical billing rates

* **Given** an invoice has been finalized using an employee billing rate
* **When** the employee's billing rate changes
* **Then** the finalized invoice retains the original rate

---

## Data Ownership

- Administrators own employee billing rate and service maintenance.
- User records identify employees.
- Time entries provide the hours used for billing calculations.
- Invoice line items preserve the rates and service information used for finalized invoices.

---

## API Requirements

- `GET /api/rates` — Retrieve billing rates.
- `GET /api/rates/{id}` — Retrieve one billing rate.
- `POST /api/rates` — Create a billing rate.
- `PUT /api/rates/{id}` — Update a billing rate.
- `GET /api/users/{id}/rates` — Retrieve an employee's rates.
- `GET /api/services` — Retrieve services.
- `GET /api/services/{id}` — Retrieve one service.
- `POST /api/services` — Create a service.
- `PUT /api/services/{id}` — Update a service.

---

## Screen Requirements

- **Employee Rate List Screen:** Display employee billing rates.
- **Rate Creation Screen:** Enter employee, hourly rate, and effective date.
- **Rate Edit Screen:** Update existing billing rate information.
- **Service Management Screen:** Create, view, and update services.
- **Service Details Screen:** Display service names and descriptions.

---

## Definition of Done

- Administrators can create and update employee billing rates.
- Rates are associated with valid employees.
- Services can be created and maintained.
- Invalid rates are rejected.
- Billable time uses the applicable rate.
- Historical invoices retain their original rates.
- Required tests pass.

---

## Out of Scope

- Employee salary and payroll management.
- Employee bonuses and commissions.
- Automatic rate recommendations.
- Tax calculation and tax filing.