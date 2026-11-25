# Lending Club Credit Risk Portfolio Monitoring System
This project develops a credit risk analysis of the Lending Club loan dataset from Kaggle. The objective is to replicate a banking portfolio monitoring system that evaluates portfolio exposure, credit performance, delinquency risk, and loss distribution.

This project was built as a learning exercise to deepen my SQL skills in a BigQuery environment and familiarise myself with core credit risk concepts such as exposure, delinquency, charge-offs, exposure at default that are central to portfolio monitoring in banking and lending.

Status: In progress. Currently adding query results for Sections 1–4, then starting Sections 5–6, which will connect Power BI directly to BigQuery. I'm still learning Power BI (via Microsoft's official training) as part of 
this project.

## Objectives

- Measure overall portfolio exposure and composition.
- Evaluate historical credit performance and default risk.
- Identify key drivers of loan losses.
- Monitor delinquency and early warning indicators.
- Segment borrowers by risk characteristics.
- Translate findings into actionable credit policy recommendations.


## Key Definitions

These are the measures calculated in this project and how each is defined in the SQL.

| Term | Meaning in this project |
|---|---|
| **Funded exposure** | The total amount of money lent, calculated as `SUM(loan_amnt)`. Used as a measure of portfolio size. |
| **Exposure concentration** | A segment's funded exposure as a percentage of total portfolio exposure (e.g. "Grade A = x% of exposure"). Shows where the portfolio is concentrated. |
| **Credit grade** | Lending Club's risk rating from A (lowest risk) to G (highest risk), assigned when the loan is issued. |
| **Loan status** | The current state of a loan, e.g. Current, Fully Paid, Late, Charged Off, Default. |
| **Default** | In this project, a loan with status `Charged Off` or `Default`. |
| **Default rate** | Defaulted loans divided by total loans, by **count** rather than by amount. Calculated overall and by grade, purpose and home ownership. |
| **Charged off** | A loan the lender has written off as unlikely to be repaid. |
| **Charged-off exposure** | The original `loan_amnt` of charged-off loans. This is the amount lent, **not** the final loss, because recoveries are not subtracted. |
| **Delinquent** | A loan that is late on payments. Here: status `Late (16-30 days)` or `Late (31-120 days)`. Used as an early warning of possible future defaults. |
| **Exposure at default (EAD)** | An estimate of the amount still owed on delinquent loans if they default. Approximated as `loan_amnt - total_pymnt`, floored at zero. |

### How to read the results
- **Concentration is not the same as risk.** A grade can hold the most losses simply because it holds the most loans. Comparing rates (losses or defaults divided by exposure or loan count) is a better test of riskiness than comparing totals.
- **Default rate counts loans, not money.** A rate of 0.019% means 19 defaulted loans out of 100,000.

### Assumptions and limitations
- **Sampled data.** Only 100,000 rows of the full dataset were used because of BigQuery storage and processing limits. The sample's loan-status mix may not match the full dataset.
- **Very few defaults.** With only 19 defaults, segment-level default rates rest on small counts and should be read as indicative, not as reliable estimates.
- **EAD is an approximation.** `total_pymnt` includes interest and fees as well as principal repaid, so `loan_amnt - total_pymnt` is not exactly the outstanding principal. Using a principal-only column would be more accurate.
- **No recoveries.** Charged-off exposure ignores any money recovered after charge-off.
- **Descriptive only.** This project describes the portfolio. It does not build a predictive model.

## Tools
- Google BigQuery (data storage and SQL processing)
- SQL (data transformation and analytical queries)

  
## Project Structure
 
This project is organised into four sections:
 
**Section 1: Portfolio Exposure**
We assess the total portfolio size and analyse capital distribution across borrower segments and loan statuses.
 
**Section 2: Credit Risk Performance**
We evaluate borrower repayment behaviour and assess the effectiveness of the credit grading system in differentiating risk levels.
 
**Section 3: Loss Distribution**
We identify where actual financial losses are concentrated across credit grades and loan purposes.
 
**Section 4: Delinquency & Exposure**
We analyse early-stage delinquency and estimate potential future exposure at default.
 
## Dataset

This project uses the Lending Club loan dataset from Kaggle.

