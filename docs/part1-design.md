# Definition and design baseline

## Scope decisions

Tabletec is the reservation service. Each installation serves one configured restaurant; the example venue is Brace e Vino. A guest chooses a date, party size and fixed seating, optionally adds meals and confirms with a name and email. Staff see bookings and disable meals that cannot be offered. This keeps the project focused on software design rather than payments, multiple restaurant onboarding or delivery logistics.

The prototype has five tables: two with 2 seats, two with 4 and one with 6. Fixed 90-minute seatings start at 18:00, 19:30 and 21:00. These are explicitly selected demonstration assumptions. Party sizes are 1-6. A guest cannot combine tables, edit an existing booking or pick a specific table. To change a visit, cancel before seating and book again, accepting that the replacement may no longer be available.

## Responsibilities and GRASP

- Browser view: collect inputs, show availability and feedback. No authority over capacity or prices.
- HTTP adapter (`server.mjs`): parse requests, check the staff demo key and map domain errors to responses. It does not allocate tables.
- `BookingService`: application controller and transaction coordinator. Owns creation, lookup, cancellation and status transitions.
- `AvailabilityService`: information expert for suitable tables and remaining meal portions.
- `ReservationBuilder`: creator for one coherent reservation, including contact validation and price snapshot total.
- `MenuService`: application service for catalog availability changes.
- `JsonStore`: persistence only, using read/transaction operations. It does not know business rules.
- `BookingEvents`: optional post-save observers. It does not decide whether a booking succeeds.

This gives cohesive responsibilities and separates the browser from the domain model. JavaScript uses structural contracts rather than runtime-enforced interfaces for the store and event listeners. A future repository adapter must support synchronous `read()` and `transaction(operation)` semantics or deliberately migrate the application to awaited database transactions. Swapping an asynchronous implementation into the current synchronous contract is unsafe.

## Risks and planned patterns

### R1. A last table or meal portion appears available to two customers

The availability screen is advisory. `BookingService.create()` rereads, validates, selects, builds and writes within one synchronous store transaction. A failure discards the entire draft. Cancellation changes status once, and derived counts release table/portions without separate counter updates.

**Pattern:** Strategy isolates table choice so a new allocation policy does not require editing the booking workflow. **Limitation:** Strategy alone does not prevent races. Correctness comes from the transaction. The current guarantee holds only within one Node process. Before multi-process use, move to a database transaction, a conditional portion update, and a unique active allocation constraint.

### R2. Optional preorders create inconsistent or half-built bookings

**Pattern:** Builder gives one construction path for guest, selected table, visit and optional order lines. Catalog names/prices are copied into order lines and totals use integer cents. The application checks date, capacity, quantities and menu state before writing. A meal failure saves no table reservation.

**Tradeoff:** Builder adds ceremony to a small object and does not replace business validation. It remains useful while the reservation carries optional order details. If the model stays small, a validated factory may be simpler.

### R3. Notification and kitchen integrations spread through the booking service

**Pattern:** Observer lets independent listeners receive a creation/cancellation event after save. The current observer only logs event type and booking ID. Subscriber failure does not undo persistence and does not skip later listeners.

**Limitation:** An in-memory event can disappear after save if the process stops. There is no delivery guarantee. Real notifications need an outbox saved in the same database transaction, retries, and idempotent consumers. No email delivery is claimed in this baseline.

### R4. Invalid status changes or exposed management keys

Explicit transition guards permit confirmed -> seated -> completed, and confirmed -> cancelled before seating. Staff cannot check in a future seating. A future State pattern becomes justified if no-show, preparation and refund workflows make guards hard to maintain; it is not forced into Part 1.

Guest management requires both a random reservation ID and private token, sent in headers rather than URLs. Staff routes require a key and the server binds to localhost. These are demo controls only. Production requires accounts, authorization, rate limits, TLS and stronger credential lifecycle management. No public deployment is in scope.

## Acceptance evidence

The supporting local prototype has an automated domain suite that verifies last-table contention in the single-process model, table reuse across seatings, server-owned price snapshots, portion limits, rollback, cancellation release, input validation, preorder cutoff after the October DST change, status guards, observer failure isolation, persistence across store instances and alternate allocation strategies. These checks do not establish multi-process safety or production security.

## Source of requirements

Read from RIT myCourses on October 25, 2026: Project Part 1 assignment, Important Dates and Submissions, and SWEN-383 Fall 2026 syllabus. The assignment requires scope, draft class and sequence UML, at least two design risks and planned patterns, and a five-minute proposal presentation. It does not explicitly require a complete deployed website at Part 1.

## SOLID review

The composition root now injects the builder provider as well as storage, time, events and allocation. Builder output owns copied snapshots, and menu administration has its own service. Shared strategy contracts and migration/snapshot tests cover these decisions.

## Restaurant identity and menu language

Restaurant identity is configuration in `src/restaurant.mjs`; the service remains Tabletec. The website uses English navigation and booking instructions. Each dish has an Italian name followed by an English name. The 31-item menu includes a charcoal grill selection and 16 drinks. Only recipes containing pork show a small outlined SVG pig symbol with an accessible label and tooltip; carbonara includes guanciale. A Boolean ingredient flag drives that display. Confirmed lines snapshot the translation and pork flag, so later catalog changes do not rewrite a receipt.

Tabletec is visible in the service bar above the restaurant header and inside the reservation panel, with a larger footer credit. This makes the project identity clear while keeping the restaurant name attached to its menu. The evening drinks list includes espresso, aperitivi, wine and an herbal digestif.

The visual identity uses a small original table-and-four-chairs SVG mark beside Tabletec. The restrained detail pass preserves the established dark palette, section order and photography. Kitchen notes use actual catalog entries and serving sizes; interface copy describes actions directly. No invented awards, restaurant history or testimonials are used.
