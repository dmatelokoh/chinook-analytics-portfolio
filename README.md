# Chinook Digital Media — Customer & Revenue Analytics
**Daniel Matel-Okoh**
---

## What This Is

An end-to-end analytics portfolio built on the Chinook digital media database. Three SQL case studies, one multi-part Python investigation, and an interactive Tableau dashboard. Each phase builds on the last, and the through-line is a practical one: where should a marketing team focus its budget?

I came to data analytics from 8 years in digital marketing (customer acquisition, retention, campaign optimization, CLV, ROAS). That background shapes every analysis here. The goal with each case study was to produce something a marketing director could actually use, not just technically correct output.

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

## Key Findings

##Three markets carry the business.

The USA, Canada, and France account for 44% of total revenue across 24 countries. The bottom 15 markets each depend on a single customer. Losing one means that market goes to zero. The recommendation: grow mid-tier markets (Germany, UK, Brazil) to reduce geographic concentration.

##Customer tiers are compressed because the product pricing is too narrow.

72% of revenue sits in a single value tier (Silver). The cause is structural: with tracks priced at $0.99 and $1.99, there isn't enough price variance to create meaningful spend separation. Segmentation works, but upsell opportunities require product or pricing changes, not just marketing campaigns.

## Four genres drive 73.5% of revenue.

Whether that's a problem depends on trend direction.** Rock, Latin, Metal, and Alternative & Punk dominate the catalog. If those four are growing, concentration is fine. If they're flat or declining, there's no strong second tier to absorb the loss. Sci Fi & Fantasy leads in catalog efficiency ($1.53/track vs. $0.64 for Rock), which suggests unmet demand in a small but high-performing genre.

## Customers buy on a ~32-day cycle, and the intervention window is narrow.

The median repurchase interval is 32 days with high natural variance. 57.6% of repeat purchases happen within 6 weeks. A proactive engagement campaign at 14-21 days and a win-back trigger at 40-100 days would cover the two highest-leverage windows in the customer lifecycle.

## Retention follows the same curve regardless of when a customer joined.

Every quarterly cohort decays at the same rate (±7 percentage points across 26 cohorts over 6 years). The steepest drop is at Q+4. Half of all churn happens by Q+5. The YoY growth deceleration isn't a retention problem. It's an acquisition or product ceiling. Retention investment should target the first 12 months, where the highest share of churn is still preventable.

---

## Skills Demonstrated

**SQL:** CTEs, window functions (ROW_NUMBER, PERCENT_RANK), JOINs (INNER, LEFT, CROSS), GROUP BY, CASE WHEN, PRINTF, ROUND, COALESCE, NULLIF

**Python:** pandas, matplotlib, seaborn, sqlite3, datetime, cohort analysis, time series, data generation

**Tableau:** Interactive dashboards, calculated fields, LOD expressions, dashboard actions, cross-filtering, Tableau Public

**Analytics:** Revenue segmentation, CLV tiering, catalog efficiency, geographic prioritization, cohort retention, YoY trend analysis

**AI Collaboration:** Prompt engineering, context priming, critical evaluation, iterative refinement

