---
name: sales-extractor
description: "Processes sales call transcripts, demos, and internal deal chats into structured framework-grounded extractions via multi-pass analysis. Triggers on pasted transcripts, 'process this transcript', 'extract from this call', 'debrief this transcript', 'here's the transcript', 'demo recap', 'internal call about [account]', or shared Granola/Gong notes. Auto-detects input mode (sales-call, demo, internal-deal-chat) and runs five framework lenses (Surface, Discovery-Depth, Forwarding-Test, Critique (Simplified), Operational) per call. Also handles 'deep dive' re-processing. Supersedes sales-transcript-extractor (single-pass). For external company research, see sales-account-research. For deal health scoring, see sales-deal-review."
---

# Sales Extractor

Process raw sales-related transcripts (calls, demos, internal deal discussions) into a structured framework-grounded extraction artifact. Each call is analyzed through five sequential framework lenses, producing typed items with auditable provenance. The artifact becomes the canonical working document downstream skills and ad-hoc questions pull from.

This skill supersedes `sales-transcript-extractor` (single-pass). The existing storage layout, account model, and identification flow are preserved; the extraction itself is now multi-pass.

## Core Principles

1. **Buyer's words are sacred.** When a buyer uses specific terminology, codenames, or phrasing, capture exact words. Verbatim quotes pass the forwarding test. Carry forward unchanged from `sales-transcript-extractor`.

2. **Quotes are the confidence signal.** Direct quote present = high-confidence, can be used directly in artifacts. Quote absent = interpretation, may need confirmation. No separate confidence field.

3. **Framework-grounded multi-pass.** Each extraction runs sequentially through specialized passes — one job per pass, one named standard per pass. The artifact's quality is auditable against named frameworks (Discovery-Depth, Forwarding-Test, Critique (Simplified), plus Surface and Operational). Never one prompt doing five things.

4. **Item-typed, provenance-tagged.** Every item declares its `item_type` and lists which pass(es) produced or tagged it. Downstream consumers query by type; auditors trace by provenance.

5. **Mode-aware extraction.** Sales-call, demo, and internal-deal-chat have different priorities (no buyer in an internal chat, different signals in a demo). Same scaffold, different pass selections per mode.

6. **Extract signal, not noise.** Item count is roughly the same as single-pass output (~10-25 typed items per call). Multi-pass adds *structure and provenance*, not bulk.

7. **Pre-analytical at the surface, framework-classified at the lens.** The Surface pass captures what was said faithfully. Subsequent lens passes apply named methodologies. Frameworks layer on top of evidence; they don't replace it.

8. **Cost is the explicit tradeoff.** Five sequential passes cost more than one. This is by design — extraction is async, the signal compounds, and downstream skills consume the result many times. See `references/artifact-schema.md` for token-budget guidance.

## Running State Across Passes

The orchestration maintains a single in-flight extraction state through the workflow. State elements:

- **`items`** — accumulating list of typed items. Each pass appends new items and/or modifies existing items (adding `framework_tags`, adding fields like `owner` to commitment items).
- **`framework_notes`** — a dict keyed by pass name. Each pass that produces a Framework Notes contribution writes its block here.
- **`metadata`** — accumulating frontmatter fields (account, date, mode, participants, passes_run, passes_failed, specificity_rating).

What each pass receives as input:
- The transcript
- Its own reference file (loaded fresh at pass start)
- The current `items` list (so it can build on prior-pass output rather than re-extract)
- The mode profile (so it knows mode-specific emphases)

What each pass returns:
- New items to append to `items`, OR modifications to existing items (adding tags or fields)
- A Framework Notes block to add to `framework_notes`
- Optional metadata contributions (e.g., Discovery-Depth contributes `specificity_rating` to `metadata`)

Passes do **not** re-extract content already captured. They build on the running state.

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
> "Detected mode: **demo** — running Surface, Discovery-Depth, Forwarding-Test, Critique (Simplified), and Operational with capability-to-pain emphasis. Override?"

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

