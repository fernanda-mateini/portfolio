# 📁 YTD Outstanding Invoices Dashboard

🚀 Goal: The purpose of this dashboard is to provide leadership and Accounts Receivable teams with a clear and consolidated view of outstanding invoices, overdue amounts, and customer payment behavior throughout the year.

The dashboard was designed to answer key business questions such as:

- How much revenue remains unpaid?
- What is the total overdue amount?
- How many invoices are currently outstanding?
- Which customer segments contribute the most to open receivables?
- How are outstanding invoices distributed across aging buckets?
- Which customers represent the highest overdue exposure?

By centralizing these KPIs into a single report, stakeholders can quickly identify risks, monitor collection performance, and prioritize recovery efforts.

## 📊 The Dashboards

The dashboard provides an executive-level overview of Year-to-Date (YTD) outstanding invoices and unpaid overdue balances.


KPI Cards: At the top of the report, users can find the main business indicators:
* YTD Revenue
* YTD Unpaid Overdue Amount
* Number of Outstanding Invoices
* Month-over-Month (MoM) performance comparison
* Overdue Ratio (ODR)

Outstanding Invoices by Segment: A donut chart displays the distribution of outstanding invoices across business segments, allowing users to identify which customer groups contribute most to open receivables.

Monthly Outstanding Invoice Evolution: A stacked column chart shows how outstanding invoices evolve over time, helping identify seasonal patterns or collection performance trends.

Aging Bucket Analysis: Two visualizations provide a breakdown of:
- Number of outstanding invoices by aging bucket
- Total overdue amount by aging bucket
This helps prioritize collection efforts based on invoice age.

Top 5 Customers: A ranking of customers with the highest unpaid overdue balances highlights accounts that require immediate attention.

Dynamic Insights Section: A dedicated insights area summarizes key findings automatically using DAX measures, making it easier for leadership to consume actionable information without manually interpreting charts.

Interactive Filters: At the top of the dashboard, the slicers dynamically update every visualization, KPI, and insight, allowing users to analyze specific business areas and drill into the data that matters most. Users can interactively filter all visuals using:
- Month Year
- Region
- Segment

## 🧹 The Data Cleaning and Transformation (Python- Pandas)

Before importing the data into Power BI, a data preparation process was performed using Python to improve data quality and create business-specific attributes required for the analysis. The complete transformation script can be found in the DATACLEANING file available in this repository.

The Python process included the following steps:

### Data Type Standardization

All financial and operational fields used in calculations were converted to appropriate numeric data types to ensure consistency and avoid calculation errors.
Examples of converted fields:
- Amount Due
- Amount Paid
- Overdue Days
 - Write-Off Amount
- Tax Amount
- Net Revenue
- Adjusted Revenue

### Missing Value Treatment 
Missing values were identified and replaced with default values (zero where applicable) to prevent incorrect aggregations and ensure reliable KPI calculations.

### Creation of Business Calculated Columns 
Several analytical fields were created to support reporting and collections analysis:

- IsOverdue: Flags invoices as overdue when the number of overdue days is greater than zero.
1 = Overdue
0 = Not Overdue

- RecoveryRate Measures the proportion of the invoice amount that has already been paid.
Recovery Rate = Amount Paid / Amount Due

- WriteOffRate: Measures the proportion of the invoice value that has been written off.
Write-Off Rate = Write-Off Amount / Amount Due

- PaymentGap: Calculates the remaining unpaid balance for each invoice.
Payment Gap = Amount Due - Amount Paid

- AgingBucket: Classifies invoices according to their overdue period:
Aging Bucket	Criteria
-- Current	0 days overdue
-- 0–30	1 to 30 days overdue
-- 31–60	31 to 60 days overdue
-- 60+	More than 60 days overdue
This classification is used throughout the dashboard to analyze risk exposure and prioritize collection efforts.

Final Dataset Preparation: After all transformations were completed, the dataset was validated, remaining null values were handled, and the final cleansed dataset (collections_analysis_dataset_4.csv) was imported in Power BI.

## 🧩 DAX (Power BI)
Some of the DAXs created for this dashboard:

- MoM % Revenue = 
DIVIDE(
    sum(collections_analysis_dataset_4[Revenue]) - [Revenue Previous Month],
    [Revenue Previous Month])

- Top 5 Customers % of Outstanding = 
VAR Top5 =
    TOPN(
        5,
        SUMMARIZE(
            collections_analysis_dataset_4,
            collections_analysis_dataset_4[CustomerName],
            "TotalCust", collections_analysis_dataset_4[Unpaid Overdue Amount]),
        [TotalCust],
        DESC)
VAR Top5Total =
    SUMX(Top5, [TotalCust])
VAR Total =
    collections_analysis_dataset_4[Unpaid Overdue Amount]
RETURN
    DIVIDE(Top5Total,Total)

- Unpaid Overdue Amount = 
SUMX(
    'collections_analysis_dataset_4',
    IF(
        'collections_analysis_dataset_4'[IsOverdue] = "1",
        'collections_analysis_dataset_4'[AmountDue] - 'collections_analysis_dataset_4'[AmountPaid],
        0)) - [Write Off Amount]

## 📈 Some Insights Found
### Dynamic Insights 
This dashboard was designed not only to display data but also to accelerate decision-making. Copilot can help users explore the data interactively and uncover trends that may not be immediately visible through traditional visualizations.
Alternatively, a more targeted approach can be implemented, as demonstrated in this project. Since leadership teams often look for specific answers and recurring business insights, predefined DAX measures can be created to automatically generate business-friendly conclusions.
This approach ensures that critical insights are immediately visible to decision-makers, helping stakeholders with data interpretation.
