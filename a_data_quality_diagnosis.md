# (a) Data Quality Diagnosis

Four distinct data quality problems identified from the six observations:

---

## Problem 1 — Structural / Data Entry Error: `delivery_time_mins = 0`

**Classification:** Data entry error / structural issue

**Evidence:** 214 rows have `delivery_time_mins = 0`. A delivery time of zero is physically impossible, an order cannot be placed and delivered in zero minutes.

**Specific Treatment:**
Cross-reference these 214 rows with the `order_id` timestamp logs. If the gap between order placement and delivery confirmation is genuinely zero, the record was created by a system bug (e.g., auto-confirmation before actual delivery). Flag these rows and **impute `delivery_time_mins` using the median delivery time for the same `hub_id` and `order_category`** combination, this preserves hub-specific and category-specific delivery patterns rather than using a global median.

---

## Problem 2 — Outlier: `order_value_Rs = Rs. 2,95,000`

**Classification:** Outlier (likely a data entry error)

**Evidence:** The mean is Rs. 620 and median is Rs. 310. A single bakery order worth Rs. 2,95,000 is ~476× the median, statistically implausible for a 15-minute grocery delivery platform. Even large catering orders would not reach this value on a platform like ZeptoFresh.

**Specific Treatment:**
Investigate the specific `order_id`. If no legitimate bulk/corporate order record exists in the CRM, **cap `order_value_Rs` at the 99th percentile value** (compute from the dataset) for modeling purposes. Do not delete the row entirely, keep it flagged with an `is_outlier` column for audit purposes.

---

## Problem 3 — Data Entry Error: Negative `prep_time_mins`

**Classification:** Data entry error / impossible value

**Evidence:** `prep_time_mins` has a minimum of −6. Preparation time is a physical duration and cannot be negative, it likely results from a system recording the prep-end timestamp before the prep-start timestamp, or a clock synchronisation issue between the kitchen system and the order management system.

**Specific Treatment:**
Set all `prep_time_mins < 0` to `NaN`, then **impute using the median `prep_time_mins` grouped by `order_category`** (e.g., Fresh Food takes longer to prepare than Bakery items). Using category-level medians respects the operational reality that different product types have different prep windows.

---

## Problem 4 — Missing Values + Invalid Values: `customer_rating`

**Classification:** Missing values + structural issue (invalid scale values)

**Evidence:** Two sub-problems exist simultaneously:
- 9,800 null values (~8.9% of 110,000 records), customers who did not submit a rating
- Some values recorded as `0`, which falls outside the declared scale of 1–5

**Specific Treatment:**
- For `rating = 0`: Treat as `NaN` (not a valid rating), these are almost certainly default placeholder values from the database schema. **Replace 0 with `NaN` before any analysis.**
- For actual nulls (9,800 rows): Do **not** impute with mean or median, a missing rating is informative (customers who had a bad experience often skip rating). Instead, **add a binary indicator column `rating_given` (1 = rated, 0 = not rated)** and keep the null as-is. Use models that handle missing values natively (e.g., LightGBM) or impute separately per analysis context.
