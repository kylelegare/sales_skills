# Operational Pass

The signals lens. Captures everything that isn't pure-discovery content but materially affects the deal: stakeholders, structured commitments, urgency triggers, competitive landscape, engagement signals, and risk patterns.

This pass does both — it creates new items AND adds structure to existing Surface commitment items. Mode-specific emphases (defined in `mode-profiles.md`) extend this pass with extra item types when the mode warrants.

This is the most varied of the five passes. It runs for all three v1 modes (sales-call, demo, internal-deal-chat). Mode shapes which item types matter most.

## What This Pass Extracts

### New Items (Default — All Modes)

- **`stakeholder-mention`** — a person referenced in the call (named, titled, role-described). Can be the buyer-side, seller-side, or third parties (their CFO, their procurement team, their security lead, etc.). Each becomes its own item even if the person is mentioned only in passing.

- **`trigger-event`** — a why-now signal: an upcoming event, deadline, recent change, leadership shift, competitive pressure, or business catalyst that creates urgency. Examples: "our board meets in October," "we just lost our biggest competitor account to [vendor]," "new CRO started in Q2 and is reorganizing GTM."

- **`competitor-mention`** — any reference to a vendor or alternative the buyer is considering, has used, or has rejected. Includes status quo as a competitor ("we just keep doing it ourselves"). Capture the verbatim language used to describe the competitor's strengths/weaknesses.

- **`happy-ears-flag`** — a moment where the seller (Kyle) interpreted enthusiasm that wasn't backed by concrete buyer action. The buyer said something positive-sounding but didn't commit, didn't quantify, didn't introduce a stakeholder. Often paired with seller-side optimistic interpretation in the conversation. **This is anti-happy-ears tagging — naming the moment so the AE can see it honestly.**

- **`risk-signal`** — a pattern that flags deal risk: hesitation on substantive questions, vague answers, deferred topics, missing stakeholders, last-minute scope creep, repeated postponement, conflicting messages from different people on the buyer side.

### Items With Structure Added (Existing Commitments)

For each Surface item already tagged `item_type: commitment`, the Operational pass adds:
- `owner` — who's responsible (name + role)
- `timeframe` — when it's due (specific date, "this week," "before next call," etc.)
- `commitment_side` — `buyer` or `seller`

The `commitment_side` field feeds the cross-cutting commitment-asymmetry analysis (see Framework Notes contribution).

### Mode-Specific Item Types

**Demo mode adds:**

- **`technical-reaction`** — buyer's reaction to a specific demonstrated capability. Captures the capability shown, the reaction (lean-in, tune-out, clarifying question, surprise, silence), and a 1-line significance note.
- **`capability-pain-mapping`** — moments where the seller demonstrated a capability that maps cleanly to a buyer-stated pain (or fails to). Useful for downstream demo-evaluation skills.

**Internal-deal-chat mode adds:**

- **`decision`** — what was decided in the discussion. Captures the decision, alternatives considered, and the decider.
- **`owner-assignment`** — who's doing what next, with deadline.
- **`concern`** — a worry someone on the team raised about this deal.
- **`hypothesis-update`** — how the team's read of the buyer or the deal changed during this discussion.
- **`dissent`** — where teammates disagreed and how (or whether) it resolved.

## Quality Standards

### Stakeholders Are People, Not Roles

Capture each named or described person as a separate `stakeholder-mention` item:

- **Named, with title:** "Sarah Chen, VP Sales" → strongest item; downstream skills can use this directly
- **Titled but unnamed:** "their CFO" → still capture; flag that name is unknown
- **Role-described, no title:** "the person who handles security reviews" → capture; this is also a stakeholder-mapping gap
- **Implied through "we":** if the buyer says "we'd need leadership approval," that's a hint at an unnamed senior stakeholder. Capture as a stakeholder-mention with role-description; flag the gap.

Each stakeholder item should include: name (or "unnamed"), title (if known), role/relationship (champion / economic buyer / technical buyer / influencer / blocker / unknown), source-of-mention (which call moment surfaced them).

**v1 limitation:** No cross-call stakeholder card resolution. Each call's mentions are captured independently. The Q2 storage layer will add stakeholder persistence and entity resolution across calls.

### Commitments Need Owner AND Timeframe

