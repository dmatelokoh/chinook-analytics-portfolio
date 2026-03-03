# AI Collaboration Log
## How I Work With AI — A Practical Demonstration

---

## Overview

This project was built with AI as a deliberate collaborator — not a shortcut. 
Every query in this portfolio went through the same structured workflow: a 
context-primed prompt, a critical evaluation, an iterative feedback loop, a 
verification pass, and a business insight written from my own marketing 
experience. The AI handled syntax and structure. I handled judgment, domain 
knowledge, and business framing.

What follows is a breakdown of the specific AI collaboration techniques 
demonstrated across the three case studies — with concrete examples from the 
actual work. This isn't a theoretical overview of AI capabilities. It's a 
record of how I actually used AI to produce something I'm proud to put my name on.

The through-line across all three case studies is this: **AI made me faster. 
My judgment made the output useful.**

---

## 1. Prompt Engineering — Building Instructions That Work

The foundation of effective AI collaboration is the prompt. A vague prompt 
produces vague output. A structured prompt produces structured output.

For this project I developed an 8-part prompt framework used consistently 
across all three case studies: Persona, Task, Context, Constraints, Format, 
References, Audience, and Evaluate. Each section serves a specific purpose — 
the Persona section establishes non-negotiable coding standards, the Context 
section provides the schema, the Constraints section prevents common SQLite 
compatibility errors before they happen.

**Concrete example:** In Case Study 1, without an explicit Constraints section 
specifying SQLite-compatible functions, an AI tool might return `FORMAT()` or 
`TOP` — functions that don't exist in SQLite. The Constraints section eliminated 
that failure mode entirely before the first line of SQL was written.

The prompt framework itself was developed using meta-prompting — asking the AI 
"what are the key components of a well-structured technical prompt?" and using 
that output to build the framework. Using AI to improve how you prompt AI is 
one of the highest-leverage habits in this space.

---

## 2. Context Priming — Starting Every Session Right

AI has no memory between sessions. Without context priming, every new chat 
starts from zero — the AI doesn't know your database, your coding standards, 
your audience, or your style preferences. You either re-specify everything in 
every prompt, or you accept inconsistent outputs.

Context priming solves this by front-loading everything the AI needs to know 
at the start of each session — once — so every subsequent prompt benefits from 
the established context without repeating it.

**Concrete example:** In Case Study 2, the context prime included seven 
non-negotiable coding standards in the Persona section: always use CTEs, always 
use PRINTF for currency, always use COALESCE for NULLs, never use SQLite-incompatible 
functions, and so on. The V1 output applied all seven correctly without a single 
reminder. That's the context prime doing its job.

I deliberately started a fresh AI session for each case study rather than 
continuing a single long conversation. Long sessions accumulate context window 
degradation — early instructions get deprioritized as new content fills the 
window. A strong context prime at the start of a fresh session is more reliable 
than hoping a long conversation stays coherent.

---

## 3. Prompt Library Management — Prompts as Living Documents

A prompt library is a personal collection of reusable, versioned prompts that 
get updated every time a gap in the output is discovered. The goal is simple: 
make V1 outputs progressively closer to production-ready across projects, so 
less time is spent on feedback loops and more time is spent on analysis.

**Concrete example:** After Case Study 1, I noticed the AI consistently omitted 
two columns I always want in analytical output — an explicit ranking column and 
a percentage of total column. Rather than accepting these as things I'd catch 
in every evaluation pass, I added two lines to the Persona section of my context 
prime:

> *"For any query that ranks or compares rows, always include:*
> *1. An explicit ROW_NUMBER() ranking column as the first column*
> *2. A percentage of total column showing each row's share of the relevant aggregate"*

By Case Study 3, both columns appeared in V1 without any feedback required. 
The gap between V1 and production-ready narrowed with each iteration — not 
because the AI got better, but because the instructions got more precise.

This is the compounding value of prompt library management. Each project makes 
the next prompt smarter.

---

## 4. Critical Evaluation — The Human Layer

Generating output is easy. Knowing whether the output is correct, meaningful, 
and ready for a leadership audience is the hard part. That's the human layer — 
and it can't be delegated to the AI.

Critical evaluation means reading every output with skepticism: Does this 
metric actually measure what I think it measures? Does the column order make 
sense for a non-technical reader? Are the tier thresholds data-informed or just 
plausible-sounding guesses?

**Concrete example:** In Case Study 3, the AI returned a "vs. Global Average" 
performance label that looked reasonable on the surface. But after reviewing 
the results, the pattern was immediately obvious: every $0.99 music track was 
labeled "Below Average" and every $1.99 video item was labeled "Above Average." 
The label wasn't measuring genre performance — it was measuring price point. 
No prompt could have caught that. It required knowing how the catalog is priced 
and what a marketing team actually needs from a genre report.

I replaced the misleading label with a catalog efficiency metric — revenue per 
unique track in the catalog — that measures how hard each available track is 
working regardless of price point. That's a judgment call the AI can't make. 
It's the analyst layer.

---

## 5. Iterative Prompting & Prompt Chaining — Closing the Gap

No prompt produces perfect output on the first pass. The workflow that closes 
the gap between V1 and production-ready is iterative prompting: evaluate the 
output, identify specific gaps, reformat the evaluation as a structured feedback 
prompt, and feed it back to the AI.

This is called prompt chaining — the output of one prompt becomes the input of 
the next. Each iteration builds on the previous one rather than starting over.

**Concrete example:** In Case Study 2, the V1 output was missing five things: 
a ranking column, data-driven tier thresholds, a percentage of total revenue 
column, presentation-ready column names, and a tier summary query. Rather than 
sending a vague "please improve this" message, I reformatted each gap as a 
numbered, specific instruction and sent it as a single structured feedback prompt. 
The V2 output addressed all five in one pass — including one unprompted addition: 
the AI added a "Changes: V2" section to the comment header block, inferring from 
the feedback that this was a versioned update worth documenting.

