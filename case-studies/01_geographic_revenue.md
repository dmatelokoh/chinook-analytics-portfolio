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
