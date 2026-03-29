# (b) Distribution Analysis

## What Does Mean > Median Indicate?

**Mean (18.4) > Median (14.2)** indicates a **right-skewed (positively skewed) distribution**.

The median represents the true "middle" delivery time and is resistant to extreme values. The mean is pulled upward by a long right tail, a small number of very late deliveries (e.g., `delivery_time_mins` of 60, 90, or 142) inflate the average well above what most customers actually experience. This is confirmed by:
- 99th percentile = 38 mins (most deliveries are fast)
- max = 142 mins (a few extreme outliers drag the mean up)
- The bulk of deliveries cluster near 12–14 minutes (the median zone)

---

## Rough ASCII Histogram

```
Frequency
  |
  |  ████
  |  ████
  | █████
  | ██████
  | ███████
  | █████████
  | ████████████
  | ██████████████
  | █████████████████
  | ████████████████████▌
  |_______________________________▄▄▄▄▄_________▄__
  0    5   10   14  18   25   35   50   75  100  142
                 ↑         ↑
              Median      Mean

Peak: 10–16 mins    Long tail extends to 142 mins
```

The histogram shows a sharp peak near 12–14 minutes (the modal delivery time) with a long, gradually declining right tail. This is the classic shape of a right-skewed distribution.

---

## Which Transformation to Apply Before Modeling, and Why?

**Apply log transformation: `log_delivery_time = log(delivery_time_mins + 1)`**

**Why:**
- Most ML algorithms (Linear Regression, Logistic Regression, distance-based models) assume or perform better with normally distributed features.
- The right skew means a small number of extreme values (60–142 mins) will disproportionately influence model coefficients and distance calculations.
- Log transformation compresses the long right tail, it brings extreme values closer to the bulk of the data without losing their relative ordering.
- The `+1` offset handles the 214 rows with `delivery_time_mins = 0` (after imputation, these should be resolved, but the offset is a safety measure).
- After log transformation, the distribution becomes approximately symmetric, making it suitable for regression modeling and feature scaling.

> Note: Tree-based models (Random Forest, LightGBM, XGBoost) are scale-invariant and do not strictly require this transformation, but applying it still improves split quality in gradient boosting by making feature distributions more balanced.
