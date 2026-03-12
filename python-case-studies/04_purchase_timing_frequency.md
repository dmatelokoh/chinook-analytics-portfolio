# Case Study 1: Is the Purchase Pattern Real?
## Purchase Timing & Frequency Analysis

---

## The Business Question

SQL Case Study 2 segmented customers by lifetime value and found something unexpected: almost every customer in the original Chinook database made exactly 7 purchases. In a customer base spanning 24 countries with different buying habits, that kind of uniformity doesn't happen organically.

I flagged three possible explanations in that case study: the purchase dates are clustered around external events (a sale, a release, a promotional push), there's a seasonal pattern driving consistent behavior, or it's a data artifact — a byproduct of how the sample database was generated rather than a reflection of real customer behavior.

SQL couldn't answer that question. A COUNT of purchases per customer tells you *how many* — not *when* or *how spread out*. To investigate timing, I needed datetime operations, rolling calculations, and visualizations that SQL alone doesn't support well.

That's what this notebook does. Using an expanded version of the Chinook database (~5,000 customers, ~54,000 invoices, 2019–2025), I'm testing whether purchase frequency looks organic, seasonal, or artificial. The core deliverables:

- **Inter-purchase interval analysis** — how many days pass between consecutive purchases, and what does the distribution look like?
- **Purchase date scatter plot** — are there visible clusters in when customers buy?
- **Monthly revenue time series** — does revenue follow seasonal patterns or stay flat?
- **Cohort retention analysis** — do customers stick around, or do they drop off after a predictable number of purchases?

If the pattern is organic, the distribution should be right-skewed with natural variance — not a tight cluster around a single number. If it's seasonal, we should see spikes aligned with calendar events. If it's artificial, we'll see suspicious uniformity that no real customer base would produce.

The answer shapes how a marketing team thinks about re-engagement timing, campaign cadence, and churn prediction.

---

## The Prompt I Gave the AI

Using the structured prompt framework from the SQL case studies, I built the following prompt in a fresh AI session. I uploaded my SQL Case Study 2 write-up as a reference file so the AI had context on the business question and the level of analytical depth I'm working at — but no prior knowledge of the expanded database or what the output should look like.

---

**Persona**
> Act as a senior data analyst who writes clean, well-commented Python code for business stakeholders. Code should be readable in a Jupyter notebook — not just functional, but structured so a non-technical reviewer can follow the logic.

**Task**
> Write Python code that pulls all invoice dates per customer from the database, calculates the inter-purchase interval (days between consecutive purchases) for each customer, and plots a frequency histogram showing the distribution of those intervals. Include summary statistics (mean, median, standard deviation) printed alongside the chart.

**Context**
> I'm working with a SQLite database called `chinook_expanded.db` — an expanded version of the Chinook database with ~5,000 customers and invoices spanning several years. The relevant table is Invoice (columns: InvoiceId, CustomerId, InvoiceDate, Total). I'm using pandas, matplotlib, and sqlite3.
>
> I've attached the SQL Case Study this analysis builds on. That case study found suspiciously uniform purchase frequency across the original customer base and flagged three possibilities: the dates are clustered around external events, there's a seasonal pattern, or it's a data artifact. This Python analysis is the follow-up investigation.

**Constraints**
> - Use `pd.read_sql()` to pull data
> - Calculate intervals using pandas `groupby` and `shift`, not SQL window functions
> - Drop each customer's first invoice from the interval calculation (no previous date to compare against)

**Format**
> Structure for a Jupyter notebook: markdown headers explaining each step, then the code, then the output. Follow clean visualization standards: title, axis labels, and a source note. Comments should explain *why*, not just *what*. Use descriptive variable names.

**References**
> I've attached my SQL Case Study 2 write-up as context for the business question and the level of analytical depth I'm looking for.

**Audience**
> This notebook will be reviewed by hiring managers evaluating my analytical thinking. The code should be readable, the visualization should tell a clear story, and the output should connect back to the business question.

**Evaluate**
> I'll check the first draft for: correct interval calculation (first invoice excluded, no off-by-one errors), a histogram that reveals the shape of the distribution, and whether the output actually helps answer the question — does this look organic, seasonal, or artificial?