# (f) Advanced Feature Engineering

Three engineered features using existing columns that could improve late-delivery prediction:

---

## Feature 1, `total_fulfillment_time`

```
total_fulfillment_time = prep_time_mins + delivery_time_mins
```

**Why it helps:**
`delivery_time_mins` alone only captures the rider-in-transit phase. Many late deliveries are caused by orders sitting in the kitchen too long before a rider even picks them up. `total_fulfillment_time` captures the full customer experience, from order placement to doorstep, and is a more complete predictor of whether the customer perceives the order as "late." A model trained on this feature can distinguish between "late due to prep" vs. "late due to rider distance," enabling targeted interventions at the right stage.

---

## Feature 2, `order_complexity_score`

```
order_complexity_score = items_count × (1 + (order_category == 'Fresh Food'))
```

**Why it helps:**
Fresh Food orders require cooking/assembly and are inherently harder to prepare quickly, especially for large item counts. This feature combines the volume of the order (`items_count`) with a category-based complexity multiplier, Fresh Food orders get double weight because they require active kitchen time, not just picking from shelves. A high `order_complexity_score` should correlate strongly with longer `prep_time_mins` and therefore higher late-delivery risk. It gives the model a single interpretable signal for "this order is inherently hard to fulfil fast."

---

## Feature 3, `demand_pressure_index`

```
demand_pressure_index = is_weekend + rain_flag + (order_hour >= 12 and order_hour <= 14) + (order_hour >= 19 and order_hour <= 21)
```

**Why it helps:**
Late deliveries are not random, they cluster under specific external pressure conditions: weekends (high order volume), rain (slower riders), and lunch/dinner rush hours (peak demand). Each of these individually correlates with delay, but their combined effect is multiplicative rather than additive. The `demand_pressure_index` aggregates these into a single composite pressure score (0–4). An order placed during rain on a weekend evening scores 3–4 and is extremely high risk; a Tuesday morning clear-weather order scores 0 and is low risk. This feature compresses multiple correlated binary signals into one interpretable demand-side risk variable, reducing multicollinearity in the model while preserving the joint signal.
