# Artifact Schema

The contract every `sales-extractor` artifact must conform to. File location, frontmatter, item layout, the item-type catalogue, provenance tagging convention, and one worked example.

This file is the single source of truth for the artifact format. If a lens reference describes an item type or framework tag that isn't in this catalogue, that's a schema-drift bug — update both files in the same commit.

## File Location and Naming

```
~/.agents/sales/<account-slug>/extractions/YYYY-MM-DD-<mode>.md
```

Where:
- `<account-slug>` is the lowercase, hyphenated account name (e.g., `acme-corp`, `global-payments`).
- `YYYY-MM-DD` is the call date.
- `<mode>` is one of `sales-call`, `demo`, `internal-deal-chat` (deep-dives append `-deep-dive`).

If a file with that exact name already exists (multiple inputs of the same mode on the same day for the same account), append a sequence suffix: `YYYY-MM-DD-<mode>-2.md`, `YYYY-MM-DD-<mode>-3.md`, etc.

The raw transcript is preserved separately at `~/.agents/sales/<account-slug>/transcripts/YYYY-MM-DD-<mode>.md` per existing convention.

## Frontmatter Schema

```yaml
---
account: <slug>                       # required
date: YYYY-MM-DD                      # required
mode: sales-call | demo | internal-deal-chat  # required
mode_detected_via: explicit-hint | linguistic-marker | speaker-composition | default | user-override  # required
participants:                         # required (may be empty for unparseable inputs)
  - name: <name>                      # required per participant
    role: <role/title>                # optional
    source: buyer | seller | team     # required per participant
passes_run:                           # required — list of passes that ran
  - surface
  - orlob
  - nasralla
  - bredvick-simplified
  - operational
passes_failed: []                     # required (empty array if none)
no_logo_rating: <1-5>                 # optional — present when Orlob pass ran and produced one
summary_stats:                        # optional but recommended
  item_count_total: <int>
  item_count_by_type:
    buyer-statement: <int>
    stakeholder-mention: <int>
    # ... etc
raw_transcript: ../transcripts/YYYY-MM-DD-<mode>.md  # optional — pointer back to raw
---
```

**Field semantics:**

- `mode_detected_via` records HOW the mode was decided. Use this when extraction quality looks off — the detection signal is the first thing to check.
- `passes_run` ALWAYS lists the passes that successfully completed (in execution order). `passes_failed` lists any that errored or returned empty. Together they document the pipeline's actual run.
- `no_logo_rating` is present only when the Orlob pass ran AND produced a rating. Internal-deal-chat artifacts (where Orlob is skipped) will not have this field.
- `summary_stats` are optional but make downstream queries faster. The orchestration pass computes them at composition time.

## Body Structure

```markdown
# <Account Name> — <Mode>, <Date>

## TL;DR

<3 sentences max. Composed from operational-pass key items (stakeholders, commitments, risk signals) and Nasralla soundbite candidates. Forwarding-test should pass — this should read like a faithful human summary, not an AI report.>

## Items

### <Item Type 1>

<all items of this type, in extraction order, separated by ---  >

### <Item Type 2>

<...>

## Framework Notes

### Orlob (Discovery Depth)
<per orlob-pass.md template — only present if Orlob pass ran>

### Nasralla (Forwarding Test)
<per nasralla-pass.md template — only present if Nasralla pass ran>

### Bredvick (Critique — Simplified)
<per bredvick-pass.md template — always present when Bredvick pass ran>

### Operational
<per operational-pass.md template — always present>
```

The Items section is grouped by `item_type` to enable downstream queries by type. Within each group, items are sorted by source-of-discovery (first-pass-found first), with cross-pass duplicates merged.

## Item Layout

Each item is a markdown subsection with a YAML-style metadata block followed by the body content:

```markdown
### [<item_type>] <Title>

**Speaker:** <name, role>
**Source:** <buyer | seller | team | n/a>
**Provenance:** [<pass>, <pass>, ...]
**Framework tags:** [<tag>, <tag>, ...]
<additional metadata fields per item type — see catalogue below>

> "<verbatim quote — omit if no faithful quote available>"

**Summary:** <faithful summary>

**Why it matters:** <1-2 sentences>

**Buyer language:** <distinctive phrasing — omit if none notable>

---
```

Items are separated by `---` (horizontal rule) for readability.

## Item Type Catalogue

The complete catalogue for v1. Lens references must produce items that fit this catalogue; orchestration must compose using only these types.

### From Surface Pass

| Item type | Required fields | Optional fields |
|-----------|----------------|-----------------|
| `buyer-statement` | speaker, source=buyer, summary, why_matters, provenance | quote (REQUIRED when transcript supports), buyer_language, framework_tags |
| `seller-statement` | speaker, source=seller, summary, why_matters, provenance | quote, framework_tags |
| `commitment` | summary, why_matters, provenance | speaker, source, quote, owner, timeframe, commitment_side, framework_tags |
| `open-question` | speaker, source, summary, why_matters, provenance | quote, framework_tags |

