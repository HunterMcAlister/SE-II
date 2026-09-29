# Feature: <Manage_Customers>

**Feature ID:** 4  
**Branch pattern:** `feature/5-Manage_Customers`
**Status:** Draft  
**Created:** 2026-09-27 
**Input:** Allows authorized users to manage customer information, update customer details, and maintain accurate customer records.
**Depends on:** Authentication/security and database persistence.git add.
**Related:** Manage Inventory, Manage Supplier, Manage Employees

---

## User Stories

### US-N.1: Manage Customers

**As a** <customer service employee>  
**I want to** <add, update, and remove customer information>  
**So that** <customer information stays accurate and up to date>

**Priority:** P1  
**Independent test:** <Create a customer, update the customer's information, and remove the customer while verifying that the database reflects each change.>  
**Acceptance scenarios:** see ### US-N.1 under Acceptance Criteria.

### US-N.2: View Customer Information

**As a** <customer service employee>  
**I want to** <view customer information>  
**So that** <I can quickly find the information needed to assist customers>

**Priority:** P1  
**Independent test:** <Search for an existing customer and verify that the customer's stored information is displayed correctly.>  
**Acceptance scenarios:** see ### US-N.2 under Acceptance Criteria

### US-N.3: Update Customer Information

**As a** <customer service employee>  
**I want to** <update customer contact information>  
**So that** <the system contains current customer information>

**Priority:** P1  
**Independent test:** <Update a customer's information and verify that the new information is stored correctly in the database.>  
**Acceptance scenarios:** see ### US-N.3 under Acceptance Criteria

---

## Requirements

### Functional Requirements

- **FR-001**: The system shall allow authorized users to create, view, update, and remove customer records.
- **FR-002**: The system shall store the customer's name and contact information.
- **FR-003**: The system shall allow authorized users to search for customers.
- **FR-004**: The system shall allow authorized users to update customer information.
- **FR-005**: The system shall require a customer's name before creating a customer record.
- **FR-006**: The system shall prevent unauthorized users from modifying customer records.
- **FR-007**: The system shall prevent duplicate customer records when the customer can be identified by existing unique information.
- **FR-008**: The system shall store customer records in the database.

---

## Assumptions

- Users managing customers are authorized to make customer changes.
- Each customer has a unique ID.
- A customer must have a name.
- Customer contact information may be updated when needed.
- Customer information is stored in the database.
- Customers may have multiple interactions with the organization.
- Removing a customer does not automatically delete required historical records.

---

## Edge Cases

- A customer is created without a name.
- A user enters invalid customer contact information.
- A duplicate customer is entered.
- A user attempts to update a customer that does not exist.
- A user attempts to remove a customer that does not exist.
- A user without the required permissions attempts to modify a customer.
- A customer's email address or phone number changes.
- A customer has no contact information.
- A customer has existing records when the customer is removed.

---

## Success Criteria

- **SC-001**: Authorized users can create, view, update, and remove customer information while keeping the database accurate.
- **SC-002**: Customer information can be quickly located and updated when changes occur.

---

## Key Entities

- **Customer**: A person or organization that purchases or receives products or services. Contains the customer's name and contact information.
- **Customer Record**: Stores information about a customer and their relationship with the organization.
- **Customer Contact Information**: Information such as the customer's email address, phone number, and address.

---

## Data Model Requirements

### `customer` table

| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `first_name` | VARCHAR(100) | Required |
| `last_name` | VARCHAR(100) | Required |
| `email` | VARCHAR(255) | Optional; should be unique when provided |
| `phone` | VARCHAR(25) | Optional |
| `address` | VARCHAR(255) | Optional |
| `created_at` | DATETIME | Required |
| `updated_at` | DATETIME | Required |

### Associations (if known)

- A **Customer** may have multiple transactions or orders.
- A **Customer** may have multiple customer records or interactions.
- Customer information can be referenced by other system features that require customer information.

---

## Acceptance Criteria

### US-N.1 — Manage Customers

#### Scenario: Customer is created successfully

* **Given** <the user is authorized to manage customers>
* **When** <the user enters valid customer information and creates the customer>
* **Then** <the system creates the customer record in the database>
* **And** <the customer's information is saved correctly>

#### Scenario: Customer information is invalid

* **Given** <the user is creating a customer>
* **When** <the user leaves a required customer field blank>
* **Then** <the system rejects the customer record>
* **And** <the customer is not saved in the database>

### US-N.2 — View Customer Information

#### Scenario: Customer information is found

* **Given** <a customer exists in the database>
* **When** <the user searches for the customer using valid information>
* **Then** <the system displays the matching customer>
* **And** <the customer's stored information is displayed correctly>

#### Scenario: Customer cannot be found

* **Given** <the customer does not exist in the database>
* **When** <the user searches for the customer>
* **Then** <the system indicates that no matching customer was found>
* **And** <the system does not display incorrect customer information>

### US-N.3 — Update Customer Information

#### Scenario: Customer information is updated successfully

* **Given** <a customer already exists>
* **When** <the user updates valid customer information>
* **Then** <the system updates the customer record>
* **And** <the new customer information is saved in the database>

#### Scenario: Customer does not exist

* **Given** <the customer does not exist in the database>
* **When** <the user attempts to update the customer>
* **Then** <the system rejects the update>
* **And** <no customer information is changed>