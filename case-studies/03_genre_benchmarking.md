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

---

## The AI's Raw Output

The context-primed prompt in a fresh AI session produced the strongest V1 output 
of all three case studies — a direct result of the cumulative prompt library 
updates from Cases 1 and 2.

Notable improvements in V1 without any feedback required:
- ROW_NUMBER() ranking included from the start — prompt library update working
- % of Total Revenue included from the start — prompt library update working
- Global average calculated correctly once in its own CTE — not recalculated per row
- Used `* 1.0` to force float division in SQLite — inferred without being told
- Used `ON 1 = 1` CROSS JOIN pattern with an inline comment explaining why
- Full comment header block applied correctly

The V1 query is saved below for reference. The evaluation follows.
```sql
WITH genre_stats AS (
    SELECT
        g.GenreId,
        g.Name                                          AS genre_name,
        SUM(il.UnitPrice * il.Quantity)                 AS total_revenue_raw,
        SUM(il.Quantity)                                AS total_tracks_sold
    FROM Genre g
    INNER JOIN Track t
        ON t.GenreId = g.GenreId
    INNER JOIN InvoiceLine il
        ON il.TrackId = t.TrackId
    GROUP BY
        g.GenreId,
        g.Name
),
global_avg AS (
    SELECT
        SUM(total_revenue_raw) * 1.0 / SUM(total_tracks_sold) AS avg_rev_per_track_global
    FROM genre_stats
),
genre_benchmarked AS (
    SELECT
        gs.genre_name,
        gs.total_revenue_raw,
        gs.total_tracks_sold,
        gs.total_revenue_raw * 1.0 / gs.total_tracks_sold  AS avg_rev_per_track_raw,
        ga.avg_rev_per_track_global,
        CASE
            WHEN (gs.total_revenue_raw * 1.0 / gs.total_tracks_sold)
                 > ga.avg_rev_per_track_global THEN 'Above Average'
            WHEN (gs.total_revenue_raw * 1.0 / gs.total_tracks_sold)
                 = ga.avg_rev_per_track_global THEN 'Average'
            ELSE                                    'Below Average'
        END                                                 AS performance_label
    FROM genre_stats gs
    INNER JOIN global_avg ga
        ON 1 = 1
)
SELECT
    ROW_NUMBER() OVER (ORDER BY total_revenue_raw DESC)         AS "Rank",
    genre_name                                                   AS "Genre",
    PRINTF('$%.2f', COALESCE(total_revenue_raw, 0))             AS "Total Revenue",
    total_tracks_sold                                            AS "Tracks Sold",
    PRINTF('$%.2f', COALESCE(avg_rev_per_track_raw, 0))         AS "Avg Revenue per Track",
    PRINTF('$%.2f', COALESCE(avg_rev_per_track_global, 0))      AS "Global Avg Revenue per Track",
    PRINTF('%.1f%%',
        COALESCE(total_revenue_raw, 0) * 100.0
        / SUM(total_revenue_raw) OVER ()
    )                                                            AS "% of Total Revenue",
    performance_label                                            AS "vs. Global Average"
FROM genre_benchmarked
ORDER BY
    total_revenue_raw DESC;
```

**V1 Query Results:**

