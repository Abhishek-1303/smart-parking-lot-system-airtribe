# Parking Fee Calculation Logic

## Objective

To calculate the parking fee for a vehicle based on its parking duration and vehicle type, using configurable pricing rules.

---

## Inputs

- Entry timestamp
- Exit timestamp
- Vehicle size (SMALL, MEDIUM, LARGE, etc.)

## Outputs

- Final parking fee amount

## Preconditions

- Vehicle has a valid parking session.
- Exit timestamp is greater than entry timestamp.
- Pricing configuration is available.

---

## Pricing Assumptions

- Parking rates are defined **per vehicle size**.
- Rates are **configurable** and externally managed.
- A **maximum daily cap** may apply.

---

## Algorithm Steps

1. **Calculate Parking Duration**  
   Compute the total parking duration as the difference between exit time and entry time.

2. **Normalize Duration**  
   - Convert the duration into the billing unit (hours or minutes).  
   - Apply rounding rules if required (round up to the nearest hour).

3. **Retrieve Applicable Rate**  
   Obtain the parking rate for the given vehicle size from the in-memory pricing configuration.

4. **Calculate Base Fee**  
   Multiply the parking duration by the applicable rate.

5. **Apply Pricing Rules**  
   - Apply a **maximum daily charge** if defined.  
   - Apply any additional rules such as **minimum charge** or **grace period** (if applicable).

6. **Return Final Amount**  
   Return the computed parking fee to the check-out process.

---

## Failure Handling

- If pricing information for the vehicle type is unavailable, reject the calculation and log an error.
---

## Design Notes

- Pricing data is treated as **configuration**, not transactional data.
- Rates are cached in memory to avoid runtime database access during fee calculation.
- The fee calculation logic is independent of payment processing.

---

## Extensibility Considerations

The fee calculation logic can be extended to support:

- Time-based pricing (peak vs off-peak hours).
- Floor-based pricing.
- Weekend or holiday pricing.
- Grace periods or promotional discounts.

---

## Postconditions

- A deterministic parking fee is calculated.
- The fee is ready to be used for payment processing.

