# Requirements Specification

**Problem Statement #36 — Restaurant Table Booking & Pre-Ordering App** (Retail, E-Commerce & Finance)

A restaurant hospitality app that lets diners pick a specific table on a live floor plan, place an advance food order, and have the kitchen's preparation timeline worked out so the food is ready when they arrive.

| Actor | Kind | Role in the system |
|---|---|---|
| Diner | Primary | Books a table, pre-orders food, pays, updates arrival time |
| Restaurant Manager | Primary | Sets up the floor plan and menu, runs the floor terminals, watches the kitchen queue |
| Payment Gateway | Secondary (external system) | Authorises and captures pre-order payments |

## Requirements Table

| ID | Type | Description | Priority | Acceptance Criteria | Rationale |
|---|---|---|---|---|---|
| **FR-001** | Functional | The system shall display an interactive 2D floor plan (as laid out by the Restaurant Manager) showing each table's position, seating capacity and live status for the diner's chosen date, arrival time and party size, and shall allow the diner to select and reserve one available table. | High | **Pass:** only tables that seat the whole party and are free for the entire dining window (arrival + 90 min dining + 15 min turnover) can be selected; confirming returns a unique reservation ID and the table shows *Reserved* for that window.<br>**Fail:** two reservations exist for the same table in overlapping windows (including two diners confirming the same table at the same moment), or a table smaller than the party can be booked. | Choosing an exact table is the app's main differentiator. Preventing double-booking protects the diner's experience and keeps floor operations predictable. |
| **FR-002** | Functional | The system shall allow the diner to attach a pre-order (menu items, quantities and customisation notes) to a held or confirmed reservation, listing only items the Restaurant Manager has marked available, until a cut-off of 60 minutes before the scheduled arrival time. | High | **Pass:** the saved pre-order is linked to the reservation ID and shows itemised prices and a running total; sold-out items cannot be added; changes attempted after the cut-off are rejected with a message.<br>**Fail:** a pre-order exists without a reservation, a sold-out item is accepted, or an edit after the cut-off is saved. | Tying food to the booking lets the kitchen plan ahead instead of reacting at the table. The cut-off keeps the preparation schedule stable. |
| **FR-003** | Functional | The system shall calculate a kitchen preparation timeline for every confirmed pre-order: *target ready time* = scheduled arrival + seating buffer (5 min); *fire time* of each item = target ready time − that item's preparation time. It shall queue each kitchen ticket at its fire time and recalculate all unfired tickets whenever the diner updates the arrival time. | High | **Pass:** for a 19:30 arrival with a 25-min main and a 10-min starter, the tickets fire at 19:10 and 19:25 and both show a ready time of 19:35; if the diner moves the arrival to 19:45 before any ticket fires, both fire times shift by 15 minutes.<br>**Fail:** any item's planned ready time is more than 2 minutes off the target, or a ticket fires at a time other than its (recalculated) fire time. | Delivers "seamless arrival dining": food reaches the table soon after the diner sits down, not cold and not late. Staggering by preparation time stops every dish being started at once. |
| **FR-004** | Functional | The system shall collect payment for a pre-order through an external payment gateway before the pre-order is confirmed. A pre-order whose payment is declined or times out (30 s) shall stay unconfirmed, and no kitchen ticket shall be scheduled for it. | Medium | **Pass:** an approved payment marks the pre-order *Paid*, issues a receipt and schedules its kitchen tickets; a declined or timed-out payment leaves it *Unpaid* with no tickets and no money captured; retrying a payment never charges the diner twice.<br>**Fail:** kitchen tickets are scheduled for an unpaid pre-order, or the diner is charged twice for one pre-order. | Prepayment stops the kitchen from cooking food that nobody pays for. Medium priority because a table can still be booked without a pre-order. |
| **FR-005** | Functional | The system shall let the Restaurant Manager move a table through its status lifecycle (Available → Reserved → Seated → Billed → Available; walk-ins Available → Seated) from any floor terminal, and mark a reservation as a *No-Show* once 15 minutes have passed after its arrival time without the party being seated. Marking a No-Show shall release the table and cancel its unfired kitchen tickets. | High | **Pass:** valid transitions are saved and shown on every terminal; invalid ones (e.g. Available → Billed, or a No-Show before the 15-minute grace period) are rejected with a reason; after a No-Show the table is bookable again and no further tickets for that reservation reach the kitchen.<br>**Fail:** an invalid transition is accepted, a released table still appears unavailable to diners, or an unfired ticket of a No-Show is still sent to the kitchen. | Accurate table statuses are what FR-001 shows to diners. Releasing no-shows recovers the table for other guests and avoids wasted food. |
| **NFR-001** | Performance | Table status changes (Reserved, Seated, Billed, Available) shall reach every connected manager floor terminal and every open diner floor-plan view within 500 ms (95th percentile), and table-availability searches shall respond within 2 s, under a peak load of 500 concurrent users. | High | **Pass:** a load test (e.g. k6 or JMeter) simulating 500 concurrent users and 20 status changes per second for 15 minutes shows p95 propagation latency ≤ 500 ms, p95 availability-search time ≤ 2 s, and no client left showing a stale status at the end.<br>**Fail:** any of these thresholds is missed. | A stale floor plan lets two diners pick the same table or leads staff to seat guests at an occupied one. Dinner rush is exactly when load and the cost of stale data are highest. |
| **NFR-002** | Security | All client–server traffic shall use TLS 1.2 or higher. Every user shall authenticate before booking or using a floor terminal, and role-based access control shall restrict floor-plan, menu and table-status changes to the Restaurant Manager role. Passwords shall be stored only as salted hashes (bcrypt or Argon2), and card details shall be handled only by the payment gateway (tokenised), never stored or logged by the app. | High | **Pass:** an OWASP ZAP scan reports no high or critical findings; a Diner-role token calling any manager endpoint receives HTTP 403; plain-HTTP requests are refused or redirected to HTTPS; a database and log audit finds no card numbers or plain-text passwords.<br>**Fail:** any one of these checks fails. | The app holds personal details and takes payments, and manager actions change live restaurant operations. Keeping card data with a PCI-DSS-compliant gateway keeps the app out of card-data scope. |

