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
and Evaluate framework from Section 2, the AI returned a materially better first draft:
```sql
-- ============================================================
-- Revenue Summary by Country
-- Source: Chinook SQLite Database
-- Purpose: Help marketing understand revenue and customer
--          concentration across geographic markets
-- ============================================================

WITH country_metrics AS (

    -- Aggregate invoice data at the country level.
    -- We use INNER JOIN to ensure only countries with actual
    -- purchases are included — no customers without invoices.
    SELECT
        i.BillingCountry                            AS country,
        SUM(i.Total)                                AS revenue_raw,
        COUNT(DISTINCT c.CustomerId)                AS unique_customers,
        SUM(i.Total) / COUNT(DISTINCT c.CustomerId) AS avg_revenue_raw

    FROM Invoice AS i
        INNER JOIN Customer AS c
            ON i.CustomerId = c.CustomerId

    GROUP BY i.BillingCountry
)

SELECT
    country                                             AS "Country",
    PRINTF('$%.2f', COALESCE(revenue_raw, 0))          AS "Total Revenue",
    unique_customers                                    AS "Unique Customers",
    PRINTF('$%.2f', COALESCE(avg_revenue_raw, 0))      AS "Avg Revenue per Customer"

FROM country_metrics
ORDER BY revenue_raw DESC;
```

This is a meaningful step up. The CTE is clean, the join logic is correct, currency 
is formatted, and the comments explain the decisions. The difference wasn't the AI 
getting smarter — it was the quality of the input I gave it.

But after running this and reviewing the output, I still had notes.

---

## My Critical Evaluation

The structured prompt produced a solid first draft — but "solid" still wasn't "done." 
This is the part of the AI workflow that I think gets undervalued: the human review pass.

Here's exactly what I flagged:

**1. No ranking column**
The output sorts by revenue descending, but there's no explicit rank number. In a 
report, a reader should be able to instantly see "USA is #1, Canada is #2" without 
counting rows manually. I added ROW_NUMBER() OVER (ORDER BY revenue DESC).

**2. No percentage of total revenue**
Raw revenue numbers tell me who's biggest, but not how concentrated the business is. 
If the USA represents 22% of total revenue, that's a very different strategic 
conversation than if it represents 60%. The AI didn't calculate this because I didn't 
explicitly ask for it — a reminder that AI answers the question you ask, not the 
question you should have asked.

**3. No global revenue benchmark**
To calculate percentage of total, I needed a second CTE capturing the grand total 
revenue across all countries. The AI's version had no mechanism for this — adding it 
required restructuring the query with an additional CTE and a CROSS JOIN pattern.

**4. Floating point noise**
Values like `523.0600000000003` are a SQLite artifact. PRINTF('$%.2f') fixes the 
display, and the AI applied it correctly — but only because I specified it explicitly 
in my prompt. Without that constraint, it wouldn't have been there.

**5. No comment header block**
The AI added inline comments, which was good. But there was no formal header block 
identifying the case study, business question, skills used, and author. That's a 
professional standard for any query that lives in a shared repo.

**The bottom line:** The structured prompt got me to a strong 65% in a fraction of 
the time it would have taken me to write from scratch. The remaining 35% — the ranking, 
the percentage of total, the global benchmark CTE, the header block — that's the human 
layer. That's where domain expertise and analytical judgment live, and no prompt 
engineering replaces it.

---

## Iterative Prompting — From V1 to V2

This is where **iterative prompting** and **prompt chaining** come into practice.

- **Iterative Prompting** means refining your prompt based on evaluation of the previous output
- **Prompt Chaining** means the output of one prompt becomes the direct input of the next

After completing my critical evaluation of the V1 output, I didn't rewrite the query 
manually. Instead, I reformatted my evaluation notes as a structured feedback prompt 
and fed them back to the AI — letting it incorporate my specific changes while 
preserving everything it had already gotten right.

**The feedback prompt I sent:**

> *"The query you returned was a strong first draft, but I have specific feedback I need 
> you to incorporate. Please update the query with the following changes:*
>
> *1. **Add a ranking column** — Use ROW_NUMBER() OVER (ORDER BY revenue DESC) to add 
> an explicit rank number as the first column.*
>
> *2. **Add percentage of total revenue** — Add a column showing each country's revenue 
> as a percentage of total global revenue, rounded to 2 decimal places using ROUND(..., 2).*
>
> *3. **Add a global revenue benchmark CTE** — Add a second CTE that captures grand total 
> revenue using SUM(Total), then use a CROSS JOIN to make that value available in the 
> final SELECT.*
>
> *4. **Confirm currency formatting is applied to all money columns** — Use PRINTF('$%.2f') 
> on every column that contains a dollar amount.*
>
> *5. **Add a formal comment header block** at the top of the query in this exact format:*
> ```
> -- ============================================
> -- Case Study 1: Geographic Revenue Analysis
> -- Business Question: [question here]
> -- Skills: [list skills used]
> -- Author: Daniel Matel-Okoh
> -- AI Partner: Claude / Gemini
> -- ============================================
> ```
> *Do not change anything else about the query structure — keep the existing CTE, 
> join logic, aliases, and inline comments exactly as they are."*

