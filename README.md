# Smart Parking Lot System – Backend Design

This project is a backend system for a smart parking lot that manages vehicle entry/exit, parking spot allocation, and fee calculation.

## Project Brief

Design the low-level architecture for a backend system of a smart parking lot with multiple floors and many parking spots. The system should:
- Automatically assign parking spots based on vehicle size and availability.
- Record vehicle entry and exit times.
- Track parking duration and calculate fees based on vehicle type and stay.
- Update parking spot availability in real time as vehicles enter and leave.

For the full problem statement, see: [Problem Statement](docs/problem-statement.md)

## Design Documentation

To keep things clean and easy to navigate, the design is split into focused documents:

- [Database Design](docs/database-design.md)  
  Schema for parking lots, floors, spots, vehicles, parking sessions (tickets), pricing rules, and payments.

- [Algorithms & Logic](docs/algorithms.md)  
  Spot allocation strategy, check-in/check-out flows, fee calculation logic, availability queries, and concurrency handling.

## Tech Stack (Planned)

- Java + Spring Boot (backend)
- Relational database (e.g., PostgreSQL / MySQL)

