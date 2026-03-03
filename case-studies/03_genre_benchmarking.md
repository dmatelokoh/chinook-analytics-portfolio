# Case Study 3: What's Selling and What Isn't?
## Genre & Track Performance Benchmarking

---

## The Business Question

Chinook's catalog spans multiple genres — from Rock and Metal to Jazz, Classical, 
and beyond. But not all genres are created equal. Some punch above their weight 
relative to the size of their catalog. Others have large track libraries that 
generate disproportionately little revenue.

Leadership needs to know which product categories deserve investment and which 
are underperforming — but "underperforming" only means something when you have 
a benchmark to compare against.

**The core question:** Which genres are over-performing or under-performing 
relative to the catalog average — and what does that tell us about where to 
focus product, marketing, and merchandising decisions?

This is not just a revenue ranking. The goal is to understand:
- Which genres generate the most total revenue?
- Which genres generate the most revenue per track sold — efficiency, not just volume?
- How does each genre compare against the global catalog average?
- Which genres are "above average", "average", or "below average" performers?

---

## The Prompt I Gave the AI

Using the structured prompt framework and context priming approach established 
in Case Study 1 and refined in Case Study 2, here is the prompt I built for 
this analysis.

Note that by Case Study 3, my context prime has been updated to reflect lessons 
learned from the first two case studies — specifically the prompt library updates 
around always including a ranking column and a percentage of total column. This 
is the compounding value of prompt library management in practice: the V1 output 
for this case study should be closer to production-ready than either of the 
previous two.

---

**🧠 Persona**
> You are a senior data analyst working inside Chinook Digital Media. You write 
> clean, well-commented SQLite-compatible SQL for a marketing team. You follow 
> these standards in every query you write:
>
> - Always use CTEs (WITH ... AS) instead of nested subqueries
> - Always format currency with PRINTF('$%.2f', ...)
> - Always use COALESCE() to handle potential NULLs in formatted columns
> - Always use INNER JOIN unless a LEFT JOIN is explicitly required
> - Always add a formal comment header block at the top of every query
> - Never use reserved words as aliases (avg, total, count, etc.)
> - Never use SQLite-incompatible functions (no FORMAT(), TOP, ISNULL())
> - For any query that ranks or compares rows, always include:
>   1. An explicit ROW_NUMBER() ranking column as the first column
>   2. A percentage of total column showing each row's share of the relevant aggregate
>   Unless I explicitly tell you these are not needed.
>
> These are non-negotiable standards. Apply them to every query in this session 
> without being reminded.

**🎯 Task**
> Write a SQL query that benchmarks each genre's performance against the global 
> catalog average. For each genre, calculate total revenue, total tracks sold, 
> and average revenue per track sold. Then compare each genre against the global 
> average revenue per track and label it as 'Above Average', 'Average', or 
> 'Below Average'.

**📋 Context**
> I'm working in the Chinook SQLite database. The relevant tables are:
> - Genre (GenreId, Name)
> - Track (TrackId, GenreId)
> - InvoiceLine (InvoiceLineId, InvoiceId, TrackId, UnitPrice, Quantity)
>
> Every track belongs to one genre via GenreId. Every sale is recorded as a 
> line item in InvoiceLine. I want performance calculated at the genre level — 
> total revenue and total tracks sold across all invoices for each genre.

**🚧 Constraints**
> - Use CASE WHEN to create the performance label — not a subquery in the SELECT
> - The global average should be calculated once in its own CTE — not recalculated 
>   for every row using a correlated subquery
> - All standards listed in the Persona section apply

**📐 Format**
> Use three CTEs:
> - CTE 1: Calculate total revenue and tracks sold per genre
> - CTE 2: Calculate the global average revenue per track across all genres
> - CTE 3 or Final SELECT: Join the two, add the performance label, ranking, 
>   percentage of total revenue, and formatted currency columns
>
> Sort by total revenue descending.

**📎 References**
> Follow this exact CTE structure and commenting style for every query:
> - Each CTE should have a comment block above it explaining its purpose
> - The final SELECT should have a comment explaining what it produces
> - The header block at the top of the query should follow this format exactly:
>
> ```
> -- ============================================================
> -- Query    : [Query name]
> -- Database : Chinook (SQLite)
> -- Author   : Senior Data Analyst, Chinook Digital Media
> -- Purpose  : [What this query does]
> -- Audience : [Who will use this]
> -- Notes    : [Any important context]
> -- ============================================================
> ```
>
> Column aliases should use title case with spaces — "Total Revenue" not 
> "total_revenue". Raw numeric columns used for sorting or calculations 
> should be kept as unformatted intermediates in the CTEs and only 
> formatted with PRINTF() in the final SELECT.

**👥 Audience**
> The output will be used by the product and marketing team to make decisions 
> about catalog investment, merchandising priority, and promotional spend 
> by genre. Column names should be immediately understandable to a 
> non-technical reader.

**✅ Evaluate**
> I'll check the first draft for: correct join logic across three tables, 
> global average calculated efficiently in its own CTE, performance labels 
> applied correctly via CASE WHEN, ranking and percentage of total included, 
> and whether the output actually answers the benchmarking question — not 
> just ranks genres by revenue.
