# (e) Business Trade-Off: Precision or Recall?

## Answer: It Depends on the Asymmetry, But the Default Should Lean Toward Recall

Kavya's statement defines two competing costs:

> *"If a customer receives delivery in 30+ minutes, the probability of churn increases significantly. However, if we aggressively flag too many orders as 'late risk', operational costs increase due to unnecessary rider reallocations."*

This is a classic **Precision vs. Recall trade-off** where both errors have real business consequences.

---

## Defining the Errors in ZeptoFresh's Context

| Error Type | What It Means | Business Consequence |
| :--- | :--- | :--- |
| **False Negative (FN)** | Model says "on time", order is actually late | Customer waits 30+ mins, churn probability increases, potential refund, negative rating |
| **False Positive (FP)** | Model says "late risk", order arrives on time | Unnecessary rider reallocation triggered, operational cost incurred, no customer benefit |

---

## Which to Prioritise?

**Recall should be the primary metric**, but with a nuanced threshold strategy, not an extreme position.

**Why Recall:**
- The cost of a missed late delivery (FN) is **asymmetric and compounding**: a churned customer represents lost lifetime value (LTV), not just one order. A customer who churns after a 35-minute wait may never return, this is a permanent revenue loss.
- Rider reallocation costs (FP) are **operational and recoverable**, they increase cost per order but do not permanently damage the customer relationship.
- ZeptoFresh's core brand promise is "15-minute delivery." Any delivery beyond 30 minutes is a direct brand failure, not just a service gap.

**Why Not Maximum Recall (Recall = 1.0):**
- If the model flags every order as late-risk, rider reallocation becomes continuous and chaotic, operational costs become unsustainable and actually worsen delivery times for everyone.
- Kavya explicitly states that "aggressively flagging too many orders" increases costs, meaning a Recall-at-all-costs approach is operationally infeasible.

---

## Recommended Strategy

Set a **tiered threshold approach** rather than a single cutoff:

*   `late_risk_probability > 0.70` → **Immediate rider reallocation** (high confidence, act now)
*   `late_risk_probability 0.45–0.70` → **Alert hub manager** to monitor (moderate confidence, watch)
*   `late_risk_probability < 0.45` → **No action** (low risk, normal flow)

This maximises Recall for high-confidence late predictions while containing False Positives (unnecessary reallocations) by reserving automatic intervention for only the most certain cases.

**Primary metric: Recall at threshold 0.45 (catch most late orders)**
**Guardrail metric: Precision at threshold 0.70 (ensure automatic actions are reliable)**
