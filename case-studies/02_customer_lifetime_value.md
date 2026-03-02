# Case Study 2: Who Are Our Best Customers?
## Customer Lifetime Value Segmentation

---

## The Business Question

Not all customers are created equal. In any digital commerce business, a small 
percentage of customers typically drive a disproportionate share of revenue — 
the classic 80/20 rule. But without segmentation, every customer looks the same 
in a database: just another row in the Customer table.

Leadership needs to know:

**The core question:** Who are our highest-value customers, how should we segment 
our customer base by lifetime value, and what does each segment tell us about 
retention risk and revenue concentration?

This is not just a ranking exercise. The goal is to understand:
- Which customers have spent the most with us over their lifetime?
- How does the customer base break down into meaningful value tiers?
- Are we dangerously dependent on a small number of high-value customers?
- What does average order value and purchase frequency look like by segment?

---

---

## The Prompt I Gave the AI

Using the structured prompt framework established in Case Study 1, here is the 
prompt I built for this analysis.

One technique I apply consistently when starting a new AI session is **Context Priming** 
— also called **Session Framing**. Rather than jumping straight into the task, I open 
every new chat by giving the AI the full context it needs to behave like a consistent, 
informed collaborator: the database I'm working in, the coding standards I follow, 
the audience I'm writing for, and the style conventions I expect.

This matters because AI has no memory between sessions. Without context priming, 
a new chat is a blank slate — the AI doesn't know you're working in SQLite, doesn't 
know you prefer CTEs over nested subqueries, and doesn't know your output is going 
to a non-technical marketing director. You'd have to re-specify all of that in every 
single prompt, or accept inconsistent outputs.

Context priming solves this by front-loading everything the AI needs to know once, 
at the start of the session — so every subsequent prompt in that chat benefits from 
the established context without repeating it.

A natural question is: why not just continue the same chat from Case Study 1 
instead of starting fresh and re-priming? There are a few reasons I deliberately 
choose to start new sessions for each case study:

**1. Context window degradation**
As a conversation gets longer, AI models have to manage an increasing amount of 
information in their context window — the total amount of text they can "hold in 
mind" at once. In very long sessions, early instructions and established patterns 
can get deprioritized as new content fills the window. Starting fresh guarantees 
the AI is working from a clean, focused context — not a cluttered one.

**2. Accumulated errors carry forward**
If the AI made a subtle mistake or developed a bad habit mid-conversation — a 
slightly wrong interpretation of the schema, a formatting inconsistency — 
continuing that session carries the error forward. A new session with a strong 
context prime resets to a clean baseline.

**3. Portability and reproducibility**
A well-written context prime is reusable. If I switch AI tools — from Claude to 
Gemini to GPT — I can drop the same prime into any new session and get consistent 
outputs. A long running conversation is locked to one tool and one session. A 
context prime is a portable asset.

**4. Professional discipline**
In a real analytics team, you can't rely on "the AI remembers what we talked about 
last week." Documenting your context as a structured prime means your workflow is 
repeatable, shareable with teammates, and not dependent on any single conversation 
surviving.

The context prime is essentially a reusable session configuration — it takes 
60 seconds to paste and saves significant cleanup time downstream.

---

**🧠 Persona**
> You are a senior data analyst working inside Chinook Digital Media. You write 
> clean, well-commented SQLite-compatible SQL for a marketing team. You follow 
> these standards in every query you write:

> - Always use CTEs (WITH ... AS) instead of nested subqueries
> - Always format currency with PRINTF('$%.2f', ...)
> - Always use COALESCE() to handle potential NULLs in formatted columns
> - Always use INNER JOIN unless a LEFT JOIN is explicitly required
> - Always add a formal comment header block at the top of every query
> - Never use reserved words as aliases (avg, total, count, etc.)
> - Never use SQLite-incompatible functions (no FORMAT(), TOP, ISNULL())

> These are non-negotiable standards. Apply them to every query in this session 
> without being reminded.

