# AI Collaboration Log
## How I Work With AI

---

## Overview

This project was built with AI as a collaborator, and that was a deliberate choice.

Using AI for the technical execution (writing SQL, building Python scripts, formatting output) freed up my time to focus on the part that matters most: deciding what questions to ask, catching when the numbers don't add up, and translating results into something a marketing team can act on. It let me take on more complex analysis than I could have completed in the same timeframe working solo, without sacrificing the critical thinking that makes the output useful.

AI tools are also becoming standard in analytics workflows. I wanted to demonstrate that I know how to use them well, not just that I've used them, so this portfolio documents the full process: structured prompts, critical evaluation, iterative refinement, and the judgment calls I made along the way.

Every query went through the same workflow: a context-primed prompt, a critical evaluation, an iterative feedback loop, a verification pass, and a business insight written from my own marketing experience. The AI handled syntax and structure. I handled judgment, domain knowledge, and business framing.

What follows is a breakdown of the specific AI collaboration techniques demonstrated across the case studies, with concrete examples from the actual work.

---

## 1. Prompt Engineering

For this project I used an 8-part prompt framework consistently across all case studies: Persona, Task, Context, Constraints, Format, References, Audience, and Evaluate. Each section serves a specific purpose. The Persona section establishes coding standards. The Context section provides the schema. The Constraints section prevents common SQLite compatibility errors before they happen.

**Concrete example:** In Case Study 1, without an explicit Constraints section specifying SQLite-compatible functions, the AI would default to `FORMAT()` or `TOP`, which don't exist in SQLite. The Constraints section caught that before the first line of SQL was written.

The prompt framework itself was developed using meta-prompting: I asked the AI "what are the key components of a well-structured technical prompt?" and used that output to build the framework.

---

## 2. Context Priming

I opened each case study in a fresh AI session with a full context prime (database schema, coding standards, audience, style conventions) so the AI started from a clean baseline every time rather than a degrading context window.

**Concrete example:** In Case Study 2, the context prime included seven coding standards in the Persona section: always use CTEs, always use PRINTF for currency, always use COALESCE for NULLs, never use SQLite-incompatible functions, and so on. The V1 output followed all seven without a single reminder.

---

## 3. Prompt Library Management

A prompt library is a personal collection of reusable, versioned prompts that get updated every time a gap in the output shows up. The goal is to get V1 outputs closer to production-ready over time, so less time goes to feedback loops and more goes to analysis.

**Concrete example:** After Case Study 1, I noticed the AI consistently left out a ranking column and a percentage-of-total column. I added two lines to the Persona section:

> *"For any query that ranks or compares rows, always include:*
> *1. An explicit ROW_NUMBER() ranking column as the first column*
> *2. A percentage of total column showing each row's share of the relevant aggregate"*

By Case Study 3, both columns appeared in V1 without any feedback. The gap between V1 and production-ready narrowed with each case study because the instructions got more specific.

---

## 4. Critical Evaluation

The AI generates output quickly. Knowing whether that output is correct, useful, and ready for a leadership audience is a separate skill.

**Concrete example:** In Case Study 3, the AI returned a "vs. Global Average" performance label that looked reasonable at first glance. But the pattern was clear after reviewing the results: every $0.99 music track was labeled "Below Average" and every $1.99 video item was labeled "Above Average." The label was measuring price point, not genre performance. Catching that required knowing how the catalog is priced and what a marketing team actually needs from a genre report.

I replaced the misleading label with a catalog efficiency metric (revenue per unique track in the catalog) that measures how hard each track is working regardless of price point.

---

## 5. Iterative Prompting

No prompt produces perfect output on the first pass. The workflow for closing the gap between V1 and production-ready is: evaluate the output, identify specific gaps, write those gaps as structured feedback, and send it back.

**Concrete example:** In Case Study 2, the V1 output was missing five things: a ranking column, data-driven tier thresholds, a percentage of total revenue column, presentation-ready column names, and a tier summary query. Instead of sending a vague "please improve this," I wrote each gap as a numbered instruction and sent them as a single feedback prompt. V2 addressed all five in one pass. The AI also added a "Changes: V2" section to the comment header block on its own, inferring from the structured feedback that this was a versioned update worth documenting.

---

## 6. Prompt Precision

One of the most useful lessons from this project: AI executes instructions literally. It doesn't infer intent. If you want something removed, you have to say so. If you want something added in a specific position, you have to specify the position.

**Concrete example:** In Case Study 3, my first feedback prompt asked the AI to replace the "vs. Global Average" column with a new catalog efficiency metric. But the same prompt also said "keep the existing performance label," which was a direct contradiction. The AI did exactly what I told it: added the new column and kept the old one, because I never said to remove it.

I caught the contradiction, updated the prompt to explicitly say "Remove the existing 'vs. Global Average' performance label column entirely," and the column was dropped on the next pass. The AI did what it was told. The instructions just needed to be clearer.

---

## 7. AI Self-Verification & Meta-Prompting

Every case study included a structured verification pass where I asked the AI to check its own output against specific mathematical and logical criteria. But the most useful part of every verification prompt was the final question: "Beyond these checks, what other verification methods would you recommend for this type of query?"

**Concrete example:** In Case Study 3, the standard verification checks all passed. The math was correct. But the meta-prompt returned a silent exclusion check that revealed Opera (a genre with 1 catalog track and zero sales) was being dropped by the INNER JOIN chain. The query was reporting 24 genres when Chinook actually carries 25. Without that question, the gap would never have been caught.

The fix required a judgment call: include Opera with $0.00 values to show dead catalog inventory, or exclude it with a footnote. I chose to include it, because for a report designed to inform catalog investment decisions, zero-revenue genres should be visible.

---

## 8. Adaptability

Not every unexpected output is a bug. Sometimes it tells you the approach needs to change.

**Concrete example:** In Case Study 2, the AI generated tier thresholds that looked reasonable. But after checking the actual spend distribution, nearly every customer landed in Gold or Silver, making the tiers meaningless. The thresholds needed to come from where natural breaks actually existed in the data, not where they sounded right.

This led to a broader observation: because Chinook's catalog is mostly priced at $0.99 and $1.99, spend clusters are almost unavoidable. The pricing structure compresses lifetime value, and that's a conversation about the business model, not data quality. Recognizing that distinction required domain knowledge from marketing.

Every gap caught in evaluation became a prompt library update. Every verification failure became a new standard check. The workflow improved across all three case studies because I treated unexpected output as information, not just a problem to fix.
