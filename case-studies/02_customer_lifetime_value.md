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