1. **Read the pass reference file** — `references/<pass>-pass.md` (e.g., `references/surface-pass.md`, `references/discovery-depth-pass.md`).

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
| Discovery-Depth | [references/discovery-depth-pass.md](references/discovery-depth-pass.md) | Three-bucket classification, phase tagging, specificity rating, quantified impact. |
| Forwarding-Test | [references/forwarding-test-pass.md](references/forwarding-test-pass.md) | Soundbite candidates, cost-of-inaction, metric-that-matters. |
| Critique (Simplified) | [references/critique-pass.md](references/critique-pass.md) | Gap detection for stage; glossed-over moments. **No cross-call contradiction detection in v1.** |
| Operational | [references/operational-pass.md](references/operational-pass.md) | Stakeholders, decisions/commitments, signals (happy ears, asymmetry, energy, risk). Mode-specific emphases. |

### Phase 5: Collate

After all passes run, normalize and deduplicate the accumulated `items` list.

**Step 1 — Group by `item_type`.** Partition `items` into groups where each group contains items of one type (all `buyer-statement` items together, all `stakeholder-mention` items together, etc.). Item types are the catalogue defined in `references/artifact-schema.md`.

**Step 2 — Dedupe within each group.** For each pair of items in the same group, decide whether they're duplicates and merge if so:

- **Items with a `quote` field present (both):** match by exact quote text after whitespace normalization (collapse runs of whitespace to single spaces, trim ends). If quotes match exactly → merge.
- **Items where one has a quote and the other doesn't:** do NOT auto-merge. Different evidence levels — keep separate. (A later v1.5 may add similarity-based merging here; for v1, keep them.)
- **Items without quotes (both):** match by whole-content string of the `summary` field after whitespace normalization. If summaries match exactly → merge.
- **All other cases:** keep separate. False-merge is more harmful than false-split for v1.

**Step 3 — Merge logic when two items match:**
- Combine `provenance` arrays (deduped, order preserved by first-appearance).
- Combine `framework_tags` arrays (deduped).
- Keep all other fields from the *first* item (which was created earlier in the pass sequence). If a later pass added structured fields (e.g., Operational pass added `owner`/`timeframe` to a Surface `commitment`), those should be preserved — they were attached to the existing item, not created on a new item, so this case isn't actually a dedup.

**Step 4 — Sort within each group.** Order items by source-of-discovery: first-pass-found first (Surface items appear before Critique-only items in the Items section). Within a single pass, preserve the order the pass returned them.

**Step 5 — Compute `item_count_by_type`.** Count items per type for the frontmatter `summary_stats.item_count_by_type`.

If false-split or false-merge rates become observable problems on real transcripts, revisit this algorithm. v1 errs on the side of false-split.

### Phase 6: Compose Artifact

Build the artifact per [references/artifact-schema.md](references/artifact-schema.md). Composition is mechanical assembly from the running state — no new extraction or analysis happens here.

**Step 1 — Frontmatter.** Emit the YAML block per the schema:
- `account`, `date`, `mode`, `mode_detected_via` from `metadata`
- `participants` from `metadata`
- `passes_run` from `metadata` (in execution order)
- `passes_failed` from `metadata` (empty array if none)
- `specificity_rating` from `metadata` (omit field entirely if Discovery-Depth didn't run or didn't produce one — common for `internal-deal-chat` mode)
- `summary_stats.item_count_total` and `summary_stats.item_count_by_type` computed in Phase 5
- `raw_transcript: ../transcripts/<filename>` pointer

**Step 2 — TL;DR composition.** Generate 3 sentences max by combining:
- One sentence on the dominant business problem (from items tagged `discovery-depth:business-problem` if present, else from the most-prominent `buyer-statement` items, else "Internal deal-chat — no buyer in the room")
- One sentence on key signals (from Operational pass: stakeholder identification, commitment asymmetry, trigger events)
- One sentence on what's open or worth probing (from Critique gaps and hypotheses, or from `open-question` items)

