---
name: sales-extractor
description: "Processes sales call transcripts, demos, and internal deal chats into structured framework-grounded extractions via multi-pass analysis. Triggers on pasted transcripts, 'process this transcript', 'extract from this call', 'debrief this transcript', 'here's the transcript', 'demo recap', 'internal call about [account]', or shared Granola/Gong notes. Auto-detects input mode (sales-call, demo, internal-deal-chat) and runs five framework lenses (Surface, Orlob, Nasralla, Bredvick-simplified, Operational) per call. Also handles 'deep dive' re-processing. Supersedes sales-transcript-extractor (single-pass). For external company research, see sales-account-research. For deal health scoring, see sales-deal-review."
---

# Sales Extractor

Process raw sales-related transcripts (calls, demos, internal deal discussions) into a structured framework-grounded extraction artifact. Each call is analyzed through five sequential framework lenses, producing typed items with auditable provenance. The artifact becomes the canonical working document downstream skills and ad-hoc questions pull from.

This skill supersedes `sales-transcript-extractor` (single-pass). The existing storage layout, account model, and identification flow are preserved; the extraction itself is now multi-pass.

## Core Principles

1. **Buyer's words are sacred.** When a buyer uses specific terminology, codenames, or phrasing, capture exact words. Verbatim quotes pass the forwarding test. Carry forward unchanged from `sales-transcript-extractor`.

2. **Quotes are the confidence signal.** Direct quote present = high-confidence, can be used directly in artifacts. Quote absent = interpretation, may need confirmation. No separate confidence field.

3. **Framework-grounded multi-pass.** Each extraction runs sequentially through specialized passes — one job per pass, one named standard per pass. The artifact's quality is auditable against named frameworks (Orlob, Nasralla, Bredvick-simplified, plus Surface and Operational). Never one prompt doing five things.

4. **Item-typed, provenance-tagged.** Every item declares its `item_type` and lists which pass(es) produced or tagged it. Downstream consumers query by type; auditors trace by provenance.

5. **Mode-aware extraction.** Sales-call, demo, and internal-deal-chat have different priorities (no buyer in an internal chat, different signals in a demo). Same scaffold, different pass selections per mode.

6. **Extract signal, not noise.** Item count is roughly the same as single-pass output (~10-25 typed items per call). Multi-pass adds *structure and provenance*, not bulk.

7. **Pre-analytical at the surface, framework-classified at the lens.** The Surface pass captures what was said faithfully. Subsequent lens passes apply named methodologies. Frameworks layer on top of evidence; they don't replace it.

8. **Cost is the explicit tradeoff.** Five sequential passes cost more than one. This is by design — extraction is async, the signal compounds, and downstream skills consume the result many times. See `references/artifact-schema.md` for token-budget guidance.

## Workflow

### Phase 1: Receive and Identify

When the user provides a transcript or input:

1. **Check for deep dive mode** — If the user is requesting a deep dive or re-process of an existing extraction, skip to the Deep Dive section.

2. **Identify the account** — Look for company names, people names, or context clues. If not obvious, ask:
   > "What account/company is this about?"

3. **Identify input metadata:**
   - **Date** — default to today if not specified.
   - **Mode hint** — check whether the user prefixed the input with an explicit mode (e.g., "internal call:", "demo:", "discovery:"). If present, use that mode. Otherwise auto-detect in Phase 2.
   - **Participants** — names and roles of speakers. If the transcript uses "Speaker 1" / "Speaker 2", ask:
     > "I see unnamed speakers. Can you tell me who was on this call and their roles? (Or I can work with Speaker 1 / Speaker 2 and you can fill in names later.)"

4. **Create the account slug** — lowercase, hyphenated company name (e.g., `acme-corp`, `global-payments`).

### Phase 2: Detect Mode

Read [references/mode-profiles.md](references/mode-profiles.md). Apply auto-detection signals in priority order:

1. Explicit user hint (from Phase 1)
2. Clear linguistic markers (product-walkthrough phrases → demo; only-internal speakers + decision/dissent language → internal-deal-chat)
3. Speaker composition (multiple speakers with buyer/seller dynamic → sales-call)
4. Default to `sales-call` on uncertainty

