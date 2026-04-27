# Surface Pass

The first pass over any input. Captures **what was said, faithfully** in the speakers' own words. Pre-analytical — no framework imposed yet.

This pass produces the corpus that subsequent lens passes (Orlob, Nasralla, Bredvick-simplified, Operational) consume and tag. Its job is to be a faithful, queryable representation of the conversation.

## What This Pass Extracts

A list of typed items, ordered chronologically through the conversation. Each item captures one meaningful piece of information: a buyer statement, a meaningful seller commitment, an explicit open question, etc.

**Target volume:** ~10-25 items per 30-60 minute call. Internal chats and shorter inputs scale down proportionally. Multi-pass extraction does NOT mean more items — it means more *structured tagging* on similar item count. If the Surface pass is producing 50+ items, it's extracting noise.

## Item Types Produced

The Surface pass owns these types:

- **`buyer-statement`** — a meaningful thing the buyer (or buyer-side participant) said. The most common item type. Each buyer-statement should aim to include a verbatim quote.
- **`seller-statement`** — extracted ONLY when one of the following applies:
  - The seller made a commitment ("I'll send you the ROI model by Friday") → tag as `seller-statement` AND let the Operational pass also mark it as a `commitment` item with owner+timeframe
  - The seller said something the buyer reacted to meaningfully ("Oh, I never thought of it that way")
  - The seller's statement provides essential context for understanding a buyer response
  - **Generally, do not extract pitches, demos, or seller monologues. The buyer's reaction is the signal, not the seller's pitch.**
- **`commitment`** — an explicit promise or next-step from either side. Surface captures the existence of the commitment and the literal phrasing; Operational pass adds owner/timeframe structure.
- **`open-question`** — a question raised in the call that wasn't answered or that explicitly remains open. Either side may raise these. Common at end-of-call ("we'll get back to you on the security review timeline").

Items of these types may later be tagged by lens passes (Orlob may add `orlob:business-problem`, Nasralla may flag a `nasralla:soundbite-candidate`, etc.). Item type is set by Surface; framework_tags accumulate from later passes.

## Quality Standards

### Buyer's Words Are Sacred

When the buyer uses specific terminology, codenames, internal project names, or phrasing, capture **exact words**. This is the single most valuable thing in the extraction. Examples:

- They call it "the weekly ops crunch," not "operational friction"
- They named the initiative "Project North Star," not "the strategic plan"
- They said "our reps give terrible demos," not "demo quality is suboptimal"

Include a `buyer-language` field in items where the buyer used distinctive language. Omit when there's nothing notable.

### Quotes Are the Confidence Signal

Every `buyer-statement` should aim to include a direct quote when one is faithfully available in the transcript. The quote IS the confidence signal:

- **Quote present** → high-confidence; this is what they actually said; can be used directly in downstream artifacts (emails, business cases) without paraphrase
- **Quote absent** → interpretation; may need confirmation; downstream skills should treat with caution

Do not invent quotes. Do not paraphrase and put it in quotes. If the transcript doesn't have a clean verbatim, omit the quote field and rely on the summary.

### Signal, Not Noise

Skip:
- Generic pleasantries ("good to see you," "how was your weekend")
- Filler ("yeah," "right," "uh-huh") unless the filler IS the signal (e.g., a long pause or hesitation matters; capture in `why_matters` rather than as its own item)
- Confirmation responses to seller summaries ("yeah, that sounds right") — these confirm the prior item, they aren't separate items
- Logistics that don't shape the deal (small talk about scheduling)

