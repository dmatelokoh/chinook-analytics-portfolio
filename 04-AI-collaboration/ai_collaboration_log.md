# AI Collaboration Log
## How I Work With AI

---

## Overview

This project was built with AI as a collaborator, and that was a deliberate choice.
AI lets me work through more complex analysis in less time. That matters because the value I bring isn't writing SQL syntax from memory; it's knowing which questions to ask, when the output doesn't make business sense, and what a marketing team would actually do with the result. Using AI for the technical execution gave me more time to focus on that critical thinking, which is where the real insight comes from.

It's also a practical skill. AI tools are becoming standard in analytics workflows, and knowing how to use them well (structured prompts, critical evaluation, iterative refinement) is something I want to demonstrate directly, not just list on a resume.

Every query in this portfolio went through the same structured workflow: a context-primed prompt, a critical evaluation, an iterative feedback loop, a verification pass, and a business insight written from my own marketing experience. The AI handled syntax and structure. I handled judgment, domain knowledge, and business framing.

What follows is a breakdown of the specific AI collaboration techniques demonstrated across the case studies, with concrete examples from the actual work.

---

## 1. Prompt Engineering: Building Instructions That Work

For this project I used an 8-part prompt framework consistently across all three case studies: Persona, Task, Context, Constraints, Format, References, Audience, and Evaluate. Each section serves a specific purpose. The Persona section establishes non-negotiable coding standards, the Context section provides the schema, the Constraints section prevents common SQLite compatibility errors before they happen.

**Concrete example:** In Case Study 1, without an explicit Constraints section specifying SQLite-compatible functions, an AI tool might return `FORMAT()` or `TOP`, functions that don't exist in SQLite. The Constraints section eliminated that failure mode entirely before the first line of SQL was written.

The prompt framework itself was developed using meta-prompting: asking the AI "what are the key components of a well-structured technical prompt?" and using that output to build the framework.

---

## 2. Context Priming: Starting Every Session Right

I opened each case study in a fresh AI session with a full context prime (database, coding standards, audience, style conventions) so the AI worked from a clean baseline every time rather than a degrading context window.

**Concrete example:** In Case Study 2, the context prime included seven non-negotiable coding standards in the Persona section: always use CTEs, always use PRINTF for currency, always use COALESCE for NULLs, never use SQLite-incompatible functions, and so on. The V1 output applied all seven correctly without a single reminder.

---

## 3. Prompt Library Management: Prompts as Living Documents

A prompt library is a personal collection of reusable, versioned prompts that get updated every time a gap in the output is discovered. The goal is to make V1 outputs progressively closer to production-ready, so less time is spent on feedback loops and more time is spent on analysis.

**Concrete example:** After Case Study 1, I noticed the AI consistently omitted a ranking column and a percentage-of-total column. I added two lines to the Persona section of my context prime:

> *"For any query that ranks or compares rows, always include:*
> *1. An explicit ROW_NUMBER() ranking column as the first column*
> *2. A percentage of total column showing each row's share of the relevant aggregate"*

By Case Study 3, both columns appeared in V1 without any feedback required. The gap between V1 and production-ready narrowed with each iteration because the instructions got more precise.

---

## 4. Critical Evaluation: The Human Layer

The AI can generate output quickly. Knowing whether that output is correct, meaningful, and ready for a leadership audience is a separate skill.

**Concrete example:** In Case Study 3, the AI returned a "vs. Global Average" performance label that looked reasonable on the surface. But after reviewing the results, the pattern was clear: every $0.99 music track was labeled "Below Average" and every $1.99 video item was labeled "Above Average." The label was measuring price point, not genre performance. Catching that required knowing how the catalog is priced and what a marketing team actually needs from a genre report.

I replaced the misleading label with a catalog efficiency metric (revenue per unique track in the catalog) that measures how hard each available track is working regardless of price point.

---

## 5. Iterative Prompting: Closing the Gap

