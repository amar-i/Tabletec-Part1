# Tabletec

Our project for SWEN-383 Software Design Principles and Patterns.

We're designing a service that lets restaurant guests book a table and optionally preorder their meals. We're using **Brace e Vino**, an Italian restaurant, as our example. **Tabletec** is the booking service.

For Part 1, we've defined the scope, drafted the class and sequence diagrams, and identified the main design risks and the patterns we plan to use.

## Report and diagrams

- [Part 1 report](deliverables/SWEN383_P1_Tabletec.pdf)
- [Design notes](docs/part1-design.md)
- [UML sources and diagrams](docs/uml/README.md)

## Scope

Guests can check available seatings, reserve a table, add meals, look up their reservation and cancel before arrival. Restaurant staff can review reservations and mark menu items unavailable.

We're keeping the initial scope to one restaurant: five tables, parties of 1-6 and three 90-minute evening seatings. We're leaving online payments, delivery, waitlists and multiple restaurants out of this version.

## Risks and planned patterns

| Risk | Our approach |
| --- | --- |
| Two guests try to book the same table, or we need to change how tables are assigned | Use **Strategy** to separate the allocation policy. Recheck availability and save the booking in one transaction. |
| Optional meals lead to an incomplete booking, or later menu edits change a confirmed order | Use **Builder** to put the reservation together and keep copies of the confirmed meal details and prices. |
| Adding kitchen updates or notifications makes the booking logic harder to change | Use **Observer** to keep those updates separate and trigger them after the booking is saved. |

We're also considering the limits of each choice: Strategy does not prevent double bookings by itself, Builder does not replace validation, and in-memory observers do not guarantee notification delivery. The report explains these tradeoffs and how we're applying SOLID and GRASP.