### From Bredvick (Simplified) Pass

| Item type | Required fields | Optional fields |
|-----------|----------------|-----------------|
| `gap-for-stage` | summary, why_matters, provenance, framework_tags=[bredvick:gap-for-stage] | (no speaker, source, or quote — these are about absence) |
| `glossed-over-moment` | speaker, source=buyer, quote, summary, why_matters, provenance, framework_tags=[bredvick:glossed-over-moment] | buyer_language |
| `hypothesis` | summary, why_matters, provenance, framework_tags=[bredvick:hypothesis, bredvick:prose-confidence-{strong\|plausible\|speculative}] | (no quote unless one supports the hypothesis) |

### From Operational Pass — All Modes

| Item type | Required fields | Optional fields |
|-----------|----------------|-----------------|
| `stakeholder-mention` | summary (name + role description), why_matters, provenance | speaker, quote, framework_tags (e.g., `operational:stakeholder-champion`) |
| `trigger-event` | summary, why_matters, provenance | speaker, quote, framework_tags |
| `competitor-mention` | summary (competitor name + framing), why_matters, provenance | speaker, quote, framework_tags |
| `happy-ears-flag` | speaker, source=buyer, quote, summary, why_matters, provenance, framework_tags=[operational:happy-ears] | buyer_language |
| `risk-signal` | summary, why_matters, provenance, framework_tags=[operational:risk-signal] | speaker, source, quote |

### From Operational Pass — Demo Mode

| Item type | Required fields | Optional fields |
|-----------|----------------|-----------------|
| `technical-reaction` | summary (capability + reaction), why_matters, provenance, framework_tags=[operational:technical-reaction] | speaker, quote, buyer_language |
| `capability-pain-mapping` | summary (capability + buyer pain it maps to or fails to), why_matters, provenance, framework_tags | (no quote required) |

### From Operational Pass — Internal-Deal-Chat Mode

| Item type | Required fields | Optional fields |
|-----------|----------------|-----------------|
| `decision` | summary (decision + alternatives + decider), why_matters, provenance, framework_tags=[operational:decision] | speaker (decider), quote |
| `owner-assignment` | summary (who + what + when), why_matters, provenance, framework_tags=[operational:owner-assignment], owner, timeframe | speaker, quote |
| `concern` | speaker (raiser), summary, why_matters, provenance, framework_tags=[operational:concern] | quote |
| `hypothesis-update` | summary (old read → new read), why_matters, provenance, framework_tags=[operational:hypothesis-update] | speaker |
| `dissent` | summary (disagreement + resolution-or-not), why_matters, provenance, framework_tags=[operational:dissent] | speaker |

## Framework Tag Catalogue

All framework tags use the `<pass>:<tag>` namespace.

**Orlob tags:** `orlob:business-problem`, `orlob:root-cause`, `orlob:desired-solution`, `orlob:phase-1-problem`, `orlob:phase-2-cause`, `orlob:phase-3-impact`, `orlob:phase-4-future-state`, `orlob:quantified`, `orlob:above-the-line`, `orlob:below-the-line`

**Nasralla tags:** `nasralla:soundbite-candidate`, `nasralla:cost-of-inaction`, `nasralla:metric-that-matters`

**Bredvick tags:** `bredvick:gap-for-stage`, `bredvick:glossed-over-moment`, `bredvick:hypothesis`, `bredvick:prose-confidence-strong`, `bredvick:prose-confidence-plausible`, `bredvick:prose-confidence-speculative`

**Operational tags:** `operational:stakeholder-champion`, `operational:stakeholder-economic-buyer`, `operational:stakeholder-technical-buyer`, `operational:stakeholder-influencer`, `operational:stakeholder-blocker`, `operational:role-<title>`, `operational:commitment-with-structure`, `operational:happy-ears`, `operational:risk-signal`, `operational:technical-reaction`, `operational:decision`, `operational:owner-assignment`, `operational:concern`, `operational:hypothesis-update`, `operational:dissent`, `operational:anti-pattern`

New tags can be added as the model encounters edge cases — but additions should be added to this catalogue in the same change. No drift.

## Provenance Convention

Every item has a `provenance` array listing the passes that produced or tagged it. Order matches the order passes ran:

- **Item created by Surface only:** `provenance: [surface]`
- **Item created by Surface, tagged by Orlob:** `provenance: [surface, orlob]`
- **Item created by Bredvick (no Surface origin):** `provenance: [bredvick-simplified]`
- **Item merged across passes (e.g., commitment created by Surface, structured by Operational):** `provenance: [surface, operational]`
- **Failed pass:** does NOT appear in provenance — `passes_failed` in frontmatter is the canonical record.