A bare commitment ("we'll send the deck") is half a commitment. The Operational pass extracts:

- **Who** is responsible (name + role)
- **When** it's due (specific date or "this week" or "before next call")
- **What** specifically (already in the Surface commitment item)
- **Side** (buyer-side commitment vs seller-side commitment — feeds asymmetry analysis)

If owner or timeframe are missing from the transcript, flag explicitly:
- `owner: unstated` — note in `why_matters` that the commitment lacks an owner
- `timeframe: unstated` — note that the timeframe is open-ended

These flags feed the Framework Notes commitment-asymmetry block AND the Critique gap-detection (an unstated-owner commitment is often a gap-for-stage).

### Trigger Events vs Wishful Thinking

Trigger events are buyer-stated, specific, and consequential:

- **Strong trigger:** "Our board meets in October and forecast accuracy is on the agenda." (Specific event + consequence + buyer-named.)
- **Weak trigger:** "We're hoping to make some progress this quarter." (Vague, no specific event, no consequence.)
- **Seller-driven trigger (NOT a real trigger):** "We thought it'd be good to wrap up by end of Q3." (Seller-proposed, buyer agreed politely. This is wishful, not a real urgency signal.)

Only tag `trigger-event` when the buyer named it AND there's a specific consequence or event. Seller-proposed timelines that the buyer agreed to should be captured in commitment items, not trigger-events. The distinction matters — buyer-driven urgency is real; seller-driven urgency is often happy-ears.

### Happy-Ears Detection

The single most disciplinary item type in this pass. A `happy-ears-flag` item names a moment where the seller interpreted enthusiasm that wasn't backed by buyer action. Patterns to watch:

- Buyer says "this looks really interesting" but does not introduce a stakeholder, agree to a next step, or commit to anything specific.
- Seller summarizes a buyer disclosure with more confidence than the buyer expressed ("it sounds like this is a top priority for you" → buyer says "yeah, I mean, sort of").
- Seller-side optimistic language in the call: "great call," "I think we're really aligned" — without buyer-side concrete confirmation.
- Buyer commits the seller to multiple things; the seller commits the buyer to nothing or near-nothing (commitment asymmetry).

The flag is anti-happy-ears: it names the moment honestly so the AE sees it. The `why_matters` note should be direct: "Seller interpreted enthusiasm; buyer made no concrete commitment. Likely the deal is less advanced than tone suggests."

### Risk Signals Compound

Single hesitations don't always matter; patterns of hesitation matter. Watch for:

- Repeated deflections from the same topic across the call (procurement, legal, security, budget)
- Vague answers to specific questions ("it's complicated," "we're working on that")
- Delayed responses to questions (long pauses transcribed)
- Stakeholder unavailability ("X would normally be here but...")
- Conflicting messages from different speakers on the buyer side (champion says one thing, exec says another)
- Last-minute scope additions or shifts

Each pattern becomes a `risk-signal` item with a 1-line significance note. The Framework Notes includes a brief assessment.

## What This Pass Contributes to Framework Notes

```markdown
### Operational

**Stakeholder map (this call):**
- [Name, Title — Role/Relationship — first surfaced when]

**Commitment asymmetry:**
- Buyer-side commitments: [count] — [list]
- Seller-side commitments: [count] — [list]
- Asymmetry assessment: [1 line — balanced / seller-heavy / buyer-heavy and what that suggests]

**Trigger events:**
- [Trigger 1 with consequence]

**Competitive landscape:**
- [Competitor 1 — buyer's framing — strength/weakness as the buyer described]
- [Status quo position, if relevant]

**Happy-ears flags:**
- [Flag 1 with verbatim moment and significance]

**Risk signals:**
- [Pattern 1 with 1-line summary]

**Engagement read:** [1-2 sentences on the call's energy and engagement signals — was the buyer leaning in or going through motions?]
```

Mode-specific additions to Framework Notes:

**Demo mode adds:**
- **Capability resonance:** which demonstrated capabilities mapped to buyer-stated pains; which didn't.
- **Reaction summary:** moments of strong positive/negative reaction.

**Internal-deal-chat mode adds:**
- **Decisions made:** [list]
- **Open dissent:** [list of unresolved disagreements]
- **Updated team hypotheses:** [how our read of the deal shifted]

## Prompt-Shaping Guidance

