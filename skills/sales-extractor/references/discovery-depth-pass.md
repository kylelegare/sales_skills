# Discovery-Depth Pass

The discovery-depth lens. Tags Surface items against the framework: three-bucket classification, four-phase architecture, specificity rating, quantified impact, above-the-line vs below-the-line.

This pass does **not** create new items. It adds `framework_tags` to items the Surface pass produced and contributes a Framework Notes summary (specificity rating, bucket distribution, phase distribution).

## Source Material

The canonical Discovery-Depth methodology is documented at `~/.claude/skills/sales-call-prep/references/discovery-methodology.md` (used by `sales-call-prep` to *generate* discovery questions). This pass uses the same framework on the *receiving* side — to classify what the buyer said back.

The two skills point at the same foundation:
- `sales-call-prep` → "what should I ask?" (uses the question patterns)
- `sales-extractor` discovery-depth pass → "what did they say?" (classifies the answers)

## What This Pass Tags

Reads the items emitted by the Surface pass. For each item where the framework applies (mostly `buyer-statement`, sometimes `seller-statement` with buyer reaction), adds:

1. **Three-bucket classification** as a `framework_tag`:
   - `discovery-depth:business-problem` — a metric that is suffering, owned by the decision maker
   - `discovery-depth:root-cause` — a behavior or symptom that produces the suffering metric
   - `discovery-depth:desired-solution` — buyer expresses a need in a way that implies an action ("we need X")

2. **Phase tag** as a `framework_tag` indicating where in the discovery depth chain this item lives:
   - `discovery-depth:phase-1-problem` — surfacing the business problem
   - `discovery-depth:phase-2-cause` — exploring root causes
   - `discovery-depth:phase-3-impact` — exploring negative impact / cost of inaction
   - `discovery-depth:phase-4-future-state` — building buying criteria / desired-state language

3. **Quantification flag** when applicable:
   - `discovery-depth:quantified` — item contains a specific metric, dollar figure, date, percentage, or named owner. These are gold; downstream artifacts (business cases, exec summaries) lean on quantified items first.

4. **Above-the-line flag** when applicable:
   - `discovery-depth:above-the-line` — the metric or impact described is in the decision maker's top priorities (not a brush fire)
   - `discovery-depth:below-the-line` — peripheral/operational concern, not boardroom-level

## What This Pass Contributes to Framework Notes

A summary block in the artifact's Framework Notes section:

```markdown
### Discovery Depth

**specificity rating rating:** [1-5] — [one-line rationale]

**Three-bucket distribution:**
- Business problems: [count] — [list of titles]
- Root causes: [count] — [list of titles]
- Desired solutions: [count] — [list of titles]

**Phase distribution:**
- Phase 1 (problem): [count]
- Phase 2 (cause): [count]
- Phase 3 (impact): [count]
- Phase 4 (future state): [count]

**Quantified items:** [count of items tagged `discovery-depth:quantified`]
**Above-the-line items:** [count]

**Discovery depth assessment:** [1-3 sentences — does this call advance through the depth chain, or stall at one phase?]
```

## Quality Standards

### The specificity rating (1-5 rating)

The single most important Discovery-Depth check. **Could a stranger remove the account name from the extraction's problem-related items and still identify which account this is?**

- **5/5** — The problem description is unique, detailed, account-specific. Quantified, named owners, account-specific language. Could only be this account.
- **4/5** — Specific and quantified, but a similar problem could plausibly apply to a competitor in the same vertical.
- **3/5** — Specific to a vertical or company size, not unique to this account. Some quantification.
- **2/5** — Generic-with-some-context. "We need to be more efficient." "Sales is hard." Vague.
- **1/5** — Could describe any company. No metrics, no names, no specifics.

The rating goes in the artifact's frontmatter (`specificity_rating: 4`) and in the Framework Notes block. **Aim is ≥4 on real discovery calls.** A rating ≤3 is a discovery-quality flag worth surfacing — the call may have stayed at the surface without going deep enough.

### Three-Bucket Classification

The buyer says many things. Each thing falls into one of three buckets — and the bucket matters for downstream skills:

- **Business problem** — A suffering metric the decision maker owns. Always has a measurable metric attached. Examples:
  - "Close rates dropped from 25% to 20%"
  - "Pipeline coverage has dropped by 50%"
  - "Cross-sell rates are below 15% but should be 30%"
- **Root cause** — A symptom or behavior that produces the suffering metric. "Peeling back the onion" leads from root cause to business problem. Examples:
  - "Reps give terrible demos" → close rates suffer
  - "Data is scattered across spreadsheets" → low cross-sell
  - "Reps skip discovery" → low close rates
- **Desired solution** — Buyer expresses a need with implied action. Verb or implied action is the giveaway. Examples:
  - "We need better sales training"
  - "We need to unify the customer experience"
  - "We need a tool that does X"

**Why the distinction matters:**
- Business problem + quantification = the heart of a Forwarding-Test-style business case
- Root cause + capability = the heart of a demo plan ("our X solves your Y")
- Desired solution = often masks the real problem; the discovery-depth pass should flag desired-solution items so downstream skills can probe ("they're describing a solution; what's the underlying problem?")

If the buyer leads with a root cause, the discovery-depth pass tags `discovery-depth:root-cause` and the Framework Notes assessment may flag "discovery stalled at root cause — business problem not surfaced."

### Phase Tagging

Map each Discovery-Depth-relevant item to its phase in the discovery depth chain:

- **Phase 1 — Problem:** items where the buyer is surfacing or articulating a business problem
- **Phase 2 — Cause:** items where the buyer is articulating root causes
- **Phase 3 — Impact:** items about ripple effects, cost of inaction, second-order consequences, who-else-is-affected
- **Phase 4 — Future state:** items about what the buyer wants in a solution, buying criteria, desired-state language

A healthy discovery call shows progression through the phases. A call stuck in Phase 1 (only surfacing problems, never exploring causes or impact) is a flag.

### Above-the-Line vs Below-the-Line

For each `discovery-depth:business-problem` item: does the decision maker own this metric, and is it in their top priorities? Or is it a brush fire that may or may not get attention?

- **Above-the-line:** The DM owns it, it's high-priority, it likely shows up in their boardroom slides or QBR. Tag `discovery-depth:above-the-line`.
- **Below-the-line:** Peripheral, operational, or owned by someone below the DM. Tag `discovery-depth:below-the-line`.

If the call doesn't reveal which, leave untagged — the absence is a discovery gap (probable item for the critique pass).

### Quantification

Tag any item containing:
- A specific number, percentage, or dollar figure ("$200k/quarter," "19% close rate," "8 weeks")
- A specific date or deadline ("by Q3," "before March 15")
- A named owner with a title ("Sarah, our CFO")
- A specific count of stakeholders, users, or accounts affected

Tag as `discovery-depth:quantified`. Quantified items are downstream gold.

## Prompt-Shaping Guidance

When the discovery-depth pass runs, it receives the transcript AND the Surface pass's items. The model should:

**Be told to do:**
- For each Surface item, decide if it falls into the three buckets. If yes, add the appropriate `framework_tag` (`discovery-depth:business-problem` | `discovery-depth:root-cause` | `discovery-depth:desired-solution`).
- For each item touched, also tag the discovery phase (`discovery-depth:phase-1-problem` | `discovery-depth:phase-2-cause` | `discovery-depth:phase-3-impact` | `discovery-depth:phase-4-future-state`).
- For each item, evaluate quantification → tag `discovery-depth:quantified` if applicable.
- For business-problem items, evaluate above-the-line → tag accordingly when evidence supports.
- Compute the specificity rating rating (1-5) for the call as a whole.
- Compose the Framework Notes contribution per the template above.

**Be told to NOT do:**
- Re-extract items the Surface pass already captured.
- Invent items not present in Surface output (if a problem is implied but not stated, that's the critique pass's job to flag as a gap, not the job to fabricate).
- Apply the forwarding-test logic (that's the forwarding-test pass).
- Identify operational signals like happy ears or commitment asymmetry (Operational pass).
- Score the deal holistically (deal-review skill's job, not extraction's job).

**Output additions** (these are added to existing Surface items, not new items):

```yaml
# An existing Surface item, after discovery-depth pass:
item_type: buyer-statement
title: Close rates dropping creates pipeline-coverage problem
# ... existing Surface fields ...
provenance: [surface, orlob]
framework_tags:
  - discovery-depth:business-problem
  - discovery-depth:phase-1-problem
  - discovery-depth:quantified
  - discovery-depth:above-the-line
```

Plus a Framework Notes contribution (separate from per-item tags) with the specificity rating and distributions.

## Worked Examples

### Example 1: Business problem, quantified, above-the-line

Original Surface item:
> "We've gone from a 25% close rate to about 19% in the last two quarters, and the result is we're now scrambling to backfill pipeline every month."

Discovery-Depth tags added:
- `discovery-depth:business-problem` (suffering metric: close rate; owned by VP Sales)
- `discovery-depth:phase-1-problem` (surfacing the business problem)
- `discovery-depth:quantified` (specific 25% → 19%, two quarters)
- `discovery-depth:above-the-line` (VP Sales owns this, top of her priorities)

### Example 2: Root cause, no quantification

Original Surface item:
> "Honestly, our reps just don't run good discovery. They jump to demo too fast."

Discovery-Depth tags added:
- `discovery-depth:root-cause` (a behavior producing the suffering metric)
- `discovery-depth:phase-2-cause` (cause analysis)
- (no `discovery-depth:quantified` — no specific metric attached)
- (no above/below-the-line tag — this is a behavior, not a metric)

### Example 3: Desired solution masking a problem

Original Surface item:
> "What we really need is better discovery training for the team."

Discovery-Depth tags added:
- `discovery-depth:desired-solution` (verb/action implied: "training")
- `discovery-depth:phase-4-future-state` (solution language)

This item is a flag for downstream skills: the buyer is jumping to solution before fully articulating problem and cause. The critique pass may surface this as a "gap — buyer skipped to solution; underlying problem not fully explored."

## Non-Goals for This Pass

- No new item creation (Discovery-Depth does not produce new items; it tags existing ones)
- No Forwarding-Test soundbite identification
- No gap detection or glossed-over flagging (critique pass)
- No stakeholder or commitment work (Operational pass)
- No framework scoring beyond specificity rating (deal-review's job, not extraction's)
- No buyer-language preservation work (already handled by Surface)

## Discovery Depth Assessment Heuristic

When composing the Framework Notes, briefly assess the call's depth progression:

- **All four phases represented** with reasonable counts in each → "discovery is progressing through the depth chain"
- **Heavy Phase 1, light Phase 2-4** → "discovery surfaced problems but stalled before exploring root causes or impact"
- **Heavy Phase 4, light Phase 1-3** → "buyer is in solution-shopping mode without articulating underlying problem; risk of selling to a stated need without confirming the real problem"
- **No Phase 1** → "no clear business problem surfaced; the call may be premature for substantive discovery, or discovery was skipped"
- **All phases empty** → "no Discovery-Depth-classifiable items; this may not be a discovery call (check mode detection)"
