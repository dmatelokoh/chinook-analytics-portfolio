# Case Study 2: Who Are Our Best Customers?
## Customer Lifetime Value Segmentation

---

## The Business Question

In any digital commerce business, a small percentage of customers typically drive a disproportionate share of revenue. But without segmentation, every customer looks the same in a database: just another row in the Customer table.

**The core question:** Who are our highest-value customers, how should we segment our customer base by lifetime value, and what does each segment tell us about retention risk and revenue concentration?

The goal is to understand where revenue is concentrated, which customers deserve different treatment, and whether the segmentation itself reveals anything about the business model.

---

## What the AI Got Right — and Where I Overruled It

The V1 query was structurally sound. The AI calculated lifetime spend from InvoiceLine (UnitPrice × Quantity), used NULLIF() to guard against division by zero in the AOV calculation, and applied the full CTE structure and comment block from the context prime. Two iterations, mostly clean.

But the tier thresholds were wrong, and no prompt can prevent that.

**The tier recalibration:** The AI set Platinum at ≥$45, Gold at ≥$35, Silver at ≥$25. Reasonable guesses, but I checked the actual spend distribution before accepting them. The data showed a dense cluster at $37.62, a small group above $40, and only 5 customers above $45. With the AI's thresholds, the vast majority of customers would have landed in Gold or Silver, making the tiers statistically meaningless.

I recalibrated based on the actual distribution:

| Tier | Threshold | Rationale |
|------|-----------|-----------|
| Platinum | ≥ $45.00 | Top 5 customers, clearly separated from the pack |
| Gold | ≥ $40.00 | Above the main cluster |
| Silver | ≥ $37.62 | The dense core of the customer base |
| Bronze | < $37.62 | Below cluster |

The AI can build the CASE WHEN logic. It can't look at the distribution and decide where the natural breaks are. That part is on the analyst.

These thresholds are static but data-informed. A more dynamic alternative using PERCENT_RANK() to automatically recalibrate as the customer base grows would be better for an ongoing dashboard. For a fixed dataset and a marketing use case, static thresholds are more stable: a customer's tier won't shift just because new customers joined.

**Prompt library update:** After two case studies, I noticed the AI consistently omitted ranking columns and percentage-of-total columns. Rather than catching this in every evaluation, I added a standing instruction to the context prime: *"For any query that ranks or compares rows, always include ROW_NUMBER() and a percentage-of-total column unless I say otherwise."* That one addition eliminated two recurring issues going forward.

---

## The Results

**Tier Summary:**

| Value Tier | Customers | Total Tier Revenue | % of Total Revenue |
|------------|-----------|-------------------|-------------------|
| Platinum | 5 | $235.10 | 10.10% |
| Gold | 9 | $379.58 | 16.30% |
| Silver | 44 | $1,677.28 | 72.03% |
| Bronze | 1 | $36.64 | 1.57% |
| **TOTAL** | **59** | **$2,328.60** | **100.00%** |

*Verification pass confirmed: all percentages sum to ~100%, tier revenues reconcile to the global total, AOV calculations match across all 59 rows. No duplicate customers, no orphaned invoices.*

---

## The Business Insight

### 72% of Revenue in Silver Is a Pricing Story, Not a Segmentation Problem

The most interesting finding here is that 72% of total revenue sits in Silver. That looks like a segmentation failure until you understand the product.

The Chinook catalog is predominantly priced at $0.99 and $1.99 per track. When customers buy similar volumes at nearly identical price points, spend clusters are almost inevitable because there's not enough price variance to create dramatic separation. The gap between our #1 customer ($49.62) and our #59 customer ($36.64) is only $13 over their entire lifetime. The tiers are real, but the compression is structural: a business model conversation, not a data quality one.

### Every Customer Made Exactly 7 Purchases

Almost every customer made exactly 7 purchases. One customer (Puja Srivastava) made 6. That's an unusually consistent pattern across 59 customers in 24 countries.

When you see uniform behavior across a diverse customer base, you look for an external cause. The questions this raised: are the purchase dates clustered around specific events? Is there a seasonal pattern? Or is this a data artifact, a byproduct of how the sample database was generated?

SQL couldn't answer those questions. This finding is what directly motivated the Python case study, using datetime operations and visualizations to test whether the purchase pattern is organic, seasonal, or artificial.

The one exception, Puja Srivastava at 6 purchases, actually has a higher AOV ($6.11) than most Silver customers, suggesting she's under-developed rather than low-value. Whether that's a tenure issue or a disengagement issue is something the Python cohort analysis can answer.

### Platinum Customers Deserve Disproportionate Attention

Platinum customers spend more in total and more per transaction. Helena Holý's AOV is $7.09 versus $5.37 for the average Silver customer. That $1.72 difference per purchase adds up over a lifetime. They're higher-quality buyers, not just higher spenders.

The retention strategy by tier: Platinum gets white-glove treatment (early access, personalized recommendations). Gold gets upsell campaigns, since they're one nudge from Platinum. Silver gets volume retention (prevent churn, don't try to upgrade). Bronze gets monitored.

### Our Best Customers Don't Come From Our Biggest Markets

The USA is our #1 market by revenue. But our top 5 customers, every Platinum customer, come from five different countries: Czech Republic, USA, Chile, Hungary, and Ireland. Canada is our #2 market overall but doesn't appear in the customer rankings until #16.

Country-level revenue analysis and customer-level value analysis tell different stories and should inform different decisions. One is a media buying conversation (where to spend acquisition budget). The other is a CRM conversation (who to protect and develop).
