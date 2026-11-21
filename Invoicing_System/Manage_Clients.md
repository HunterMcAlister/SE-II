# Feature: Manage Clients

***

**Feature ID:** 2  
**Branch pattern:** `feature/2-manage-clients`  
**Status:** Draft  
**Created:** 2026-11-20  
**Input:** Invoicing System requirements and project notes  
**Depends on:** Manage_Users_Roles  
**Related:** Manage_Projects, Manage_Time, Manage_Expenses, Manage_Invoices

---

## User Stories

### US-2.1: Create and Manage Client Accounts

**As an** administrator  
**I want to** create and manage client records  
**So that** the company can maintain accurate information about its customers.

**Priority:** P1  
**Independent test:** Create a client with valid information and verify that the client appears in the client list.  
**Acceptance scenarios:**
- A valid client record is created successfully.
- Missing required client information is rejected.

### US-2.2: View and Search Clients

**As a** manager  
**I want to** search and view client information  
**So that** I can find the correct client when managing projects and invoices.

**Priority:** P1  
**Independent test:** Search for an existing client by name and verify that the correct record is displayed.  
**Acceptance scenarios:**
- A matching client is displayed.
- A search with no matching client displays an appropriate message.

### US-2.3: Maintain Client Billing Information

**As an** administrator  
**I want to** update client contact and billing information  
**So that** invoices contain accurate customer information.

**Priority:** P1  
**Independent test:** Update a client's billing address and verify that the new information is saved.  
**Acceptance scenarios:**
- Updated client information is saved.
- Invalid information is rejected.

---

## Requirements

### Functional Requirements

- **FR-001:** The system MUST allow authorized users to create client records.
- **FR-002:** The system MUST assign each client a unique ClientID.
- **FR-003:** The system MUST store client names and contact information.
- **FR-004:** The system MUST store client email addresses and phone numbers.
- **FR-005:** The system MUST store client billing addresses.
- **FR-006:** The system MUST allow authorized users to view and search clients.
- **FR-007:** The system MUST allow authorized users to update client information.
- **FR-008:** The system MUST prevent unintended duplicate client records.
- **FR-009:** The system MUST allow authorized users to deactivate clients.
- **FR-010:** The system MUST preserve historical projects and invoices associated with a deactivated client.

---

## Assumptions

- Each client has a unique ClientID.
- A client can have multiple projects.
- A client can have multiple invoices.
- Client contact and billing information is maintained by authorized users.
- Deactivated clients cannot be used for new projects or invoices unless reactivated.
- Historical records remain available after client deactivation.

---

## Edge Cases

- A user attempts to create a client without a required name.
- A user attempts to create a duplicate client.
- A client has an invalid email address.
- A client has missing billing information.
- A user attempts to deactivate a client with unpaid invoices.
- A user attempts to delete a client associated with existing projects.

---

## Success Criteria

- **SC-001:** Every client has a unique ClientID.
- **SC-002:** Authorized users can create and update client records.
- **SC-003:** Users can search and view existing clients.
- **SC-004:** Invalid required information is rejected.
- **SC-005:** Duplicate client records are prevented.
- **SC-006:** Historical records remain available after client deactivation.

---

## Key Entities

- **Client:** Represents a customer receiving services from the company.
- **Project:** Represents work performed for a client.
- **Invoice:** Represents charges billed to a client.
- **Contact Information:** Represents the client's communication and billing details.

---

## Initial Data Model

### `clients` table

| Field | Type | Rules |
|---|---|---|
| ClientID | INTEGER PK | Unique, auto-increment |
| ClientName | VARCHAR(100) | Required |
| ContactName | VARCHAR(100) | Optional |
| Email | VARCHAR(100) | Required, valid format |
| Phone | VARCHAR(25) | Required |
| BillingAddress | VARCHAR(255) | Required |
| IsActive | BOOLEAN | Required |

---

## Acceptance Criteria

### US-2.1 — Create and Manage Client Accounts

#### Scenario: Create a valid client

* **Given** an authorized user is creating a client
* **When** the user submits all required client information
* **Then** the system creates the client record
* **And** assigns the client a unique ClientID

#### Scenario: Reject incomplete client information

* **Given** an authorized user is creating a client
* **When** the user leaves a required field empty
* **Then** the system rejects the client record
* **And** identifies the missing information

### US-2.2 — View and Search Clients

#### Scenario: Search for an existing client

* **Given** a client exists in the system
* **When** an authorized user searches using the client's name
* **Then** the system displays matching client records

#### Scenario: Search for a nonexistent client

* **Given** no client matches the search
* **When** the user submits the search
* **Then** the system displays a message indicating no results were found

### US-2.3 — Maintain Client Billing Information

#### Scenario: Update client billing information

* **Given** an existing client has a billing address
* **When** an authorized user enters a new valid billing address
* **Then** the system saves the updated information
* **And** displays the updated client record

#### Scenario: Deactivate a client

* **Given** a client has existing projects or invoices
* **When** an authorized user deactivates the client
* **Then** the system marks the client as inactive
* **And** preserves the client's historical records

---

## Data Ownership

- Administrators and authorized managers own client record maintenance.
- The Client record is the primary source of client contact and billing information.
- Projects and invoices reference ClientID.
- Historical invoice details should remain available even if client information changes.

---

## API Requirements

- `GET /api/clients` — Retrieve clients.
- `GET /api/clients/{id}` — Retrieve one client.
- `POST /api/clients` — Create a client.
- `PUT /api/clients/{id}` — Update a client.
- `PATCH /api/clients/{id}/deactivate` — Deactivate a client.
- `GET /api/clients/{id}/projects` — Retrieve a client's projects.
- `GET /api/clients/{id}/invoices` — Retrieve a client's invoices.

---

## Screen Requirements

- **Client List Screen:** Display and search client records.
- **Client Creation Screen:** Enter new client information.
- **Client Details Screen:** Display client contact and billing information.
- **Client Edit Screen:** Update existing client information.
- **Client History Screen:** Display the client's projects and invoices.

---

## Definition of Done

- Authorized users can create, view, update, and deactivate clients.
- Client records have unique identifiers.
- Client information can be searched.
- Required fields are validated.
- Duplicate records are handled.
- Historical projects and invoices remain available.
- Required tests pass.

---

## Out of Scope

- Client payment processing.
- Client marketing campaigns.
- Customer support ticket management.
- Automatic client email campaigns.