**🎯 Task**
> Write a SQL query that calculates total lifetime spend, number of purchases, 
> and average order value per customer. Then segment customers into value tiers 
> (Platinum, Gold, Silver, Bronze) based on their total lifetime spend.

**📋 Context**
> I'm working in the Chinook SQLite database. The relevant tables are:
> - Customer (CustomerId, FirstName, LastName, Country)
> - Invoice (InvoiceId, CustomerId, Total)
> - InvoiceLine (InvoiceLineId, InvoiceId, UnitPrice, Quantity)
>
> Every invoice belongs to one customer via CustomerId. Each invoice has one or 
> more line items in InvoiceLine. I want lifetime value calculated at the 
> customer level — not the invoice level.

**🚧 Constraints**
> - Use CASE WHEN with explicit spend thresholds for tiering — not NTILE() — 
>   so the tier logic is transparent and business-meaningful, not just 
>   mathematical quartiles.
> - Tier labels must be: Platinum, Gold, Silver, Bronze.
> - All standards listed in the Persona section apply.

**📐 Format**
> Use two CTEs:
> - CTE 1: Calculate raw customer metrics (lifetime spend, purchase count, AOV)
> - CTE 2: Apply tier logic using CASE WHEN
>
> Final SELECT: customer full name, country, total lifetime spend (formatted), 
> number of purchases, average order value (formatted), tier label.
> Sort by total lifetime spend descending.

**📎 References**
> Follow the same CTE structure and commenting style you used in Case Study 1 
> of this project — readable top-to-bottom, each logical step clearly named 
> and commented.

**👥 Audience**
> The output will be used by the marketing team to build segment-specific 
> retention campaigns. Tier labels should be immediately actionable — a marketer 
> should be able to look at the output and know exactly which customers get 
> which treatment.

**✅ Evaluate**
> I'll check the first draft for: correct join type, accurate lifetime spend 
> calculation, transparent tier thresholds, formatted currency, and whether 
> the segmentation produces meaningful groups — not just four equal-sized buckets.

---

## The AI's Raw Output

Following the same structured prompt approach established in Case Study 1, I ran 
the context-primed prompt in a fresh AI session. The V1 output was notably stronger 
than Case Study 1's first draft — a direct result of the context priming doing its job.

A few things the AI got right unprompted:
- Calculated lifetime spend from InvoiceLine (UnitPrice × Quantity) rather than 
  Invoice.Total — more precise because it's driven by actual line item data
- Used NULLIF() to guard against division by zero in the AOV calculation
- Documented tier threshold rationale clearly in the comments
- Applied the full comment header block without being reminded

The V1 query is saved below for reference. The evaluation follows.
```sql
WITH customer_metrics AS (
    SELECT
        c.CustomerId,
        c.FirstName,
        c.LastName,
        c.Country,
        SUM(il.UnitPrice * il.Quantity)                             AS lifetime_spend,
        COUNT(DISTINCT i.InvoiceId)                                 AS purchase_count,
        SUM(il.UnitPrice * il.Quantity) / NULLIF(COUNT(DISTINCT i.InvoiceId), 0)
                                                                    AS avg_order_value
    FROM       Customer     c
    INNER JOIN Invoice      i   ON i.CustomerId    = c.CustomerId
    INNER JOIN InvoiceLine  il  ON il.InvoiceId    = i.InvoiceId
    GROUP BY
        c.CustomerId,
        c.FirstName,
        c.LastName,
        c.Country
),
customer_segments AS (
    SELECT
        CustomerId,
        FirstName,
        LastName,
        Country,
        lifetime_spend,
        purchase_count,
        avg_order_value,
        CASE
            WHEN lifetime_spend >= 45.00 THEN 'Platinum'
            WHEN lifetime_spend >= 35.00 THEN 'Gold'
            WHEN lifetime_spend >= 25.00 THEN 'Silver'
            ELSE                              'Bronze'
        END AS value_tier
    FROM customer_metrics
)
SELECT
    cs.FirstName || ' ' || cs.LastName                              AS customer_name,
    cs.Country,
    COALESCE(PRINTF('$%.2f', cs.lifetime_spend),  'N/A')           AS total_lifetime_spend,
    cs.purchase_count                                               AS number_of_purchases,
    COALESCE(PRINTF('$%.2f', cs.avg_order_value), 'N/A')           AS avg_order_value,
    cs.value_tier
FROM customer_segments cs
ORDER BY cs.lifetime_spend DESC;
```

