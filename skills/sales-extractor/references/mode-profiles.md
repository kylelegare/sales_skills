# Mode Profiles

Defines the three v1 input modes for `sales-extractor`: which framework lenses run, what mode-specific emphases apply, and how to auto-detect mode from input.

Voice-note and prospect-chat modes are **NOT defined here** — both are deferred (voice-note → its own future action-taking skill; prospect-chat → v1.5 once real examples exist).

## Pass Selection Matrix

| Mode | Surface | Orlob | Nasralla | Bredvick (simplified) | Operational |
|------|:-------:|:-----:|:--------:|:---------------------:|:-----------:|
| `sales-call` | ✓ | ✓ | ✓ | ✓ | ✓ |
| `demo` | ✓ | ✓ | ✓ | ✓ | ✓ + tech-reaction emphasis |
| `internal-deal-chat` | ✓ | — | — | ✓ | ✓ + decisions/owners/dissent emphasis |

**Why some passes are skipped for `internal-deal-chat`:**
- Orlob is buyer-discovery focused — no buyer in the room to discover.
- Nasralla is forwarding-test focused — internal-chat output isn't being forwarded to a buyer.
- Surface still runs because internal language matters; Bredvick still runs because gap/glossed-over patterns apply within team thinking; Operational dominates because it's the natural shape of internal discussion.

## Mode Profiles (Detail)

### Mode: `sales-call`

**When this mode applies:** Any conversation with at least one buyer-side participant — discovery, qualification, exec meetings, pricing/negotiation calls, check-ins, QBRs, kickoffs.

**Passes run:** Surface → Orlob → Nasralla → Bredvick (simplified) → Operational. **All five.**

**Emphases:** None beyond each pass's default. This is the baseline mode.

**Item-type distribution to expect:**
- Surface: `buyer-statement`, `seller-statement` (only when carrying commitment or context), `commitment`, `open-question`
- Orlob: framework_tags layered onto Surface items (`orlob:business-problem`, `orlob:root-cause`, `orlob:desired-solution`, `orlob:phase-X`); plus a `no_logo_rating` summary
- Nasralla: framework_tags + Framework Notes contribution (soundbite candidates, cost-of-inaction)
- Bredvick: `gap-for-stage`, `glossed-over-moment`, `hypothesis`
- Operational: `stakeholder-mention`, `commitment` (with owner/timeframe), `trigger-event`, `competitor-mention`, `happy-ears-flag`, `risk-signal`

---

### Mode: `demo`

**When this mode applies:** Product demonstrations and walkthroughs where the seller is showing the product and the buyer is reacting. Often co-occurs with discovery (a discovery-then-demo call should be processed as `demo` if the demo is the dominant phase, otherwise as `sales-call`).

**Passes run:** Surface → Orlob → Nasralla → Bredvick (simplified) → Operational. **All five.**

**Emphases (added to default behavior):**
- **Operational pass adds `technical-reaction` items.** Capture moments where the buyer leaned in vs. tuned out, asked clarifying questions, expressed surprise (positive or negative), or fell silent during specific capabilities. Note timestamp or location in the transcript when possible.
- **Capability-to-pain mapping.** When the seller demonstrates a capability that maps to a buyer-stated pain (from this call or a prior extraction visible in context), flag it explicitly. When the seller demonstrates a capability that does NOT map to any stated pain, that's also a flag (potential demo-bloat).
- **Anti-pattern detection.** If the seller demonstrates something that contradicts a buyer-stated root cause (e.g., buyer said "we need a unified UX," seller demos a separate admin portal), flag it as a `risk-signal` with `framework_tag: anti-pattern`.

**Item-type distribution to expect:** Same as sales-call plus heavier `technical-reaction` and `capability-pain-mapping` from Operational. Less weight on Orlob's discovery items unless there's a discovery phase in the call.

---

### Mode: `internal-deal-chat`

**When this mode applies:** Internal team discussions about a specific deal — strategy syncs, pre-call prep, post-call debriefs, Slack threads about an account, deal-review meetings, "what should we do about Acme?" conversations.

