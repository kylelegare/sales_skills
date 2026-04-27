# Critique Pass (Simplified for v1)

The critique lens. Asks: **what should be here that isn't?**

Where Surface, Discovery-Depth, and Forwarding-Test extract or tag what *was* said, the critique pass surfaces what was *missing*, *implied but unexplored*, or *steered around*. This is the only lens pass that creates new items rather than tagging existing Surface items.

This pass is **simplified for v1**. Cross-call contradiction detection — where Critique really shines — is **explicitly deferred** until the storage/retrieval layer (Q2) is built. Until then, this pass operates within a single call.

## Non-Goals for v1 (Explicit)

- **No cross-call contradiction detection.** "The buyer said X in this call but Y in call 3 last month" is NOT this pass's job in v1. That requires querying prior extractions, which requires the Q2 storage/retrieval layer. **Do not attempt this in v1.**
- **No semantic similarity search across calls.** Same reason.
- **No deal-stage tracking across time.** Single call, in isolation, is the unit of analysis.

When the Q2 layer lands, this pass will expand. For now, it stays single-call.

## What This Pass Extracts

New items (not tags on Surface items) of these types:

- **`gap-for-stage`** — something that should have been surfaced at this point in the deal but wasn't. Not all gaps are flaws; some are appropriate ("don't expect paper-process detail on call 1"). Only flag gaps that are reasonable to expect given context.
- **`glossed-over-moment`** — a moment where the buyer hinted at something significant and steered away, OR the seller (Kyle) moved past a thread the buyer was opening. Often hesitation, qualifiers, or topic-shifts after a meaningful disclosure.
- **`hypothesis`** — what might be true but wasn't said outright. Prose-confidence, not numeric. ("Likely the real concern is procurement, not security — they keep deflecting security questions but pivot to budget conversations." Confidence: medium-low.)

## Quality Standards

### Gaps Are Stage-Aware

A first discovery call won't have budget detail or paper-process clarity. Don't flag those as gaps on call 1 — that's noise. **Only flag what's reasonable to expect given:**

1. The mode (sales-call → discovery-shaped expectations; demo → product-fit-shaped expectations; internal-deal-chat → strategy-clarity-shaped expectations)
2. The apparent deal stage (which the call may signal — early discovery, late-stage, exec, etc.)
3. The specific items already produced by Surface, Discovery-Depth, Forwarding-Test

Examples of stage-aware gaps:

- **Sales-call, early discovery, Surface produced 3 problem items but no quantification** → `gap-for-stage`: "Discovery surfaced problems without quantifying them. Specific metrics — close rate %, dollar impact — were not surfaced. Reasonable to expect for a substantive discovery call."
- **Sales-call, mid-cycle, no stakeholder beyond the champion** → `gap-for-stage`: "After multiple calls only the champion has been engaged; no economic buyer or technical buyer surfaced. Reasonable to expect by mid-cycle."
- **Demo, capability shown but no buyer reaction recorded** → `gap-for-stage`: "Demo covered [capability] but the buyer's reaction was not surfaced. Without reaction signal, can't tell if this resonated or was filler."

Examples of NOT-gaps that should NOT be flagged:

- "No paper-process detail on call 1" — too early to expect
- "No exec-level stakeholder yet on call 2" — too early
- "Procurement timeline not discussed in a demo" — wrong call type

### Glossed-Over Moments Are Subtle

The richest signal in any call is often what the buyer ALMOST said. Watch for:

- **Hedging language** before a meaningful disclosure: "I mean, sort of," "I think," "probably," "maybe"
- **Topic pivots** right after a buyer disclosure (the buyer or seller moves past it)
- **Hesitations** transcribed (long pauses, "uh," "you know")
- **Vague answers** to specific questions ("it's complicated," "we've talked about that")
- **Deflections** away from a thread the buyer started ("but anyway, let me tell you about X")

Each glossed-over moment becomes its own item with the verbatim language, the buyer's behavior, and a 1-2 sentence note on why it might matter. The note should NOT speculate too far — it just flags "this is worth probing on the next call."

### Hypotheses Are Prose-Confident

Hypotheses go beyond what was said and explore what *might* be true. Only generate hypotheses when there's pattern evidence in the call (multiple data points pointing the same direction).

Confidence is **prose, not numeric**. Examples of confidence framing:

- **"Strong evidence — multiple items support this":** the buyer said X in three different ways; sees the data converging
- **"Plausible — fits the pattern but only one or two data points":** suggestive, not confirmed
- **"Speculative — worth probing":** thin evidence; should not drive decisions but worth surfacing for the next call

Avoid numeric confidence (no "0.7," no "70%"). Numeric confidence is false precision in this domain.

Hypotheses are inputs to thinking, not deal-driving claims. They feed the next call (sales-call-prep can use them to generate probing questions).

## What This Pass Contributes to Framework Notes

A Framework Notes block:

