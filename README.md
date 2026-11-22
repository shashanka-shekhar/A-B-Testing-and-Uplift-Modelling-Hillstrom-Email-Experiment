# Hillstrom Email Experiment: A/B Testing and Uplift Modelling

Does emailing a customer change what they do, and can we use that to choose who to email? This project analyses Kevin Hillstrom's MineThatData email experiment: 64,000 customers randomly assigned to a men's email, a women's email, or no email.

**Short answer:** emails raise visits, conversions and spend. In this data, uplift models do not identify a reliable subset to target, and emailing everyone gives the highest net revenue at $0.10 and $0.25 per email.

## Key results

| Result | Estimate |
|---|---|
| Conversion lift, email vs control | +0.50 pp (0.57% to 1.07%), 95% CI 0.35 to 0.64 pp |
| Spend lift, email vs control | +$0.60 per customer, 95% CI $0.38 to $0.82 |
| Men's vs women's email | Men's wins on visits (+3.1 pp) and conversion (+0.37 pp) |
| Net revenue at $0.25 per email | Email everyone $22,195; best targeted rule $20,138 |

Past spend gave the highest point estimate at the top 10% of customers ($1.36 spend uplift per customer, against $0.60 for everyone), but this is not tested with confidence intervals here, so treat it as a lead to confirm on new data.

## What the analysis does

1. **Randomisation check.** Arm sizes are nearly equal, and the largest standardised mean difference across pre-treatment traits is 0.016.
2. **Overall effect.** Welch t-tests with 95% confidence intervals, emailed vs control.
3. **Men's vs women's email.** Direct comparison of the two emails.
4. **Uplift models.** T-learner with gradient boosting, five-fold stratified cross-fitting, so every customer's score comes from a model that never saw them.
5. **Targeting.** Spend uplift per customer when emailing the top 10% to 100% of customers under four rules (spend model, conversion model, past spend, random), with net revenue after email cost at $0.10 and $0.25.

## Repository layout

```
.
├── ab-uplift-hillstrom_simplified.ipynb   # the analysis, runs top to bottom
├── docs/
│   └── hillstrom-code-explained.md         # cell-by-cell explanation
├── Outputs/                                # CSV results (written by the notebook)
└── README.md
```

The raw data file is not included. See below.

## Getting started

**Requirements:** Python 3 with `numpy`, `pandas`, `scipy` and `scikit-learn`.

**Data:** place `Kevin_Hillstrom_MineThatData.csv` in the project folder. The file has 64,000 rows and 12 columns: `recency`, `history_segment`, `history`, `mens`, `womens`, `zip_code`, `newbie`, `channel`, `segment`, `visit`, `conversion`, `spend`.

**Paths:** the first cell of the notebook sets `RAW` (the CSV) and `OUT` (the output folder). The paths in the notebook are for a Windows machine, so edit them to match yours. Create the `Outputs` folder before running, because this version does not create it.

**Run:** open the notebook and run all cells from top to bottom.

## Outputs

| File | Contents |
|---|---|
| `m1_outcomes_by_arm.csv` | Customers, visit rate, conversion rate and average spend by arm |
| `m2_overall_test.csv` | Emailed vs control, with confidence intervals and p-values |
| `m3_mens_vs_womens.csv` | Men's vs women's email, with confidence intervals and p-values |
| `m4_cf_spend_deciles.csv` | Actual spend uplift by decile of predicted spend uplift |
| `m5_targeting_curves.csv` | Spend uplift per customer and net revenue, by rule and fraction emailed |
| `customer_scores_crossfit.csv` | Each customer's cross-fitted uplift scores and outcomes |

## Limitations

- Spend is measured over two weeks only, so repeat purchases are missed.
- Gross margin and email cost are not in the data, so net figures are revenue, not profit.
- The data is from 2008.
- Targeting results are point estimates in this version, and several rules were compared on the same data.
- P-values are rounded to five decimals, so very small values display as `0.0`.

## Data source

Kevin Hillstrom's MineThatData email experiment (2008). The CSV is not redistributed in this repository.

## Licence

Not yet specified.
