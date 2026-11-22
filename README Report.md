# A/B Testing and Uplift Modelling: Hillstrom Email Experiment

## Business question

Which customers respond to an email? If we can predict who responds, we can email only the customers with positive uplift instead of emailing everyone.

## Data

Kevin Hillstrom's MineThatData email experiment (2008). 64,000 customers were randomly assigned to one of three arms: No E-Mail (21,306), Men's E-Mail (21,307) or Women's E-Mail (21,387). Outcomes are visit, conversion and spend over a two-week follow-up window.

The data has no customer ID, so the 6,562 duplicate rows are kept. This notebook does not test whether removing them changes the results.

## Method

- **Randomisation check.** Arm sizes are nearly equal (21,306, 21,307 and 21,387). Across the pre-treatment traits, the largest standardised mean difference (SMD) is 0.016 for men's email and 0.014 for women's email, well under the 0.1 threshold.
- **Overall effect.** Welch t-tests with 95% confidence intervals, comparing emailed customers (both arms pooled) with control.
- **Men's vs women's email.** The two emails compared directly. Both arms were randomised, so the comparison is causal.
- **Uplift model.** T-learner using gradient boosting: one model for emailed customers and one for control customers. Uplift is the difference between their predictions. Five-fold stratified cross-fitting gives every customer a score from models that never saw that customer. Models are fitted for conversion and spend; decile calibration is shown for spend only.
- **Targeting.** Spend uplift per customer when emailing the top 10% to 100% of customers, for four rules: the spend model, the conversion model, past spend, and random. Net revenue after email cost is calculated at $0.10 and $0.25 per email and saved to CSV.

## Key results

| Result | Estimate |
| --- | --- |
| Visit lift, email vs control | +6.1 pp, 95% CI 5.5 to 6.6 pp (10.6% in control, 16.7% emailed) |
| Conversion lift, email vs control | +0.50 pp, 95% CI 0.35 to 0.64 pp (0.57% in control, 1.07% emailed) |
| Spend lift, email vs control | +$0.60 per customer, 95% CI $0.38 to $0.82 |
| Men's vs women's email | Visits +3.1 pp (95% CI 2.4 to 3.8); conversion +0.37 pp (95% CI 0.17 to 0.56); spend +$0.35 (95% CI $0.03 to $0.66, p = 0.03). The spend gap is not significant once three outcomes are tested. |
| Spend uplift model, deciles | Not monotone. The two lowest-ranked deciles show $0.97 and $1.00 of uplift, decile 8 shows -$0.09, and decile 10 shows $1.03. Without intervals in this notebook, no ordering can be confirmed. |
| Spend uplift per customer, top 10% | Past spend $1.36, spend model $1.03, conversion model $0.86, random $0.27 (everyone: $0.60) |
| Net revenue at $0.10 per email | Email everyone: $31,795. Best targeted rule: $28,778 (past spend, top 90%) |
| Net revenue at $0.25 per email | Email everyone: $22,195. Best targeted rule: $20,138 (past spend, top 90%) |

## Conclusion

Email has a clear positive effect on visits, conversions and spend, and the men's email performs better on visits and conversions. In this experiment, uplift targeting does not beat emailing everyone at $0.10 or $0.25 per email: the uplift models show weak and non-monotone rankings. Past spend gave the highest point estimate at the top 10% and 20% of customers ($1.36 and $1.14 per customer), so if a campaign is capped at that size it is the simplest rule to try. That gain is suggestive and should be confirmed on new data.

## Limitations

- Spend is measured over a two-week window only. Repeat purchases after that are missed, so the value is understated.
- Gross margin and email cost are not in the data, so the net figures are revenue, not profit. Multiply by your margin to get profit.
- Spend is heavy-tailed, so spend uplift estimates are noisy.
- The targeting results are point estimates in this notebook. It does not calculate confidence intervals for them, so differences between rules are not tested.
- Several models and targeting rules were compared on the same data, so the best-looking result is partly chance.
- The men-vs-women spend difference is borderline and does not survive a correction for three outcomes.
- The data is from 2008 and may not reflect current customers or email practices.

## Files

- **ab-uplift-hillstrom\_simplified.ipynb:** the analysis, from loading the data to saving the outputs. Runs top to bottom.
- **docs/hillstrom-code-explained.md:** the notebook explained cell by cell.
- **Kevin\_Hillstrom\_MineThatData.csv:** the raw data (not included in the repository).
- **Outputs/:** CSV results: m1\_outcomes\_by\_arm.csv, m2\_overall\_test.csv, m3\_mens\_vs\_womens.csv, m4\_cf\_spend\_deciles.csv, m5\_targeting\_curves.csv and customer\_scores\_crossfit.csv.

## 
