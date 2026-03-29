# (c) Correlation Interpretation

## What Is Logically Incorrect About the PM's Conclusion?

The product manager concludes:
> *"Late deliveries cause refunds. So solving delay will eliminate refunds."*

This statement commits the classic logical error of **confusing correlation with causation**.

**What is wrong:**

1. **Correlation ≠ Causation.** A correlation of r = +0.74 between `delivery_time_mins` and `refund_issued` tells us that the two variables move together, when one is high, the other tends to be high. It does not tell us *why* they move together or whether one causes the other.

2. **The causal direction is not proven.** The PM assumes: `late delivery → refund`. But it is equally possible that certain order types (e.g., Fresh Food, Medicines) are both harder to deliver quickly *and* more likely to result in refunds when quality is compromised, making order category a common cause of both.

3. **"Solving delay will eliminate refunds" is an overstatement.** Even if delay does contribute to refunds, refunds can be issued for reasons entirely unrelated to delivery time: wrong items, damaged packaging, missing items, billing errors. Eliminating delay alone will not eliminate these.

---

## What Does Correlation Actually Indicate?

Correlation measures the **strength and direction of a linear relationship** between two variables. An r = +0.74 means:

- There is a **strong positive linear association**, orders with longer delivery times tend to have a higher rate of refunds.
- The relationship explains approximately **r² = 0.55 (55%)** of the variance in one variable given the other.
- It is a **statistical association**, not a mechanism. The data alone cannot confirm that reducing `delivery_time_mins` will directly reduce `refund_issued`.

---

## Two Possible Confounders

**Confounder 1, `order_category`:**
Fresh Food and Medicines are both time-sensitive (higher churn if late) *and* more likely to be refunded if quality is compromised during a longer delivery. `order_category` could be independently driving both longer delivery times *and* higher refund rates, making the correlation between the two appear causal when it is not.

**Confounder 2, `rain_flag`:**
Rainy conditions simultaneously increase `delivery_time_mins` (slower rider speed, traffic) *and* increase `refund_issued` (damaged packaging, delayed fresh food spoilage). Rain independently affects both variables, the correlation between delay and refund is partly a reflection of weather conditions affecting both, not delay causing refunds.