---

## My Critical Evaluation

The V1 output was a strong foundation — but after running it and reviewing the 
results, I had five specific issues to address before this could go in front of 
a marketing team.

**1. No ranking column**
Same issue as Case Study 1 — the output sorts by lifetime spend descending but 
there's no explicit rank number. In a customer value report, a marketer should 
be able to instantly see that customer #1 is our highest-value relationship 
without counting rows. I added ROW_NUMBER() OVER (ORDER BY lifetime_spend DESC).

**2. Tier thresholds are not data-driven**
This is the most important issue. The AI's thresholds (Platinum ≥ $45, Gold ≥ $35, 
Silver ≥ $25) were reasonable guesses — but I checked the actual spend distribution 
before accepting them. Here's what the data shows:

| Spend Range | Customer Count | Observation |
|-------------|---------------|-------------|
| $46 — $50 | 4 customers | Clearly separated from the rest |
| $40 — $45 | ~15 customers | Above the main cluster |
| $37.62 — $39.62 | ~35 customers | The dense core cluster |
| Below $37.62 | 5 customers | Below cluster |

With the AI's original thresholds, the vast majority of customers would land in 
Gold or Silver — making the tiers statistically meaningless. I recalibrated based 
on the actual distribution:

| Tier | New Threshold | Rationale |
|------|--------------|-----------|
| Platinum | ≥ $45.00 | Top 4 customers — clearly separated |
| Gold | ≥ $40.00 | Above the main cluster |
| Silver | ≥ $37.62 | Mid cluster |
| Bronze | < $37.62 | Below cluster |

> **A note on threshold methodology:** The thresholds in this query are static, 
> but they were derived by examining the actual spend distribution — making them 
> data-informed rather than arbitrary. A more dynamic alternative would use 
> PERCENT_RANK() to automatically recalibrate tiers as the customer base grows:
>
> ```sql
> CASE
>     WHEN PERCENT_RANK() OVER (ORDER BY lifetime_spend) >= 0.90 THEN 'Platinum'
>     WHEN PERCENT_RANK() OVER (ORDER BY lifetime_spend) >= 0.75 THEN 'Gold'
>     WHEN PERCENT_RANK() OVER (ORDER BY lifetime_spend) >= 0.50 THEN 'Silver'
>     ELSE 'Bronze'
> END
> ```
>
> The tradeoff is consistency vs. adaptability. Static thresholds are more stable 
> for retention campaigns — a customer's tier won't shift just because new customers 
> joined. Dynamic percentiles are better for ongoing reporting dashboards where the 
> distribution matters more than individual stability. For a dataset this size and 
> a marketing use case, static thresholds win. But in a growing business, I'd 
> schedule a quarterly threshold review to keep the tiers meaningful over time.

**3. No percentage of total revenue per customer**
Knowing a customer's absolute spend is useful. Knowing they represent X% of total 
revenue is what triggers a retention decision. A customer who represents 2% of your 
total revenue churning is a very different emergency than one who represents 0.2%. 
I added a global revenue CTE and a pct_of_total_revenue column using the same 
CROSS JOIN pattern from Case Study 1.

**4. Column naming is not presentation-ready**
The AI used `customer_name` and `avg_order_value` as aliases — functional but not 
professional. Column headers in a stakeholder report should read like column headers, 
not variable names. I updated all aliases to use proper title case with spaces:
"Customer Name", "Total Lifetime Spend", "Avg Order Value", "Value Tier".

