# Vehicle Check-In and Check-Out Algorithm

## Objective

To manage the vehicle’s parking session by recording entry and exit events and maintaining real-time availability.

---

## Vehicle Check-In (Entry) Algorithm

### Inputs

- Vehicle ID
- Vehicle size (SMALL / MEDIUM / LARGE)
- Entry timestamp

### Preconditions

- Vehicle is not currently parked
- Parking lot is operational

### Algorithm Steps

1. **Validate Vehicle Status**  
   Confirm that the vehicle does not have an active parking session.

2. **Initiate Spot Allocation**  
   Invoke the Parking Spot Allocation Algorithm to assign a suitable parking spot.

3. **Record Entry Event**  
   Create a new parking log entry with:
   - Vehicle ID
   - Assigned parking spot ID
   - Entry timestamp

4. **Update Spot Availability**  
   - Mark the assigned parking spot as unavailable.  
   - Update the available spot count on the corresponding floor.

5. **Confirm Check-In**  
   Return parking spot and floor details to the entry system.

### Failure Handling

- If no parking spot is available, return a **Parking Full** response.
- If the vehicle already has an active session, reject the check-in request.

### Postconditions

- Parking session is active.
- Parking spot is reserved for the vehicle.
- Availability is updated in real time.

---

## Vehicle Check-Out (Exit) Algorithm

### Inputs

- Vehicle ID
- Exit timestamp

### Preconditions

- Vehicle has an active parking session.

### Algorithm Steps

1. **Retrieve Active Parking Session**  
   Locate the active parking log entry associated with the vehicle.

2. **Record Exit Time**  
   Update the parking log with the exit timestamp.

3. **Calculate Parking Duration**  
   Compute the total duration of stay using entry and exit times.

4. **Calculate Parking Fee**  
   Invoke the Fee Calculation Logic using:
   - Parking duration
   - Vehicle type

5. **Generate Payment Record**  
   Create a payment entry with:
   - Parking log reference
   - Calculated fee
   - Payment status (initiated)

6. **Release Parking Spot**  
   - Mark the parking spot as available.  
   - Increment the available spot count on the corresponding floor.

7. **Close Parking Session**  
   Mark the parking session as completed.

8. **Confirm Check-Out**  
   Return payment and exit confirmation details.

### Failure Handling

- If no active parking session exists, reject the check-out request.
- If payment processing fails, retain the parking session state and allow retry.

### Concurrency Considerations

- Check-in and check-out operations are executed within transactional boundaries.
- Parking spot state updates and parking log updates are performed atomically.
- Concurrent exit requests for the same vehicle are prevented through session validation.

### Postconditions

- Parking session is closed.
- Parking spot is released.
- Floor and spot availability are consistent.
- Payment record is created.