State the detected mode in one line and proceed unless the user overrides:
> "Detected mode: **demo** — running Surface, Orlob, Nasralla, Bredvick (simplified), and Operational with capability-to-pain emphasis. Override?"

Look up the mode's pass list and emphases in `mode-profiles.md`.

### Phase 3: Store Raw Transcript

1. Create directory structure if it doesn't exist:
   ```
   ~/.agents/sales/<account-slug>/transcripts/
   ~/.agents/sales/<account-slug>/extractions/
   ```

2. Save the raw input exactly as provided:
   ```
   ~/.agents/sales/<account-slug>/transcripts/YYYY-MM-DD-<mode>.md
   ```
   If a file with that name already exists, append a number: `YYYY-MM-DD-<mode>-2.md`.

### Phase 4: Run Passes (Sequential, Mode-Conditional)

For each pass in the mode profile's pass list, in order:

1. **Read the pass reference file** — `references/<pass>-pass.md` (e.g., `references/surface-pass.md`, `references/orlob-pass.md`).

2. **Construct the focused extraction prompt internally**, combining:
   - the loaded reference's extraction priorities, item types, quality standards, and prompt-shaping guidance
   - the transcript (or relevant chunk if chunked)
   - items collected by prior passes, passed forward as **context only** (do not re-extract; build on)

3. **Emit typed items**, each tagged with `provenance: <pass-name>` and any framework_tags the pass applies. Append to in-flight extraction state.

4. **On pass failure** (no items returned, error, or model refusal): record the pass in `passes_failed` in frontmatter and continue to the next pass. Do NOT abort the extraction. Partial-extraction-with-honest-frontmatter beats hard failure.

The five passes:

| Pass | Reference | Job |
|------|-----------|-----|
| Surface | [references/surface-pass.md](references/surface-pass.md) | Capture what was said. Faithful items with quotes. |
| Orlob | [references/orlob-pass.md](references/orlob-pass.md) | Three-bucket classification, phase tagging, No Logo Test, quantified impact. |
| Nasralla | [references/nasralla-pass.md](references/nasralla-pass.md) | Soundbite candidates, cost-of-inaction, metric-that-matters. |
| Bredvick (simplified) | [references/bredvick-pass.md](references/bredvick-pass.md) | Gap detection for stage; glossed-over moments. **No cross-call contradiction detection in v1.** |
| Operational | [references/operational-pass.md](references/operational-pass.md) | Stakeholders, decisions/commitments, signals (happy ears, asymmetry, energy, risk). Mode-specific emphases. |

### Phase 5: Collate

After all passes run:

1. **Group items by `item_type`.**
2. **Dedupe within each group:**
   - Items with quotes: match by exact quote text (whitespace-normalized). If matched, merge.
   - Items without quotes: match by whole-content string. If matched, merge.
   - On merge: combine provenance arrays; combine framework_tags arrays (deduped).
3. **Sort items within each group** by source-of-discovery (first-pass-found first).

