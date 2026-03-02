# Chinook Digital Media — Customer & Revenue Analytics Report

**A SQL portfolio project by Daniel Matel-Okoh**  
*Built on the Chinook SQLite database | Developed with AI collaboration (Claude / Gemini)*

---

## About This Project

This portfolio was built to demonstrate SQL analytics skills applied to real business problems — framed the way an analyst would actually encounter them at a digital media company.

I came to data analytics from **8 years in digital marketing** (customer acquisition, retention, campaign optimization, CLV, ROAS, funnel analysis). That background shapes every query in this project: I'm not just counting rows, I'm asking whether the numbers should change a business decision.

The database is **Chinook** — a fictional digital media store modeled on iTunes. Each case study follows a deliberate structure that shows not just *what* I built, but *how* I think:

> **Business Question → AI Prompt → Raw AI Output → My Evaluation → Refined Query → Business Insight**

---

## Case Studies

| # | Title | Business Question | Key Skills |
|---|-------|-------------------|------------|
| [1](case-studies/01_geographic_revenue.md) | Where Is the Revenue Coming From? | Which geographic markets should Chinook prioritize? | CTEs, ROW_NUMBER(), PRINTF, GROUP BY |
| [2](case-studies/02_customer_lifetime_value.md) | Who Are Our Best Customers? | How should we segment customers by lifetime value? | Multi-table JOINs, CASE WHEN tiering, CTEs |
| [3](case-studies/03_genre_benchmarking.md) | What's Selling and What Isn't? | Which genres over- or under-perform vs. average? | Benchmarking CTEs, AVG, CASE WHEN labels |

---

## SQL Files

- [`queries/01_geographic_revenue.sql`](queries/01_geographic_revenue.sql)
- [`queries/02_customer_lifetime_value.sql`](queries/02_customer_lifetime_value.sql)
- [`queries/03_genre_benchmarking.sql`](queries/03_genre_benchmarking.sql)

---

## AI Collaboration Approach

I used AI (Claude, Gemini) as a **thinking partner**, not a shortcut. My workflow:
1. Write a structured prompt with business context, schema details, and explicit output requirements.
2. Run the AI's output and evaluate it critically — checking for join logic errors, inefficiencies, and missing business framing.
3. Refine the query myself: add CTEs, formatting, tier labels, benchmark comparisons.
4. Interpret the results through my marketing lens.

Full breakdown: [`ai-collaboration/ai_collaboration_log.md`](ai-collaboration/ai_collaboration_log.md)

---

## Skills Demonstrated

**SQL:** SELECT, WHERE, JOINs, GROUP BY, HAVING, CTEs, window functions (ROW_NUMBER, NTILE), CASE WHEN, PRINTF, ROUND, COALESCE  
**Analytics:** Revenue segmentation, CLV, Pareto analysis, benchmarking, geographic prioritization  
**AI Fluency:** Prompt engineering, critical evaluation, iterative refinement, workflow integration

---

## Database Schema (Quick Reference)

| Table | Key Columns | Role |
|-------|-------------|------|
| Customer | CustomerId, FirstName, LastName, Country | Who bought |
| Invoice | InvoiceId, CustomerId, InvoiceDate, BillingCountry, Total | Each transaction |
| InvoiceLine | InvoiceLineId, InvoiceId, TrackId, UnitPrice, Quantity | Line items |
| Track | TrackId, Name, AlbumId, GenreId, UnitPrice | Product catalog |
| Genre | GenreId, Name | Genre categorization |

*All queries are SQLite-compatible and tested against the Chinook database.*
