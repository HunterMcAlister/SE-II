# Feature: Manage Projects

***

**Feature ID:** 3  
**Branch pattern:** `feature/3-manage-projects`  
**Status:** Draft  
**Created:** 2026-11-20  
**Input:** Invoicing System requirements and project notes  
**Depends on:** Manage_Users_Roles, Manage_Clients  
**Related:** Manage_Time, Manage_Rates_Services, Manage_Expenses, Manage_Invoices

---

## User Stories

### US-3.1: Create and Manage Projects

**As a** manager  
**I want to** create, view, and update projects  
**So that** work can be organized according to the client receiving the service.

**Priority:** P1  
**Independent test:** Create a project for an existing client and verify that the project appears in the project list.  
**Acceptance scenarios:**
- A valid project is created and connected to a client.
- A project cannot be created without a valid client.

### US-3.2: Assign Employees to Projects

**As a** manager  
**I want to** assign employees to projects  
**So that** employees know which projects they are responsible for.

**Priority:** P1  
**Independent test:** Assign an active employee to a project and verify that the employee appears in the project assignments.  
**Acceptance scenarios:**
- An active employee can be assigned to a project.
- An inactive employee cannot be assigned.

### US-3.3: View Assigned Projects

**As an** employee  
**I want to** view my assigned projects  
**So that** I can record time and expenses for the correct work.

**Priority:** P1  
**Independent test:** Log in as an employee and verify that the employee's assigned projects are displayed.  
**Acceptance scenarios:**
- Employees can view their assigned projects.
- Employees cannot access projects they are not authorized to view.

---

## Requirements

### Functional Requirements

- **FR-001:** The system MUST allow authorized users to create projects.
- **FR-002:** The system MUST assign each project a unique ProjectID.
- **FR-003:** The system MUST associate each project with an existing client.
- **FR-004:** The system MUST store a project name, description, start date, and status.
- **FR-005:** The system MUST allow authorized managers to update project information.
- **FR-006:** The system MUST allow managers to assign employees to projects.
- **FR-007:** The system MUST allow employees to view their assigned projects.
- **FR-008:** The system MUST prevent inactive employees from being assigned to new projects.
- **FR-009:** The system MUST prevent inactive clients from being assigned to new projects.
- **FR-010:** The system MUST preserve historical project records associated with time entries, expenses, and invoices.

---

## Assumptions

- Each project belongs to one client.
- A client may have multiple projects.
- A project may have multiple assigned employees.
- An employee may work on multiple projects.
- Only authorized managers and administrators can create projects and manage assignments.
- Completed projects remain available for historical reporting and invoicing.

---

## Edge Cases

- A project is created without a client.
- A manager attempts to assign an inactive employee.
- A manager attempts to create a project for an inactive client.
- A project is assigned to the same employee more than once.
- An employee attempts to view an unauthorized project.
- A project is marked completed while it still has unbilled time or expenses.

---

## Success Criteria

- **SC-001:** Every project has a unique ProjectID.
- **SC-002:** Every project is associated with a valid client.
- **SC-003:** Authorized managers can create and update projects.
- **SC-004:** Employees can be assigned to projects.
- **SC-005:** Employees can view their assigned projects.
- **SC-006:** Historical project records remain available.

---

## Key Entities

- **Project:** Represents work performed for a client.
- **Client:** Represents the customer associated with a project.
- **User:** Represents an employee assigned to a project.
- **ProjectAssignment:** Connects employees to projects.

---

## Initial Data Model

### `projects` table

| Field | Type | Rules |
|---|---|---|
| ProjectID | INTEGER PK | Unique, auto-increment |
| ClientID | INTEGER FK | Required, references clients |
| ProjectName | VARCHAR(100) | Required |
| Description | TEXT | Optional |
| StartDate | DATE | Required |
| EndDate | DATE | Optional |
| Status | VARCHAR(30) | Required |

### `project_assignments` table

| Field | Type | Rules |
|---|---|---|
| AssignmentID | INTEGER PK | Unique, auto-increment |
| ProjectID | INTEGER FK | Required |
| UserID | INTEGER FK | Required |
| AssignedDate | DATE | Required |

---

## Acceptance Criteria

### US-3.1 — Create and Manage Projects

#### Scenario: Create a valid project

* **Given** an active client exists
* **When** an authorized manager submits valid project information
* **Then** the system creates the project
* **And** associates it with the selected client

#### Scenario: Reject a project without a client

* **Given** a manager is creating a project
* **When** the manager does not select a valid client
* **Then** the system rejects the project
* **And** displays a validation message

### US-3.2 — Assign Employees to Projects

#### Scenario: Assign an active employee

* **Given** an active project and active employee exist
* **When** a manager assigns the employee to the project
* **Then** the system creates the project assignment
* **And** makes the project available to the employee

#### Scenario: Prevent inactive employee assignment

* **Given** an employee account is inactive
* **When** a manager attempts to assign that employee to a project
* **Then** the system rejects the assignment
* **And** leaves the project assignments unchanged

### US-3.3 — View Assigned Projects

#### Scenario: View assigned projects

* **Given** an employee is assigned to one or more projects
* **When** the employee opens the project list
* **Then** the system displays the employee's assigned projects

#### Scenario: Prevent unauthorized project access

* **Given** an employee is not assigned to a restricted project
* **When** the employee attempts to access that project
* **Then** the system denies access

---

## Data Ownership

- Managers and administrators own project creation and assignment.
- Client records provide client information.
- User records provide employee information.
- Time entries and expenses reference ProjectID.
- Invoices use project information to identify the client receiving the billed work.

---

## API Requirements

- `GET /api/projects` — Retrieve projects.
- `GET /api/projects/{id}` — Retrieve one project.
- `POST /api/projects` — Create a project.
- `PUT /api/projects/{id}` — Update project information.
- `GET /api/projects/{id}/employees` — Retrieve assigned employees.
- `POST /api/projects/{id}/employees` — Assign an employee.
- `DELETE /api/projects/{id}/employees/{userId}` — Remove an assignment when permitted.
- `GET /api/users/{id}/projects` — Retrieve an employee's assigned projects.

---

## Screen Requirements

- **Project List Screen:** Display and search projects.
- **Project Creation Screen:** Enter project details and select a client.
- **Project Details Screen:** Display project information and status.
- **Employee Assignment Screen:** Assign and remove employees.
- **My Projects Screen:** Display projects assigned to the logged-in employee.

---

## Definition of Done

- Authorized users can create and update projects.
- Projects are associated with valid clients.
- Managers can assign active employees.
- Employees can view their assigned projects.
- Invalid assignments are rejected.
- Historical project information is preserved.
- Required tests pass.

---

## Out of Scope

- Project task management.
- Project scheduling and resource forecasting.
- Project milestone tracking.
- Project budgeting and profitability reports.