# Feature: <Manage Inventory>

**Feature ID:** 1  
**Branch pattern:** `feature/1-Manage_Inv`
**Status:** Draft  
**Created:** 2026-09-20 
**Input:**  Allows people to manage inventory and change anything if needed.
**Depends on:** none
**Related:** Authentication/security ADRs may be added later  

---

## User Stories

### US-N.1: Manage Inventory
**As a** <Automated Process>  
**I want to** <Order Items when supply is low>  
**So that** <Inventory is maintained>

**Priority:** P1  
**Independent test:** <Set an item's inventory below its reorder threshold and verify that an order is automatically created for the required item.>  
**Acceptance scenarios:** see ### US-N.1 under Acceptance Criteria.

### US-N.2: Logs
**As a** <Docker>  
**I want to** <log in-coming items>  
**So that** <Inventory is maintained>

**Priority:** P1  
**Independent test:** <Record a valid incoming shipment and verify that the inventory quantity and corresponding log entry are updated>  
**Acceptance scenarios:** see ### US-N.2 under Acceptance Criteria

### US-N.3: Logs and missing
**As a** <Docker>  
**I want to** <log Damaged and Missing Items>  
**So that** <Inventory is properly updated and suppliers is notified>

**Priority:** P1  
**Independent test:** <Record a shipment containing damaged or missing items and verify that inventory is adjusted and a supplier notification is generated.>  
**Acceptance scenarios:** see ### US-N.3 under Acceptance Criteria
---

## Requirements

### Functional Requirements

- **FR-001**: The system MUST maintain the current quantity of each inventory item.
- **FR-002**: The system MUST allow authorized processes or users to update inventory quantities.
- **FR-003**: The system MUST define a reorder threshold for inventory items.
- **FR-004**: The system MUST automatically create an order when an item's available quantity falls below its reorder threshold.
- **FR-005**: The system MUST record incoming inventory, including the item, quantity, and time received.
- **FR-006**: The system MUST record damaged inventory separately from successfully received inventory.
- **FR-007**: The system MUST record items reported as missing from an expected shipment.
- **FR-008**: The system MUST update inventory quantities when incoming, damaged, or missing items are recorded.
- **FR-009**: The system MUST retain an inventory log for each inventory-changing event.
- **FR-010**: The system MUST prevent inventory quantities from being reduced below zero through normal inventory operations.
- **FR-011**: The system MUST reject inventory updates containing missing or invalid required fields.
- **FR-012**: The system MUST prevent duplicate processing of the same inventory event when the event has a unique identifier.
- **FR-013**: The system MUST provide enough information in inventory logs to identify the item, quantity affected, event type, and timestamp.
- **FR-014**: The system MUST support supplier notification for shipments containing missing or damaged items.
- **FR-015**: The system MUST preserve an audit trail when inventory is manually changed.

---

## Assumptions

- An inventory item has a unique identifier.
- Each inventory item has a current quantity and a reorder threshold.
- Suppliers can be associated with inventory items.
- The application can receive inventory events from a Docker-based process.
- Authentication and authorization may be implemented separately.
- Supplier notification will initially be represented by an application event, message, or notification mechanism rather than a specific external supplier API.
- Inventory forecasting, demand prediction, and advanced purchasing optimization are not part of this feature.
- User interfaces for inventory management are not required unless separately specified.
- Authentication/security ADRs will be added later.

---

## Edge Cases

- Empty required field → Reject the request and do not modify inventory.
- Cross-user access → Unauthorized users or processes MUST NOT modify inventory.
- Duplicate / invalid input → Do not apply the inventory change more than once   

---

## Success Criteria

- **SC-001**: Every Gherkin scenario has at least one automated test before merge
- **SC-002**: <measurable outcome for this feature>

---

## Key Entities

- **Inventory Item**: A product or material stored in the warehouse. Contains its current quantity, reorder threshold, and supplier information.
- **Supplier**: A company or organization that provides inventory items to the warehouse.
- **Shipment**: A delivery from a supplier containing one or more inventory items.
- **Purchase Order**: An order created for a supplier when inventory needs to be replenished.
- **Supplier Notification**: A message generated when missing or damaged inventory needs to be reported to a supplier.
- **Audit Record**: A record of a manual inventory change, including who made the change, when it occurred, and why

---

## Data Model Requirements

### `table_name` table
| Field | Type | Rules |
|-------|------|-------|
| `id` | INTEGER PK | Auto-increment |
| `name` | VARCHAR | Required |
| `status` | VARCHAR | Required |
| `updated_at` | VARCHAR | Required |
| `quantity` | VARCHAR | Required |
| `supplier_id` | VARCHAR | Required |

### Associations (if known)
- A supplier can provide many inventory items.
- An inventory item belongs to one supplier.
- A shipment belongs to one supplier.
- A shipment contains many shipment items.
- A shipment item references one inventory item.
- An inventory item can have many inventory logs.
- An inventory item can have multiple purchase orders.
- A purchase order belongs to one supplier and one inventory item.

---

## Acceptance Criteria

### US-N.1 — Manage Inventory

#### Scenario: Inventory is above the reorder threshold
*   **Given** <an inventory item has a quantity of its minimum>
*   **When** <the inventory is check>
*   **Then** <reorder back to close to the max>
*   **And** <an inventory log records the reorder event>

#### Scenario: Inventory is above the reorder threshold
*   **Given** <an inventory item has a quantity above its reorder threshold>
*   **When** <the system checks the item's inventory level>
*   **Then** <no purchase order is created>
*   **And** <no reorder event is recorded>

### US-N.2 — Logs

#### Scenario: Incoming inventory is successfully received
*   **Given** <a valid incoming shipment contains an inventory item and quantity>
*   **When** <the shipment is recorded as received>
*   **Then** <the inventory quantity is increased by the received quantity>
*   **And** <an inventory log records the item, quantity, event type, and timestamp>

#### Scenario: Incoming inventory contains invalid information
*   **Given** <an incoming shipment is missing a required field or contains an invalid quantity>
*   **When** <the shipment is recorded>
*   **Then** <the inventory update is rejected>
*   **And** <no inventory quantity or inventory log is changed>

### US-N.3 — Damaged inventory is received

#### Scenario: Descriptive name (happy path)
*   **Given** <a shipment contains an inventory item that is damaged>
*   **When** <an item is damaged>
*   **Then** <the damaged quantity is recorded separately from successfully received inventory>
*   **And** <a supplier notification is generated for the damaged items>

#### Scenario: Items are missing from a shipment
*   **Given** <an expected shipment contains fewer items than the expected quantity>
*   **When** <the shipment is recorded as received>
*   **Then** <the missing quantity is recorded in the inventory log>
*   **And** <a supplier notification is generated for the missing items>