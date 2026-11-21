# Feature: Manage Users & Roles

***

**Feature ID:** 1  
**Branch pattern:** `feature/1-manage-users-roles`  
**Status:** Draft  
**Created:** 2026-11-20  
**Input:** Invoicing System requirements and project notes  
**Depends on:** None  
**Related:** Manage_Clients, Manage_Projects, Manage_Time, Manage_Rates_Services, Manage_Expenses, Manage_Invoices

---

## User Stories

### US-1.1: Create and Manage User Accounts

**As a** system administrator  
**I want to** create, view, update, and deactivate employee accounts  
**So that** employees can access the invoicing system according to their responsibilities.

**Priority:** P1  
**Independent test:** Create a user account, save the user's information, and verify that the account appears in the user list.  
**Acceptance scenarios:**
- A valid user account is created and stored successfully.
- Invalid or incomplete required information is rejected.

### US-1.2: Authenticate Users

**As a** registered user  
**I want to** log in using my credentials  
**So that** I can securely access the features available to my account.

**Priority:** P1  
**Independent test:** Log in with valid credentials and verify that access is granted, then attempt to log in with invalid credentials and verify that access is denied.  
**Acceptance scenarios:**
- A user with valid credentials can log in.
- A user with invalid credentials cannot log in.

### US-1.3: Manage User Roles

**As a** system administrator  
**I want to** assign roles to users  
**So that** users can access only the functions they are authorized to use.

**Priority:** P1  
**Independent test:** Assign different roles to two accounts and verify that each account has the correct access.  
**Acceptance scenarios:**
- An administrator can assign an approved role to a user.
- A user cannot perform an action their role does not authorize.

---

## Requirements

### Functional Requirements

- **FR-001:** The system MUST allow authorized administrators to create, view, update, and deactivate user accounts.
- **FR-002:** The system MUST assign each user a unique UserID.
- **FR-003:** The system MUST store required user information, including name, username, and email.
- **FR-004:** The system MUST securely store password credentials.
- **FR-005:** The system MUST authenticate users before granting access to protected features.
- **FR-006:** The system MUST support user roles and permissions.
- **FR-007:** The system MUST prevent users from performing actions outside their assigned permissions.
- **FR-008:** The system MUST prevent duplicate usernames.
- **FR-009:** The system MUST prevent deactivated accounts from accessing protected features.
- **FR-010:** The system MUST preserve records associated with a user when that account is deactivated.

---

## Assumptions

- Each user account has a unique UserID.
- Each user has a unique username.
- Users must log in before accessing protected features.
- Administrators manage user accounts and roles.
- Deactivating an account is preferred over deleting it when historical records must be preserved.
- The exact roles and permissions will be determined by the project.

---

## Edge Cases

- A user attempts to register with an existing username.
- A user submits missing or invalid login credentials.
- A deactivated user attempts to log in.
- A user attempts to access a feature without permission.
- An administrator attempts to deactivate their own account and remove all administrative access.
- An administrator attempts to delete a user associated with existing records.

---

## Success Criteria

- **SC-001:** Every user account has a unique UserID.
- **SC-002:** The system rejects duplicate usernames.
- **SC-003:** Users with valid credentials can authenticate successfully.
- **SC-004:** Invalid credentials and deactivated accounts cannot access protected features.
- **SC-005:** Users cannot perform actions beyond their assigned permissions.
- **SC-006:** Passwords are not stored in plaintext.

---

## Key Entities

- **User:** Represents an employee or administrator who can access the system.
- **Role:** Represents a category of system access.
- **Permission:** Represents an action a user is authorized to perform.

---

## Initial Data Model

### `users` table

| Field | Type | Rules |
|---|---|---|
| UserID | INTEGER PK | Unique, auto-increment |
| Username | VARCHAR(50) | Required, unique |
| PasswordHash | VARCHAR(255) | Required |
| FirstName | VARCHAR(50) | Required |
| LastName | VARCHAR(50) | Required |
| Email | VARCHAR(100) | Required, valid format |
| RoleID | INTEGER FK | References roles |
| IsActive | BOOLEAN | Required |

### `roles` table

| Field | Type | Rules |
|---|---|---|
| RoleID | INTEGER PK | Unique |
| RoleName | VARCHAR(30) | Required, unique |
| Description | VARCHAR(255) | Optional |

---

## Acceptance Criteria

### US-1.1 — Create and Manage User Accounts

#### Scenario: Create a valid user account

* **Given** an authorized administrator is creating a user account
* **When** the administrator submits all required valid information
* **Then** the system creates the user account
* **And** assigns the account a unique UserID

#### Scenario: Reject a duplicate username

* **Given** a username already belongs to an existing account
* **When** an administrator attempts to create another account using that username
* **Then** the system rejects the new account
* **And** displays an appropriate validation message

### US-1.2 — Authenticate Users

#### Scenario: Log in with valid credentials

* **Given** an active user account exists
* **When** the user submits the correct username and password
* **Then** the system authenticates the user
* **And** grants access according to the user's role

#### Scenario: Reject invalid credentials

* **Given** a user submits incorrect login credentials
* **When** the system validates the credentials
* **Then** the system denies access
* **And** displays an appropriate login error message

### US-1.3 — Manage User Roles

#### Scenario: Assign a role to a user

* **Given** an authorized administrator is managing a user account
* **When** the administrator assigns an approved role
* **Then** the system saves the role
* **And** applies the permissions associated with that role

#### Scenario: Prevent unauthorized access

* **Given** a user does not have permission to perform an administrative action
* **When** the user attempts that action
* **Then** the system denies the request
* **And** leaves the protected data unchanged

---

## Data Ownership

- Administrators own user account and role management.
- The system owns authentication and access control.
- User records are referenced by time entries, expenses, and project assignments.

---

## API Requirements

- `POST /api/auth/login` — Authenticate a user.
- `POST /api/auth/logout` — End a user session.
- `GET /api/users` — Retrieve users.
- `GET /api/users/{id}` — Retrieve one user.
- `POST /api/users` — Create a user.
- `PUT /api/users/{id}` — Update a user.
- `PATCH /api/users/{id}/deactivate` — Deactivate a user.
- `GET /api/roles` — Retrieve available roles.

---

## Screen Requirements

- **Login Screen:** Allow users to enter their username and password.
- **User List Screen:** Display and search user accounts.
- **User Details Screen:** Display employee information and assigned role.
- **User Creation Screen:** Allow administrators to create accounts.
- **User Edit Screen:** Allow administrators to update accounts and roles.

---

## Definition of Done

- Administrators can create, view, update, and deactivate accounts.
- Users can log in and log out.
- Roles control access to protected features.
- Duplicate usernames are rejected.
- Passwords are stored securely.
- Required tests pass.

---

## Out of Scope

- Employee payroll processing.
- Employee performance evaluations.
- Public user registration.
- Multi-factor authentication.