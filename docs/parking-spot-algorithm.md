# Parking Spot Allocation Algorithm

## Objective

Automatically assign an available parking spot to an incoming vehicle based on vehicle size and real-time availability.

---

## Inputs

- Vehicle ID
- Vehicle size (SMALL / MEDIUM / LARGE)
- Entry timestamp

## Outputs

- Assigned parking spot ID
- Assigned floor number
- Parking session initiated

## Preconditions

- Vehicle is not currently parked.
- Parking lot is operational.

---

## Algorithm Steps

1. **Identify Eligible Floors**  
   Retrieve all floors where `available_spots > 0`.

2. **Select Target Floor**  
   Choose the most suitable floor based on a predefined strategy (for example):
   - Lowest floor number, or
   - Nearest floor to the entry gate.

3. **Filter Parking Spots by Size**  
   Within the selected floor, identify parking spots that:
   - Are currently available.
   - Can accommodate the incoming vehicle’s size.

4. **Lock Selected Parking Spot**  
   Temporarily lock the selected parking spot to prevent concurrent allocation (e.g., using a row-level lock / `SELECT ... FOR UPDATE`).

5. **Allocate Parking Spot**  
   - Mark the parking spot as unavailable / occupied.  
   - Decrement the available spot count on the floor.

6. **Create Parking Session**  
   Create a new parking log / session entry with:
   - Vehicle ID.
   - Parking spot ID.
   - Entry timestamp.

7. **Confirm Allocation**  
   Return the assigned parking spot details (spot ID, floor number, and any label) to the entry system.

---

## Failure Handling

- If no eligible floor or suitable parking spot is found, return a **Parking Full** response.
- If spot allocation fails due to concurrency conflict (e.g., two requests targeting the same spot), retry the allocation process a limited number of times before failing.

---

## Concurrency Considerations

- Spot selection and allocation are performed as a **single atomic operation**.
- Parking spot and floor availability updates are executed within a **transactional boundary**.
- **Row-level locking** is applied to prevent multiple vehicles from being assigned the same parking spot.

---

## Postconditions

- The vehicle is successfully parked and associated with a parking session.
- Parking spot availability is updated in real-time.
- Floor availability count is consistent with the actual number of free spots.

---

## Design Notes

- The allocation strategy can be extended to support advanced rules such as priority parking, electric vehicle zones, or reserved spots.
- Floor-level availability is maintained to reduce the cost of scanning individual parking spots during allocation.

