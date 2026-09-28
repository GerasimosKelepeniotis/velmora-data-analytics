# Velmora Therapeutics — Commercial & Financial Performance Analytics

An end-to-end analytics project for a fictional European pharmaceutical group: raw, messy source files are cleaned and modelled in **Microsoft Fabric notebooks**, loaded into a star schema, and reported in **Power BI**.

> [!NOTE]
> **Data disclaimer.** All data in this project is entirely fictional. It was generated specifically for learning and portfolio purposes. Velmora Therapeutics Group, its products, markets, customers, prices and financial figures do not exist and do not represent any real company, customer or business information. Any resemblance to real organisations or products is coincidental.

## The business scenario

Velmora is a mid-sized pharmaceutical group selling eight products across several European and North American markets, in therapeutic areas including cardiovascular, oncology, respiratory, neurology, diabetes and immunology.

Leadership wants one reliable view of:

- **Sales performance** — net sales, units, discounts and average selling price by product, market and customer
- **Plan vs actual** — how results compare against budget and forecast
- **Profitability** — gross margin and operating expenses
- **Inventory and quality** — stock position and data quality across source systems

The source data arrives as separate files with inconsistent formats, which is the realistic starting point for most reporting projects.

## Tools

| Stage                | Tool                                 |
|----------------------|--------------------------------------|
| Data preparation     | Microsoft Fabric notebooks (PySpark) |
| Storage              | Fabric Lakehouse, delta tables       |
| Modelling & measures | Power BI, DAX                        |
| Reporting            | Power BI Desktop                     |

## How this project was built

I used an AI assistant (Claude) as a development partner on this project. It generated the synthetic source data, including the deliberate data quality problems. It also helped write the initial Bronze, Silver and validation notebooks, correct DAX measures, and parts of this README.

My part was setting up the Fabric Lakehouse, running and debugging the pipeline, and confirming that all 41 validation checks pass. I built the semantic model and relationships, implemented the DAX measures, and designed the Power BI report.

## Data preparation

Cleaning and transformation were done in Fabric notebooks rather than Power Query, so every step is written down, repeatable, and can be re-run when new files arrive.

The pipeline follows a layered approach:

1. **Raw layer** — source files loaded as they arrived, with no changes.
2. **Cleaned layer** — data quality issues fixed. Issues handled included: [edit this list to match what you actually did — e.g. duplicate transactions, inconsistent market and product codes, missing values, mixed date formats, and a file whose column structure changed between periods (schema drift)].
3. **Gold layer** — analysis-ready fact and dimension tables (prefixed `gld_`) loaded into delta tables for Power BI.

All amounts are standardised to **EUR** using an exchange-rate table, so markets can be compared directly.

## Data model

A star schema with dimension tables (date, product, market, customer) filtering several fact tables (sales, budget, forecast, inventory, operating expenses). Relationships are single-direction, one-to-many, from dimension to fact. This keeps filtering predictable when multiple fact tables share the same dimensions.

## Key DAX measures

Measures are kept in a dedicated `_Measures` table and grouped into display folders (Sales, Plan, P&L, Time, Inventory, Quality).

```dax
Net Sales = SUM ( gld_fact_sales[NetSalesEUR] )

Gross Profit = [Net Sales] - [Cost of Sales]

Gross Margin % = DIVIDE ( [Gross Profit], [Net Sales] )

Average Selling Price =
DIVIDE (
    CALCULATE ( [Net Sales], gld_fact_sales[IsReturn] = FALSE ),
    CALCULATE ( [Units Sold], gld_fact_sales[IsReturn] = FALSE )
)
```

Average Selling Price deliberately excludes returns. Including them would make a month with heavy returns show a price that was never actually charged.

## Report pages

The full report is available as a PDF: [PB_Velmora.pdf](PB_Velmora.pdf)

## Key findings

*(Fictional data, so these describe the story the report tells rather than real-world conclusions.)*

- [Finding 1 — e.g. which product or market drives most of the margin]
- [Finding 2 — e.g. where actuals fall furthest from budget]
- [Finding 3 — e.g. a pattern in discounts or returns]

## What I learned / what I'd do next

- Cleaning in notebooks allows for more complex logic, since you have access to more libraries, while Power Query is better suited to common transformations. Notebooks also allow for more automation.
- Adding automated data quality checks that flag problems before they reach the report.

## Repository contents

| File                               | Description                       |
|------------------------------------|-----------------------------------|
| [`PB_Velmora.pdf`](PB_Velmora.pdf) | Full Power BI report export       |
| [`screenshots/`](screenshots/)     | Report page and data model images |
| [`notebooks/`](notebooks/)         | Fabric notebooks (`.ipynb`)       |

---

**Gerasimos Kelepeniotis** · [LinkedIn](https://www.linkedin.com/in/gerasimos-kelepeniotis)
