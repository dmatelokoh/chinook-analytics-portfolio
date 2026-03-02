# Case Study 1: Where Is the Revenue Coming From?
## Geographic Revenue Analysis

---

## The Business Question

Chinook operates as a digital media store selling music tracks and albums globally. 
Before making any decisions about where to invest in marketing or customer acquisition, 
leadership needs to understand the geographic landscape of our revenue.

**The core question:** Which geographic markets should Chinook prioritize for growth 
and customer acquisition — and are we over-investing in markets where we're already saturated?

This is not just a "show me a ranked list" request. The goal is to understand:
- Where is revenue concentrated?
- Which markets have high revenue but low customer counts (high value per customer)?
- How dependent is the business on any single market?

---

## The Prompt I Gave the AI

Rather than asking vaguely for "revenue by country," I used a structured prompt framework 
to ensure the AI had everything it needed to return something useful the first time.

---

**🧠 Persona**
> Act as a senior data analyst who writes clean, well-commented SQL for business stakeholders — not just code that runs, but code that can be read and maintained by others.

**🎯 Task**
> Write a SQL query that returns total revenue by country, number of unique customers per country, and average revenue per customer — sorted by total revenue descending.

**📋 Context**
> I'm working in the Chinook SQLite database. The relevant tables are Invoice (columns: InvoiceId, CustomerId, BillingCountry, Total) and Customer (column: CustomerId). Every invoice is tied to a customer via CustomerId.

**🚧 Constraints**
> - This is SQLite — do not use FORMAT(), TOP, or ISNULL. Use PRINTF(), LIMIT, and COALESCE instead.
> - Use INNER JOIN, not LEFT JOIN — I only want countries where purchases actually occurred.
> - Do not use reserved words like `avg` or `total` as aliases.

**📐 Format**
> Return a single clean SELECT statement. Use clear column aliases. Format all currency columns with PRINTF('$%.2f', ...). Add brief inline comments explaining key decisions.

**📎 References**
> Follow the same CTE structure and alias style used in standard business intelligence reporting — readable top-to-bottom, with each logical step named.

**👥 Audience**
> The output will be reviewed by a non-technical marketing director. Column names should be self-explanatory. No raw decimals. No cryptic aliases.

**✅ Evaluate**
> I'll check the first draft for: correct join type, no SQLite-incompatible functions, readable aliases, formatted currency, and whether the output actually answers the business question — not just runs without errors.

---

## How This Prompt Was Built — Meta-Prompting in Practice

The structured prompt above didn't come from nowhere. It was itself the product of 
a meta-prompting process — using AI to help me build better prompts.

**Here's what that looked like:**

My first instinct was to write something like this:

> *"Using the Chinook database, write me a SQL query that shows revenue by country, 
> number of customers, and average spend. Sort by revenue descending."*

That's a functional prompt. It would return something. But "something" isn't the 
same as "something good." The output would likely have raw decimals, weak aliases, 
no comments, and no awareness of SQLite's limitations.

So instead of sending that prompt directly, I stepped back and asked a different 
question first:

> *"What are the key components of a well-structured AI prompt for a technical task? 
> Give me a reusable framework I can apply to SQL and analytics requests."*

The AI returned a framework: Persona, Task, Context, Constraints, Format, References, 
Audience, Evaluate. I then used that framework as a template and rebuilt my original 
vague prompt against each category — deliberately filling in every slot.

The result was the structured prompt you see above. Same underlying ask. 
Completely different quality of output.

**Why this matters:**

This is meta-prompting — using AI to improve the inputs you give to AI. It's a 
compounding skill. The better your prompts, the better your outputs, the less time 
you spend fixing AI mistakes downstream. In a data analytics context, a bad prompt 
might give you a query that runs but answers the wrong question. That's worse than 
no query at all, because it looks correct until someone makes a decision based on it.

Going forward, I apply this framework to the majority of my AI interactions in this 
project — especially anywhere the output will be used in a business context. Not every 
prompt needs all eight components, but thinking through each one, even briefly, 
consistently produces better results than typing the first thing that comes to mind.

---

## The AI's Raw Output

To demonstrate why prompt structure matters, I ran two versions of the same request.

**First — a generic, unstructured prompt:**
> *"Write me a SQL query that shows revenue by country, number of customers, 
> and average spend. Sort by revenue descending."*

The AI returned this:
```sql
SELECT BillingCountry, 
       SUM(Total) as total, 
       COUNT(DISTINCT CustomerId) as customers, 
       SUM(Total)/COUNT(DISTINCT CustomerId) as avg
FROM Invoice
GROUP BY BillingCountry
ORDER BY total DESC;
```

**The output looked like this:**

| BillingCountry | total | customers | avg |
|----------------|-------|-----------|-----|
| USA | 523.0600000000003 | 13 | 40.23538... |
| Canada | 303.9599999999999 | 8 | 37.99499... |
| France | 195.09999999999994 | 5 | 39.01999... |
| Brazil | 190.09999999999997 | 5 | 38.01999... |

It runs. It's directionally correct. But it would never go in front of a marketing 
director as-is — raw floating point decimals, no ranking, no percentage of total, 
no comments, and a reserved word (`avg`) used as an alias.

---

**Then — using my structured prompt framework:**

