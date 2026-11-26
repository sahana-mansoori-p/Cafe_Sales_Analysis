# Cafe Sales Performance Analysis (2023)

A Power BI project that assesses, cleans and models one year of cafe transactions, then presents the results in an interactive four-page dashboard with validated insights and recommendations.

## Objective
The cafe records every sale but has no clear view of its performance. This project answers four questions: how much did the cafe earn, which items drive revenue, how do customers pay and where do they buy, and how complete is the data?

**Intended users:** the cafe owner or manager, the shift supervisor and finance staff.

**Decisions supported:** which menu items to protect or promote, how to plan staff and stock, which payment methods and channels matter, and where data capture at the till must improve.

## Dataset
- **Source:** `dirty_cafe_sales.csv`, a public practice dataset ("Cafe Sales - Dirty Data for Cleaning Training", Kaggle). Access date: 18 November 2026.
- **Size:** 10,000 rows x 8 columns. One row = one transaction.
- **Reporting period:** 1 January 2023 to 31 December 2023.
- **Key fields:** Transaction ID, Item, Quantity, Price Per Unit, Total Spent, Payment Method, Location, Transaction Date.
- **Data-sharing limitations:** none. The data contains no personal or confidential information.

## Repository Structure
```
Cafe_Sales_Analysis/
├── BRD/
│   └── BRD_Submitted.pdf                 Business requirements
├── FRD/
│   └── FRD_Submitted.pdf                 Functional requirements
├── Data/
│   ├── raw/                              Original dataset, unchanged
│   ├── cleaned/                          Cleaned dataset and documented Power Query steps
│   └── assessment/                       Column_Assessment.xlsx (column and dataset-level assessment)
├── Visuals/                              Dashboard mockup and draft data model (editable files and PDF exports)
├── PowerBI/
│   ├── pbix/                             dashboard.pbix
│   └── exports/                          dashboard_export.pdf
├── Reports/                              Analysis_Report.pdf, validation workbook, limitations and measures documentation
├── Scripts/                              Optional supporting scripts
└── README.md
```

| Folder | What it holds |
|---|---|
| BRD | The business problem, audience, scope, data source, KPIs and 8-day plan |
| FRD | Dashboard pages, visuals, filters, measures and acceptance criteria |
| Data | Raw file (kept unchanged), cleaned CSV, Power Query steps and the column assessment |
| Visuals | Dashboard mockup (PPTX and PDF), draft data model, Model view screenshot |
| PowerBI | The working `.pbix` file and a PDF export of the final dashboard |
| Reports | The analysis report (includes the data model documentation), validation checks, limitations and the measures list |
| Scripts | Optional supporting scripts |

## Tools Used
- **Power BI Desktop** for the dashboard and data model
- **Power Query** for data cleaning and for building the dimension and fact tables
- **DAX** for the date table and 13 measures
- **Excel** for the column assessment and validation checks
- **PowerPoint** for the dashboard mockup and draft model diagram
- **Git and GitHub** for version control

## Data Preparation
The raw file had many cells holding ERROR, UNKNOWN or nothing at all, and every column loaded as text. 6,911 of 10,000 rows (69%) had at least one problem, so rows were repaired and never deleted.
1. Blank, ERROR and UNKNOWN were all replaced with null (treated as missing).
2. Data types were set (Quantity as whole number, Price Per Unit and Total Spent as decimal, Transaction Date as date).
3. Missing Price Per Unit, Quantity and Total Spent were recovered using a rule that holds in every complete row: Total Spent = Quantity x Price Per Unit. Each item has one fixed price.
4. A missing Item was filled from the price only where the price belongs to one item (1 Cookie, 1.5 Tea, 2 Coffee, 5 Salad). Prices 3 and 4 are shared by two items each, so no guess was made and those rows are labelled "Unknown".
5. Missing Payment Method and Location were labelled "Unknown" because they cannot be derived.
6. Rows were sorted by Transaction Date.

**Result:** all 10,000 rows kept. Cleaning added 4,332.50 to revenue (about 5.1%), all explained by recovered values. After cleaning, 23 totals, 23 quantities, 6 prices and 460 dates remain missing. The steps are documented in `Data/cleaned/`.

## Data Model and Measures
**Structure:** a star schema with one fact table and four dimension tables.

| Table | Grain (one row =) |
|---|---|
| Fact_Sales | One transaction (10,000 rows) |
| Dim_Item | One item, including Unknown (9 rows) |
| Dim_Payment | One payment method, including Unknown (4 rows) |
| Dim_Location | One location, including Unknown (3 rows) |
| Dim_Date | One day of 2023 (365 rows), created with DAX and marked as the date table |

**Relationships:** four one-to-many relationships with single filter direction, from each dimension to Fact_Sales (Item ID, Payment ID, Location ID and Date to Transaction Date).

**Key measures** (all 13 are held in the `_measures` table):
- Total Revenue, Total Transactions, Units Sold, Average Transaction Value
- Revenue Share %, Units Share %, Revenue per Unit
- Revenue Previous Month, Revenue MoM %
- % Unknown Item, % Unknown Payment, % Unknown Location, % No Date

The full definitions and DAX are in `Reports/Analysis_Report.pdf`.

## Steps to Reproduce
1. Obtain the permitted source dataset (`Data/raw/dirty_cafe_sales.csv`, or download it from Kaggle).
2. Review the column assessment (`Data/assessment/`) and the Power Query steps (`Data/cleaned/`).
3. Open `PowerBI/pbix/dashboard.pbix` in Power BI Desktop.
4. If the data source cannot be found, go to **Transform data** → **Data source settings** and point `dirty_cafe_sales` to the CSV on your computer.
5. Click **Refresh** and check that the key measures match: Total Revenue 89,096.00, Total Transactions 10,000, Units Sold 30,180 and Average Transaction Value 8.93.
6. Review the exported dashboard (`PowerBI/exports/dashboard_export.pdf`) and the analysis report (`Reports/Analysis_Report.pdf`).

## Key Insights and Recommendations
| Finding | Recommendation |
|---|---|
| Revenue is steady: months range from 6,644 (Feb) to 7,353 (Jun), weekdays from 11,680.50 (Wed) to 12,401.50 (Thu) | Keep staffing and stock steady through the week and year. Re-check with more than one year of data |
| Price drives item revenue: units sold are similar (3,336 to 3,904) but revenue runs from 3,598 (Cookie) to 19,095 (Salad) | Keep high-priced items prominent on the menu |
| Salad, Sandwich and Smoothie give 51.80% of revenue from 35.07% of units | Protect their availability, and test pairing low-priced items (Cookie, Tea, Coffee) with them |
| 31.78% of payment methods, 39.61% of locations, 4.80% of items and 4.60% of dates are Unknown | Make payment method and location compulsory at checkout |

Headline numbers: revenue 89,096.00 from 10,000 transactions, an average of 8.93 per sale.

## Limitations
- Payment Method is Unknown for 31.78% of transactions and Location for 39.61%, so payment and channel results are indicative only.
- 460 transactions have no date. They count in the totals but not in monthly or weekday views, which add up to 84,922.50 instead of 89,096.00.
- One year of data only, so seasonal patterns and year-on-year change cannot be confirmed.
- The data is likely synthetic and very even, so real-world patterns may be weak.
- There is no cost, profit or customer data. The analysis covers revenue and sales mix only.
- The currency is not stated in the source data, so values are shown without a currency symbol.
- The item-pairing recommendation is a test to run, not a proven result.
