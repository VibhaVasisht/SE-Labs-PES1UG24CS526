# Lab 2: Agile Backlog Creation & Sprint Simulation in Jira

**System:** Restaurant Table Booking & Pre-Ordering App (requirements from [Lab 1](../lab-1/requirements.md))

## Epics

| Epic | Description | Requirement |
|---|---|---|
| EPIC 1: Table Reservation | Let diners view the live floor plan and book a specific table without double booking. | FR-001 |
| EPIC 2: Food Pre-Ordering & Payment | Let diners attach a paid food order to their reservation before they arrive. | FR-002, FR-004 |
| EPIC 3: Kitchen Prep Scheduling | Time each dish so the food is ready when the diner sits down. | FR-003 |
| EPIC 4: Floor & Table Management | Let the manager set up the floor plan and keep table statuses up to date. | FR-005 |

## User Stories

| Story | As a | I want to | So that | Priority | Points |
|---|---|---|---|---|---|
| **1.1** View Floor Plan | diner | see the floor plan with each table's capacity and live status for my date, time and party size | I can pick a table that suits me | High | 5 |
| **1.2** Reserve a Table | diner | select a free table and confirm the booking | my table is guaranteed when I arrive | High | 8 |
| **1.3** Booking Confirmation | diner | receive a reservation ID after booking | I can view or change my booking later | Medium | 2 |
| **2.1** Add Pre-Order | diner | add available menu items, quantities and notes to my reservation | my food is planned before I arrive | High | 5 |
| **2.2** Edit Pre-Order | diner | change my pre-order up to 60 minutes before arrival | I can adjust my order if plans change | Medium | 3 |
| **2.3** Pay for Pre-Order | diner | pay for my pre-order online | my order is confirmed and sent to the kitchen | High | 8 |
| **3.1** Calculate Prep Timeline | restaurant manager | have the system work out when each dish should start cooking | food is ready when the diner sits down | High | 8 |
| **3.2** Update Arrival Time | diner | change my arrival time | the kitchen timing shifts to match | Medium | 5 |
| **3.3** View Kitchen Queue | restaurant manager | see upcoming kitchen tickets in order of fire time | the kitchen knows what to cook next | Medium | 3 |
| **4.1** Configure Floor Plan | restaurant manager | lay out tables with their positions and seating capacity | diners see the real layout | High | 5 |
| **4.2** Update Table Status | restaurant manager | move a table between Available, Reserved, Seated and Billed | every screen shows the correct status | High | 3 |
| **4.3** Mark No-Show | restaurant manager | mark a party as a no-show 15 minutes after their arrival time | the table is released and unfired dishes are cancelled | Low | 2 |

Story points use the Fibonacci scale (1, 2, 3, 5, 8, 13) and were agreed with Planning Poker. **Total: 57 points.**

## Sprint Plan (1 week each)

| Sprint | Stories | Points |
|---|---|---|
| Sprint 1 | 4.1, 1.1, 1.2, 1.3, 2.1, 4.2 | 28 |
| Sprint 2 | 2.3, 2.2, 3.1, 3.2, 3.3, 4.3 | 29 |
