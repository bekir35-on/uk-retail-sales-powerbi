# UK Regional Sales Performance — Interactive Power BI Report

An interactive Power BI report analysing UK retail sales against regional targets for
2023–2025. Built end-to-end in Power BI Desktop: data cleaning in Power Query, a
star-schema data model, DAX measures (including time intelligence), and an
interactive dashboard with slicers, cross-filtering and drill-through.

![Dashboard](dashboard.png)

---

## Overview

This project takes raw retail transaction data and regional sales targets and turns
them into a single, interactive management dashboard that answers three business
questions at a glance:

- **How are we selling** — total sales, sales by product category, and the sales
  trend over time (2023–2025)?
- **Where are we selling** — sales by UK city (map) and by region?
- **Are we hitting target** — actual sales vs the target **for the same period** per
  region, the variance, and year-on-year growth.

## Dataset

| File | Description | Rows |
|------|-------------|------|
| `UK_Retail_Sales.csv` | Retail transactions (Order Date, City, Region, Category, Sales, Quantity, etc.) | ~6,200 |
| `UK_Region_Targets.csv` | Annual sales target for each of the 8 UK regions | 8 |

*A sample dataset used for a portfolio / learning project — no real customer data.*

## Tools & techniques

- **Power Query (ETL):** cleaned the source data — corrected data types, trimmed and
  standardised text, removed duplicates and handled blanks, and reshaped the targets
  table so it could be modelled.
- **Data modelling (star schema):** related a `UK_Retail_Sales` fact table to a
  `UK_Region_Targets` dimension (one-to-many on Region) and a dedicated `Calendar`
  date table (marked as the date table) for reliable time intelligence.
- **DAX measures:**
  - `Total Sales = SUM(UK_Retail_Sales[Sales])`
  - `Total Target` — the annual target scaled to the selected period, so sales and target always
    cover the same time span:
    ```DAX
    Total Target =
        CALCULATE (
            SUM ( UK_Region_Targets[Annual Target] ),
            TREATAS ( VALUES ( UK_Retail_Sales[Region] ), UK_Region_Targets[Region] )
        )
        * CALCULATE ( DISTINCTCOUNT ( 'Calendar'[Year] ), UK_Retail_Sales )
    ```
    `TREATAS` makes the target follow a region filter coming from either table; the second part
    counts the years in the current selection (3 for 2023–2025, 1 when a single year is selected).
  - `Variance = [Total Sales] - [Total Target]`
  - `% of Target = DIVIDE([Total Sales], [Total Target])`
  - **Year-on-year growth** using `SAMEPERIODLASTYEAR` over the Calendar table.
- **Interactivity:** a Region slicer, visual cross-filtering (edit interactions), and
  **drill-through** from the dashboard to a detailed regional performance page.
- **Formatting:** a dark theme, report title, KPI card, and a performance matrix with
  **traffic-light conditional formatting** on the variance.

## Key findings

| Region | Sales 2023–25 | Target 2023–25 | % of target |
|---|--:|--:|--:|
| Wales | £1.05M | £1.62M | **64.6%** |
| South West | £1.05M | £2.07M | 50.7% |
| Yorkshire | £0.98M | £2.28M | 43.2% |
| West Midlands | £1.06M | £2.61M | 40.5% |
| South East | £1.09M | £2.94M | 37.1% |
| Scotland | £1.07M | £3.15M | 34.0% |
| North West | £0.93M | £3.30M | 28.3% |
| London | £0.94M | £3.75M | **25.0%** |
| **Total** | **£8.17M** | **£21.72M** | **37.6%** |

- Sales are spread evenly across regions (£0.93M–£1.09M), so **the differences in % of target come
  almost entirely from the targets**: London has the highest target and the lowest attainment.
- Every region is far below its target on an annual basis — the targets look unrealistic for this
  sales level (or are mislabelled), which is worth confirming with the business before using them
  to judge regional performance.
- Sales dipped slightly in 2024 (−0.9%) and grew **+12.7% in 2025**.

> **Correction (Oct 2026):** the first version of this report divided three years of sales by a
> single year's target, which overstated attainment (e.g. London showed ~75% instead of ~25%).
> `Total Target` now scales the target to the selected period, and the page names, measure names
> and unused tables were tidied up.

## What's in the report

**Page 1 — Dashboard**
- KPI card: total sales (£8.17M)
- Sales by product category (bar)
- Sales trend by order date (2023–2025)
- Sales by UK city (map)
- Sales vs target per region with % of target (table)
- Sales by region (bar)
- Region slicer + cross-filtering across every visual

**Page 2 — Regional detail (drill-through)**
- A detailed performance matrix: sales, target, variance and % of target by region,
  with conditional formatting — reachable by right-clicking a region and drilling through.

## How to open

1. Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free, Windows).
2. Download `UK Sale Report.pbix` from this repository and open it.
3. The map is a Bing bubble map, so an internet connection is needed for it to render.

## Author

**Bekir Onal** — Data Analyst
[LinkedIn](https://www.linkedin.com/in/bekir-onal-636b57250) · [GitHub](https://github.com/bekir35-on)