The Operational pass receives the transcript AND the Surface, Discovery-Depth, Forwarding-Test, and Critique outputs. The model should:

**Be told to do:**
- Generate `stakeholder-mention` items for every named or role-described person in the call
- Add structure (owner, timeframe, commitment_side) to existing Surface `commitment` items
- Generate `trigger-event` items for buyer-stated, specific, consequential urgency signals — and NOT for seller-driven wishful timelines
- Generate `competitor-mention` items for any vendor or alternative referenced
- Generate `happy-ears-flag` items where the call shows enthusiasm-without-action patterns
- Generate `risk-signal` items for patterns (not single moments) of hesitation, vagueness, deflection, conflict
- Apply mode-specific item types per `mode-profiles.md` (technical-reaction for demo; decision/owner-assignment/concern/hypothesis-update/dissent for internal-deal-chat)
- Compose the Framework Notes block per the template above, including mode-specific additions

**Be told to NOT do:**
- Re-extract content already captured by Surface
- Apply Discovery-Depth 3-bucket or Forwarding-Test soundbite tags (those passes already ran)
- Resolve stakeholder identities across calls (deferred to v1.5+)
- Score the deal holistically (deal-review's job)
- Fabricate trigger events from seller-driven language

**Output additions** (mix of new items and additions to existing commitment items):

```yaml
# A NEW stakeholder-mention item
item_type: stakeholder-mention
title: Sarah Chen — VP Sales (champion)
speaker: (referenced by herself + colleague)
source: buyer
quote: "I run the AE org — the SDR team rolls up to a different VP."
summary: Sarah Chen, VP Sales, owns the AE function. Likely champion (most engaged, asked most questions, set agenda).
why_matters: First clearly identified buyer-side stakeholder. Probable champion role; not yet tested as champion (no act-on-our-behalf evidence).
buyer_language:
provenance: [operational]
framework_tags:
  - operational:stakeholder-champion
  - operational:role-vp-sales
```

```yaml
# Existing Surface commitment item, after Operational pass:
item_type: commitment
title: Send ROI model by Friday
# ... existing Surface fields ...
provenance: [surface, operational]
framework_tags: [operational:commitment-with-structure]
owner: Kyle (rep)
timeframe: 2026-04-30 (Friday this week)
commitment_side: seller
```

```yaml
# A NEW happy-ears-flag item
item_type: happy-ears-flag
title: Buyer enthusiasm not backed by concrete next step
speaker: Sarah Chen
source: buyer
quote: "This looks really interesting — let me think about it."
summary: Strong positive language from the buyer but no commitment to a specific next step, no introduction to a stakeholder, no agreement on follow-up timing.
why_matters: The seller side may interpret this as advancing the deal. It is not — without commitment, "looks really interesting" is at best parking, at worst a soft no. Worth probing directly on next call: "When you say it's interesting, what specifically resonates? And what would need to be true for you to move forward?"
buyer_language:
provenance: [operational]
framework_tags: [operational:happy-ears]
```

## Worked Example: Commitment Asymmetry

A 45-minute call surfaces 6 commitments:

- Seller commits: send ROI model (Friday); send 2 customer references (next week); set up technical deep-dive with security (TBD); follow up after their internal sync (next Wednesday). [4 commitments]
- Buyer commits: review the ROI model and respond (no date given); think about it. [2 commitments — and one of those is vague]

Operational pass output:
- Each commitment becomes a structured item with owner/timeframe/side
- Commitment asymmetry assessment in Framework Notes: "Seller-heavy (4 vs 2). Buyer commitments are vague (one has no date, one is 'think about it')."
- Likely also a `happy-ears-flag` item: "Seller committing aggressively to advance the deal; buyer committing minimally. Pattern of advancing without buyer reciprocity is a deal-stage risk."

This is the kind of honest read the Operational pass exists to surface.

## Non-Goals for This Pass

- No cross-call stakeholder resolution (deferred to Q2)
- No deal scoring (deal-review's job)
- No artifact generation (downstream skills consume Operational items)
- No re-extraction of Surface content
- No Discovery-Depth/Forwarding-Test framework tagging (already done)
- No Critique gap detection (handled by critique pass — though Operational risk-signals and Critique gaps may overlap; let them coexist with their own provenance)
