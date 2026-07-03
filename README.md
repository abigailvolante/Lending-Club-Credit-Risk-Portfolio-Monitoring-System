# Lending Club Credit Risk Portfolio Monitoring System
This project develops a credit risk analysis of the Lending Club loan dataset from Kaggle. The objective is to replicate a banking portfolio monitoring system that evaluates portfolio exposure, credit performance, delinquency risk, and loss distribution.

Status: In progress. Adding Section 5 and 6.

## Objectives

- Measure overall portfolio exposure and composition.
- Evaluate historical credit performance and default risk.
- Identify key drivers of loan losses.
- Monitor delinquency and early warning indicators.
- Segment borrowers by risk characteristics.
- Translate findings into actionable credit policy recommendations.
- Build a Power BI dashboard for ongoing monitoring.

## Tools
- Google BigQuery (data storage and SQL processing)
- SQL (data transformation and analytical queries)
- Power BI (interactive dashboarding and reporting)

 ## Project Structure

This project is organised into four sections:

- **Section 1: Portfolio Exposure**  
  We assess the total portfolio size and analyse capital distribution across borrower segments and loan statuses.

- **Section 2: Credit Risk Performance**  
  We evaluate borrower repayment behaviour and assess the effectiveness of the credit grading system in differentiating risk levels.

- **Section 3: Loss Distribution**  
  We identify where actual financial losses are concentrated across credit grades and loan purposes.

- **Section 4: Delinquency & Exposure**
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