After applying the Persona, Task, Context, Constraints, Format, References, Audience, 
and Evaluate framework from Section 2, the AI returned a materially better first draft — 
one that required far less manual cleanup and was already closer to production-ready.

The difference wasn't the AI. It was the quality of the input I gave it.

That refined output, along with my own improvements on top of it, is what you'll 
see in the next two sections.

---

## My Critical Evaluation

The structured prompt produced a noticeably better first draft — but "better" still 
wasn't "done." This is the part of the AI workflow that I think gets undervalued: 
the human review pass.

Here's exactly what I flagged before touching the query:

**1. No ranking column**
The output sorts by revenue descending, but there's no explicit rank number. In a 
report, a reader should be able to instantly see "USA is #1, Canada is #2" without 
having to count rows manually. I needed to add ROW_NUMBER() OVER (ORDER BY revenue DESC).

**2. No percentage of total revenue**
The raw revenue numbers tell me who's biggest, but not how concentrated the business 
is. If the USA represents 22% of total revenue, that's a very different strategic 
conversation than if it represents 60%. The AI didn't calculate this because I didn't 
explicitly ask for it — a reminder that AI answers the question you ask, not the 
question you should have asked.

**3. Floating point noise in the output**
Values like `523.0600000000003` are a SQLite artifact of floating point arithmetic. 
PRINTF('$%.2f') fixes the display, but the AI only partially applied this — it 
formatted some columns and missed others.

**4. No CTEs — just a flat query**
The AI returned a single SELECT statement. That works for a simple query, but as 
soon as I need to reference the global total for percentage calculations, a flat 
query becomes a nested subquery mess. CTEs make the logic readable and maintainable. 
This was my addition, not the AI's.

**5. The alias `avg` is a reserved word**
Small issue, but the kind of thing that causes subtle bugs in more complex queries 
or when porting to other SQL dialects. I renamed it to `avg_revenue_per_customer` — 
descriptive and safe.

**6. No comment header or inline comments**
Nothing in the AI's output explained *why* decisions were made. A query without 
comments is a liability — it works today, but the next person (or future me) has 
to reverse-engineer the logic. I added a full header block and inline comments 
throughout.

**The bottom line:** The AI got me to a working 60% in a fraction of the time it 
would have taken me to write from scratch. The remaining 40% — the ranking, the 
percentage of total, the CTE structure, the business framing — that's the human layer. 
That's where domain expertise and analytical judgment live, and no prompt engineering 
replaces it.

---

## My Refined Query

Taking the AI's first draft as a starting point, here is the production-grade version 
I built after my evaluation pass. This is also saved in full in 
[`queries/01_geographic_revenue.sql`](../queries/01_geographic_revenue.sql).
```sql
-- ============================================
-- Case Study 1: Geographic Revenue Analysis
-- Business Question: Which geographic markets 
-- should Chinook prioritize for growth and 
-- customer acquisition?
-- Skills: CTEs, ROW_NUMBER(), PRINTF, GROUP BY,
--         INNER JOIN, ROUND
-- Author: Daniel Matel-Okoh
-- AI Partner: Claude / Gemini
-- ============================================


-- CTE 1: Calculate the global revenue total.
-- We'll use this to compute each country's share.
WITH global_totals AS (
    SELECT SUM(Total) AS grand_total_revenue
    FROM Invoice
),

-- CTE 2: Aggregate revenue metrics by country.
-- We join Invoice to Customer to get accurate 
-- customer counts (not just invoice counts).
country_metrics AS (
    SELECT
        i.BillingCountry                          AS country,
        COUNT(DISTINCT i.CustomerId)              AS total_customers,
        COUNT(i.InvoiceId)                        AS total_orders,
        SUM(i.Total)                              AS raw_revenue,
        SUM(i.Total) / COUNT(DISTINCT i.CustomerId) AS raw_revenue_per_customer
    FROM Invoice i
    INNER JOIN Customer c ON i.CustomerId = c.CustomerId
    GROUP BY i.BillingCountry
)

-- Final SELECT: Add ranking, formatting, and 
-- percentage of total. This is what goes in the report.
SELECT
    ROW_NUMBER() OVER (ORDER BY cm.raw_revenue DESC) AS revenue_rank,
    cm.country,
    cm.total_customers,
    cm.total_orders,
    PRINTF('$%.2f', cm.raw_revenue)               AS total_revenue,
    PRINTF('$%.2f', cm.raw_revenue_per_customer)  AS avg_revenue_per_customer,
    ROUND(cm.raw_revenue / gt.grand_total_revenue * 100, 2) AS pct_of_total_revenue
FROM country_metrics cm
CROSS JOIN global_totals gt
ORDER BY cm.raw_revenue DESC;
```

**What I added beyond the AI's first draft:**

- **CTE 1** captures the global revenue total once — so the percentage calculation 
  doesn't require a correlated subquery recalculating on every row
- **CTE 2** isolates the per-country aggregation cleanly, making the final SELECT 
  easy to read at a glance
- **ROW_NUMBER()** adds an explicit rank so the report is instantly scannable
- **PRINTF('$%.2f')** applied consistently across all currency columns — no raw decimals
- **pct_of_total_revenue** answers the concentration question the flat query couldn't
- **CROSS JOIN** on global_totals is the clean SQLite pattern for referencing a 
  single-row aggregate across all result rows
- **Full comment header** and inline comments throughout for maintainability
