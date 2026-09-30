## 8. Final analytical summary

*Every number below is taken from the executed output cells in §1–§7. "Observed" means the analysis found it; "possible implication" means it is a hypothesis to be tested, not a finding.*

### 8.1 Data and preprocessing

The dataset has 29,999 people in 21,449 households and 33 variables. The dictionary defines no missing-value codes or units, so every candidate code was judged from the column's meaning:
- `0` is a valid "No" in all boolean flags.
- `0` in `disabilities_name` means "None".
- `0` in the parent-name columns is a placeholder and was set to missing (father 17,909, mother 17,916).
- `99` in SpO₂, pulse and BP is a real reading.

**Coding problems fixed:**
- A header-less row-index column was dropped.
- `diabetic` and `profile_hypertensive` were stored as TRUE/FALSE while the other flags used 0/1; all flags are now 0/1.
- 1,489 `union_name` values had stray spaces.
- Status labels had inconsistent case.

**Removals and flags:**
- There were no duplicate rows or duplicate IDs.
- 176 impossible values were set to missing. No rows were deleted. Examples: 136 ages over 110, SpO₂ values of 5–54 %, pulses of 1–25, 6 BMIs below 10, glucose values of 0.11 and 0.75.
- IQR outliers were flagged and kept. They range from 0.3 % of age values to 15.4 % of SpO₂ values, and all lie inside physiological limits.

Three data-quality problems shape the analysis:
1. **The status labels contradict their own measurements.** For example, BP "High" covers systolic 78–211 mmHg, and glucose "Low" covers 4.46–4.87 mmol/L. All tests therefore use the measured values.
2. **Coverage is very uneven.** BP and pulse are recorded for about 92 % of people, SpO₂ for 14 %, glucose for 5 %, BMI for 4 % and MUAC for 0.25 %. MUAC exists only for children aged 0–4.
3. **`is_poor` is constant** (always 0), so it carries no information.

### 8.2 Descriptive results

- Adults (≥ 18) are 27,137 people.
- The sample is 77.5 % female, and 66.6 % are "Lower class".
- Median age is 37 (IQR 26–50).
- Median systolic/diastolic BP is 120/75 mmHg.
- Median pulse is 83 bpm, and median SpO₂ is 99 %.
- Recorded conditions: hypertension 5.25 %, diabetes 2.00 %, cardiovascular disease 0.11 %, stroke 0.08 %.

### 8.3 Target variable

**Observed target: `profile_hypertensive`.** It was chosen by the rule stated in §3: a binary health flag, non-constant, and the most events with EPV ≥ 10.
- Among adults it has 1,575 events (5.80 %).
- None of the condition flags has a single case under age 18, so the analysis uses adults only.
- `diabetic` also qualified (601 events) and is used as a predictor.
- Stroke (23), CVD (34) and freedom-fighter status (6) are too rare to model.

### 8.4 Associations with the target (15 tests, BH-FDR)

11 of the 15 tests are significant after BH, and 10 are also significant after Bonferroni (income class is not: p_Bonf = 0.10). Ranked by effect size:

| Variable | Test | N | Result | Effect size | Magnitude |
|---|---|---|---|---|---|
| Age | Welch t | 27,137 | t = 38.5, df = 1,819, q < 10⁻²³⁰ | d = 0.92 (median 52 vs 38 y) | large |
| Systolic BP | Welch t | 25,996 | t = 31.9, df = 1,636, q < 10⁻¹⁷⁰ | d = 0.91 (137 vs 120 mmHg) | large |
| Diastolic BP | Welch t | 25,996 | t = 21.6, df = 1,666, q < 10⁻⁹⁰ | d = 0.57 (82 vs 75 mmHg) | medium |
| Diabetic | χ² | 27,137 | χ² = 3,071, df = 1, p ≈ 0 | V = 0.34 (58.1 % vs 4.6 % hypertensive) | medium |
| Union | χ² | 27,137 | χ² = 1,685, df = 15, p ≈ 0 | V = 0.25 (0.0 % to 25.0 %) | small |
| Random glucose | Mann–Whitney | 914 | U = 60,230, q = 0.0001 | r_rb = 0.22 (10.7 vs 8.5 mmol/L) | small |
| Pulse | Welch t | 25,854 | t = −7.1, df = 1,602, q = 3×10⁻¹² | d = −0.21 (80 vs 83 bpm) | small |
| Stroke / CVD | Fisher exact | 27,137 | q < 10⁻¹⁷ | V = 0.09 / 0.09 | negligible* |
| Disability, income class | Fisher / χ² | 27,137 | q = 2×10⁻⁵ / 0.009 | V = 0.03 / 0.02 | negligible |

\*Among the 23 people with a stroke, 78.3 % are hypertensive; among the 34 with CVD, 64.7 % are. V is small only because these conditions are so rare.

Not significant: BMI (q = 0.24, n = 909, r_rb = 0.16), SpO₂ (q = 0.26), gender (p = 0.63, V = 0.003) and freedom-fighter status.

**The target is not recorded consistently (§4.1).** Recorded prevalence does not follow measured BP across unions: Spearman ρ = −0.44, p = 0.09, over 16 unions. In five unions (8,777 adults), fewer than 1 % of adults are flagged even though 10–26 % have a systolic ≥ 140 mmHg. SHASHORA, for example, has 0 hypertensives among 5,160 adults, yet 21.9 % have a high reading. So the union association most likely reflects **recording practice**, not geography.

### 8.5 Correlations between numeric variables (adults, BH over 33 pairs)