**Passes run:** Surface → Bredvick (simplified) → Operational. **Three passes — Orlob and Nasralla skipped.**

**Emphases (added to default behavior):**
- **Operational pass dominates** and prioritizes:
  - `decision` items (what was decided, who decided, what alternatives were considered)
  - `owner-assignment` items (who is doing what by when)
  - `concern` items (what worries the team about this deal)
  - `hypothesis-update` items (how the team's read of the buyer / deal changed)
  - `dissent` items (where teammates disagreed and how it was resolved or not)
- **Surface items** flag `seller-statement` for everything (no buyer in the room) but should still preserve specific phrasing — internal codenames, named hypotheses, idioms ("we're in the dark on procurement").
- **Bredvick (simplified) pass** reframes from "what's missing for the BUYER's stage" to "what's missing for OUR understanding of the deal" — gaps in our model of the account, glossed-over assumptions about the buyer.

**Item-type distribution to expect:** Light on Surface items; heavy on Operational items (`decision`, `owner-assignment`, `concern`, `hypothesis-update`); some Bredvick gaps and glossed-over moments. NO Orlob or Nasralla framework_tags.

---

## Auto-Detection Signals

The skill's Phase 2 applies these signals **in priority order**. Stop at the first signal that matches with reasonable confidence; otherwise fall through to the next priority.

### Priority 1: Explicit user hint

The user prefixed the input with one of:
- `sales-call:`
- `demo:`
- `discovery:` (treat as `sales-call`)
- `internal call:`
- `internal:`
- `internal sync:`
- `team chat:`
- `slack thread:` (treat as `internal-deal-chat`)
- `debrief:` (ambiguous — fall through to other signals)

OR the user stated the mode in surrounding context: "this is a demo recording," "internal sync about Acme," "Slack thread from the deal team."

### Priority 2: Linguistic markers

**Demo signals (any 2 of these → `demo`):**
- Product-walkthrough phrases: "let me show you," "if I click here," "here's how that works," "you can see," "let me share my screen"
- Capability-question-response pattern: buyer asks "does it do X?" → seller demos
- Long single-speaker stretches followed by reactive comments
- Filename or transcript header contains "demo" or "walkthrough"

**Internal-deal-chat signals (any 2 of these → `internal-deal-chat`):**
- Speakers all appear to be from the same organization (e.g., all use "we" referring to the seller-side org; no one uses "your team" toward another speaker)
- Discussion is *about* an account, not *with* an account
- Decision/strategy language: "should we," "let's go with," "I disagree," "the play here," "next step is to..."
- Slack-thread paste markers (timestamps in messaging-app format, message-and-reply structure)
- Filename or context contains "internal," "sync," "huddle," "debrief"

### Priority 3: Speaker composition

- **2+ speakers with clear buyer/seller dynamic** (one side asking discovery questions, other side describing problems) → `sales-call`
- **Speakers using "your" toward each other** (e.g., "your team," "your company") → `sales-call`
- **All speakers using "we" referring to the same org** → likely `internal-deal-chat` (re-check Priority 2)

### Priority 4: Default

If no signal fires with reasonable confidence, default to `sales-call` and explicitly flag the assumption to the user:
> "Detected mode: **sales-call** (default, weak signal). Override?"

The user can correct, and the override is captured in frontmatter as `mode_detected_via: user-override`.

---

## How the Skill Uses This File

The orchestrating model (Phase 2) reads this file once at the start of an invocation. After detecting mode:

1. Look up the pass list for the chosen mode in the matrix above.
2. Apply mode-specific emphases — these become prompt-shaping additions to the relevant pass's reference content (e.g., when running the Operational pass in `demo` mode, the emphasis section is appended to the pass guidance).
3. Confirm the mode (and its detected_via signal) to the user before running passes.
4. Record `mode`, `mode_detected_via`, and `passes_run` in the artifact frontmatter.

**Mode detection is auditable.** Frontmatter records both the mode and the signal that drove the decision (`mode_detected_via: explicit-hint | linguistic-marker | speaker-composition | default | user-override`). If extraction quality drops, the detection signal is the first thing to check.