```markdown
### Critique (Simplified)

**Stage-aware gaps:**
- [Gap 1 with 1-line justification — "reasonable to expect because..."]
- [Gap 2]

**Glossed-over moments:**
- [Moment 1 — verbatim hedge/deflection language and 1-line significance note]

**Hypotheses worth probing next call:**
- [Hypothesis 1]: [prose confidence] — [1-line evidence summary]

**Critique-pass assessment:** [1-2 sentences on the call's depth/honesty — did the buyer get specific, or did the conversation stay surface-level? Were there moments worth probing that we let slide?]

*v1 limitation: this pass does NOT compare against prior calls. Cross-call contradictions and trend tracking will come once the storage/retrieval layer is built.*
```

## Prompt-Shaping Guidance

The critique pass receives the transcript AND the Surface, Discovery-Depth, and Forwarding-Test outputs. The model should:

**Be told to do:**
- Read the transcript end-to-end with the Discovery-Depth/Forwarding-Test tags in mind
- For each implicit-but-unstated thing the call should have surfaced (given mode, stage, and what was actually produced), generate a `gap-for-stage` item with a 1-line "reasonable to expect because..." justification
- Scan the transcript for hedging language, topic pivots, hesitations, vague answers, and deflections — generate `glossed-over-moment` items with verbatim language
- Where multiple items in this call point toward an unstated pattern, generate a `hypothesis` item with prose-confidence
- Compose the Framework Notes block per the template above
- Include the v1-limitation footer in Framework Notes (cross-call detection deferred)

**Be told to NOT do:**
- Compare against prior extractions or any data outside this single call (deferred to v1.5+ when storage layer exists)
- Fabricate gaps that aren't reasonable to expect given mode/stage
- Hypothesize wildly without pattern evidence
- Use numeric confidence on hypotheses (prose only)
- Re-extract or re-tag items already handled by Surface, Discovery-Depth, or Forwarding-Test
- Identify operational signals like commitment asymmetry (Operational pass)

**Output (all NEW items, not tags on existing items):**

```yaml
item_type: gap-for-stage
title: No quantified business problem surfaced
speaker:
source:
quote:
summary: Discovery surfaced two problem statements but neither was quantified. Reasonable to expect on a substantive discovery call: specific metrics (close-rate %, dollar impact, number of affected accounts).
why_matters: Without quantification, downstream artifacts (Forwarding-Test soundbites, business case) lack load-bearing data. Likely the next call should explicitly probe for the metric.
buyer_language:
provenance: [bredvick]
framework_tags: [critique:gap-for-stage]
```

```yaml
item_type: glossed-over-moment
title: Buyer hedged on procurement timeline
speaker: Sarah Chen, VP Sales
source: buyer
quote: "Procurement is, I mean, sort of its own thing. Anyway, let me tell you about how we'd actually use this..."
summary: Buyer started to discuss procurement, hedged, then pivoted to product use. Possible procurement-process risk being avoided.
why_matters: Procurement is often where deals stall. The hedge + pivot pattern suggests buyer may not know the process or may be uncomfortable with it. Worth probing directly on next call.
buyer_language:
provenance: [bredvick]
framework_tags: [critique:glossed-over-moment]
```

```yaml
item_type: hypothesis
title: Real concern may be internal politics, not security
speaker:
source:
quote:
summary: Buyer was specific about technical capabilities and quantified business problems, but every time security came up she pivoted to "we'd need to discuss with leadership." Pattern suggests the actual blocker may be securing internal alignment with a non-security stakeholder, not security itself.
why_matters: Hypothesis worth probing — direct security questions on next call may surface the real underlying stakeholder concern. If correct, this changes the closing motion.
buyer_language:
provenance: [bredvick]
framework_tags: [critique:hypothesis, critique:prose-confidence-plausible]
```

## Worked Example: Decision Logic on Whether to Flag a Gap

Setup: this is the second discovery call with a mid-market account. Surface produced 4 buyer-statement items about close rates dropping, root causes, and team dynamics. No quantified metrics on cost-of-inaction. No mention of procurement or paper process.

Critique decisions:

1. **Quantified cost-of-inaction missing** → flag as `gap-for-stage`. Reasonable to expect by call 2: the dollar impact of the close-rate drop. Justification: "Discovery has surfaced the problem and root causes but not the cost. Without cost, urgency can't be argued."

2. **Procurement detail missing** → DO NOT flag. Too early. Even on call 2, procurement detail is rarely relevant unless the buyer raises it.

3. **No exec-level stakeholder** → BORDERLINE. Flag with appropriate language: "Reasonable to expect introductions to be initiated by call 3-4; not a flag for call 2 unless this is enterprise-deal-shaped." The model should err on the side of NOT flagging when uncertain.

4. **Buyer hesitated when asked about timeline** → flag as `glossed-over-moment`. The hesitation is data.

5. **Pattern across multiple items: buyer keeps mentioning "the team" but never names anyone** → flag as `hypothesis` with plausible confidence: "Stakeholder visibility may be lower than the champion is signaling — names of teammates not surfaced."

## Future Work (Post-Q2)

When the storage/retrieval layer lands, this pass expands to:
- Compare current call's items against the account's prior extractions
- Detect contradictions (buyer said X in call 1, Y in call 4)
- Detect stalled threads (problem mentioned in call 2, never quantified despite calls 3-5)
- Detect declining engagement signals (specific commitments shrinking call-over-call)

Until then, single-call critique is the v1 scope.