---

## 6. Prompt Precision — AI Executes Literally

One of the most important lessons from this project: AI executes instructions 
literally. It doesn't infer intent. If you want something removed, you have to 
say so explicitly. If you want something added in a specific position, you have 
to specify the position. Assuming the AI will read between the lines is one of 
the most common sources of unexpected output.

**Concrete example:** In Case Study 3, my first feedback prompt asked the AI 
to replace the "vs. Global Average" column with a new catalog efficiency metric. 
But my prompt also said "keep the existing performance label" — a direct 
contradiction. The AI did exactly what I told it: it added the new column and 
kept the old one, because I hadn't explicitly said to remove it.

I caught the contradiction, updated the feedback prompt to explicitly state 
"Remove the existing 'vs. Global Average' performance label column entirely", 
and the column was cleanly dropped on the next iteration. The lesson isn't that 
the AI failed — it's that precise instructions produce precise outputs. Ambiguous 
instructions produce ambiguous outputs.

---

## 7. AI Self-Verification & Meta-Prompting — Catching What Math Can't

Every case study included a structured verification pass — asking the AI to 
check its own output against a specific set of mathematical and logical criteria. 
But the most valuable part of every verification prompt was the final question: 
"Beyond these checks, what other verification methods would you recommend for 
this specific type of query?"

This is meta-prompting — using the AI to surface verification approaches you 
might not have thought of. The AI consistently identified failure modes specific 
to the query type: silent exclusion checks, orphaned record audits, tier boundary 
edge cases. These aren't things that show up in the formatted output. They require 
deliberately going looking for them.

**Concrete example:** In Case Study 3, the standard verification checks all 
passed. The math was correct. But the meta-prompt surfaced a silent exclusion 
check that revealed Opera — a genre with 1 catalog track and zero sales — was 
being completely dropped by the INNER JOIN chain. The query was reporting against 
24 genres when Chinook actually carries 25. Without the meta-prompt, that gap 
would never have been caught — and we would have presented an incomplete picture 
of the catalog to leadership.

The fix required a judgment call: include Opera with $0.00 values to surface 
dead catalog inventory, or exclude it with a footnote. I chose to include it — 
because for a report designed to inform catalog investment decisions, zero-revenue 
genres are exactly the kind of finding that should be visible, not hidden.

---

## 8. Adaptability — Knowing When to Change the Approach

The most sophisticated AI collaboration skill isn't any single technique — it's 
knowing when the standard approach isn't working and adapting in real time. That 
means recognizing when a metric is misleading, when a threshold isn't data-informed, 
when a query design decision has downstream consequences for how the output is 
interpreted.

**Concrete example:** In Case Study 2, the AI generated tier thresholds 
(Platinum ≥ $45, Gold ≥ $35, Silver ≥ $25) that were reasonable on their face. 
But after checking the actual spend distribution, I found that nearly every 
customer would land in Gold or Silver — making the tiers statistically meaningless. 
The thresholds needed to be recalibrated based on where natural breaks actually 
existed in the data, not where they sounded plausible.

This led to a broader observation about the dataset: because Chinook's catalog 
is predominantly priced at $0.99 and $1.99, spend clusters are almost inevitable. 
The pricing structure creates natural compression in lifetime value — and that's 
a business model conversation, not a data quality one. Recognizing that distinction 
required marketing domain knowledge that no prompt could supply.

Adaptability also means knowing when to update the workflow itself. Every gap 
caught in evaluation became a prompt library update. Every verification failure 
became a new standard check. The workflow improved across all three case studies 
because I treated every unexpected output as information — not just a problem 
to fix, but a signal about where the process needed to get sharper.

---

## 9. Tool Selection — Using the Right AI for the Right Job

Not all AI models are equal, and not all tasks benefit from the same tool. 
Part of working effectively with AI is understanding the strengths of different 
models and deliberately matching the tool to the task — rather than defaulting 
to one model for everything.

For this project I used two different Claude models at two distinct stages:

**Claude Sonnet** handled the technical execution — SQL generation, iterative 
refinement, verification passes, and structured output. Sonnet is fast, 
efficient, and well-suited for tasks with clear inputs and measurable outputs. 
When the job is "write a CTE that calculates lifetime spend per customer and 
formats it correctly for SQLite," speed and precision matter more than depth 
of reasoning.

**Claude Opus** handled the editorial review — reading the completed project 
end-to-end and flagging passages that sounded robotic, transitions that felt 
abrupt, and phrasing that didn't match the voice of a confident marketing 
professional. Opus is better suited for tasks that require judgment about 
nuance, tone, and coherence across a large body of work. When the job is 
"does this sound like a human wrote it," depth of reasoning matters more 
than speed.

This two-model workflow — Sonnet for execution, Opus for review — mirrors 
how a professional team operates: one person builds, another reviews. The 
reviewer isn't better than the builder. They're optimized for a different 
part of the process.

**The broader principle:** AI tool selection is a skill. Knowing that a 
faster, more efficient model is the right choice for structured technical 
tasks — and that a more reasoning-capable model is the right choice for 
open-ended qualitative review — is the kind of workflow judgment that 
compounds over time. Every project gets a little more efficient when you 
stop treating all AI tools as interchangeable.

---

## Summary — The 9 Dimensions in Practice

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
| Tool Selection | Sonnet for SQL execution, Opus for editorial review |

The through-line across all nine dimensions is the same: AI accelerates the 
work. Human judgment makes the output worth presenting.