**5. No tier summary**
The raw output shows individual customers but gives no sense of how the customer 
base breaks down by tier. A marketing director's first question will be "how many 
Platinum customers do we have?" — and the current output doesn't answer that without 
manually counting rows. I added a second query that produces a tier summary: 
customer count per tier, total revenue per tier, and percentage of total revenue 
per tier.

**The bottom line:** Context priming produced a noticeably stronger V1 than Case 
Study 1's first draft — fewer structural issues, better comments, correct join logic 
from the start. But the tier threshold problem required human judgment that no 
prompt could replace. You have to look at the actual data distribution before 
deciding where to draw the lines. That's the analyst layer, not the AI layer.

---

> ### 📚 Prompt Library Update — Living, Breathing Prompts
>
> One of the most valuable habits I've built working with AI is maintaining a 
> **prompt library** — a personal collection of reusable, versioned prompts that 
> get updated every time I discover a gap in the output.
>
> After completing Case Study 1 and now Case Study 2, I noticed a recurring pattern:
> the AI consistently omitted two columns I always want in an analytical output —
> a ranking column and a percentage of total column. Neither was included in V1 
> of either query despite strong context priming.
>
> Rather than accepting this as something I'll just catch in every evaluation pass, 
> I treat it as a signal to update my prompt. Specifically, I'd add the following 
> to the **Persona / Standards** section of my context prime:
>
> > *"For any query that ranks or compares rows, always include:*
> > *1. An explicit ROW_NUMBER() ranking column as the first column*
> > *2. A percentage of total column showing each row's share of the relevant aggregate*
> > *Unless I explicitly tell you these are not needed."*
>
> This one addition to the context prime would have eliminated two of the five 
> issues in my Critical Evaluation — before the AI even wrote the first line of SQL.
>
> **This is the compounding value of prompt library management.** Each case study 
> makes the next prompt smarter. Over time, V1 outputs get closer and closer to 
> production-ready — not because the AI got better, but because the instructions 
> got more precise. The gap between V1 and V2 shrinks with every iteration.
>
> In a professional context, a well-maintained prompt library is a productivity 
> asset — the kind of institutional knowledge that makes an analyst faster and 
> more consistent over time, regardless of which AI tool they're using.

---

## Iterative Prompting — From V1 to V2

Following the same iterative prompting and prompt chaining approach established 
in Case Study 1, I reformatted my evaluation notes as a structured feedback prompt 
and fed them back to the AI.

**The feedback prompt I sent:**

> *"The V1 query was a strong start, but I have specific changes I need you to 
> incorporate. Please update the query with the following:*
>
> *1. **Add a ranking column** — Use ROW_NUMBER() OVER (ORDER BY lifetime_spend DESC) 
> as the first column in the final SELECT. Label it "Rank".*
>
> *2. **Recalibrate the tier thresholds** — After reviewing the actual spend 
> distribution, the original thresholds create unbalanced tiers. Use these 
> updated values instead:*
> - *Platinum: lifetime_spend ≥ $45.00*
> - *Gold: lifetime_spend ≥ $40.00*
> - *Silver: lifetime_spend ≥ $37.62*
> - *Bronze: lifetime_spend < $37.62*
>
> *3. **Add a percentage of total revenue column** — Add a third CTE that captures 
> global lifetime spend across all customers. Then add a pct_of_total_revenue column 
> in the final SELECT using the same CROSS JOIN pattern from Case Study 1. Round to 
> 2 decimal places.*
>
> *4. **Update all column aliases to title case** — Replace all snake_case aliases 
> with properly spaced, presentation-ready labels. Examples:*
> - *customer_name → "Customer Name"*
> - *total_lifetime_spend → "Total Lifetime Spend"*
> - *number_of_purchases → "Number of Purchases"*
> - *avg_order_value → "Avg Order Value"*
> - *value_tier → "Value Tier"*
>
> *5. **Add a tier summary query** — After the main customer-level query, add a 
> second standalone query that produces a tier summary showing:*
> - *Value tier label*
> - *Number of customers in that tier*
> - *Total revenue from that tier (formatted)*
> - *Percentage of total revenue from that tier*
>
> *Keep everything else — the CTE structure, join logic, NULLIF guard, inline 
> comments, and header block — exactly as it is."*

