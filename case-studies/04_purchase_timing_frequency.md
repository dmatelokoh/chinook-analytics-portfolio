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
