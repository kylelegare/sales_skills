# Forwarding-Test Pass

The forwarding-test lens. Identifies which buyer statements are *gold for downstream artifacts* — soundbites that pass the forwarding test, cost-of-inaction language verbatim, the metric-that-matters.

This pass does **not** create new items. It tags Surface items with `framework_tags` indicating their value for downstream artifact generation (emails, business cases, exec summaries) and contributes a Framework Notes block with assembled soundbite candidates.

## Philosophy

The forwarding-test philosophy in one sentence: **the most valuable thing a buyer can say is something a champion can forward to their CFO without editing.**

## What This Pass Tags

For each Surface item where the framework applies (mostly `buyer-statement` items, especially those Discovery-Depth has tagged `business-problem` or `root-cause` with quantification), adds:

1. **Soundbite candidacy** as a `framework_tag`:
   - `forwarding-test:soundbite-candidate` — this quote could be used directly in a downstream artifact (recap email, business case, exec summary). High forwarding-test score.

2. **Cost-of-inaction language** as a `framework_tag`:
   - `forwarding-test:cost-of-inaction` — buyer described what happens (or has happened) when the problem is NOT solved. Verbatim quotes here are the heart of urgency-building artifacts.

3. **Metric-that-matters identification** as a `framework_tag`:
   - `forwarding-test:metric-that-matters` — the SINGLE metric the buyer most cares about. Often quantified, often above-the-line. There may be only one item per call carrying this tag (the most-load-bearing metric).

## What This Pass Contributes to Framework Notes

A Framework Notes block with **actually-assembled soundbite candidates** in the structure, plus the cost-of-inaction language and metric-that-matters surfaced for easy reuse:

```markdown
### Forwarding Test

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

The single most important Forwarding-Test check. A soundbite candidate must pass the forwarding test:

> If the champion received a 1-page recap from this call and forwarded it to their CFO **without editing**, would the CFO read it and think "yes, this is what we need to solve" — or would the CFO think "this is vendor language, who wrote this"?

Soundbites that pass:
- Use the buyer's exact words (codenames, internal phrasing, idioms preserved)
- Quantified where possible (specific metrics, dollar figures, dates)
- Specific to the company (not generic; the specificity rating from Discovery-Depth applies here too)
- Carry both problem and consequence (or trigger and desired outcome)

Soundbites that fail:
- Vendor language ("operational efficiency," "synergies," "best-in-class")
- Generic statements that could apply to any company
- Paraphrased — even faithful paraphrasing loses the buyer's voice

### Soundbite Structure

the canonical structure:

> **Because of** [problem in buyer's words], **now's the time** [trigger or urgency], **so that** [desired outcome], **instead of** [cost of inaction or status quo].

The forwarding-test pass should attempt to assemble at least one full soundbite from items the call surfaced. If a slot can't be filled with verbatim language from the call, mark it explicitly as TBD rather than fabricate. Examples:

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

The TBDs are not failures of the pass — they're honest gaps that the next call should explore (critique pass may flag this gap; sales-call-prep may surface it for the next conversation).

### Metric-That-Matters

Sales-call extractions often surface multiple metrics. Forwarding-Test insists there is usually **one** metric that load-bears the deal — the one the decision maker most cares about. The forwarding-test pass identifies it.

Heuristics for the metric-that-matters:
- The metric the decision maker cited most prominently or first
- The metric tied to the most explicit cost-of-inaction language
- The metric the buyer described in the most personal terms ("my number," "my forecast")
- Above-the-line per Discovery-Depth (the DM owns it; it's in their priorities)

If multiple metrics tie for prominence, tag both — but flag it as a finding ("multiple competing metrics cited; possible discovery gap on which one truly drives the deal").

### Verbatim, Always

The forwarding-test pass is brutally strict on verbatim. Paraphrasing a soundbite candidate destroys its forwarding value. If the transcript doesn't have a clean verbatim, tag it as a candidate but mark "needs verbatim confirmation" in the Framework Notes — don't fake the quote.

## Prompt-Shaping Guidance

The forwarding-test pass receives the transcript AND the Surface items (with Discovery-Depth tags already applied). The model should:

**Be told to do:**
- Scan items already tagged `discovery-depth:business-problem`, `discovery-depth:root-cause`, or with `discovery-depth:quantified` — these are the most likely Forwarding-Test candidates
- Apply the forwarding test mentally to each: would this quote survive being forwarded to a CFO?
- For items that pass, add `framework_tag: forwarding-test:soundbite-candidate`
- Tag items containing cost-of-inaction language with `forwarding-test:cost-of-inaction`
- Identify the SINGLE metric-that-matters and tag the item containing it with `forwarding-test:metric-that-matters`
- Compose the Framework Notes block with at least one assembled soundbite (with TBDs marked honestly where slots can't be filled)

**Be told to NOT do:**
- Fabricate quotes or fill in TBD slots with paraphrasing
- Apply soundbite tagging to items that fail the forwarding test (vendor language, generic statements)
- Score the deal or apply MEDDPICC/SPICED (deal-review's job)
- Identify gaps or missing-discovery items (critique pass)
- Re-classify the three buckets (already done by discovery-depth pass)
- Tag operational signals like commitment asymmetry (Operational pass)

**Output additions** (added to existing Surface items, plus Framework Notes contribution):

```yaml
# An existing Surface item, after Discovery-Depth and Forwarding-Test:
item_type: buyer-statement
title: Close rates dropping creates pipeline-coverage problem
# ... existing Surface fields ...
provenance: [surface, orlob, nasralla]
framework_tags:
  - discovery-depth:business-problem
  - discovery-depth:phase-1-problem
  - discovery-depth:quantified
  - discovery-depth:above-the-line
  - forwarding-test:soundbite-candidate
  - forwarding-test:metric-that-matters
```

## Worked Example

### Composing a soundbite for Acme

Surface items relevant to soundbite assembly:

1. > "We've gone from a 25% close rate to about 19% in the last two quarters." (discovery-depth:business-problem, discovery-depth:quantified, discovery-depth:above-the-line)
2. > "Our board meets in October and forecast accuracy is on the agenda." (discovery-depth:phase-3-impact)
3. > "The result is we're scrambling to backfill pipeline every single month." (discovery-depth:phase-3-impact)
4. > "We need to be predictably hitting our number — not surprising the CFO every quarter." (discovery-depth:phase-4-future-state, discovery-depth:desired-solution)

Forwarding-Test assembly:

> **Because of** "we've gone from a 25% close rate to about 19% in the last two quarters,"
> **now's the time** "our board meets in October and forecast accuracy is on the agenda,"
> **so that** "we [can be] predictably hitting our number — not surprising the CFO every quarter,"
> **instead of** "scrambling to backfill pipeline every single month."

Items get tagged:
- Item 1: `forwarding-test:soundbite-candidate`, `forwarding-test:metric-that-matters`
- Item 2: `forwarding-test:soundbite-candidate` (provides "now's the time" slot)
- Item 3: `forwarding-test:soundbite-candidate`, `forwarding-test:cost-of-inaction`
- Item 4: `forwarding-test:soundbite-candidate` (provides "so that" slot)

Framework Notes records the assembled soundbite for downstream skill consumption.

## Non-Goals for This Pass

- No new item creation
- No Discovery-Depth 3-bucket classification (already done by discovery-depth pass)
- No gap detection (critique pass)
- No stakeholder, commitment, or operational signal work (Operational pass)
- No deal scoring (deal-review's job)
- No artifact generation (forwarding-test pass surfaces material for downstream artifacts; it does NOT write the email or business case itself)
