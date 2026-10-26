# Tabletec - SWEN-383 Project Part 1

Tabletec is a restaurant reservation and meal preorder service. Brace e Vino is the example restaurant used to explain the design. Team name: **Tabletec**.

This repository contains the **Definition and Design Baseline** for Part 1. It publishes the proposal and draft UML. The website and supporting prototype remain local at this stage.

## Part 1 materials

- [Proposal report](deliverables/SWEN383_P1_Tabletec.pdf): project description, scope, every diagram, principles and anticipated risks with planned patterns.
- [Design notes](docs/part1-design.md): responsibilities, SOLID/GRASP and pattern rationale.
- [UML diagrams](docs/uml/README.md): editable PlantUML sources, full-resolution PNGs and SVG exports.

## Project scope

Guests check fixed-seating availability, reserve one suitable table, optionally preorder meals, retrieve confirmation and cancel before seating. Staff review bookings and control menu availability. Tabletec rechecks table and meal capacity before confirming the whole reservation.

The baseline serves one restaurant, with five demonstration tables, parties of 1-6 and three 90-minute evening seatings. Payments, delivery, shared multi-restaurant hosting and real email delivery are outside scope.

## Anticipated risks and planned patterns

| Risk | Planned pattern | Important limit |
| --- | --- | --- |
| Allocation policies change while bookings compete for capacity | Strategy | A transaction prevents conflicting reservations; Strategy alone does not. |
| Optional meal details produce incomplete bookings or mutable receipts | Builder | Construction must accompany business validation and owned snapshots. |
| Optional kitchen/notification integrations affect confirmation | Observer | In-memory observers do not guarantee reliable delivery. |

## Course delivery

Part 1 requires description and scope, draft class and sequence UML, at least two design risks with planned patterns, and a five-minute proposal presentation. The PDF is named `SWEN383_P1_Tabletec.pdf`; the actual repository URL goes in the myCourses comment. The presentation is retained separately and is not published here.

The course specifies one submission per team. Every member must understand the design; commit history corroborates actual contribution. This repository does not claim contributions that have not occurred.