---

**The V2 Query — AI Output After Prompt Chaining:**
```sql
-- ============================================
-- Case Study 1: Geographic Revenue Analysis
-- Business Question: Which countries generate the most revenue,
--                   how many unique customers does each have,
--                   and what share of global revenue do they represent?
-- Skills: CTEs, Window Functions, Aggregate Functions,
--         CROSS JOIN, PRINTF formatting, ROUND, ROW_NUMBER
-- Author: Daniel Matel-Okoh
-- AI Partner: Claude
-- ============================================

WITH country_metrics AS (
    SELECT
        i.BillingCountry                                        AS country,
        SUM(i.Total)                                            AS revenue_raw,
        COUNT(DISTINCT i.CustomerId)                            AS unique_customer_count,
        SUM(i.Total) / COUNT(DISTINCT i.CustomerId)             AS avg_revenue_per_customer_raw
    FROM Invoice AS i
        INNER JOIN Customer AS c
            ON i.CustomerId = c.CustomerId
    GROUP BY
        i.BillingCountry
),
global_totals AS (
    SELECT
        SUM(Total) AS global_revenue_raw
    FROM Invoice
)
SELECT
    ROW_NUMBER() OVER (ORDER BY cm.revenue_raw DESC)              AS "Rank",
    cm.country                                                    AS "Country",
    PRINTF('$%.2f', COALESCE(cm.revenue_raw, 0))                  AS "Total Revenue",
    cm.unique_customer_count                                      AS "Unique Customers",
    PRINTF('$%.2f', COALESCE(cm.avg_revenue_per_customer_raw, 0)) AS "Avg Revenue Per Customer",
    ROUND((cm.revenue_raw / gt.global_revenue_raw) * 100.0, 2)   AS "% of Global Revenue"
FROM
    country_metrics AS cm
    CROSS JOIN global_totals AS gt
ORDER BY
    cm.revenue_raw DESC;
```

**What the prompt chaining achieved:**

| | V1 (Initial AI Output) | V2 (After Prompt Chaining) |
|---|---|---|
| Ranking column | ❌ | ✅ ROW_NUMBER() |
| % of global revenue | ❌ | ✅ ROUND(..., 2) |
| Global benchmark CTE | ❌ | ✅ CROSS JOIN pattern |
| Float division fix (100.0) | ❌ | ✅ AI inferred this edge case |
| Comment header block | ❌ | ✅ Full header |
| Currency formatting | ✅ | ✅ |
| Correct join type | ✅ | ✅ |
| Inline comments | ✅ | ✅ |

One thing worth noting: the AI added `100.0` instead of `100` in the percentage 
calculation to force float division in SQLite — an edge case I didn't explicitly 
specify, but that my feedback prompt gave it enough context to reason through. 
That's the compounding value of precise prompting.

---

**V2 Query Results:**

| Rank | Country | Total Revenue | Unique Customers | Avg Rev/Customer | % of Global Revenue |
|------|---------|---------------|------------------|------------------|---------------------|
| 1 | USA | $523.06 | 13 | $40.24 | 22.46% |
| 2 | Canada | $303.96 | 8 | $37.99 | 13.05% |
| 3 | France | $195.10 | 5 | $39.02 | 8.38% |
| 4 | Brazil | $190.10 | 5 | $38.02 | 8.16% |
| 5 | Germany | $156.48 | 4 | $39.12 | 6.72% |
| 6 | United Kingdom | $112.86 | 3 | $37.62 | 4.85% |
| 7 | Czech Republic | $90.24 | 2 | $45.12 | 3.88% |
| 8 | Portugal | $77.24 | 2 | $38.62 | 3.32% |
| 9 | India | $75.26 | 2 | $37.63 | 3.23% |
| 10 | Chile | $46.62 | 1 | $46.62 | 2.00% |
| 11 | Hungary | $45.62 | 1 | $45.62 | 1.96% |
| 12 | Ireland | $45.62 | 1 | $45.62 | 1.96% |
| 13 | Austria | $42.62 | 1 | $42.62 | 1.83% |
| 14 | Finland | $41.62 | 1 | $41.62 | 1.79% |
| 15 | Netherlands | $40.62 | 1 | $40.62 | 1.74% |
| 16 | Norway | $39.62 | 1 | $39.62 | 1.70% |
| 17 | Sweden | $38.62 | 1 | $38.62 | 1.66% |
| 18 | Argentina | $37.62 | 1 | $37.62 | 1.62% |
| 19 | Australia | $37.62 | 1 | $37.62 | 1.62% |
| 20 | Belgium | $37.62 | 1 | $37.62 | 1.62% |
| 21 | Denmark | $37.62 | 1 | $37.62 | 1.62% |
| 22 | Italy | $37.62 | 1 | $37.62 | 1.62% |
| 23 | Poland | $37.62 | 1 | $37.62 | 1.62% |
| 24 | Spain | $37.62 | 1 | $37.62 | 1.62% |