---
---

**What the prompt chaining achieved:**

| | V1 (Initial AI Output) | V2 (After Prompt Chaining) |
|---|---|---|
| Ranking column | ❌ | ✅ ROW_NUMBER() |
| Data-driven tier thresholds | ❌ | ✅ Recalibrated from distribution |
| % of total revenue per customer | ❌ | ✅ CROSS JOIN pattern |
| Global revenue benchmark CTE | ❌ | ✅ CTE 3 added |
| Title case column aliases | ❌ | ✅ Presentation-ready |
| Tier summary query | ❌ | ✅ Standalone second query |
| V2 changelog in header block | ❌ | ✅ AI inferred this unprompted |
| NULLIF guard on division | ✅ | ✅ |
| Correct join type | ✅ | ✅ |
| Inline comments | ✅ | ✅ |

One thing worth noting: the AI added a "Changes: V2" section to the comment 
header block without being asked — it inferred from the feedback prompt that 
this was a versioned update and documented it accordingly. That's the compounding 
value of strong context priming carrying through the session.

---

**V2 Main Query Results:**

| Rank | Customer Name | Country | Total Lifetime Spend | Purchases | Avg Order Value | % of Total | Value Tier |
|------|--------------|---------|---------------------|-----------|-----------------|------------|------------|
| 1 | Helena Holý | Czech Republic | $49.62 | 7 | $7.09 | 2.13% | Platinum |
| 2 | Richard Cunningham | USA | $47.62 | 7 | $6.80 | 2.05% | Platinum |
| 3 | Luis Rojas | Chile | $46.62 | 7 | $6.66 | 2.00% | Platinum |
| 4 | Ladislav Kovács | Hungary | $45.62 | 7 | $6.52 | 1.96% | Platinum |
| 5 | Hugh O'Reilly | Ireland | $45.62 | 7 | $6.52 | 1.96% | Platinum |
| 6 | Frank Ralston | USA | $43.62 | 7 | $6.23 | 1.87% | Gold |
| 7 | Julia Barnett | USA | $43.62 | 7 | $6.23 | 1.87% | Gold |
| 8 | Fynn Zimmermann | Germany | $43.62 | 7 | $6.23 | 1.87% | Gold |
| 9 | Astrid Gruber | Austria | $42.62 | 7 | $6.09 | 1.83% | Gold |
| 10 | Victor Stevens | USA | $42.62 | 7 | $6.09 | 1.83% | Gold |
| 11 | Terhi Hämäläinen | Finland | $41.62 | 7 | $5.95 | 1.79% | Gold |
| 12 | František Wichterlová | Czech Republic | $40.62 | 7 | $5.80 | 1.74% | Gold |
| 13 | Isabelle Mercier | France | $40.62 | 7 | $5.80 | 1.74% | Gold |
| 14 | Johannes Van der Berg | Netherlands | $40.62 | 7 | $5.80 | 1.74% | Gold |
| 15 | Luís Gonçalves | Brazil | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 16 | François Tremblay | Canada | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 17 | Bjørn Hansen | Norway | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 18 | Jack Smith | USA | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 19 | Dan Miller | USA | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 20 | Heather Leacock | USA | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 21 | João Fernandes | Portugal | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 22 | Wyatt Girard | France | $39.62 | 7 | $5.66 | 1.70% | Silver |
| 23 | Jennifer Peterson | Canada | $38.62 | 7 | $5.52 | 1.66% | Silver |
| 24 | Tim Goyer | USA | $38.62 | 7 | $5.52 | 1.66% | Silver |
| 25 | Camille Bernard | France | $38.62 | 7 | $5.52 | 1.66% | Silver |
| 26 | Dominique Lefebvre | France | $38.62 | 7 | $5.52 | 1.66% | Silver |
| 27 | Joakim Johansson | Sweden | $38.62 | 7 | $5.52 | 1.66% | Silver |
| 28 | Manoj Pareek | India | $38.62 | 7 | $5.52 | 1.66% | Silver |
| 29 | Leonie Köhler | Germany | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 30 | Daan Peeters | Belgium | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 31 | Kara Nielsen | Denmark | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 32 | Eduardo Martins | Brazil | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 33 | Alexandre Rocha | Brazil | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 34 | Roberto Almeida | Brazil | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 35 | Fernanda Ramos | Brazil | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 36 | Mark Philips | Canada | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 37 | Frank Harris | USA | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 38 | Michelle Brooks | USA | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 39 | Kathy Chase | USA | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 40 | John Gordon | USA | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 41 | Patrick Gray | USA | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 42 | Robert Brown | Canada | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 43 | Edward Francis | Canada | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 44 | Martha Silk | Canada | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 45 | Aaron Mitchell | Canada | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 46 | Ellie Sullivan | Canada | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 47 | Madalena Sampaio | Portugal | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 48 | Hannah Schneider | Germany | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 49 | Niklas Schröder | Germany | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 50 | Marc Dubois | France | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 51 | Lucas Mancini | Italy | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 52 | Stanisław Wójcik | Poland | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 53 | Enrique Muñoz | Spain | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 54 | Emma Jones | United Kingdom | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 55 | Phil Hughes | United Kingdom | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 56 | Steve Murray | United Kingdom | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 57 | Mark Taylor | Australia | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 58 | Diego Gutiérrez | Argentina | $37.62 | 7 | $5.37 | 1.62% | Silver |
| 59 | Puja Srivastava | India | $36.64 | 6 | $6.11 | 1.57% | Bronze |

