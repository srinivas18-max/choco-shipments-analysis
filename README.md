# Chocolate Shipments Analysis (Power BI)

An end-to-end Power BI report analysing chocolate product shipments from January 2023 to March 2025. The project covers data modelling, DAX measures, and a multi-page dashboard that answers business questions on profit, shipment volume, sales-person and team performance, and year-over-year variance.

## Table of Contents
- [Business Questions](#business-questions)
- [Dataset](#dataset)
- [Data Model](#data-model)
- [Key Measures](#key-measures)
- [Dashboard Pages](#dashboard-pages)
- [Project Structure](#project-structure)
- [How to Open the Report](#how-to-open-the-report)
- [Data Notes and Limitations](#data-notes-and-limitations)
- [Tools](#tools)
- [Data Source](#data-source)

## Business Questions

1. **Profitability**
   - Which product is the most profitable?
   - Which sales persons generate the most profit?
2. **Shipment volume**
   - How many shipments are made each month?
   - How many shipments does each team and each sales person handle?
3. **Total amount**
   - What is the Total amount per sales person?
   - What is the Total amount per month?
4. **Top performers**
   - Which team is the most profitable?
   - Which team and which product are the most profitable overall?
5. **Year-over-year variance**
   - How did the total amount change this year compared with the previous year?
   - Did performance improve, and if it dropped, what drove the decline?

## Dataset

The source file is an Excel workbook with three sheets.

| Sheet | Rows | Description |
|---|---|---|
| `Shipments` | 25,076 | Transaction-level shipment records |
| `Dimension Data` | 22 products, 25 sales persons, 4 teams | Product, region, team and sales-person lookup tables |
| `Calendar` | 821 | Date table starting 2023-01-01 with month, year and weekday attributes |

**Shipments columns**

| Column | Type | Description |
|---|---|---|
| `ShipmentID` | Text | Unique shipment identifier |
| `SPID` | Text | Sales person ID (foreign key to dimension) |
| `PID` | Text | Product ID (foreign key to dimension) |
| `GID` | Text | Geography ID (foreign key to dimension) |
| `Shipdate` | Date | Shipment date (2023-01-02 to 2025-03-31) |
| `Amount` | Decimal | Sales amount for the shipment |
| `Boxes` | Integer | Number of boxes shipped |
| `Order_Status` | Text | Delivered, Shipped, Placed or Cancelled |

## Data Model

A star schema with `Shipments` at the centre.

- `Shipments[PID]` → `Products[PID]`
- `Shipments[SPID]` → `Sales Persons[SPID]`
- `Shipments[GID]` → `Geography[GID]`
- `Shipments[Shipdate]` → `Calendar[cal_date]`

## Key Measures

Table names follow the model in the `.pbix` (`Shipments`, `Calendar`). `Profit %` uses `DIVIDE` to avoid divide-by-zero errors.

| Measure | Definition |
|---|---|
| `Total Amount` | `SUM(Shipments[Amount])` |
| `Total Boxes` | `SUM(Shipments[Boxes])` |
| `Total Costs` | `SUM(Shipments[Cost])` |
| `Total Profit` | `Total Amount - Total Costs` |
| `Profit %` | `Total Profit / Total Amount` |
| `Shipment Count` | `COUNTROWS(Shipments)` |
| `Amount per Box` | `DIVIDE(Total Amount, Total Boxes)` |
| `Total Amount (last year)` | `CALCULATE([Total Amount], SAMEPERIODLASTYEAR(Calendar[cal_date]))` |
| `Total Amount (12 month variance)` | `Total Amount - Total Amount (last year)`, blank when no prior-year data |
| `Total Boxes (last year)` | Same pattern as above, applied to `Total Boxes` |
| `Total Boxes (12 month variance)` | `Total Boxes - Total Boxes (last year)`, blank when no prior-year data |

The full DAX is in [`measures/measures.md`](measures/measures.md).

## Dashboard Pages

The report contains the following analysis pages.

| Page | Focus |
|---|---|
| Page 1 | Total amount by product, geography and team |
| Page 2 | Total profit by geography and product, with total amount and total profit |
| Page 3 | Shipment count by year and month number, broken down by team |
| Page 4 | Total amount per sales person, by country |
| Page 5 | Total profit by year and month number, with top 5 and bottom 5 records |
| Page 6 | Total amount compared with the same period last year |
| Page 7 | Total amount by product |

Open the `.pbix` file in Power BI Desktop to explore the visuals and filters.

## Project Structure

```
choco-shipments-analysis/
├── README.md
├── .gitignore
├── Choco_Project_report.pbix
├── data/
│   └── sample-chocolate-shipments-data-all-Apr-2025.xlsx
├── measures/
│   └── measures.md
│ Screenshots/
│   └──Screenshots/Choco Shipment  from USA.png
       Screenshots/Choco Shipment from INDIA.png
       Screenshots/Overall Choco shipment Analysis.png
       Screenshots/Shipment by geo and amount paid.png
       Screenshots/Shipment of top 5 bottom 5.png

## How to Open the Report

1. Install [Power BI Desktop](https://www.microsoft.com/power-bi/desktop) (Windows).
2. Open `Choco_Project_report.pbix`.
3. If prompted, update the data source path to `data/sample-chocolate-shipments-data-all-Apr-2025.xlsx` under **Transform data → Data source settings**.
4. Click **Refresh** to load the data.

## Data Notes and Limitations

- **Cancelled orders**: 1,215 shipments have the status `Cancelled`. Measures currently include all statuses. Filter `Order_Status <> "Cancelled"` for realised revenue.
- **Incomplete dimension data**: Geography (`GID`, `Geo`, `Region`) is populated for only the first six products. Products P07 to P22 have no region assigned.
- **Sales persons**: The dimension table lists 25 sales persons across 4 teams (Yummies, Delish, Jucies, Tempo). Shipments reference all 25 SPIDs.
- **Cost column**: `Total Costs` expects a `Cost` column on `Shipments`. The raw sheet has no such column, so it must be created as a calculated column (`Cost_per_box` from `Dimension Data` × `Boxes`) in the model.
- **Date range**: The Calendar table covers 2023 to early 2025, so year-over-year measures return blank for 2023.

## Tools

- Power BI Desktop
- DAX
- Power Query (M)
- Microsoft Excel

## Data Source 

The shipments dataset is sample data used for learning purposes. Its original source is not recorded. If you are the rights holder and want it removed or credited, please open an issue
