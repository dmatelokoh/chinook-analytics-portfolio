# Chinook Digital Media — Customer & Revenue Analytics Report
**A SQL portfolio project by Daniel Matel-Okoh**  
*Built on the Chinook SQLite database | Developed with AI collaboration (Claude / Gemini)*

---

## About This Project

This portfolio was built to demonstrate SQL analytics skills applied to real 
business problems — framed the way an analyst would actually encounter them at 
a digital media company.

I came to data analytics from **8 years in digital marketing** (customer 
acquisition, retention, campaign optimization, CLV, ROAS, funnel analysis). 
That background shapes every query in this project: I'm not just counting rows, 
I'm asking whether the numbers should change a business decision.

The database is **Chinook** — a fictional digital media store modeled on iTunes. 
Each case study follows a deliberate structure that shows not just *what* I 
built, but *how* I think:

> **Business Question → AI Prompt → Raw AI Output → My Evaluation → Refined Query → Business Insight**

---

## Case Studies

| # | Title | Business Question | Key Skills |
|---|-------|-------------------|------------|
| [1](case-studies/01_geographic_revenue.md) | Where Is the Revenue Coming From? | Which geographic markets should Chinook prioritize? | CTEs, ROW_NUMBER(), PRINTF, GROUP BY |
| [2](case-studies/02_customer_lifetime_value.md) | Who Are Our Best Customers? | How should we segment customers by lifetime value? | Multi-table JOINs, CASE WHEN tiering, CTEs |
| [3](case-studies/03_genre_benchmarking.md) | What's Selling and What Isn't? | Which genres over- or under-perform vs. average? | Benchmarking CTEs, UNION ALL, NULLIF, LEFT JOIN |

---

## SQL Files

- [`queries/01_geographic_revenue.sql`](queries/01_geographic_revenue.sql)
- [`queries/02_customer_lifetime_value.sql`](queries/02_customer_lifetime_value.sql)
- [`queries/03_genre_benchmarking.sql`](queries/03_genre_benchmarking.sql)

---

## AI Collaboration Approach

I used AI (Claude, Gemini) as a **deliberate collaborator**, not a shortcut. 
My workflow across all three case studies:

1. **Context priming** — every session opened with a full context prime 
   establishing coding standards, schema, audience, and style conventions.
2. **Structured prompting** — an 8-part prompt framework (Persona, Task, 
   Context, Constraints, Format, References, Audience, Evaluate) built to 
   eliminate common failure modes before the first line of SQL was written.
3. **Critical evaluation** — every V1 output reviewed for correctness, business 
   meaning, and presentation quality. AI can't know that a performance label is 
   measuring price point instead of genre demand. That requires domain knowledge.
4. **Iterative refinement** — evaluation gaps reformatted as structured feedback 
   prompts and fed back to the AI. V1 → V2 in every case study.
5. **Prompt library management** — recurring gaps updated into the context prime 
   so V1 outputs improved across case studies. By Case Study 3, columns that 
   required feedback in Case Study 1 appeared automatically.
6. **Verification** — structured AI self-check pass on every query, including a 
   meta-prompt asking the AI what *else* to verify. In Case Study 3, the 
   meta-prompt caught a silent exclusion error that standard math checks would 
   never have found.

Full breakdown with concrete examples: [`ai-collaboration/ai_collaboration_log.md`](ai-collaboration/ai_collaboration_log.md)

---

## Skills Demonstrated

**SQL:** SELECT, WHERE, JOINs (INNER, LEFT), GROUP BY, HAVING, CTEs, window 
functions (ROW_NUMBER, PERCENT_RANK), CASE WHEN, PRINTF, ROUND, COALESCE, 
NULLIF, UNION ALL  
**Analytics:** Revenue segmentation, CLV, Pareto analysis, benchmarking, 
catalog efficiency, geographic prioritization  
**AI Fluency:** Prompt engineering, context priming, prompt library management, 
critical evaluation, iterative refinement, verification, meta-prompting

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
