
# Case Study 3: What's Selling and What Isn't?
## Genre & Track Performance Benchmarking

---

## The Business Question

Chinook's catalog spans multiple genres — from Rock and Metal to Jazz, Classical, and beyond. But not all genres are created equal. Some punch above their weight relative to the size of their catalog. Others have large track libraries that generate disproportionately little revenue.

**The core question:** Which genres are over-performing or under-performing relative to the catalog average — and what does that tell us about where to focus product, marketing, and merchandising decisions?

This is not just a revenue ranking. The goal is to understand catalog efficiency: which genres generate the most revenue per available track, and which have deep catalogs sitting idle.

---

## What the AI Got Right — and Where I Overruled It

By Case Study 3, the prompt library updates from Cases 1 and 2 had eliminated the recurring structural issues. The V1 output included ROW_NUMBER(), percentage of total, the CROSS JOIN pattern, and a full comment header block — all without being reminded. The context prime was compounding.

But the V1 output had one critical flaw that required product domain knowledge to catch.

**The misleading benchmark:** The AI created a "vs. Global Average" column labeling each genre as Above Average, Average, or Below Average based on revenue per track sold. The pattern was immediately obvious: every $0.99 music genre was "Below Average" and every $1.99 video genre was "Above Average." The label wasn't measuring genre popularity or catalog efficiency — it was measuring price point. The global average landed at $1.04, just above the $0.99 music floor, so almost every music genre automatically failed the benchmark regardless of how well it actually sold.

A good report shouldn't have meaningless columns. I replaced the performance label with **revenue per unique catalog track** — how much revenue each available track in the genre's catalog generates on average. This measures catalog efficiency: a genre with 500 tracks generating $826 has very different economics than one with 10 tracks generating $826.

**The Opera discovery:** The verification pass surfaced this one. I asked the AI to run a meta-verification check for silently excluded data. It recommended a LEFT JOIN check against InvoiceLine — and found that Opera (1 catalog track, zero sales) was being dropped entirely by the INNER JOIN chain. The original report showed 24 genres; the database actually carries 25.

I chose to switch the InvoiceLine JOIN to LEFT JOIN and let Opera appear at the bottom with $0.00 values. For a report designed to inform catalog investment decisions, dead inventory is exactly the kind of finding that should surface — not be silently excluded.

**The prompt precision lesson:** When I told the AI to "replace" the old performance label with the new catalog efficiency metric, it added the new column but kept the old one too. I hadn't explicitly said "remove." AI executes instructions literally — if you want something gone, you have to say so. A small lesson that saved me from learning it on something bigger.

---

## The Results

**Final Query Results — 25 Genres + TOTAL:**

| Rank | Genre | Total Revenue | Tracks Sold | Avg Rev/Track | % of Total | Rev/Catalog Track |
|------|-------|---------------|-------------|---------------|------------|-------------------|
| 1 | Rock | $826.65 | 835 | $0.99 | 35.5% | $0.64 |
| 2 | Latin | $382.14 | 386 | $0.99 | 16.4% | $0.66 |
| 3 | Metal | $261.36 | 264 | $0.99 | 11.2% | $0.70 |
| 4 | Alternative & Punk | $241.56 | 244 | $0.99 | 10.4% | $0.73 |
| 5 | TV Shows | $93.53 | 47 | $1.99 | 4.0% | $1.01 |
| 6 | Jazz | $79.20 | 80 | $0.99 | 3.4% | $0.61 |
| 7 | Blues | $60.39 | 61 | $0.99 | 2.6% | $0.75 |
| 8 | Drama | $57.71 | 29 | $1.99 | 2.5% | $0.90 |
| 9 | R&B/Soul | $40.59 | 41 | $0.99 | 1.7% | $0.67 |
| 10 | Classical | $40.59 | 41 | $0.99 | 1.7% | $0.55 |
| 11 | Sci Fi & Fantasy | $39.80 | 20 | $1.99 | 1.7% | $1.53 |
| 12 | Reggae | $29.70 | 30 | $0.99 | 1.3% | $0.51 |
| 13 | Pop | $27.72 | 28 | $0.99 | 1.2% | $0.58 |
| 14 | Soundtrack | $19.80 | 20 | $0.99 | 0.9% | $0.46 |
| 15 | Comedy | $17.91 | 9 | $1.99 | 0.8% | $1.05 |
| 16 | Hip Hop/Rap | $16.83 | 17 | $0.99 | 0.7% | $0.48 |
| 17 | Bossa Nova | $14.85 | 15 | $0.99 | 0.6% | $0.99 |
| 18 | Alternative | $13.86 | 14 | $0.99 | 0.6% | $0.35 |
| 19 | World | $12.87 | 13 | $0.99 | 0.6% | $0.46 |
| 20 | Science Fiction | $11.94 | 6 | $1.99 | 0.5% | $0.92 |
| 21 | Heavy Metal | $11.88 | 12 | $0.99 | 0.5% | $0.42 |
| 22 | Electronica/Dance | $11.88 | 12 | $0.99 | 0.5% | $0.40 |
| 23 | Easy Listening | $9.90 | 10 | $0.99 | 0.4% | $0.41 |
| 24 | Rock And Roll | $5.94 | 6 | $0.99 | 0.3% | $0.49 |
| 25 | Opera | $0.00 | 0 | $0.00 | 0.0% | $0.00 |
| — | **TOTAL** | **$2,328.60** | **2,240** | **—** | **100.0%** | **—** |