**Why this matters:** if a downstream skill or auditor wants to know "which framework tagged this item," provenance is the answer. If extraction quality drops on a specific dimension, provenance lets us trace which pass produced the bad output.

## Worked Example

A complete artifact for an imagined call:

```markdown
---
account: acme-corp
date: 2026-04-25
mode: sales-call
mode_detected_via: speaker-composition
participants:
  - name: Sarah Chen
    role: VP Sales
    source: buyer
  - name: Marcus Lee
    role: RevOps Director
    source: buyer
  - name: Kyle
    role: AE
    source: seller
passes_run:
  - surface
  - orlob
  - nasralla
  - bredvick-simplified
  - operational
passes_failed: []
no_logo_rating: 4
summary_stats:
  item_count_total: 14
  item_count_by_type:
    buyer-statement: 5
    seller-statement: 1
    commitment: 3
    open-question: 1
    stakeholder-mention: 2
    risk-signal: 1
    happy-ears-flag: 1
raw_transcript: ../transcripts/2026-04-25-sales-call.md
---

# Acme Corp — Sales-Call, 2026-04-25

## TL;DR

Sarah Chen (VP Sales, champion) is dealing with a quantified close-rate decline (25% → 19% over two quarters) creating pipeline-coverage stress; the root cause she named is reps skipping discovery. Specific board pressure in October creates buyer-driven urgency. Procurement timeline and security-review owner remain unknown — both worth probing on the next call.

## Items

### Buyer Statements

### [buyer-statement] Close rates dropping creates pipeline-coverage problem

**Speaker:** Sarah Chen, VP Sales
**Source:** buyer
**Provenance:** [surface, orlob, nasralla]
**Framework tags:** [orlob:business-problem, orlob:phase-1-problem, orlob:quantified, orlob:above-the-line, nasralla:soundbite-candidate, nasralla:metric-that-matters]

> "We've gone from a 25% close rate to about 19% in the last two quarters, and the result is we're now scrambling to backfill pipeline every month."

**Summary:** Sarah named a specific drop in close rates over two quarters and connected it to current pipeline-coverage stress.

**Why it matters:** Quantified suffering metric attached to the decision maker. Likely the central business problem driving the conversation.

**Buyer language:** "scrambling to backfill pipeline" — preserve verbatim.

---

### [buyer-statement] Reps skipping discovery as the root cause

**Speaker:** Sarah Chen, VP Sales
**Source:** buyer
**Provenance:** [surface, orlob]
**Framework tags:** [orlob:root-cause, orlob:phase-2-cause]

> "Honestly, our reps just don't run good discovery anymore. They jump to demo too fast."

**Summary:** Sarah identified the root cause she sees behind the close-rate decline.

**Why it matters:** Buyer-articulated root cause; matches the pattern of capability we sell.

**Buyer language:**

---

### Commitments

### [commitment] Send ROI model by Friday

**Provenance:** [surface, operational]
**Framework tags:** [operational:commitment-with-structure]
**Owner:** Kyle (rep)
**Timeframe:** 2026-04-30
**Commitment side:** seller

> "I'll send you the ROI model by Friday — we'll plug in your numbers."

**Summary:** Seller committed to delivering an ROI model by Friday using the buyer's reported metrics.

**Why it matters:** Seller-side commitment. Tracks against asymmetry signal in Framework Notes.

---

### Stakeholder Mentions

### [stakeholder-mention] Sarah Chen — VP Sales (champion)

**Source:** buyer
**Provenance:** [operational]
**Framework tags:** [operational:stakeholder-champion, operational:role-vp-sales]

**Summary:** Sarah Chen, VP Sales, owns the AE function. Likely champion role; highest engagement on the call, set the agenda, asked the most questions.

**Why it matters:** First clearly identified buyer-side stakeholder. Champion role probable but not yet tested (no act-on-our-behalf evidence). Worth asking on next call: has she shared anything about us internally yet?

---

### Risk Signals

### [risk-signal] Repeated deflection on procurement timeline

**Provenance:** [operational]
**Framework tags:** [operational:risk-signal]

**Summary:** Buyer was asked twice about procurement timeline; both times responded vaguely ("it's its own thing") and pivoted topics. Pattern of deflection on a load-bearing topic.

**Why it matters:** Procurement is often where deals stall. Buyer may not know the process or may be uncomfortable with it. Worth probing directly on next call.

---

### Happy-Ears Flags

### [happy-ears-flag] Buyer enthusiasm not backed by concrete next step

**Speaker:** Sarah Chen
**Source:** buyer
**Provenance:** [operational]
**Framework tags:** [operational:happy-ears]

> "This looks really interesting — let me think about it."

**Summary:** Strong positive language from the buyer but no commitment to a specific next step or stakeholder introduction.

**Why it matters:** Seller-side may interpret this as deal advancement. It is not. Without commitment, "looks really interesting" is at best parking. Worth probing directly on next call: "When you say it's interesting, what specifically resonates? What would need to be true for you to move forward?"

**Buyer language:**

---

### Gap-for-Stage (Bredvick)

### [gap-for-stage] Procurement-process owner unknown

**Provenance:** [bredvick-simplified]
**Framework tags:** [bredvick:gap-for-stage]

**Summary:** Buyer cannot identify procurement-process owner ("it's its own thing"). Reasonable to expect by mid-cycle: a specific person or function.

**Why it matters:** Without the procurement owner identified, deal close-date forecasting is unreliable. Plan to surface this on next call as a specific question.

---

## Framework Notes

### Orlob (Discovery Depth)

**No Logo Test rating:** 4/5 — Quantified close-rate decline (25% → 19%, two quarters) and buyer's distinctive language ("scrambling to backfill pipeline") makes the problem largely account-specific. Could plausibly apply to other VP Sales roles in the same vertical, but the specifics rule out most.

**Three-bucket distribution:**
- Business problems: 1 — Close rates dropping
- Root causes: 1 — Reps skipping discovery
- Desired solutions: 0

**Phase distribution:**
- Phase 1 (problem): 1
- Phase 2 (cause): 1
- Phase 3 (impact): 0
- Phase 4 (future state): 0

**Quantified items:** 1
**Above-the-line items:** 1

**Discovery depth assessment:** Discovery surfaced a quantified business problem and a root cause but did not explore negative impact (cost of inaction) or future-state buying criteria. Phase 3 and Phase 4 are the call's gap.

### Nasralla (Forwarding Test)

**Soundbite candidates:**

1. **Because of:** "we've gone from a 25% close rate to about 19% in the last two quarters,"
   **Now's the time:** "our board meets in October and forecast accuracy is on the agenda,"
   **So that:** [TBD — no future-state language captured this call],
   **Instead of:** "scrambling to backfill pipeline every single month."

**Metric that matters:** Close rate (25% → 19%, owned by Sarah Chen, VP Sales)
> "We've gone from a 25% close rate to about 19%."

**Cost-of-inaction language:**
- > "scrambling to backfill pipeline every single month"

**Forwarding-test assessment:** Strong material for a champion-forwardable recap email. The board-meeting trigger gives urgency. Future-state slot is missing — call 2 should explicitly probe what good looks like in their words.

### Bredvick (Critique — Simplified)

**Stage-aware gaps:**
- Procurement-process owner unknown — reasonable to expect by mid-cycle
- No quantified cost-of-inaction — reasonable to expect on a substantive discovery call once business problem is named

**Glossed-over moments:**
- Buyer hedged on procurement: "it's its own thing"

**Hypotheses worth probing next call:**
- The real concern around timeline may be procurement, not security — buyer pivots away from procurement specifically. Confidence: plausible.

**Critique-pass assessment:** Discovery surfaced problem and cause but stalled before impact and future-state. Worth pushing into negative-impact territory next call.

*v1 limitation: this pass does NOT compare against prior calls. Cross-call contradictions and trend tracking will come once the storage/retrieval layer is built.*

### Operational

**Stakeholder map (this call):**
- Sarah Chen, VP Sales — champion (highest engagement, set agenda)
- Marcus Lee, RevOps Director — buyer-side present but quiet (one statement); role unclear

**Commitment asymmetry:**
- Buyer-side commitments: 1 (vague — "I'll think about it")
- Seller-side commitments: 3 (ROI model, customer references, follow-up call)
- Asymmetry assessment: Seller-heavy (3 vs 1, and the buyer's commitment is vague). Pattern of advancing without buyer reciprocity is a deal-stage risk.

**Trigger events:**
- Board meets in October with forecast accuracy on agenda — buyer-stated, specific, consequential.

**Competitive landscape:**
- Status quo (current process) referenced as the alternative; no other vendor mentioned.

**Happy-ears flags:**
- "This looks really interesting — let me think about it." — enthusiasm without commitment.

**Risk signals:**
- Repeated deflection on procurement timeline.

**Engagement read:** Sarah was engaged and specific on the problem and root cause but went vague on procurement and timeline. Engagement is real but with clear avoidance patterns on operational topics.
```

## Schema Drift Prevention

If you find yourself writing an item type, framework tag, or frontmatter field that isn't in this file:

1. **Stop.** Don't add it ad-hoc.
2. Update this file (`artifact-schema.md`) with the new field/type/tag.
3. Update the relevant lens reference if the addition is pass-specific.
4. Commit both changes together.

The schema is the contract. Drift between this file and lens references is a real bug — it makes the artifact's metadata unreliable for downstream consumers.