| Rank | Genre | Total Revenue | Tracks Sold | Avg Rev/Track | Global Avg | % of Total | vs. Global Avg |
|------|-------|---------------|-------------|---------------|------------|------------|----------------|
| 1 | Rock | $826.65 | 835 | $0.99 | $1.04 | 35.5% | Below Average |
| 2 | Latin | $382.14 | 386 | $0.99 | $1.04 | 16.4% | Below Average |
| 3 | Metal | $261.36 | 264 | $0.99 | $1.04 | 11.2% | Below Average |
| 4 | Alternative & Punk | $241.56 | 244 | $0.99 | $1.04 | 10.4% | Below Average |
| 5 | TV Shows | $93.53 | 47 | $1.99 | $1.04 | 4.0% | Above Average |
| 6 | Jazz | $79.20 | 80 | $0.99 | $1.04 | 3.4% | Below Average |
| 7 | Blues | $60.39 | 61 | $0.99 | $1.04 | 2.6% | Below Average |
| 8 | Drama | $57.71 | 29 | $1.99 | $1.04 | 2.5% | Above Average |
| 9 | R&B/Soul | $40.59 | 41 | $0.99 | $1.04 | 1.7% | Below Average |
| 10 | Classical | $40.59 | 41 | $0.99 | $1.04 | 1.7% | Below Average |
| 11 | Sci Fi & Fantasy | $39.80 | 20 | $1.99 | $1.04 | 1.7% | Above Average |
| 12 | Reggae | $29.70 | 30 | $0.99 | $1.04 | 1.3% | Below Average |
| 13 | Pop | $27.72 | 28 | $0.99 | $1.04 | 1.2% | Below Average |
| 14 | Soundtrack | $19.80 | 20 | $0.99 | $1.04 | 0.9% | Below Average |
| 15 | Comedy | $17.91 | 9 | $1.99 | $1.04 | 0.8% | Above Average |
| 16 | Hip Hop/Rap | $16.83 | 17 | $0.99 | $1.04 | 0.7% | Below Average |
| 17 | Bossa Nova | $14.85 | 15 | $0.99 | $1.04 | 0.6% | Below Average |
| 18 | Alternative | $13.86 | 14 | $0.99 | $1.04 | 0.6% | Below Average |
| 19 | World | $12.87 | 13 | $0.99 | $1.04 | 0.6% | Below Average |
| 20 | Science Fiction | $11.94 | 6 | $1.99 | $1.04 | 0.5% | Above Average |
| 21 | Heavy Metal | $11.88 | 12 | $0.99 | $1.04 | 0.5% | Below Average |
| 22 | Electronica/Dance | $11.88 | 12 | $0.99 | $1.04 | 0.5% | Below Average |
| 23 | Easy Listening | $9.90 | 10 | $0.99 | $1.04 | 0.4% | Below Average |
| 24 | Rock And Roll | $5.94 | 6 | $0.99 | $1.04 | 0.3% | Below Average |

---

## My Critical Evaluation

The V1 output was structurally clean — the prompt library updates eliminated the 
ranking and percentage issues that required feedback in the previous two case 
studies. But after reviewing the results, I had three substantive issues to address.

**1. The "vs. Global Average" metric is misleading**

This is the most important issue — and it's one the AI couldn't have caught because 
it requires product domain knowledge to recognize.

Looking at the output, the pattern is immediately obvious: every $0.99 track is 
labeled "Below Average" and every $1.99 TV show or movie is labeled "Above Average." 
The performance label isn't measuring genre popularity, catalog efficiency, or 
customer demand — it's measuring price point. Music tracks are priced at $0.99. 
Video content is priced at $1.99. The global average lands at $1.04 — just above 
the $0.99 music floor — which means almost every music genre automatically fails 
the benchmark regardless of how well it actually sells.

A good report shouldn't have meaningless columns. Each metric should provide 
actionable context for leadership to make informed decisions. This one doesn't.

The better metric is **revenue per unique track in the catalog** — how much revenue 
each available track is generating on average. This measures catalog efficiency: 
a genre with 500 tracks generating $826 has very different economics than a genre 
with 10 tracks generating $826. The first has a deep catalog that's modestly 
utilized. The second has a tight catalog that's working extremely hard.

I replaced the "vs. Global Average" column with a catalog efficiency metric: 
revenue divided by the number of unique tracks available in that genre — not 
tracks sold, but tracks that exist in the catalog. This required adding a fourth 
CTE to count unique tracks per genre from the Track table.

**2. Column order is not intuitive**

The "vs. Global Average" column appeared at the far right — separated from the 
"Avg Revenue per Track" column it's commenting on. In a report, comparative 
context should sit immediately next to the metric it references. I moved the 
performance label to appear directly after "Avg Revenue per Track" so the reader's 
eye naturally flows: here's the number, here's what it means.

**3. No total row**

The output has no summary row showing total revenue and total percentage across 
all genres. Adding a total row serves two purposes: it gives leadership an instant 
sanity check that percentages sum to 100%, and it shows the full revenue picture 
at a glance. I added this using a UNION ALL pattern at the bottom of the final 
SELECT.

**4. Rock and Roll anomaly — flagged for investigation**

Rock is our #1 genre with $826.65 in revenue and 835 tracks sold. Rock And Roll 
sits at #24 with just $5.94 and 6 tracks sold. In most music catalogs, Rock and 
Roll is the parent genre of Rock — not a separate category. This could indicate 
a catalog tagging issue where a small number of tracks are labeled "Rock And Roll" 
instead of "Rock." If those tracks were recategorized, Rock's numbers would shift 
slightly. Worth flagging to the product team before presenting this report.

**The bottom line:** The prompt library updates from Cases 1 and 2 eliminated two 
recurring structural issues before V1 was even written. The remaining issues required 
product domain knowledge — specifically understanding that track pricing creates a 
misleading benchmark — and a presentation judgment call about column order. That's 
the human layer: knowing not just whether the query runs, but whether the output 
actually means something to the person reading it.