No prompt produces perfect output on the first pass. The workflow that closes the gap between V1 and production-ready is straightforward: evaluate the output, identify specific gaps, reformat the evaluation as a structured feedback prompt, and feed it back to the AI.

**Concrete example:** In Case Study 2, the V1 output was missing five things: a ranking column, data-driven tier thresholds, a percentage of total revenue column, presentation-ready column names, and a tier summary query. Rather than sending a vague "please improve this" message, I reformatted each gap as a numbered, specific instruction and sent it as a single structured feedback prompt. The V2 output addressed all five in one pass, including one unprompted addition: the AI added a "Changes: V2" section to the comment header block, inferring from the feedback that this was a versioned update worth documenting.

---

## 6. Prompt Precision: AI Executes Literally

One of the most important lessons from this project: AI executes instructions literally. It doesn't infer intent. If you want something removed, you have to say so explicitly. If you want something added in a specific position, you have to specify the position.

**Concrete example:** In Case Study 3, my first feedback prompt asked the AI to replace the "vs. Global Average" column with a new catalog efficiency metric. But my prompt also said "keep the existing performance label," a direct contradiction. The AI did exactly what I told it: it added the new column and kept the old one, because I hadn't explicitly said to remove it.

I caught the contradiction, updated the feedback prompt to explicitly state "Remove the existing 'vs. Global Average' performance label column entirely", and the column was cleanly dropped on the next iteration. The AI did what it was told. The instructions just needed to be clearer.

---

## 7. AI Self-Verification & Meta-Prompting — Catching What Math Can't

Every case study included a structured verification pass, asking the AI to check its own output against specific mathematical and logical criteria. But the most valuable part of every verification prompt was the final question: "Beyond these checks, what other verification methods would you recommend for this specific type of query?"

**Concrete example:** In Case Study 3, the standard verification checks all passed. The math was correct. But the meta-prompt returned a silent exclusion check that revealed Opera (a genre with 1 catalog track and zero sales) was being completely dropped by the INNER JOIN chain. The query was reporting against 24 genres when Chinook actually carries 25. Without the meta-prompt, that gap would never have been caught.

The fix required a judgment call: include Opera with $0.00 values to show dead catalog inventory, or exclude it with a footnote. I chose to include it, because for a report designed to inform catalog investment decisions, zero-revenue genres should be visible.

---

## 8. Adaptability — Knowing When to Change the Approach

Not every unexpected output is a bug to fix. Sometimes it tells you the approach itself needs to change.

**Concrete example:** In Case Study 2, the AI generated tier thresholds that were reasonable on their face. But after checking the actual spend distribution, I found that nearly every customer would land in Gold or Silver, making the tiers statistically meaningless. The thresholds needed to be recalibrated based on where natural breaks actually existed in the data, not where they sounded right.

This led to a broader observation: because Chinook's catalog is predominantly priced at $0.99 and $1.99, spend clusters are almost inevitable. The pricing structure creates natural compression in lifetime value, and that's a conversation about the business model, not data quality. Recognizing that distinction required marketing domain knowledge.

Every gap caught in evaluation became a prompt library update. Every verification failure became a new standard check. The workflow improved across all three case studies because I treated every unexpected output as information, not just a problem to fix.

---

## Summary

| Dimension | Where Demonstrated |
|-----------|-------------------|
| Prompt Engineering | 8-part framework, meta-prompting to build the framework itself |
| Context Priming | Fresh session + full context prime for each case study |
| Prompt Library Management | ROW_NUMBER() and % of total added after Case Study 1; carried forward |
| Critical Evaluation | Tier thresholds (CS2), misleading performance label (CS3) |
| Iterative Prompting | V1 → V2 feedback loops across all three case studies |
| Prompt Precision | Explicit removal instruction after contradictory feedback (CS3) |
| AI Self-Verification | Opera silent exclusion caught by meta-prompt (CS3) |
| Adaptability | Threshold recalibration (CS2), catalog efficiency metric (CS3) |