## Assumptions

- Dining duration (90 min), turnover buffer (15 min), pre-order cut-off (60 min), seating buffer (5 min), table hold (10 min) and no-show grace period (15 min) are restaurant-configurable defaults; the acceptance tests use these default values.
- Each menu item has a preparation time recorded by the Restaurant Manager when the menu is set up.
- One reservation is for one table; large parties needing joined tables are out of scope for this version.

## Traceability: Use Cases ↔ Requirements

| Use case (see [use-case diagram](use-case-diagram.png)) | Actor(s) | Requirement(s) |
|---|---|---|
| UC-01 Reserve Table | Diner | FR-001, NFR-001 |
| UC-02 Check Table Availability | *(included by UC-01)* | FR-001, NFR-001 |
| UC-03 Pre-Order Food | Diner *(also extends UC-01)* | FR-002 |
| UC-04 Pay for Pre-Order | Payment Gateway | FR-004, NFR-002 |
| UC-05 Calculate Kitchen Prep Timeline | *(included by UC-03, UC-06)* | FR-003 |
| UC-06 Update Arrival Time | Diner | FR-003 |
| UC-07 Configure Floor Plan | Restaurant Manager | FR-001, NFR-002 |
| UC-08 Manage Menu Availability | Restaurant Manager | FR-002, NFR-002 |
| UC-09 View Kitchen Prep Queue | Restaurant Manager | FR-003 |
| UC-10 Update Table Status | Restaurant Manager | FR-005, NFR-001, NFR-002 |
| UC-11 Mark No-Show | *(extends UC-10)* | FR-005 |