- **Systolic ~ diastolic:** Pearson r = 0.69 [0.69, 0.70], n = 25,996. This is the strongest correlation.
- **Height ~ weight:** r = 0.40.
- **Age ~ systolic:** r = 0.33 [0.32, 0.34]. This is moderate.
- **Weak correlations (|ρ| 0.10–0.21):** age ~ random glucose, systolic ~ weight, and systolic ~ glucose.
- **Negligible:** 16 pairs have |coef| < 0.1. Two of them are still significant because n is large.
- BMI ~ height/weight is mathematically derived, so it is not interpreted.

### 8.6 Logistic regression (cluster-robust SEs by household)

**Assumption checks:**
- EPV is 120–175.
- All VIFs are ≤ 2.3.
- Box–Tidwell rejected a linear logit for age (p = 3×10⁻¹⁸), so age was entered in bands.
- BP and pulse also showed non-linearity, so their ORs are average effects per 10 units.

**Adjusted odds ratios** (OR [95 % CI]):

| Term | Model A (n = 27,137) | Model B (n = 25,805) | B-sens (n = 17,166) |
|---|---|---|---|
| Age 30–39 / 40–49 / 50–59 / 60+ (vs 18–29) | 6.3 / 14.4 / 24.6 / 37.1 | 5.1 / 9.8 / 15.1 / 20.0 | 4.9 / 9.8 / 15.6 / 22.7 |
| Diabetic | 21.3 [17.6, 25.8] | 23.4 [18.8, 29.1] | 13.8 [11.1, 17.3] |
| Male (vs female) | 0.68 [0.59, 0.77] | 0.60 [0.52, 0.69] | 0.58 [0.50, 0.68] |
| Middle class (vs Lower) | 0.78 [0.67, 0.90] | 0.71 [0.61, 0.83] | 1.12 [0.95, 1.33], n.s. |
| Systolic, per 10 mmHg | – | 1.25 [1.21, 1.30] | 1.29 [1.24, 1.33] |
| Diastolic, per 10 mmHg | – | 1.11 [1.04, 1.18] | 1.19 [1.11, 1.27] |
| Pulse, per 10 bpm | – | 0.90 [0.85, 0.95] | 0.90 [0.85, 0.95] |

All age-band ORs have p < 10⁻¹³. Upper class was not significant in any model.

**Model performance** (held-out households):

| Model | Test AUC [95 % CI] | CV AUC | PR-AUC (prevalence) | Brier (no-skill) | Sensitivity / specificity |
|---|---|---|---|---|---|
| A | 0.80 [0.78, 0.82] | 0.80 | 0.27 (0.058) | 0.046 (0.055) | 0.83 / 0.63 |
| B | 0.84 [0.81, 0.86] | 0.83 | 0.34 (0.057) | 0.044 (0.053) | 0.78 / 0.75 |
| B-sens | 0.87 [0.85, 0.89] | 0.84 | 0.43 (0.084) | 0.060 (0.077) | 0.85 / 0.74 |

PPV was only 0.12–0.23 at the Youden threshold.

**Feature importance** is measured as the drop in test AUC when a feature is permuted:

| Feature | Model A | Model B |
|---|---|---|
| Age | 0.20 | 0.14 |
| Systolic | – | 0.06 |
| Diabetic | 0.05 | 0.03 |
| Gender, income class, diastolic, pulse | ≤ 0.008 each | ≤ 0.008 each |

### 8.7 Significant versus practically important

The findings fall into three groups:
- **Statistically significant and practically important:**
  - age: large d, the largest importance, and ORs of 5–37;
  - measured systolic BP: large d, the second-largest importance in Model B;
  - diabetes: medium V, and ORs of 14–23.
- **Significant but negligible in size:**
  - stroke, CVD, disability and income class all have V < 0.1;
  - income adds < 0.005 AUC, and its effect disappears in the sensitivity model.
- **Significant only after adjustment:**
  - gender is unrelated to the target on its own (V = 0.003);
  - after adjusting for age and other factors, men have about 40 % lower odds;
  - it adds almost nothing to prediction (AUC drop ≤ 0.007).

The model discriminates well (AUC 0.80–0.87). However, with PPV ≤ 0.23 it is **not** a usable screening tool, since most people it flags would be false positives.

### 8.8 Possible analytical implications (hypotheses, not findings)

- Recorded hypertension appears to depend partly on whether a union records diagnoses at all. The flag may measure **diagnosis/recording** rather than disease. Comparing the flag with measured BP ≥ 140/90 could estimate how much hypertension goes unrecorded.
- Hypertension and diabetes are strongly co-recorded. Their association weakens (OR 23 to 14) once apparently non-recording unions are excluded. Part of the co-occurrence may be shared recording practice rather than comorbidity.
- The income effect vanishes in the sensitivity model. The apparent socio-economic gradient is probably confounded by union.
- Recorded hypertensives have *lower* pulse (d = −0.21, OR 0.90 per 10 bpm). One possible explanation is treatment such as beta-blockers, but the data contain no medication records to test it.

### 8.9 Limitations

- **Observational, cross-sectional data.** No association here is causal.
- **Unreliable target.** It is under-recorded in at least 5 of 16 unions. The "no" group mixes people who are truly unaffected with people who were not recorded.
- **Sparse measurements.** BMI, glucose and SpO₂ are available for only 3–12 % of adults, and those people were not randomly selected, so their tests have low power and may be biased.
- **Unexplained variables.** The dictionary gives no units and no missing-value codes, and the status labels are internally inconsistent.
- **Clustering.** It is handled only in the regression (by household) and in the train/test split, not in the χ² tests. With about 27,000 people, trivial effects become significant, so effect sizes matter more than p-values here.
- **Model fit.** The linear-logit assumption is violated for BP and pulse, and the model was validated only internally on one dataset.