---

**V2 Tier Summary Results:**

| Value Tier | Number of Customers | Total Tier Revenue | % of Total Revenue |
|------------|--------------------|--------------------|-------------------|
| Silver | 44 | $1,677.28 | 72.03% |
| Gold | 9 | $379.58 | 16.30% |
| Platinum | 5 | $235.10 | 10.10% |
| Bronze | 1 | $36.64 | 1.57% |

---

## The Business Insight

*Interpreted through a digital marketing lens by Daniel Matel-Okoh*

---

### The Tier Structure Reveals a Pricing Reality

The most striking finding from the tier summary isn't which customers are Platinum 
— it's that 72% of total revenue sits in Silver. At first glance that looks like a 
segmentation problem. But the deeper explanation is a product pricing one.

The Chinook catalog is predominantly priced at $0.99 and $1.99 per track. When 
customers buy similar volumes of tracks at nearly identical price points, spend 
clusters are almost inevitable — there's simply not enough price variance in the 
product to create dramatic revenue separation between customers. This is why the 
gap between our #1 customer ($49.62) and our #59 customer ($36.64) is only $13 
over their entire customer lifetime.

This is a case where the data is correct, but the interpretation requires product 
context. A hiring manager who only looks at the numbers might conclude the 
segmentation is poorly designed. The reality is that the pricing structure creates 
natural compression — and that's a business model conversation, not a data quality 
one.

This is also where a more dynamic approach to tiering becomes worth exploring. 
Rather than fixed dollar thresholds, a PERCENT_RANK() approach would automatically 
adapt to the actual distribution — always surfacing the top 10% as Platinum 
regardless of how compressed the spend range is. For a catalog-priced business 
like Chinook, that's likely the more durable long-term solution.

---

### Purchase Frequency — A Signal Worth Investigating

Almost every customer made exactly 7 purchases. One customer (Puja Srivastava) 
made 6. That's an unusually consistent pattern across 59 customers in 24 countries.

In digital marketing, when you see uniform behavior across a diverse customer base, 
you look for an external cause — not an internal one. The questions I'd want to 
answer next:

