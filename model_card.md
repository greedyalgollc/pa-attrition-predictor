# Model Card — Employee Attrition Predictor

## Model Details

| Field | Value |
|-------|-------|
| **Name** | Employee Attrition Predictor v1.0 |
| **Type** | Binary classification (attrited = 1, retained = 0) |
| **Algorithm** | Random Forest (sklearn RandomForestClassifier) |
| **Author** | GreedyAlgoLLC · GreedyAlgo Analytics |
| **Date** | 2026 |

### Hyperparameters
- `n_estimators`: 300
- `max_depth`: 8
- `min_samples_leaf`: 5
- `class_weight`: balanced (handles ~10% attrition base rate)
- Decision threshold: **0.40** (tuned to favor recall over precision for HR use case)

---

## Intended Use

**Primary use:** Identify employees at elevated risk of voluntary resignation in the next 90 days to enable proactive retention conversations.

**Target users:** HR Business Partners, People Analytics teams, CHROs.

**Out-of-scope uses:** This model should NOT be used as the sole basis for employment decisions, performance reviews, or compensation changes. It is a flag for human-led conversations, not an automated decision system.

---

## Training Data

- **Source:** Fully synthetic dataset generated with NumPy/Pandas
- **Size:** 1,176 training employees (80% stratified split of 1,470 total)
- **Target class balance:** ~10% attrition rate (class_weight='balanced' applied)
- **Features:** 16 numeric and encoded categorical features (see README for schema)

All data is fabricated. No real employee records were used.

---

## Performance Metrics

| Metric | Value |
|--------|-------|
| ROC-AUC (test set) | 0.777 |
| ROC-AUC (5-fold CV mean) | 0.777 |
| ROC-AUC (5-fold CV std) | ±0.057 |
| Precision (Attrited, threshold=0.40) | 0.26 |
| Recall (Attrited, threshold=0.40) | 0.41 |
| F1 (Attrited) | 0.32 |

**Threshold rationale:** A 0.40 threshold is used rather than 0.50. In a retention context, the cost of a false negative (missing a flight-risk employee who then resigns) substantially exceeds the cost of a false positive (flagging a retained employee for an unnecessary check-in). Operating at 0.40 improves recall from ~28% to ~41% with an acceptable precision trade-off.

---

## Top Predictors

1. Compa Ratio (compensation vs. market) — 22.5%
2. Engagement Score — 10.4%
3. Overtime Hours per Week — 9.9%
4. Job Satisfaction Score — 9.8%
5. Tenure Years — 8.5%

*Importance = mean decrease in impurity across all trees, normalized to 100%.*

---

## Limitations

- **Causal inference is not valid from this model.** Feature importance identifies statistical correlates of attrition, not levers that will reduce it if changed.
- **Synthetic data patterns may not reflect your organization.** Retrain on proprietary data before using for real decisions.
- **Model may encode demographic proxies.** Gender and department are included as features. Before deployment, conduct a disparate impact analysis to verify the model does not produce discriminatory outputs.
- **Static snapshot.** The model is trained on a point-in-time dataset. Workforce dynamics change; re-evaluate quarterly.

---

## Ethical Considerations

- Attrition predictions should only be shared with a manager or HRBP on a need-to-know basis — not the employee themselves.
- Model outputs should inform conversations, not replace them.
- Any interventions triggered by model outputs (e.g., compensation adjustments, promotion decisions) should follow established HR governance processes.

---

*GreedyAlgo Analytics · Hari Vemula*
