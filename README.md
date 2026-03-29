# Week-05-Day-27

# Case Study: ZeptoFresh — 15-Minute Food & Essentials Delivery
**PG Diploma · AI-ML & Agentic AI Engineering · IIT Gandhinagar**

---

## Business Context

ZeptoFresh operates **350+ micro-fulfillment hubs** across 25 Indian cities, promising 15-minute delivery of groceries and ready-to-eat meals.

The analytics team is performing **EDA on 110,000 order records from Q2 2025** before building a late delivery risk prediction model.

**VP Operations' constraint:**
> *"If a customer receives delivery in 30+ minutes, the probability of churn increases significantly. However, if we aggressively flag too many orders as 'late risk', operational costs increase due to unnecessary rider reallocations."*
> — Kavya Sharma, VP Operations

---

## Questions & Answers

### (a) Data Quality Diagnosis
Identify four data quality problems from the six analyst observations. Classify each and state a specific treatment. <br>
[Answer](a_data_quality_diagnosis.md)

### (b) Distribution Analysis
Mean (18.4) > Median (14.2) — what does this indicate? ASCII histogram + transformation recommendation. <br>
[Answer](b_distribution_analysis.md)

### (c) Correlation Interpretation
`delivery_time_mins` and `refund_issued` → r = +0.74. Why is the PM's causal conclusion wrong? What are two confounders? <br>
[Answer](c_correlation_interpretation.md)

### (d) Bimodal Pattern in Tier-1 Cities
Why does Mumbai/Bangalore show a bimodal delivery time distribution? Why does it matter for modeling? <br>
[Answer](d_bimodal_pattern.md)

### (e) Business Trade-Off
Should ZeptoFresh prioritize Precision or Recall for late delivery risk prediction? Justify with cost trade-offs. <br>
[Answer](e_business_tradeoff.md)

### (f) Advanced Feature Engineering
Propose three engineered features using existing columns that improve late-delivery prediction. <br>
[Answer](f_feature_engineering.md)

---

## Quick Summary

| Question | Key Answer |
| :--- | :--- |
| **(a) Data Quality** | `delivery_time_mins = 0` (entry error), `order_value_Rs = 2.95L` (outlier), `prep_time_mins < 0` (impossible value), `customer_rating` nulls + zeros (missing + invalid) |
| **(b) Distribution** | Right-skewed; apply `log(delivery_time_mins + 1)` before modeling |
| **(c) Correlation** | Correlation ≠ causation; `order_category` and `rain_flag` are confounders |
| **(d) Bimodal** | Two rider availability states / zone types mixed; model will underpredict the slow cluster |
| **(e) Trade-off** | Lean toward **Recall** with tiered thresholds; don't flag everything |
| **(f) Features** | `total_fulfillment_time`, `order_complexity_score`, `demand_pressure_index` |