Extract:
- Anything that reveals priorities, constraints, decision criteria, stakeholders, timeline, problems, root causes, impact
- Corrections to prior statements ("actually, it's $3M not $2M")
- Hesitations, qualifiers, or vagueness on substantive points (note in `why_matters`)
- Buying signals (verbal commitments, requests for paperwork, references, active negotiation)
- Tactical disclosures (paper process, procurement steps, who's involved in approval)

### Faithful, Not Interpretive

The Surface pass does NOT classify items into Orlob's three buckets, does NOT score for No Logo Test, does NOT identify Nasralla soundbite candidates. It captures what was said. Subsequent passes do the interpretation.

Resist the urge to be clever. "The buyer said X" is the goal. "The buyer is implying X" is for the lens passes.

### Order Items Chronologically

Follow the conversation flow. This preserves context, lets later passes reason about sequence ("the buyer said X early, then contradicted with Y"), and makes traceback to the raw transcript easy.

## Prompt-Shaping Guidance

When constructing the Surface pass prompt internally, the model should:

**Be told to do:**
- Read through the transcript end-to-end
- Extract ~10-25 items in chronological order
- For each item, produce: a brief descriptive title, the speaker (name + role + buyer/seller), an exact quote when available, a faithful summary, a `why_matters` note (1-2 sentences explaining why this item is being extracted), and a `buyer_language` field when distinctive phrasing applies
- Apply the item-type field strictly: `buyer-statement` | `seller-statement` | `commitment` | `open-question`

**Be told to NOT do:**
- Apply Orlob's three-bucket classification (that's the Orlob pass's job)
- Identify Nasralla soundbite candidates (that's Nasralla pass's job)
- Score for No Logo Test (Orlob pass)
- Identify gaps or glossed-over moments (Bredvick pass)
- Resolve stakeholder identities or compute commitment asymmetry (Operational pass)
- Invent quotes
- Extract small talk, filler, or logistics

**Output shape per item** (this becomes the in-flight extraction state that subsequent passes read):

```yaml
item_type: buyer-statement
title: <brief descriptive title>
speaker: <name, role>
source: buyer | seller
quote: <verbatim, or omit>
summary: <faithful summary>
why_matters: <1-2 sentence note>
buyer_language: <distinctive phrasing, or omit>
provenance: [surface]
framework_tags: []  # other passes will add here
```

## Worked Examples

### Example 1: Strong buyer-statement with quote

```yaml
item_type: buyer-statement
title: Close rates dropping creates pipeline-coverage problem
speaker: Sarah Chen, VP Sales
source: buyer
quote: "We've gone from a 25% close rate to about 19% in the last two quarters, and the result is we're now scrambling to backfill pipeline every month."
summary: Sarah named a specific drop in close rates (25% → 19%) over two quarters and connected it to current pipeline-coverage stress.
why_matters: Quantified suffering metric attached to the decision maker. Likely the central business problem driving the conversation.
buyer_language: "scrambling to backfill pipeline" — preserve verbatim; this is how she frames it internally
provenance: [surface]
framework_tags: []
```

### Example 2: Weak buyer-statement (no quote)

```yaml
item_type: buyer-statement
title: Procurement timeline could push past Q4
speaker: Sarah Chen, VP Sales
source: buyer
quote:
summary: Sarah indicated their procurement process typically takes 6-8 weeks once a vendor is selected, which would push contract execution into early Q1 if a decision isn't made by mid-October.
why_matters: First mention of procurement timeline. Affects deal close-date forecasting. Note: no direct quote captured — confirm timeline on next call.
buyer_language:
provenance: [surface]
framework_tags: []
```

### Example 3: Commitment

```yaml
item_type: commitment
title: Send ROI model by Friday
speaker: Kyle (rep)
source: seller
quote: "I'll send you the ROI model by Friday — we'll plug in your numbers."
summary: Seller committed to delivering an ROI model by Friday using the buyer's reported metrics.
why_matters: First seller-side commitment of this call. Tracks against the commitment-asymmetry signal Operational pass evaluates.
buyer_language:
provenance: [surface]
framework_tags: []
```

### Example 4: Open question

```yaml
item_type: open-question
title: Security review process and approver unknown
speaker: Sarah Chen, VP Sales
source: buyer
quote: "I'd need to loop in our security team — I'm honestly not sure who runs that process here anymore."
summary: Buyer is unable to identify security-review owner. Open question for follow-up.
why_matters: Stakeholder gap. Champion does not have visibility into the security-review owner — a procurement-process risk.
buyer_language:
provenance: [surface]
framework_tags: []
```

## Non-Goals for This Pass

- No framework classification (Orlob's three buckets are NOT applied here)
- No No Logo Test scoring
- No soundbite identification
- No gap/glossed-over detection
- No stakeholder card resolution across calls
- No commitment-asymmetry summary computation (Operational pass produces that)
- No TL;DR composition (Phase 6 of orchestration produces that)

The Surface pass's only output is a faithful corpus of typed items. Everything else compounds on top.