(Detailed dedup behavior and Unit 5's exact algorithm are filled in by the Unit 5 implementation pass — this skeleton documents the intent.)

### Phase 6: Compose Artifact

Build the artifact per [references/artifact-schema.md](references/artifact-schema.md):

1. **Frontmatter** — accumulated metadata (account, date, mode, participants, passes_run, passes_failed if any, summary stats including no_logo_rating from Orlob, item_count_by_type).
2. **TL;DR** — 3 sentences max, composed from operational-pass key items + Nasralla soundbite candidates.
3. **Items** — grouped by `item_type` per the schema.
4. **Framework Notes** — cross-cutting per-pass summaries (Orlob's rating + bucket distribution; Nasralla's soundbites + cost-of-inaction; Bredvick-simplified's gaps + glossed-over).

No separate synthesis pass for v1.

### Phase 7: Write and Report

1. **Write the extraction artifact** to:
   ```
   ~/.agents/sales/<account-slug>/extractions/YYYY-MM-DD-<mode>.md
   ```

2. **Confirm completion** to the user:
   > **Extraction complete.**
   >
   > Raw transcript: `~/.agents/sales/<account>/transcripts/<filename>`
   > Extraction: `~/.agents/sales/<account>/extractions/<filename>`
   >
   > Mode: `<mode>` (auto-detected — confirm or override?)
   > Passes run: `[surface, orlob, nasralla, bredvick-simplified, operational]`
   > [`passes_failed: [<pass>]` if any]
   >
   > [TL;DR derived from artifact]

---

## Deep Dive Mode

When the user requests deeper analysis of a previously processed input ("deep dive on the Acme call", "re-process the last extraction", "I think we missed something"):

1. **Find the original** — locate the existing extraction in `~/.agents/sales/<account>/extractions/`. If ambiguous, ask which one to re-process.

2. **Read both files** — original raw input AND existing extraction.

3. **Run a deeper Bredvick-style pass** — explicitly look for:
   - **Dropped threads** — topics raised or hinted at, not followed up
   - **Subtle signals** — hesitation, qualifiers, energy shifts
   - **Implications not explored** — statements implying something important, not unpacked
   - **Stakeholder clues** — names, roles, dynamics mentioned in passing
   - **Within-call contradictions** — moments where the buyer said one thing early and another later

4. **Produce a supplementary extraction** — don't replace the original. Save as:
   ```
   ~/.agents/sales/<account-slug>/extractions/YYYY-MM-DD-<mode>-deep-dive.md
   ```
   In each item's `Why it matters`, note why the standard pass may have missed it — subtle, buried in tangent, only meaningful in context.

5. **Output a deep-dive summary** — compare supplementary findings to the original. Highlight what's new and why.

Cross-call contradiction detection is **deferred to a future version** once the storage/retrieval layer (Q2) is built.

---

## What This Skill Does NOT Do

- **No coaching.** Does not evaluate seller performance. A coaching skill can do its own pass on the raw transcript.
- **No framework scoring.** Does not score against MEDDPICC, SPICED, or any methodology end-to-end. Lens passes apply specific framework lenses (Orlob's 3-bucket, Nasralla's forwarding test, etc.) but do not produce holistic deal scores. `sales-deal-review` is the scoring skill.
- **No cross-transcript synthesis.** Processes one input at a time. Cross-call queries, contradictions, and per-account synthesis are deferred to the Q2 storage/retrieval layer.
- **No artifact generation.** Does not produce follow-up emails, business cases, or demo plans. Those are separate downstream skills that read from the extraction artifacts.
- **No external integrations.** Input is pasted text. No Gong API, no Salesforce sync.
- **No voice-note ingestion.** Voice notes are structurally different (action-oriented, conversational, brainstorm-shaped). They will be served by a future dedicated skill, not a mode of this extractor.
- **No prospect-chat mode (yet).** Prospect-chat mode is deferred to v1.5 once real examples clarify what counts as one.

---

## Reference Files

| File | Purpose |
|------|---------|
| [references/mode-profiles.md](references/mode-profiles.md) | Three input modes; pass selections; mode-specific emphases; auto-detection signals. |
| [references/surface-pass.md](references/surface-pass.md) | What was said, faithfully. Item types, buyer-language preservation, quote handling. |
| [references/orlob-pass.md](references/orlob-pass.md) | Discovery depth — 3-bucket classification, phase tagging, No Logo Test, quantified impact. |
| [references/nasralla-pass.md](references/nasralla-pass.md) | Forwarding test — soundbite candidates, cost-of-inaction, metric-that-matters. |
| [references/bredvick-pass.md](references/bredvick-pass.md) | Critique pass (simplified) — gaps for stage, glossed-over moments. |
| [references/operational-pass.md](references/operational-pass.md) | Stakeholders, decisions/commitments, operational signals; mode-specific emphases. |
| [references/artifact-schema.md](references/artifact-schema.md) | File location, frontmatter schema, item layout, item-type catalogue, worked example. |