Due to Google BigQuery storage and processing constraints, a sampled subset of the original dataset was used for analysis. A total of 100,000 rows were selected instead of the full dataset.

Source: https://www.kaggle.com/datasets/adarshsng/lending-club-loan-data-csv

## Findings

### Section 1: Portfolio Overview
- The sample of 100,000 loan accounts represents approximately £1.59 billion in funded exposure.
- Approximately 83% of funded exposure is concentrated in Grades A–C.
- Current loans account for ~97.1% of total funded exposure.
- Delinquent and charged-off loans represent less than 1% of portfolio exposure.


### Section 2: Credit Risk Performance

- The observed portfolio default rate is 0.019% (19 defaulted loans out of 100,000). (The extremely low observed default rate is likely influenced by sampling bias and under-representation of defaulted loans in the dataset. Result should be interpreted as an insight rather than a true estimate.)
- Credit grade is the strongest predictor of default risk, with Grade G showing the highest default rate. The low observed default rate by other higher risk grades is also likely influence by sampling bias. 
- Higher credit grades (A–C)  exhibit very low observed default rates.
- Loan purpose shows limited variation in default risk hence is not a strong predictive variable
- Home ownership has some relationship with default risk, with homeownes showing slightly higher observed default rates than renters.


### Section 3: Credit Risk Performance
- The total charged off exposure amounted to approximately £300,000.
- Grades A to C account for the highest number of charged-off exposure. This could be due to the high concentration of loans allocated to borrowers of higher grades. 
- Debt consolidation, home improvement, and credit card loans account for the largest share of charged-off exposure. Higher losses in these categories are likely driven by their larger share of overall lending activity.

### Section 4: Delinquency and Exposure at Default
- A total of 441 loans were identified as delinquent within the portfolio.
- Total Exposure at Default (EAD) across delinquent accounts was approximately £6.4 million
- EAD was concentrated within Grades B-D, indicating that these segments contribute the largest share of potential future losses.
- Debt consolidation accounted for the highest EAD among loan purposes.
- 36-month loans exhibited higher EAD than 60-month loans, suggesting that shorter-term loans may contribute more to future credit risk.

## Technical Highlight: Calculating Exposure Concentration with `CROSS JOIN`
 
**The problem:** To show each grade's share of total portfolio exposure (e.g., "Grade A = 32% of funded exposure"), you need two different aggregation levels at once — the grand total across the entire portfolio, and the total per group. A single `GROUP BY` can't produce both, since grouping collapses the grand total away.
 
**The approach:** Calculate each aggregation independently as its own CTE — `portfolio_exposure` (one row, the grand total) and `grade_exposure` (one row per grade) — then `CROSS JOIN` them together. Because `portfolio_exposure` only ever returns a single row, the cross join doesn't multiply row counts the way it normally would with two multi-row tables; it simply attaches that one grand-total value onto every row of `grade_exposure`, making the percentage calculation a simple division in the final `SELECT`.
 
**Why this matters:** It's a deliberate alternative to two common mistakes — a correlated subquery per row (which re-scans the whole table for every group and gets slower as the table grows) or a window function like `SUM(loan_amnt) OVER ()` (which works too, but is less explicit about the fact that you're combining two genuinely different aggregation grains). The CTE + single-row cross join pattern keeps each piece of logic isolated and easy to audit — you can check `portfolio_exposure` and `grade_exposure` independently before trusting the final combined output. The same pattern is reused for exposure by loan status.
 
## Key SQL Techniques Used
 
- **CTEs (Common Table Expressions):** Isolated grand-total and per-group aggregations into separate, auditable steps before combining them.
- **`CROSS JOIN` (single-row pattern):** Attached a portfolio-wide total onto every group row to calculate exposure concentration as a percentage.
- **Aggregate functions (`SUM`, `COUNT`):** Measured portfolio size, funded exposure, and exposure at default across borrower segments.
  
## Future Improvements
 
- Refactor the repeated grand-total + `CROSS JOIN` pattern (used for both grade and loan-status exposure) into a single reusable window-function version or parameterised view, to avoid duplicating the same CTE structure across sections.
- Investigate the root cause of the low observed default rate more directly, e.g. by comparing the sample's loan-status distribution against the full Lending Club dataset.
- Complete Sections 5 and 6.
