# Smart Parking Lot System – Database Design & Schema Documentation

## 1. Overview

This document describes the database design and schema for the backend of a Smart Parking Lot System.  

---

## 2. Entities and Responsibilities

| Entity       | Responsibility                             |
|-------------|---------------------------------------------|
| `Customer`   | Represents vehicle owners                  |
| `Vehicle`    | Represents vehicles entering the parking lot |
| `Floor`      | Maintains floor-level parking state        |
| `ParkingSpot`| Represents individual parking spots        |
| `ParkingLog` | Tracks each parking session (entry → exit) |
| `ParkingRate`| Configuration for parking fee calculation  |
| `Payment`    | Tracks payment lifecycle                   |

---

## 3. Entity Relationship Summary

### Customer Vehicle Relationship
- One `Customer` can own multiple `Vehicle`s.
- Each `Vehicle` belongs to one `Customer`.

### Floor ParkingSpot Relationship
- One `Floor` contains multiple `ParkingSpot`s.
- Each `ParkingSpot` belongs to one `Floor`.

### Vehicle ParkingSpot Relationship
- Many-to-Many relationship resolved via `ParkingLog`.
- A `Vehicle` can occupy multiple `ParkingSpot`s over time (different parking sessions).
- A `ParkingSpot` can be occupied by multiple `Vehicle`s over time (different parking sessions).

### ParkingLog Relationship with Vehicle and ParkingSpot
- One `Vehicle` can have multiple `ParkingLog` entries (multiple parking sessions).
- One `ParkingSpot` can have multiple `ParkingLog` entries (multiple parking sessions).
- One `ParkingLog` can only associate one `Vehicle` and one `ParkingSpot`.

### ParkingLog Payment Relationship
- One `ParkingLog` has one associated `Payment`.
- Each `Payment` corresponds to one `ParkingLog`.

### Diagrammatic Representation

```text
Customer 1 ───< Vehicle

Floor 1 ───< ParkingSpot

Vehicle 1 ───< ParkingLog >─── 1 ParkingSpot

ParkingLog 1 ─── 1 Payment

ParkingRate (lookup / configuration)
```

---

## 4. Database Schema

### 4.1 Customer

Stores vehicle owner details.

```sql
customer (
    customer_id        BIGINT PRIMARY KEY,
    name               VARCHAR(100),
    email              VARCHAR(100),
    phone              VARCHAR(20)
);
```

### 4.2 Vehicle

Represents vehicles entering the parking lot.

```sql
vehicle (
    vehicle_id         BIGINT PRIMARY KEY,
    customer_id        BIGINT NOT NULL,
    vehicle_number     VARCHAR(20) UNIQUE,
    vehicle_type       VARCHAR(20),   -- BIKE, CAR, BUS
    vehicle_size       VARCHAR(20),   -- SMALL, MEDIUM, LARGE

    FOREIGN KEY (customer_id) REFERENCES customer(customer_id)
);
```

### 4.3 Floor

Represents each floor in the parking lot.

```sql
floor (
    floor_id           BIGINT PRIMARY KEY,
    floor_number       INT,
    total_spots        INT,
    available_spots    INT,
    reserved_spots     INT,
    is_active          BOOLEAN
);
```

### 4.4 ParkingSpot

Represents individual parking spots.

```sql
parking_spot (
    spot_id            BIGINT PRIMARY KEY,
    floor_id           BIGINT NOT NULL,
    spot_size          VARCHAR(20),   -- SMALL, MEDIUM, LARGE
    is_available       BOOLEAN,

    FOREIGN KEY (floor_id) REFERENCES floor(floor_id)
);
```

### 4.5 ParkingLog 

Tracks each parking session from entry to exit.

```sql
parking_log (
    parking_log_id     BIGINT PRIMARY KEY,
    vehicle_id         BIGINT NOT NULL,
    spot_id            BIGINT NOT NULL,
    entry_time         TIMESTAMP NOT NULL,
    exit_time          TIMESTAMP,
    calculated_amount  DECIMAL(10,2),

    FOREIGN KEY (vehicle_id) REFERENCES vehicle(vehicle_id),
    FOREIGN KEY (spot_id) REFERENCES parking_spot(spot_id)
);
```
 
`ParkingLog` resolves the many-to-many relationship between `Vehicle` and `ParkingSpot` and represents a real-world parking event.

### 4.6 ParkingRate 

Stores configurable parking rates.

```sql
parking_rate (
    rate_id            BIGINT PRIMARY KEY,
    vehicle_type       VARCHAR(20),  -- BIKE, CAR, BUS
    rate_per_hour      DECIMAL(10,2),
    max_daily_rate     DECIMAL(10,2)
);
```

Externalizing pricing avoids hardcoding and allows dynamic rate updates without code changes.

### 4.7 Payment

Tracks payment lifecycle for each parking session.

```sql
payment (
    payment_id         BIGINT PRIMARY KEY,
    parking_log_id     BIGINT UNIQUE,
    amount_paid        DECIMAL(10,2),
    payment_method     VARCHAR(20),  -- CARD, UPI, CASH
    payment_status     VARCHAR(20),  -- INITIATED, SUCCESS, FAILED
    payment_time       TIMESTAMP,
    transaction_ref    VARCHAR(100),

    FOREIGN KEY (parking_log_id) REFERENCES parking_log(parking_log_id)
);
```

---

## 5. Key Design Decisions

### 5.1 Why `ParkingLog` is an Entity

- Represents a real-world event.  
- Contains time-based attributes (entry/exit, calculated amount).  
- Resolves many-to-many relationships cleanly between vehicles and spots over time.


### 5.2 Why Pricing is Configurable

- Supports dynamic rate changes without code deployments.  
- Keeps business logic clean and decoupled from rate storage.  
- Enables future extensions (peak pricing, floor-based pricing, etc.).

---

## 6. Concurrency Considerations

- Parking spot allocation and release are performed within database **transactions**.  
- Floor and spot availability are updated **atomically**.

---

## 7. Conclusion

This database design provides a scalable, extensible, and production-grade foundation for the Smart Parking Lot System.  
It supports efficient operations, clear data ownership, and future enhancements without requiring major schema changes.

