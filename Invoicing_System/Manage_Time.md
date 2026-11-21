# Feature: Manage Time

***

**Feature ID:** 4  
**Branch pattern:** `feature/4-manage-time`  
**Status:** Draft  
**Created:** 2026-11-20  
**Input:** Invoicing System requirements and project notes  
**Depends on:** Manage_Users_Roles, Manage_Projects  
**Related:** Manage_Rates_Services, Manage_Expenses, Manage_Invoices

---

## User Stories

### US-4.1: Record Time Worked

**As an** employee  
**I want to** enter the hours I worked on a project  
**So that** my work can be tracked and billed to the correct client.

**Priority:** P1  
**Independent test:** Enter a valid time entry for an assigned project and verify that it is saved.  
**Acceptance scenarios:**
- A valid time entry is saved successfully.
- Invalid hours or project assignments are rejected.

### US-4.2: Review and Approve Time Entries

**As a** manager  
**I want to** review and approve employee time entries  
**So that** only verified work is included in client invoices.

**Priority:** P1  
**Independent test:** Submit a time entry and approve it as a manager, then verify its approval status.  
**Acceptance scenarios:**
- A manager can approve a valid time entry.
- An unauthorized user cannot approve time entries.

### US-4.3: View Time Records

**As an** administrator  
**I want to** view time entries by employee, project, and date  
**So that** I can review work before billing clients.

**Priority:** P1  
**Independent test:** Filter time entries by project and verify that only matching records are displayed.  
**Acceptance scenarios:**
- Time records can be filtered.
- Records outside the selected criteria are not displayed.

---

## Requirements

### Functional Requirements

- **FR-001:** The system MUST allow employees to create time entries.
- **FR-002:** The system MUST assign each time entry a unique TimeEntryID.
- **FR-003:** Each time entry MUST be associated with an employee and project.
- **FR-004:** The system MUST record the work date, hours worked, and description.
- **FR-005:** The system MUST identify whether a time entry is billable.
- **FR-006:** The system MUST allow employees to edit eligible time entries.
- **FR-007:** The system MUST allow authorized managers to approve or reject time entries.
- **FR-008:** The system MUST prevent zero or negative hour entries.
- **FR-009:** The system MUST allow authorized users to view time entries by employee, project, and date range.
- **FR-010:** The system MUST make approved billable time available for invoicing.
- **FR-011:** The system MUST prevent a time entry from being billed more than once.
- **FR-012:** The system MUST preserve time entries associated with finalized invoices.

---

## Assumptions

- Employees enter their own work hours.
- Employees must be assigned to a project before entering time for it.
- Managers are responsible for reviewing submitted time.
- Time entries are measured in hours.
- The company uses a biweekly billing cycle.
- Only approved billable time can be included in invoices.
- Time entries already included in finalized invoices cannot be edited without an authorized correction process.

---

## Edge Cases

- An employee enters zero or negative hours.
- An employee enters time for a project they are not assigned to.
- An employee submits duplicate time entries.
- A manager attempts to approve an invalid entry.
- An employee attempts to edit an approved time entry.
- A time entry has already been included in an invoice.
- A time entry falls outside the selected billing period.

---

## Success Criteria

- **SC-001:** Employees can create time entries for assigned projects.
- **SC-002:** Each time entry has a unique TimeEntryID.
- **SC-003:** Invalid hour values are rejected.
- **SC-004:** Managers can approve or reject submitted entries.
- **SC-005:** Approved billable time is available for invoicing.
- **SC-006:** Time entries cannot be billed more than once.

---

## Key Entities

- **TimeEntry:** Represents hours an employee worked.
- **User:** Represents the employee who recorded the time.
- **Project:** Represents the project where the work was performed.
- **Invoice:** Represents the invoice that may contain the billable time.

---

## Initial Data Model

### `time_entries` table

| Field | Type | Rules |
|---|---|---|
| TimeEntryID | INTEGER PK | Unique, auto-increment |
| UserID | INTEGER FK | Required |
| ProjectID | INTEGER FK | Required |
| WorkDate | DATE | Required |
| HoursWorked | DECIMAL(6,2) | Required, greater than zero |
| Description | TEXT | Required |
| IsBillable | BOOLEAN | Required |
| ApprovalStatus | VARCHAR(30) | Required |
| InvoiceID | INTEGER FK | Nullable, references invoices |

---

## Acceptance Criteria

### US-4.1 — Record Time Worked

#### Scenario: Create a valid time entry

* **Given** an employee is assigned to an active project
* **When** the employee enters a valid date, number of hours, and description
* **Then** the system saves the time entry
* **And** associates it with the employee and project

#### Scenario: Reject invalid hours

* **Given** an employee is entering a time entry
* **When** the employee enters zero or negative hours
* **Then** the system rejects the entry
* **And** displays a validation message

#### Scenario: Prevent unauthorized project time

* **Given** an employee is not assigned to a project
* **When** the employee attempts to record time for that project
* **Then** the system rejects the entry

### US-4.2 — Review and Approve Time Entries

#### Scenario: Approve a time entry

* **Given** an employee has submitted a valid time entry
* **When** an authorized manager approves the entry
* **Then** the system updates its approval status
* **And** makes it available for invoicing if it is billable

#### Scenario: Reject a time entry

* **Given** a submitted time entry contains incorrect information
* **When** an authorized manager rejects the entry
* **Then** the system marks it as rejected
* **And** records the updated status

### US-4.3 — View Time Records

#### Scenario: Filter time entries

* **Given** time entries exist for multiple projects
* **When** an authorized user selects a project filter
* **Then** the system displays only time entries associated with that project

#### Scenario: Prevent duplicate billing

* **Given** a time entry has already been included in a finalized invoice
* **When** another invoice is generated
* **Then** the system prevents that time entry from being billed again

---

## Data Ownership

- Employees own the submission of their time entries.
- Managers own time entry approval.
- The system tracks approval and invoice association.
- Projects identify the client associated with the work.
- Invoice records identify which time entries have been billed.

---

## API Requirements

- `GET /api/time-entries` — Retrieve time entries.
- `GET /api/time-entries/{id}` — Retrieve one time entry.
- `POST /api/time-entries` — Create a time entry.
- `PUT /api/time-entries/{id}` — Update an eligible time entry.
- `PATCH /api/time-entries/{id}/approve` — Approve a time entry.
- `PATCH /api/time-entries/{id}/reject` — Reject a time entry.
- `GET /api/projects/{id}/time-entries` — Retrieve project time entries.
- `GET /api/users/{id}/time-entries` — Retrieve employee time entries.

---

## Screen Requirements

- **Time Entry Screen:** Enter work date, project, hours, and description.
- **My Time Screen:** Display an employee's submitted time entries.
- **Time Approval Screen:** Allow managers to review and approve or reject entries.
- **Time Reports Screen:** Filter time by employee, project, and date range.

---

## Definition of Done

- Employees can create and edit eligible time entries.
- Entries are associated with valid projects and employees.
- Invalid hour values are rejected.
- Managers can approve or reject entries.
- Approved billable hours can be used in invoices.
- Previously invoiced time cannot be billed again.
- Required tests pass.

---

## Out of Scope

- Employee clock-in and clock-out tracking.
- GPS location tracking.
- Payroll calculation.
- Automatic employee scheduling.