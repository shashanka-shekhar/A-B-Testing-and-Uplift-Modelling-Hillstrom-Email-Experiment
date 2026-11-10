# A/B Testing and Uplift Modelling: Hillstrom Email Experiment

## Business question

Which customers respond to an email? If we can predict who responds, we can email only the customers with positive uplift instead of emailing everyone.

## Data

Kevin Hillstrom's MineThatData email experiment (2008). 64,000 customers were randomly assigned to one of three arms: No E-Mail (21,306), Men's E-Mail (21,307) or Women's E-Mail (21,387). Outcomes are visit, conversion and spend over a two-week follow-up window.

The data has no customer ID, so the 6,562 duplicate rows are kept. Removing them changes no conclusion (visits +6.2 pp, conversions +0.55 pp, spend +$0.66 without them).

## Method

1. **Randomisation check.** Chi-square test on arm sizes (p = 0.90). ANOVA and chi-square tests on eight pre-treatment covariates (none significant at p \< 0.05). Standardised mean differences (largest \|SMD\| = 0.016, against a threshold of 0.1).
2. **Overall effect.** Welch t-tests with 95% confidence intervals, comparing emailed customers (both arms pooled) with control.
3. **Power.** With about 21,000 customers per arm, the test can detect a conversion lift of roughly 0.25 percentage points at 80% power.
4. **Arm comparison.** Each email compared with control, and the men's email compared directly with the women's email.
5. **Uplift model.** T-learner using gradient boosting: one model for emailed customers and one for control customers. Uplift is the difference between their predictions. Five-fold cross-fitting gives every customer a score from a model that never saw them. Calibrated by decile.
6. **Targeting.** Cumulative uplift curves, with 95% intervals, for four rules: the spend uplift model, the conversion uplift model, past spend, and random. Net revenue after email cost, at $0.05 to $0.50 per email.

## Key results

| Result | Estimate |
| --- | --- |
| Visit lift, email vs control | +6.1 pp (+57%) |
| Conversion lift, email vs control | +0.50 pp (+87%), 95% CI 0.35 to 0.64 pp |
| Spend lift, email vs control | +$0.60 per customer (+91%), 95% CI $0.38 to $0.82 |
| Men's vs women's email | Men's wins on visits (+3.1 pp) and conversion (+0.37 pp). Spend is +$0.35 (p ≈ 0.03), borderline once three outcomes are tested |
| Conversion uplift model, top decile | 0.82 pp, 95% CI 0.30 to 1.34 pp. The ranking is not monotone |
| Spend uplift model, deciles | No clear ordering. The two lowest-ranked deciles show about $1.00 of uplift, as high as the top deciles |
| Past spend, top 10% of customers | $1.36 spend uplift per customer and 1.24 pp conversion uplift, against $0.60 and 0.50 pp for everyone |
| Net revenue at $0.25 per email | Email everyone: $22,195. Best targeted rule: $20,138 (past spend, top 90%) |

## Conclusion

Email has a clear positive effect on visits, conversions and spend, and the men's email performs better. In this experiment, uplift targeting does not beat emailing everyone at realistic costs: the uplift models show only weak heterogeneity, and their rankings are not reliable. Past spend gave the highest point estimate at the top 10% to 20% of customers, so if a campaign is capped at that size it is the simplest rule to try, but that gain is suggestive and should be confirmed on new data.

## Limitations

- Spend is measured over a two-week window only. Repeat purchases after that are missed, so the value is understated.
- Gross margin and email cost are not in the data, so the net figures are revenue, not profit. Multiply by your margin to get profit.
- Spend is heavy-tailed, so spend uplift estimates are noisy.
- Several models and targeting rules were compared on the same data, so the best-looking result is partly chance. The targeting curves are not monotone, and small differences between rules should not be over-read.
- The men-vs-women spend difference is borderline after allowing for three outcomes being tested.
- The data is from 2008 and may not reflect current customers or email practices.

## How to run

1. Install Python 3.10 or later, with pandas, numpy, scipy and scikit-learn. The analysis was run with pandas 3.0 and scikit-learn 1.9.
2. Download the Hillstrom CSV (`Kevin_Hillstrom_MineThatData.csv`) from the MineThatData challenge page, and place it in the project root. It is not included in this repository.
3. Open `ab-uplift-hillstrom_final.ipynb` and run all cells top to bottom, or run `python hillstrom_ab_uplift.py`.
4. Results are written to `Outputs/`. Edit the `RAW` and `OUT` paths at the top of the notebook or script if your folders differ.

## Files

- `ab-uplift-hillstrom_final.ipynb`: the full analysis, from loading the data to saving the outputs. Runs top to bottom.
- `hillstrom_ab_uplift.py`: the same code as a script.
- `docs/hillstrom-code-explained.md`: the code with a line-by-line explanation.
- `Kevin_Hillstrom_MineThatData.csv`: the raw data (not included in the repository).
- `Outputs/`: CSV results for each step, including the cross-fitted customer scores.
