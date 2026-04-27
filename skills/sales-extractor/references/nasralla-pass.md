# Nasralla Pass

The forwarding-test lens. Identifies which buyer statements are *gold for downstream artifacts* — soundbites that pass the forwarding test, cost-of-inaction language verbatim, the metric-that-matters.

This pass does **not** create new items. It tags Surface items with `framework_tags` indicating their value for downstream artifact generation (emails, business cases, exec summaries) and contributes a Framework Notes block with assembled soundbite candidates.

## Source Material

- "Brief and Brilliant" — Nate Nasralla. Source: `coding/sales_compounding/Resources To Help Ideate/Brief and Brilliant Nate Nasralla.epub`
- Fluint's enterprise sales playbook (same ecosystem). Source: `Resources To Help Ideate/The Enterprise Sales Playbook _ Fluint playbook.pdf`

The Nasralla philosophy in one sentence: **the most valuable thing a buyer can say is something a champion can forward to their CFO without editing.**

## What This Pass Tags

For each Surface item where Nasralla's framework applies (mostly `buyer-statement` items, especially those Orlob has tagged `business-problem` or `root-cause` with quantification), adds:

1. **Soundbite candidacy** as a `framework_tag`:
   - `nasralla:soundbite-candidate` — this quote could be used directly in a downstream artifact (recap email, business case, exec summary). High forwarding-test score.

2. **Cost-of-inaction language** as a `framework_tag`:
   - `nasralla:cost-of-inaction` — buyer described what happens (or has happened) when the problem is NOT solved. Verbatim quotes here are the heart of urgency-building artifacts.

3. **Metric-that-matters identification** as a `framework_tag`:
   - `nasralla:metric-that-matters` — the SINGLE metric the buyer most cares about. Often quantified, often above-the-line. There may be only one item per call carrying this tag (the most-load-bearing metric).

## What This Pass Contributes to Framework Notes

A Framework Notes block with **actually-assembled soundbite candidates** in Nasralla's structure, plus the cost-of-inaction language and metric-that-matters surfaced for easy reuse:

```markdown
### Nasralla (Forwarding Test)

**Soundbite candidates** (assembled in "because of / now's the time / so that / instead of" structure):

1. **Because of:** [verbatim buyer language naming the problem]
   **Now's the time:** [verbatim trigger or urgency language, if present — else "TBD: no urgency stated"]
   **So that:** [verbatim desired outcome]
   **Instead of:** [verbatim cost-of-inaction]

2. [next candidate, if multiple]

**Metric that matters:** [single metric, verbatim, with owner if known]
> "[verbatim quote naming the metric]"

**Cost-of-inaction language** (verbatim, ready for artifact reuse):
- > "[quote 1]"
- > "[quote 2]"

**Forwarding-test assessment:** [1-3 sentences — does this call produce material that would survive being forwarded to a CFO without editing? Or is the buyer too vague / too internal-jargon-y for downstream artifacts?]
```

## Quality Standards

### The Forwarding Test

The single most important Nasralla check. A soundbite candidate must pass the forwarding test:

> If the champion received a 1-page recap from this call and forwarded it to their CFO **without editing**, would the CFO read it and think "yes, this is what we need to solve" — or would the CFO think "this is vendor language, who wrote this"?

Soundbites that pass:
- Use the buyer's exact words (codenames, internal phrasing, idioms preserved)
- Quantified where possible (specific metrics, dollar figures, dates)
- Specific to the company (not generic; the No Logo Test from Orlob applies here too)
- Carry both problem and consequence (or trigger and desired outcome)

Soundbites that fail:
- Vendor language ("operational efficiency," "synergies," "best-in-class")
- Generic statements that could apply to any company
- Paraphrased — even faithful paraphrasing loses the buyer's voice

### Soundbite Structure

Nasralla's canonical structure:

> **Because of** [problem in buyer's words], **now's the time** [trigger or urgency], **so that** [desired outcome], **instead of** [cost of inaction or status quo].

The Nasralla pass should attempt to assemble at least one full soundbite from items the call surfaced. If a slot can't be filled with verbatim language from the call, mark it explicitly as TBD rather than fabricate. Examples:

**Strong assembly (all four slots from buyer language):**

> **Because of** "the weekly ops crunch where deals slip past quarter-end every time,"
> **now's the time** "before our board meeting in October when forecast accuracy will be a topic,"
> **so that** "we can stop scrambling to backfill pipeline at the end of every month,"
> **instead of** "another quarter where we close 20 instead of 28 and the CFO asks why."

**Partial assembly (some slots verbatim, some TBD):**

> **Because of** "our cross-sell rate sitting at 11%, half of where it should be,"
> **now's the time** [TBD — no urgency language captured this call],
> **so that** "we hit 25% by year-end like the leadership KPI requires,"
> **instead of** [TBD — no cost-of-inaction language captured this call].

The TBDs are not failures of the pass — they're honest gaps that the next call should explore (Bredvick pass may flag this gap; sales-call-prep may surface it for the next conversation).

### Metric-That-Matters

Sales-call extractions often surface multiple metrics. Nasralla insists there is usually **one** metric that load-bears the deal — the one the decision maker most cares about. The Nasralla pass identifies it.

Heuristics for the metric-that-matters:
- The metric the decision maker cited most prominently or first
- The metric tied to the most explicit cost-of-inaction language
- The metric the buyer described in the most personal terms ("my number," "my forecast")
- Above-the-line per Orlob (the DM owns it; it's in their priorities)

If multiple metrics tie for prominence, tag both — but flag it as a finding ("multiple competing metrics cited; possible discovery gap on which one truly drives the deal").

### Verbatim, Always

The Nasralla pass is brutally strict on verbatim. Paraphrasing a soundbite candidate destroys its forwarding value. If the transcript doesn't have a clean verbatim, tag it as a candidate but mark "needs verbatim confirmation" in the Framework Notes — don't fake the quote.

## Prompt-Shaping Guidance

The Nasralla pass receives the transcript AND the Surface items (with Orlob tags already applied). The model should:

**Be told to do:**
- Scan items already tagged `orlob:business-problem`, `orlob:root-cause`, or with `orlob:quantified` — these are the most likely Nasralla candidates
- Apply the forwarding test mentally to each: would this quote survive being forwarded to a CFO?
- For items that pass, add `framework_tag: nasralla:soundbite-candidate`
- Tag items containing cost-of-inaction language with `nasralla:cost-of-inaction`
- Identify the SINGLE metric-that-matters and tag the item containing it with `nasralla:metric-that-matters`
- Compose the Framework Notes block with at least one assembled soundbite (with TBDs marked honestly where slots can't be filled)

**Be told to NOT do:**
- Fabricate quotes or fill in TBD slots with paraphrasing
- Apply soundbite tagging to items that fail the forwarding test (vendor language, generic statements)
- Score the deal or apply MEDDPICC/SPICED (deal-review's job)
- Identify gaps or missing-discovery items (Bredvick pass)
- Re-classify Orlob's three buckets (already done by Orlob pass)
- Tag operational signals like commitment asymmetry (Operational pass)

**Output additions** (added to existing Surface items, plus Framework Notes contribution):

```yaml
# An existing Surface item, after Orlob and Nasralla:
item_type: buyer-statement
title: Close rates dropping creates pipeline-coverage problem
# ... existing Surface fields ...
provenance: [surface, orlob, nasralla]
framework_tags:
  - orlob:business-problem
  - orlob:phase-1-problem
  - orlob:quantified
  - orlob:above-the-line
  - nasralla:soundbite-candidate
  - nasralla:metric-that-matters
```

## Worked Example

### Composing a soundbite for Acme

Surface items relevant to soundbite assembly:

1. > "We've gone from a 25% close rate to about 19% in the last two quarters." (orlob:business-problem, orlob:quantified, orlob:above-the-line)
2. > "Our board meets in October and forecast accuracy is on the agenda." (orlob:phase-3-impact)
3. > "The result is we're scrambling to backfill pipeline every single month." (orlob:phase-3-impact)
4. > "We need to be predictably hitting our number — not surprising the CFO every quarter." (orlob:phase-4-future-state, orlob:desired-solution)

Nasralla assembly:

> **Because of** "we've gone from a 25% close rate to about 19% in the last two quarters,"
> **now's the time** "our board meets in October and forecast accuracy is on the agenda,"
> **so that** "we [can be] predictably hitting our number — not surprising the CFO every quarter,"
> **instead of** "scrambling to backfill pipeline every single month."

Items get tagged:
- Item 1: `nasralla:soundbite-candidate`, `nasralla:metric-that-matters`
- Item 2: `nasralla:soundbite-candidate` (provides "now's the time" slot)
- Item 3: `nasralla:soundbite-candidate`, `nasralla:cost-of-inaction`
- Item 4: `nasralla:soundbite-candidate` (provides "so that" slot)

Framework Notes records the assembled soundbite for downstream skill consumption.

## Non-Goals for This Pass

- No new item creation
- No Orlob 3-bucket classification (already done by Orlob pass)
- No gap detection (Bredvick pass)
- No stakeholder, commitment, or operational signal work (Operational pass)
- No deal scoring (deal-review's job)
- No artifact generation (Nasralla pass surfaces material for downstream artifacts; it does NOT write the email or business case itself)