*Verification pass confirmed: all percentages sum to 100%, genre revenues reconcile to the TOTAL row, catalog track counts verified against the Track table. No duplicate tracks, no orphaned line items.*

---

## The Business Insight

### Rock Is the Business — But the Top 4 Concentration Is the Real Story

Rock, Latin, Metal, and Alternative & Punk represent 73.5% of total revenue combined. Whether that's healthy or risky depends entirely on trend direction — and this snapshot can't answer that alone.

If those four genres are growing year-over-year, concentration is a feature — it tells us where to focus resources. If they're flat or declining, 73.5% in four categories with no strong second tier emerging is a vulnerability. The remaining 21 genres represent only 26.5% of revenue — not enough cushion if the top 4 soften. This is the strongest argument for the YoY trend analysis that the Python case study picks up.

### Sci Fi & Fantasy — The Hidden Efficiency Champion

Sci Fi & Fantasy ranks #11 by total revenue but #1 by catalog efficiency at $1.53 per catalog track. Even compared only against other video genres (TV Shows at $1.01, Drama at $0.90, Comedy at $1.05), it's a significant outlier within its own pricing tier.

That gap signals either a very tight, well-curated catalog or customer demand that exceeds the current catalog depth. Either way, it's worth investigating with the same framework applied to Rock — top titles, top buyers, geographic performance, and catalog gaps. If demand exceeds supply, targeted catalog expansion could move this genre up the revenue rankings without requiring new customers.

### Video Content Looks Attractive — But Get the COGS First

TV Shows, Drama, Comedy, Sci Fi & Fantasy, and Science Fiction all price at $1.99 — double the music track price. Their catalog efficiency figures are stronger than most music genres. On the surface, that's a clear argument for investing more in video content.

But the surface is misleading without cost data. Video licensing costs are fundamentally different from music track costs. A genre generating $1.53 per catalog track at 20% margin is worth less than one generating $0.99 at 70% margin. This is the one question the dataset can't answer — and it's the right question to bring to a leadership conversation.

### Bossa Nova — What Good Catalog Curation Looks Like

Every single track in the Bossa Nova catalog has sold at least once. 100% sell-through across 15 tracks. It represents only 0.6% of total revenue, but the catalog is perfectly matched to demand — a small, well-curated genre serving a specific audience efficiently. That's a model worth applying to underperforming genres before investing in catalog expansion.

### Opera and Alternative — The Cleanup Candidates

Opera has 1 track and zero sales. Alternative has the worst catalog efficiency at $0.35 per catalog track — approximately 40 tracks in the catalog with only 14 ever sold, roughly 35% sell-through. Both deserve investigation before any action: is the unsold inventory genuinely unpopular, or simply underpromoted? If it's been promoted and isn't selling, catalog pruning makes sense. If it's never been featured, low sell-through is a visibility problem, not a demand problem.

The Alternative / Alternative & Punk and Rock / Rock And Roll splits also suggest a catalog tagging issue worth flagging to the product team. If consolidated, the combined genres would rank differently — and the efficiency metrics would shift.

### The YoY Dimension — What This Report Can't Tell Us Alone

Almost every insight here would be sharpened by year-over-year trend data. Is Rock's 35.5% share growing or declining? Is Sci Fi & Fantasy's efficiency improving as the catalog expands? Is the global average revenue per track trending upward? A trend analysis would transform this snapshot into a strategic planning tool — and that's exactly what the Python case study investigates.