- **Are the purchase dates clustered?** If most customers bought at similar times, 
  something external drove that behavior — a sale, a new album release, a promotional 
  email, a payday cycle.
- **Are there seasonal patterns?** A time-series analysis of invoice dates by customer 
  would answer this quickly.
- **Is this a data artifact?** In a sample dataset like Chinook, uniform purchase 
  counts can sometimes reflect how the data was generated rather than real customer 
  behavior. Worth flagging when presenting to stakeholders.

The next logical query would be a purchase date distribution analysis — pulling 
InvoiceDate by customer and looking for clustering. If the dates align, we have a 
promotional calendar opportunity. If they don't, the consistency is organic and tells 
us something genuinely interesting about this customer base's buying habits.

---

### Prioritize Platinum for Retention — The AOV Story

When asked which tier to prioritize for retention investment, the instinct might be 
Silver — after all, 72% of revenue lives there. But I'd argue Platinum deserves the 
highest-touch retention strategy, and the AOV column is why.

Platinum customers aren't just spending more in total — they're spending more per 
transaction. Helena Holý's AOV is $7.09. The average Silver customer's AOV is $5.37. 
That $1.72 difference per purchase compounds significantly over a customer lifetime. 
A Platinum customer isn't just a higher spender — they're a higher-quality buyer. 
They select higher-value items, buy more per session, or both.

In practice this means:
- **Platinum:** White-glove retention — early access to new releases, personalized 
  recommendations, loyalty rewards. The cost of losing one Platinum customer is 
  disproportionate to their headcount.
- **Gold:** Upsell and upgrade campaigns — these customers are one behavioral nudge 
  away from Platinum. Targeted promotions on higher-priced tracks or albums could 
  move them up.
- **Silver:** Volume retention — the tier is large and consistent. Focus on preventing 
  churn rather than upgrading spend. Subscription models or bundle offers would serve 
  this segment well.
- **Bronze:** Re-engagement — one customer, one data point. Monitor and investigate 
  before investing campaign budget.

---

### Puja Srivastava — The Anomaly Worth Watching

Puja is the only Bronze customer and the only customer with fewer than 7 purchases. 
But her AOV ($6.11) is higher than most Silver customers and comparable to Gold. 
She's not a low-value customer — she's an under-developed one.

In acquisition terms, she looks like a high-potential customer who hasn't been 
fully activated yet. The strategic question is whether her lower purchase count 
reflects disengagement or simply a shorter customer tenure. I'd want to look at 
her first purchase date relative to the rest of the customer base before drawing 
any conclusions. If she's a newer customer, she may naturally grow into Silver 
or Gold over time. If she's been around as long as everyone else and still only 
made 6 purchases, that's a re-engagement conversation.

---

### The Geographic Insight No One Expected

The USA is our #1 market by revenue. But our top 5 customers — every single 
Platinum tier customer — come from five different countries: Czech Republic, 
USA, Chile, Hungary, and Ireland.

This is a meaningful disconnect. At the country level, the US dominates. At the 
individual customer level, our most valuable relationships are geographically 
dispersed. That tells us two things:

**First**, high-value customers don't correlate with market size. The Czech Republic 
has our #1 customer (Helena Holý at $49.62) despite being a relatively small market 
overall. Chile has our #3. These are markets that wouldn't get budget priority in a 
country-level revenue analysis — but they're producing our best individual customers.

**Second**, Canada is our #2 market by total revenue but doesn't appear in the 
customer rankings until #16 (François Tremblay at $39.62). That means Canada's 
revenue strength comes from volume — more customers spending at average rates — 
not from a concentration of high-value relationships. That's a fundamentally 
different growth lever than a market like Czech Republic, where the opportunity 
is to find more customers like Helena Holý.

The strategic implication: country-level revenue analysis and customer-level value 
analysis tell different stories and should inform different decisions. One is a 
media buying conversation. The other is a CRM conversation.
