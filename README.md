# Chinook Digital Media: Customer & Revenue Analytics
**Daniel Matel-Okoh**
---

**[Executive Summary of the key findings from the case study (PDF)](executive_summary.pdf)**

## What This Is

An end-to-end analytics portfolio built on the Chinook digital media database. Three SQL case studies, one multi-part Python investigation, and an interactive Tableau dashboard. Every analysis comes back to the same question: where should a marketing team focus its budget?

I came to data analytics from 8 years in digital marketing (customer acquisition, retention, campaign optimization, budget management, CLV, ROAS). That background shapes every analysis here. The goal with each case study was to produce something a marketing director could actually use, not just technically correct output.

---

## Portfolio Structure

### SQL Case Studies

| # | Title | Business Question | Key Skills |
|---|-------|-------------------|------------|
| [1](01-SQL-case-studies/01_geographic_revenue.md) | Where Is the Revenue Coming From? | Which geographic markets should Chinook prioritize? | CTEs, ROW_NUMBER(), CROSS JOIN, PRINTF |
| [2](01-SQL-case-studies/02_customer_lifetime_value.md) | Who Are Our Best Customers? | How should we segment customers by lifetime value? | Multi-table JOINs, CASE WHEN tiering, NULLIF |
| [3](01-SQL-case-studies/03_genre_benchmarking.md) | What's Selling and What Isn't? | Which genres over- or under-perform relative to catalog size? | Benchmarking CTEs, LEFT JOIN, catalog efficiency |

**SQL query files:** [`01-SQL-case-studies/SQL-queries/`](01-SQL-case-studies/SQL-queries/)

### Python Case Study

| Part | Title | What It Investigates |
|------|-------|---------------------|
| [1](02-Python-case-studies/04_purchase_timing_frequency.md#the-business-question) | Purchase Timing & Frequency | Is the purchase rhythm artificial or organic? |
| [2](02-Python-case-studies/04_purchase_timing_frequency.md#part-2-testing-for-date-clustering-and-seasonal-patterns) | Seasonal Patterns & Growth | Are purchases driven by seasons? Is growth decelerating? |
| [3](02-Python-case-studies/04_purchase_timing_frequency.md#part-3-do-customers-stick-around) | Cohort Retention | Do customers stick around, and where's the intervention window? |

SQL Case Study 2 found that almost every customer made exactly 7 purchases. The Python case study investigates why, using an expanded synthetic dataset (~5,000 customers, ~54,000 invoices, 2019–2025).

**Jupyter notebooks:** [`02-Python-case-studies/Python-Queries/`](02-Python-case-studies/Python-Queries/)

### Tableau Dashboard

**[Chinook Executive Dashboards — Tableau Public](https://public.tableau.com/app/profile/daniel.matel.okoh/viz/ChinookExecutiveDashboards/GeographicPerformance)**

Three interactive views with cross-filtering:

- **Geographic Performance** — Country revenue map, top markets, genre mix by country
- **Customer Segmentation** — Value tier distribution, CLV scatter, cohort retention curves
- **Genre Performance** — Revenue ranking with catalog efficiency, YoY trends, market share

The dashboards bring together the findings from all three SQL case studies and the Python retention analysis into one stakeholder-ready tool.

**[Dashboard walkthrough — why each view exists and how they connect](03-Tableau-case-studies/05_tableau_dashboard_walkthrough.md)**

**Tableau workbook + data:** [`03-Tableau-case-studies/`](03-Tableau-case-studies/)

---

## About the Data

The SQL case studies use the original Chinook database (59 customers, 24 countries). The Python and Tableau work uses an expanded synthetic version I generated to create realistic variance, seasonality, and churn patterns (~5,000 customers, ~54,000 invoices, ~296,000 line items, 2019–2025). The expanded database was built from the original Chinook schema using Python (sqlite3, pandas, faker, datetime). The generation script and validation checks are in the local project files.

---

## AI Collaboration

I used AI as a deliberate part of my workflow throughout this project. It handled the technical execution (SQL, Python, formatting), which gave me more time to focus on the analysis, the business framing, and the judgment calls that make the output useful. The full process is documented, including structured prompts, critical evaluation, iterative refinement, and every place I overruled the AI and why.

Detailed log: [`04-AI-collaboration/ai_collaboration_log.md`](04-AI-collaboration/ai_collaboration_log.md)

---

## Skills Demonstrated

**SQL:** CTEs, window functions (ROW_NUMBER, PERCENT_RANK), JOINs (INNER, LEFT, CROSS), GROUP BY, CASE WHEN, PRINTF, ROUND, COALESCE, NULLIF

**Python:** pandas, matplotlib, seaborn, sqlite3, datetime, cohort analysis, time series, data generation

**Tableau:** Interactive dashboards, calculated fields, LOD expressions, dashboard actions, cross-filtering, Tableau Public

**Analytics:** Revenue segmentation, CLV tiering, catalog efficiency, geographic prioritization, cohort retention, YoY trend analysis

**AI Collaboration:** Prompt engineering, context priming, critical evaluation, iterative refinement

