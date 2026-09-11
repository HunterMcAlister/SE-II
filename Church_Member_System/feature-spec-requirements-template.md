# Feature: <Church Site>

**Feature ID:** 1  
**Branch pattern:** `feature/1-user-authentication`  
**Status:** Draft  
**Created:** 2026-09-11  
**Input:** Allow people to create their own account, log in, log out, and manage their account so they can access the church system.  
**Depends on:** none
**Related:** Authentication/security ADRs may be added later 

---

## User Stories

### US-1.1: Create an Account
**As a** <Visitor>  
**I want to** <create my own account using my email and password>  
**So that** <I can access the church system>

**Priority:** P1  
**Independent test:** <A visitor can register with valid information and successfully access their authenticated account>  
**Acceptance scenarios:** see ### US-1.1 under Acceptance Criteria

### US-1.2: Login
**As a** <registered user>  
**I want to** <log in to my account>  
**So that** <I can access the church system>

**Priority:** P1  
**Independent test:** <A registered user can provide valid credentials and successfully access an authenticated page.>  
**Acceptance scenarios:** see ### US-1.2 under Acceptance Criteria  

### US-1.3: Log out
**As a** <church system user>  
**I want to** <log out of my account>  
**So that** <my church account remains secure when I am finished using the system>

**Priority:** P1  
**Independent test:** <An authenticated user can log out and can no longer access authenticated church pages without logging in again.>  
**Acceptance scenarios:** see ### US-1.3 under Acceptance Criteria  

### US-1.4: Manage Account
**As a** <church system user>  
**I want to** <manage my account information>  
**So that** <my information remains accurate and up to date>

**Priority:** P1  
**Independent test:** <An authenticated user can view and update permitted account information without affecting another users account.>  
**Acceptance scenarios:** see ### US-1.4 under Acceptance Criteria  

---

## Requirements

### Functional Requirements

- **FR-001**: The church system MUST allow a visitor to create an account using a valid email address and password.
- **FR-002**: Authenticated users MUST be able to log out of the church system.
- **FR-003**: Account authentication MUST be designed so that church-specific features can be added without replacing the authentication system.

---

## Assumptions

- People from the church
- Able to count the number of people attending

--- 

## Edge Cases

- Empty required registration field → The system displays a validation error and does not create the account.
- Invalid email address → The system rejects the registration and explains that a valid email address is required.
- Duplicate email address → The system informs the visitor that an account already exists for that email.
- Weak or invalid password → The system rejects the password and explains the password requirements.
- Incorrect login credentials → The system rejects the login attempt without creating an authenticated session.
- Login with an unregistered email → The system rejects the login attempt.
- Accessing a protected church page while logged out → The system prevents access and directs the visitor to the login page.
- Accessing another user's account → The system prevents unauthorized access.
- Logging out → The user's authenticated session is invalidated.
- Expired session → The user must authenticate again before accessing protected church information.
- Updating account information with invalid data → The system rejects the update and displays appropriate validation errors.
- Missing church member record → The system must handle the situation safely without exposing another user's information.

---

## Success Criteria

- **SC-001**: Every Gherkin scenario has at least one automated test before merge.
- **SC-002**: A new church visitor can create an account and reach an authenticated church page successfully.
- **SC-003**: A registered church user can log in and access protected church functionality.
- **SC-004**: A logged-out user cannot access protected church functionality.
- **SC-005**: A church user cannot access another user's private account information.
- **SC-006**: A church user can successfully log out and end their authenticated session.
- **SC-007**: Registration and login failures provide clear, actionable validation messages.

---

## Key Entities

- **User**: A person who has an account in the church system. A user may be a visitor, church member, volunteer, ministry leader, or church staff member.
- **Church Member**: A user who has an established membership or relationship with the church. A member may have additional church-specific information associated with their account.
- **Account**: The authentication credentials and account information used by a user to access the church system.
- **Session**: The authenticated connection between a user and the church system after successful login.
- **Role**: Defines the level of access a user has within the church system, such as member, volunteer, ministry leader, or administrator.
- **Church**: The organization whose members, ministries, events, and services are managed by the system.

---

## Data Model Requirements

### `user` table
| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `email` | VARCHAR | 	Required, unique, valid email |
| `password` | VARCHAR | 	Required |
| `firstName` | VARCHAR | 	Required |
| `lastName` | VARCHAR | 	Required |

### Associations (if known)
- …
*    users.id → church_members.user_id
*    churches.id → church_members.church_id
---

## Acceptance Criteria

### US-1.1 — Create an Account

#### Scenario: creates a church account
*   **Given** <a visitor does not already have a church system account>
*   **When** <the visitor submits the registration form>
*   **Then** <the church system creates the users account>
*   **And** <the user is authenticated>

#### Scenario: register with an existing email
*   **Given** <a church system account already exists for the email address>
*   **When** <a visitor attempts to create another account using that email>
*   **Then** <the church system rejects the registration>

### US-1.2 — Login

#### Scenario: Successfully logs in
*   **Given** <a registered church user has a valid account>
*   **When** <the user provides the correct email address>
*   **Then** <the church system authenticates the user>

#### Scenario: Incorrect password
*   **Given** <a registered church user has an account>
*   **When** <the user enters an incorrect password>
*   **Then** <the user is not authenticated>

### US-1.3 — Log Out

#### Scenario: User logs out
*   **Given** <a church user is authenticated>
*   **When** <the user selects Log Out>
*   **Then** <the users session is invalidated>
*   **And** <the user is no longer authenticated>

#### Scenario: User attempts
*   **Given** <a church user has logged out>
*   **When** <the user attempts to access a protected church page>
*   **Then** <the church system prevents access>

### US-1.4 — Manage Account

#### Scenario: View user account
*   **Given** <a church user is authenticated>
*   **When** <the user opens their account page>
*   **Then** <the system displays their own account information>

#### Scenario: updates their account
*   **Given** <a church user is authenticated>
*   **When** <the user saves the changes>
*   **Then** <the users account is updated>