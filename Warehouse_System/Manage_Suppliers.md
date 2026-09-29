# Feature: <Manage_Supplier_Order>

**Feature ID:** 5  
**Branch pattern:** `feature/6-Manage_Supplier_Order`
**Status:** Draft  
**Created:** 2026-09-27 
**Input:** Allows authorized users to create, update, view, and manage orders placed with suppliers for inventory items.
**Depends on:** Manage Inventory, Manage Supplier, and database persistence
**Related:** Manage Inventory, Manage Supplier, Authentication/Security

---

## User Stories

### US-N.1: Manage Supplier Orders

**As a** <warehouse employee>  
**I want to** <create, update, and cancel supplier orders>  
**So that** <the warehouse can keep track of inventory orders placed with suppliers>

**Priority:** P1  
**Independent test:** <Create a supplier order, update the order information, and cancel the order while verifying that the database reflects each change.>  
**Acceptance scenarios:** see ### US-N.1 under Acceptance Criteria.

### US-N.2: Track Supplier Orders

**As a** <warehouse employee>  
**I want to** <view the status of supplier orders>  
**So that** <I can track orders from the time they are placed until they are received>

**Priority:** P1  
**Independent test:** <Create a supplier order and update its status while verifying that the correct status is displayed.>  
**Acceptance scenarios:** see ### US-N.2 under Acceptance Criteria

### US-N.3: Receive Supplier Orders

**As a** <warehouse employee>  
**I want to** <record when a supplier order is received>  
**So that** <the inventory quantity can be updated when ordered items arrive>

**Priority:** P1  
**Independent test:** <Mark a supplier order as received and verify that the ordered inventory quantity is updated correctly.>  
**Acceptance scenarios:** see ### US-N.3 under Acceptance Criteria

---

## Requirements

### Functional Requirements

- **FR-001**: The system shall allow authorized users to create supplier orders.
- **FR-002**: The system shall allow authorized users to view supplier orders.
- **FR-003**: The system shall allow authorized users to update supplier order information.
- **FR-004**: The system shall allow authorized users to cancel supplier orders.
- **FR-005**: The system shall associate each supplier order with a supplier.
- **FR-006**: The system shall associate supplier orders with the inventory items being ordered.
- **FR-007**: The system shall store the quantity of each inventory item ordered.
- **FR-008**: The system shall track the status of each supplier order.
- **FR-009**: The system shall allow authorized users to mark a supplier order as received.
- **FR-010**: The system shall update inventory quantities when a supplier order is received.

---

## Assumptions

- Users creating supplier orders are authorized to manage supplier orders.
- Each supplier order has a unique ID.
- Each supplier order is associated with a supplier.
- A supplier order contains one or more inventory items.
- Supplier order quantities must be greater than zero.
- Supplier orders have a status such as pending, ordered, received, or cancelled.
- Receiving an order increases the available inventory quantity.
- Cancelled orders do not increase inventory quantities.

---

## Edge Cases

- A supplier order is created without a supplier.
- A supplier order is created without any inventory items.
- A user enters a quantity of zero.
- A user enters a negative quantity.
- A user attempts to order an inventory item that does not exist.
- A user attempts to update an order that does not exist.
- A user attempts to cancel an order that has already been received.
- A user attempts to receive an order that has already been cancelled.
- A supplier order is partially received.
- A supplier order contains multiple inventory items.
- A supplier order is received with a different quantity than originally ordered.

---

## Success Criteria

- **SC-001**: Authorized users can create, view, update, and cancel supplier orders while keeping the database accurate.
- **SC-002**: Received supplier orders correctly update the inventory quantities and order status.

---

## Key Entities

- **Supplier Order**: An order placed with a supplier for one or more inventory items. Contains the supplier, order status, order date, and expected delivery information.
- **Supplier**: A company or source that provides inventory items to the warehouse.
- **Order Item**: An inventory item and quantity included in a supplier order.

---

## Data Model Requirements

### `supplier_order` table

| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `supplier_id` | INTEGER FK | Required; references `supplier.id` |
| `status` | VARCHAR(50) | Required; pending, ordered, received, or cancelled |
| `order_date` | DATETIME | Required |
| `expected_date` | DATETIME | Optional |
| `received_date` | DATETIME | Optional |
| `created_at` | DATETIME | Required |
| `updated_at` | DATETIME | Required |

### `supplier_order_item` table

| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `supplier_order_id` | INTEGER FK | Required; references `supplier_order.id` |
| `inventory_item_id` | INTEGER FK | Required; references `inventory_item.id` |
| `quantity_ordered` | INTEGER | Required; must be greater than 0 |
| `quantity_received` | INTEGER | Required; must be 0 or greater |

### Associations (if known)

- A **Supplier** may have many **Supplier Orders**.
- A **Supplier Order** belongs to one **Supplier**.
- A **Supplier Order** may contain many **Order Items**.
- An **Order Item** belongs to one **Supplier Order**.
- An **Order Item** references one **Inventory Item**.
- An **Inventory Item** may appear in many **Supplier Orders**.

---

## Acceptance Criteria

### US-N.1 — Manage Supplier Orders

#### Scenario: Supplier order is created successfully

* **Given** <the warehouse employee is authorized to manage supplier orders>
* **When** <the employee enters a valid supplier, inventory item, and order quantity>
* **Then** <the system creates the supplier order in the database>
* **And** <the supplier order is assigned a pending or ordered status>

#### Scenario: Supplier order information is invalid

* **Given** <the warehouse employee is creating a supplier order>
* **When** <the employee enters an invalid quantity or does not select a supplier>
* **Then** <the system rejects the supplier order>
* **And** <the invalid order is not saved in the database>

### US-N.2 — Track Supplier Orders

#### Scenario: Supplier order status is updated

* **Given** <a supplier order exists in the database>
* **When** <the warehouse employee updates the order status>
* **Then** <the system saves the new supplier order status>
* **And** <the current status is displayed when the order is viewed>

#### Scenario: Cancelled supplier order cannot be received

* **Given** <a supplier order has been cancelled>
* **When** <the warehouse employee attempts to mark the order as received>
* **Then** <the system rejects the update>
* **And** <the inventory quantity is not changed>

### US-N.3 — Receive Supplier Orders

#### Scenario: Supplier order is received successfully

* **Given** <a supplier order has been placed and contains valid inventory items>
* **When** <the warehouse employee marks the supplier order as received>
* **Then** <the system updates the supplier order status to received>
* **And** <the ordered quantities are added to the appropriate inventory items>

#### Scenario: Received quantity does not match ordered quantity

* **Given** <a supplier order contains an ordered quantity of 10 items>
* **When** <the warehouse employee records that only 8 items were received>
* **Then** <the system records the actual quantity received>
* **And** <the inventory quantity is increased by 8 instead of 10>