The TL;DR should pass the forwarding test — read it as if a champion were forwarding to a CFO. If it sounds like AI-summary-speak, rewrite using buyer language from the items.

**Step 3 — Items section.** For each `item_type` group (in this order: buyer-statement, seller-statement, commitment, open-question, stakeholder-mention, technical-reaction, capability-pain-mapping, decision, owner-assignment, concern, hypothesis-update, dissent, trigger-event, competitor-mention, happy-ears-flag, risk-signal, gap-for-stage, glossed-over-moment, hypothesis), emit a subsection with all items in the group. Skip groups that are empty for this artifact.

Each item rendered per the layout in `artifact-schema.md`: heading with `[<item_type>] <Title>`, metadata block, optional blockquote, summary, why-it-matters, optional buyer-language, separator.

**Step 4 — Framework Notes.** For each pass that produced a Framework Notes contribution (in this order: Discovery-Depth, Forwarding-Test, Critique (Simplified), Operational), emit its block per its reference file's template. Include the v1-limitation footer in the Critique block. Skip passes that didn't run or failed.

No separate synthesis pass — Framework Notes are assembled from per-pass outputs already collected during Phase 4.

### Phase 7: Write and Report

**Step 1 — Write the extraction artifact** to:
```
~/.agents/sales/<account-slug>/extractions/YYYY-MM-DD-<mode>.md
```
If a file with that exact name already exists, append a sequence suffix (`-2.md`, `-3.md`, etc.).

**Step 2 — Report to the user.** Output the following block in the conversation:

> **Extraction complete.**
>
> **Raw transcript:** `~/.agents/sales/<account>/transcripts/<filename>`
> **Extraction:** `~/.agents/sales/<account>/extractions/<filename>`
>
> **Mode:** `<mode>` (detected via: `<mode_detected_via>`)
> **Passes run:** `[<list>]`
> [If `passes_failed` is non-empty: `**⚠ Passes failed:** [<list>] — partial extraction; some signals may be missing.`]
> [If `specificity_rating` was produced: `**specificity rating:** <rating>/5`]
>
> **TL;DR:**
> <three-sentence TL;DR from the artifact>
>
> [If significant gaps were flagged by Critique: `**Worth probing next call:** <list of bredvick gaps and hypotheses>`]
> [If `mode_detected_via: default`: `**Note:** Mode was a default-fallback. Confirm or override before relying on this extraction.`]

This report is the "feedback summary" — it's what makes processing a transcript worth doing. The user sees it immediately; the extraction file is there for downstream consumption.

---

## Deep Dive Mode

When the user requests deeper analysis of a previously processed input ("deep dive on the Acme call", "re-process the last extraction", "I think we missed something"):

1. **Find the original** — locate the existing extraction in `~/.agents/sales/<account>/extractions/`. If ambiguous, ask which one to re-process.

2. **Read both files** — original raw input AND existing extraction.

3. **Run a deeper Critique-style pass** — explicitly look for:
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
- **No framework scoring.** Does not score against MEDDPICC, SPICED, or any methodology end-to-end. Lens passes apply specific framework lenses (the 3-bucket, the forwarding test, etc.) but do not produce holistic deal scores. `sales-deal-review` is the scoring skill.
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
| [references/discovery-depth-pass.md](references/discovery-depth-pass.md) | Discovery depth — 3-bucket classification, phase tagging, specificity rating, quantified impact. |
| [references/forwarding-test-pass.md](references/forwarding-test-pass.md) | Forwarding test — soundbite candidates, cost-of-inaction, metric-that-matters. |
| [references/critique-pass.md](references/critique-pass.md) | Critique pass (simplified) — gaps for stage, glossed-over moments. |
| [references/operational-pass.md](references/operational-pass.md) | Stakeholders, decisions/commitments, operational signals; mode-specific emphases. |
| [references/artifact-schema.md](references/artifact-schema.md) | File location, frontmatter schema, item layout, item-type catalogue, worked example. |
