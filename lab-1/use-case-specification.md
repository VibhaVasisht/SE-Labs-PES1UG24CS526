# Use-Case Specification — UC-01 Reserve Table

| Field | Detail |
|---|---|
| **Use case ID / name** | UC-01 Reserve Table |
| **Primary actor** | Diner |
| **Stakeholders** | Restaurant Manager (sees the booking on the floor terminals) |
| **Related requirements** | FR-001, NFR-001, NFR-002 (and FR-002 through the extension) |
| **Relationships** | «include» UC-02 Check Table Availability · extension point *Add Pre-Order* ← «extend» UC-03 Pre-Order Food |
| **Trigger** | The Diner taps **Book a Table** in the app. |

## Preconditions
1. The Diner is logged in to the app (NFR-002).
2. The Restaurant Manager has published the floor plan with every table's position and capacity (UC-07).
3. The requested date and time fall within the restaurant's opening hours and booking window.

## Postconditions
- **Success guarantee:** a reservation with a unique ID exists for one table, party size, date and dining window; the table shows *Reserved* for that window on every manager terminal and diner view within 500 ms (NFR-001); the Diner has received a confirmation; a pre-order is linked if the extension was taken.
- **Minimal guarantee (on failure or cancel):** no reservation is created, any temporary hold is released, and no table is left blocked.

## Main Success Scenario
1. The Diner enters the date, arrival time and party size.
2. The system performs **UC-02 Check Table Availability** «include»: it computes the dining window (arrival + 90 min + 15 min turnover) and finds the tables that seat the party and are free for the whole window.
3. The system shows the live 2D floor plan, highlighting available tables and greying out the rest with their current status.
4. The Diner taps an available table; the system shows its capacity and features (e.g. window side, outdoor).
5. The Diner selects **Hold this table**.
6. The system re-checks availability, places a 10-minute hold on the table, and pushes the status change to all floor terminals and open diner views.
7. **Extension point — Add Pre-Order:** the Diner may add a pre-order now (**UC-03 Pre-Order Food** «extend»).
8. The Diner confirms contact details and any special requests, then taps **Confirm Booking**.
9. The system turns the hold into a confirmed reservation, generates a reservation ID and marks the table *Reserved* for the dining window.
10. The system sends the confirmation to the Diner (in-app and email/SMS) and adds the booking to the Restaurant Manager's reservation list. The use case ends.

## Alternate Flow A1 — Table taken by another diner (branches at step 6)
6a. The system finds that another diner held or booked the same table moments earlier.
1. The system rejects the hold, tells the Diner the table has just been taken, and refreshes the floor plan.
2. The system highlights up to three comparable available tables (same capacity, nearest on the floor plan).
3. If the Diner picks one of them, the flow resumes at step 5.
4. If no table fits the window, the system offers the nearest available arrival times (±30 min). The Diner picks one and the flow resumes at step 2, or cancels and the use case ends under the minimal guarantee.
