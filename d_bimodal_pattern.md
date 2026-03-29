# (d) Bimodal Pattern in Tier-1 Cities

## What Operational Reasons Could Explain the Bimodal Pattern?

In Mumbai and Bangalore, `delivery_time_mins` shows two distinct peaks:
- **Peak 1: 12–14 minutes**, fast deliveries
- **Peak 2: 28–32 minutes**, slow deliveries

This bimodal shape strongly suggests that **two operationally distinct sub-populations** are being mixed together in the same distribution. Likely explanations:

**1. Two distinct rider availability states:**
Mumbai and Bangalore are high-demand cities. During peak hours (`order_hour` = 12–14 or 19–21), rider queues form and orders wait longer before assignment. Off-peak orders are fulfilled almost immediately. The 12–14 min peak represents off-peak delivery; the 28–32 min peak represents peak-hour delivery where a rider was not immediately available.

**2. Hub proximity vs. distant delivery zones:**
Large Tier-1 cities have complex delivery geographies. Some `hub_id` locations serve nearby dense neighbourhoods (12–14 min radius), while the same hub may also serve farther zones due to demand spillover, resulting in structurally longer deliveries from the same hub. The two peaks may reflect these two zone types being aggregated.

**3. Order category mix:**
Grocery and Bakery orders may have short prep and delivery times, while Fresh Food requires cooking/assembly time. If Tier-1 cities have a higher proportion of Fresh Food orders, the second peak could represent the prep-heavy order segment being mixed with instant-pickup orders.

**4. Time-of-day operational shift:**
Rider shift changes (e.g., morning to afternoon shift) may create a gap in rider availability that results in a cluster of delayed deliveries, these appear as a second peak rather than a smooth tail.

---

## Why Must This Be Addressed Before Building a Prediction Model?

A bimodal distribution violates a core assumption underlying many ML models: that the training data comes from a **single, relatively homogeneous population**. If the two peaks represent two fundamentally different types of orders (near vs. far, peak vs. off-peak, simple vs. complex), then:

- A single model trained on the mixed distribution will **learn an average of two different realities**, it will neither accurately predict 12-minute deliveries nor 30-minute deliveries.
- The model's decision boundary will be miscalibrated, it may predict a "medium" delivery time of ~20 minutes for all orders, consistently missing both fast and slow cases.
- Feature importance will be confused: features that drive the 12-min peak (e.g., `rider_distance_km` being low) and features that drive the 28-min peak (e.g., `order_hour` being peak time) will partially cancel each other out.

---

## What Modeling Mistake Might Occur If This Is Ignored?

**Underfitting due to population mixing:**

If the bimodal pattern is ignored and a single model is trained on the combined Tier-1 data, the model will attempt to fit a unimodal response surface over a genuinely bimodal distribution. The practical mistake:

- The model will systematically **underpredict late deliveries for the 28–32 min cluster** (because the 12–14 min cluster dominates training and pulls the fitted line/boundary toward fast delivery predictions).
- This means the late delivery risk model will have **low Recall for the slow-delivery sub-population**, exactly the high-risk orders ZeptoFresh needs to catch.
- Operationally, the model will fail to flag the second-peak orders for early rider reallocation, defeating the purpose of the prediction system.

**Correct approach:** Segment Tier-1 city data by `order_hour` (peak vs. off-peak) or by `hub_id` delivery zone radius, and either train separate models per segment or add a derived feature (e.g., `is_peak_hour`) that allows the model to distinguish the two sub-populations.
