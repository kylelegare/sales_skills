---
name: sales-company-profile
description: "Generates a deep, framework-grounded company profile from public sources — Dunford positioning, Force Management value framework + 3-tier differentiation, Andy Raskin strategic narrative, multi-ICP support. One-time setup that other sales skills reference. Triggers on 'set up my company profile', 'analyze my company', 'create a company profile', 'here's my company website', 'deepen my company profile', 'refresh the profile', or when other sales skills lack product context. For researching prospect companies, see sales-account-research."
---

# Sales Company Profile Generator

Generate a framework-grounded company profile from public sources — your website, case studies, reviews, press releases, executive interviews, recent funding. This profile is the **bedrock substrate** every other sales skill pulls from for tailoring messaging, demos, business cases, and multi-thread outreach. Without it, AI doesn't know what you sell. With it, every transcript extraction, account research, follow-up email, and business case is contextualized to YOUR product, market, and ICP segments.

## Core Principles

1. **Framework-grounded, not website-summarized** — Three named frameworks ground the synthesis: April Dunford for positioning (category, segments, alternatives, unique attributes), Force Management for value framework + 3-tier differentiation (defensible / comparative / assumed), and Andy Raskin for strategic narrative (world-changing / change-the-game / promised land / cost of staying still). Each framework's quality standards apply. Don't regurgitate marketing copy — extract the underlying value architecture.

2. **Buyer's language over marketing language** — Capture how real buyers and reviewers describe the product, not how marketing describes it. G2 reviews, customer quotes in case studies, and testimonial language are more valuable than homepage hero copy.

3. **Multi-ICP supported, not assumed** — Many products serve materially different customer segments (e.g., community banks AND credit unions). The skill auto-detects ICPs from research signals and confirms with the user. Each ICP gets its own personas + value mappings; positioning, narrative, and core differentiation are product-level.

4. **Honest about what's producible** — Some sections will be richer than others depending on what's publicly available. A public company with earnings calls and 50 case studies produces a different profile than a Series A startup with a landing page. Don't fake depth. Flag gaps explicitly in the Honest Gaps section so downstream consumers know what's research-derived vs. needs direct input.

5. **Research-only in v1** — No required AE interview. Stage 2 (v1.5, future) is "react-and-refine" over a draft. Stage 3 (v2, future) is auto-refinement from accumulated extractions. Today: paste a URL, get a compelling first draft.

## Workflow

The skill orchestrates a 5-phase pipeline. Phase content for each is loaded from `references/` on demand; the orchestration logic itself lives in this SKILL.md.

### Phase 1: Receive & Identify

Get the company URL (from the user's request or by asking). Derive the product-slug (lowercase, hyphenated; defaults to slugified company-name when there's only one product, or asks the user when multiple products are clearly in play).

Check existing profiles and branch:

| Scenario | Handling |
|---|---|
| No existing profile | Fresh run. |
| Existing profile at old flat path (`~/.agents/sales/company-profile.md`) | Prompt: "Found a profile from the prior version. Regenerate with deeper framework grounding into the new product-aware path?" If yes, fresh-run + archive old. If no, exit. |
| Existing profile at new path (`~/.agents/sales/<product-slug>/company-profile.md`) | Update Mode candidate. Prompt: "Found existing profile for [product]. Update or regenerate fresh?" |
| Profiles at BOTH old and new paths | Warn user. Default to new. Archive old. |
| Malformed existing profile (frontmatter unparseable, sections corrupted) | Graceful parse attempt; fallback to fresh-run with warning. Archive corrupted file with `.malformed-<date>` suffix. |
| Multiple products inferred but single profile at old path | Clarify with user before migrating; product-slug = whichever product user identifies. |

**Step-by-step:**

1. **Get the company URL.** From the user's request, or ask: *"What's your company website? I'll analyze it to build your profile."* Get a confirmed URL before proceeding.

2. **Get the company and product names.** Parse company-name from the URL or homepage. If the user has multiple products, ask: *"What product is this profile for?"* — name + product-slug both. For single-product companies, default product-slug = slugified company-name.

3. **Detect existing profile state.** Check for files at:
   - Old flat path: `~/.agents/sales/company-profile.md`
   - New product-aware path: `~/.agents/sales/<product-slug>/company-profile.md`

4. **Branch on the migration matrix above.** For the migration scenarios:
   - **Old-only**: prompt user verbatim. If yes, plan to archive old + run fresh into new path. Archive happens in Phase 5 (after the new artifact succeeds — never delete old before new exists).
   - **New-only**: prompt user — Update or regenerate fresh? If Update, branch into Update Mode (skip to bottom of file). If fresh, continue.
   - **Both paths**: warn the user that both exist; default to using the new path; flag the old for archive after the run.
   - **Malformed existing**: try to parse frontmatter and section headings. If parse fails, warn user, archive corrupted to `<path>.malformed-<YYYY-MM-DD>.md`, treat as fresh-run.
   - **Multi-product inferred but single old profile**: ask user to clarify which product the old profile represents. Migrate accordingly.

5. **Get optional context.** *"Anything specific you want me to know that might not be on the website? (Optional — I can work with just the URL. Useful: pitch decks, battlecards, win-loss reports.)"* Pasted material gets incorporated during synthesis.

6. **Set frontmatter scaffolding** in the in-flight artifact state: company, product, product_slug, website, generated (today's date). Other frontmatter fields populate as later phases complete.

### Phase 2: Research

Load `references/research-sources.md` for the per-section source list and search query strategy. Run parallel web fetches across:

- Website pages (homepage, product, pricing, case studies, comparison, blog, careers, customers)
- Buyer voice (G2, Gartner Peer Insights, TrustRadius, Reddit, LinkedIn customer testimonials)
- Market & competitive context (competitor searches, funding, exec interviews/podcasts)
- Positioning context (analyst categorization, "vs"-pages, category-defining content)
- Strategic narrative (CEO/founder interviews, recent investor letters, conference talks, blog thought-leadership)
- Displacement signal (case-study before/after framing, "switched from X" review patterns)
- Trigger events (customer-win press, news re: customer companies, recent hires/leadership changes)

Aggregate research corpus in-memory.

**Multi-ICP detection** (after research, before synthesis): scan corpus for distinct customer segments using these signals:
- Case studies clearly segmented by industry / size / role
- Comparison or "for X / for Y" content on the website
- Distinct customer-logo clusters (visually grouped)
- Pricing tiers tied to customer profile
- Distinct G2 / Gartner segment mentions in reviews
- Explicit ICP statement on website

**Threshold:** ≥2 signals at the same ICP boundary AND segments would produce materially different value chains → propose multiple ICPs. If only 1 signal, default to single-ICP.

**User confirm step** — present candidate ICPs as a hypothesis with evidence. Avoid binary yes/no framing. If user is uncertain, ask them to identify ICPs themselves: *"List the customer types you actually sell to in your real practice."*

**Step-by-step:**

1. **Read `references/research-sources.md`** to know which sources to consult and what to extract from each.

2. **Run parallel web fetches.** Use the platform's web-fetch tool (WebFetch) and web-search tool (WebSearch) in parallel for the source categories enumerated in research-sources.md:
   - Website pages: 8-10 distinct page-fetches (homepage, product, pricing, case studies, comparison, blog, careers, customers, about)
   - Buyer voice: 3-5 search queries (G2, Gartner, TrustRadius, Reddit if industry-relevant, LinkedIn customer testimonials)
   - Market context: 4-5 search queries (competitors, funding, exec interviews, analyst categorization)
   - Strategic narrative sources: 2-4 search queries (CEO interviews, podcasts, recent investor letters, conference keynotes)
   - Trigger events: 1-2 search queries (recent customer-win press releases)

3. **Aggregate results.** Build an in-memory research corpus organized by source. Track which sources returned content vs. which failed (`sources_failed` for the frontmatter). For long sources (full earnings call, hour-long podcast transcript), summarize by themes — don't load verbatim.

4. **Multi-ICP detection.** Scan the aggregated corpus for the 6 ICP signals:
   - Case studies clearly segmented by industry/size/role
   - Comparison or "for X / for Y" content
   - Distinct customer-logo clusters on the customers page
   - Pricing tiers tied to customer profile
   - Distinct G2 / Gartner segment mentions
   - Explicit ICP statement on website

   For each candidate ICP boundary, count how many signals point at it. **Threshold: ≥2 signals at the same boundary AND segments would produce materially different value chains → propose multiple ICPs.** Otherwise default to single-ICP.

5. **Confirm ICPs with user.** Present the detection result as a hypothesis with evidence. Format:

   > Based on research, this product appears to serve **N distinct ICPs**:
   >
   > - **<ICP 1 name>** — supported by [signal 1, signal 2]
   > - **<ICP 2 name>** — supported by [signal 1, signal 2]
   >
   > Confirm or correct? If you're not sure, list the customer types you actually sell to in your real practice.

   Avoid binary "I detected N ICPs — yes/no?" framing. The hypothesis-with-evidence framing reduces sycophantic agreement.

6. **Capture confirmed ICPs** in the in-flight artifact-state. Set `icps_detected` and `icp_detection_signals` in frontmatter. The confirmed ICP set becomes input to the value-framework synthesis pass.

### Phase 3: Synthesis (3 sequential framework passes)

For each lens in **[positioning, value-framework, strategic-narrative]** (in order):

- Read `references/<lens>-lens.md` for that pass's quality standards, prompt-shaping guidance, and example output structure.
- Construct a focused synthesis prompt: research corpus + lens guidance + accumulated artifact state + (for value-framework) confirmed ICP set.
- Receive structured output for that lens.
- Append to in-flight artifact-state.

**Pass-failure handling:** if a pass returns empty or errors, log to `passes_failed` in the artifact's frontmatter and continue with remaining passes. Partial-output-with-honest-frontmatter is more valuable than a hard abort.

**Step-by-step:**

For each lens in the ordered list `[positioning, value-framework, strategic-narrative]`:

1. **Read the lens reference.** `references/positioning-lens.md` for the first pass, then `references/value-framework-lens.md`, then `references/strategic-narrative-lens.md`. Each reference contains the lens's quality standards, prompt-shaping guidance (told-to / told-not-to), and example output structure.

2. **Construct a focused synthesis prompt.** Combine:
   - The research corpus from Phase 2 (filtered to sources relevant to this lens — see per-section source mapping in `research-sources.md`)
   - The lens reference's guidance (what to extract, quality standards, prompt-shaping)
   - The accumulated artifact state from prior passes (positioning output is available to value-framework; both are available to strategic-narrative)
   - **For value-framework specifically**: the confirmed ICP set from Phase 2 — produce per-ICP persona blocks
   - **For strategic-narrative specifically**: the thin-source fallback rule (if sources don't support real narrative, output 4 sub-field stubs marked `[insufficient public data]` + high-severity gap-probe)

3. **Capture structured output.** Each lens produces specific sections (per artifact-schema.md). Append the output to the in-flight artifact-state.

4. **Pass-failure handling.** If a synthesis pass returns empty, errors, or hits a thin-source case it can't handle:
   - Log the pass to `passes_failed` in frontmatter
   - Continue with remaining passes (don't abort)
   - The downstream Phase 4 gap-detection step will flag the missing content with appropriate gap-probes

**Order matters:** positioning runs first because it frames everything. Value-framework second because it consumes positioning output. Strategic-narrative last because it can mutually-inform with the soundbite (composed in Phase 4 from all three lens outputs).

### Phase 4: Composition + Gap Detection

Compose the artifact per `references/artifact-schema.md` (9-section structure):

1. Positioning (Dunford)
2. Soundbite (assembled from positioning + value-framework + strategic-narrative outputs)
3. Strategic Narrative (Raskin — 4 structured sub-fields: world-changing / change-the-game / promised land / cost of staying still)
4. Buyer's Problem Stack (Force Management — with "before" sub-field that captures the displacement signal)
5. ICPs container (per-ICP: firmographic profile + personas × Force-Management value mappings)
6. Differentiation 3-tier (Force Management)
7. Proof Points (mapped to problems)
8. Trigger Events (paired to problems)
9. Honest Gaps + Suggested Probes (structured gap-probe items)

**Gap detection:** for each section, evaluate whether research and synthesis produced sufficient content. If not, emit a structured gap-probe item to the Honest Gaps section: `gap / framework_area / research_says / probe / severity`. The Strategic Narrative section in particular outputs `[insufficient public data]` stubs paired with high-severity probes when public sources are thin (early-stage / private / quiet companies).

**Step-by-step:**

1. **Read `references/artifact-schema.md`** for the full 9-section structure, frontmatter schema, per-section field schemas, and gap-probe schema.

2. **Compose the body sections** in order:
   - **TL;DR** (top-of-doc): assemble a 3-5 sentence summary using positioning category, ICPs detected, top 3 problems, defensible differentiation, cost of staying still. Forwarding-test should pass — read like a thoughtful human summary, not AI report.
   - **Section 1: Positioning** — directly from positioning-pass output
   - **Section 2: Soundbite** — assemble the Nasralla 4-sentence using:
     - "Because of" — from strategic-narrative's world-changing
     - "Now's the time" — from positioning category + change-the-game
     - "So that" — from value-framework's primary outcome
     - "Instead of" — from strategic-narrative's cost-of-staying-still
     - Plus a one-sentence version
   - **Section 3: Strategic Narrative** — directly from strategic-narrative-pass output (4 structured sub-fields)
   - **Section 4: Buyer's Problem Stack** — directly from value-framework-pass output (with "before" displacement-signal field populated)
   - **Section 5: ICPs container** — for each confirmed ICP: firmographic profile + detection signals + N persona blocks (each with full Force-Management value chain)
   - **Section 6: Differentiation 3-tier** — directly from value-framework-pass output
   - **Section 7: Proof Points** — extract from research corpus (case studies); map each to which problem from Section 4 it addresses
   - **Section 8: Trigger Events** — extract from research corpus (case-study contexts, recent customer wins, news); pair each to which problem from Section 4 it activates

3. **Run gap detection.** For each section, evaluate whether the content meets the lens's quality standards (per the lens reference). For each section that falls short, emit a structured gap-probe item:

   ```yaml
   - gap: <short description>
     framework_area: positioning | value-framework | strategic-narrative | proof | triggers | icps | other
     research_says: <what research found, if anything>
     probe: <specific question for the AE in v1.5>
     severity: high | medium | low
   ```

   **Severity rules (per artifact-schema.md):**
   - `high` — gap blocks downstream tactical-skill use
   - `medium` — gap reduces output quality but doesn't block
   - `low` — polish-level gap

4. **Compose Section 9: Honest Gaps + Suggested Probes.** Concatenate all gap-probe items into the structured YAML block. Compute `gap_count_by_severity` for the frontmatter.

5. **Finalize frontmatter.** Set `passes_run`, `passes_failed`, `sources_consulted`, `sources_failed`, `icps_detected`, `icp_detection_signals`, `gap_count_by_severity`. Optionally compute `no_logo_rating` (1-5 self-assessment of how specific-to-this-company the doc is).

### Phase 5: Write & Report

Write the artifact to `~/.agents/sales/<product-slug>/company-profile.md`.

If migrating from old flat path: archive old to `~/.agents/sales/.archive/company-profile-pre-evolution-<date>.md`.

Report to user:
- Path written
- TL;DR (positioning category, ICPs detected, top 3 problems, defensible differentiation)
- Gap count by severity (high / medium / low)
- Suggested next step (downstream tactical use today; v1.5 react-and-refine when available)

**Step-by-step:**

1. **Create destination directory if needed.** `~/.agents/sales/<product-slug>/` — `mkdir -p` semantics. Don't fail if it already exists.

2. **Write the artifact** to `~/.agents/sales/<product-slug>/company-profile.md`. Frontmatter on top, body sections in order.

3. **Handle migration archival** (only if migrating from old flat path):
   - Create `~/.agents/sales/.archive/` if needed.
   - Copy old profile to `~/.agents/sales/.archive/company-profile-pre-evolution-<YYYY-MM-DD>.md`.
   - Delete old file at `~/.agents/sales/company-profile.md` ONLY after archive succeeds AND the new artifact write succeeds.

4. **Report to user.** Format:

   > **Company profile created for [Company Name].**
   >
   > **Saved to:** `~/.agents/sales/<product-slug>/company-profile.md`
   > [if migrated:] *Migrated from old path; archived to `~/.agents/sales/.archive/company-profile-pre-evolution-<date>.md`.*
   >
   > **TL;DR:** [reproduce the doc's TL;DR]
   >
   > **ICPs detected:** [list, with detection-signal counts]
   >
   > **Top 3 problems you solve:**
   > 1. [problem 1]
   > 2. [problem 2]
   > 3. [problem 3]
   >
   > **Defensible differentiation:**
   > - [what only you can do — or "no defensible tier identified, see Honest Gaps"]
   >
   > **Gaps in the profile:** [count by severity — e.g., "1 high / 3 medium / 4 low"]
   > Top high-severity gaps:
   > - [gap 1]
   > - [gap 2]
   >
   > **What's next:** Use this profile as the bedrock substrate for downstream tactical skills (multi-thread email, follow-up email, business case, demo prep). Refresh anytime by pasting new material (case studies, battlecards, win-loss reports) and asking me to update.

---

## Update Mode

When the user says "update my company profile" or "refresh the profile":

1. **Locate the existing profile** at `~/.agents/sales/<product-slug>/company-profile.md` (fall back to old flat path if not present; offer migration if found at old path).

2. **Determine the update mode:**
   - **Full refresh** (default for v1) — re-run from Phase 2 Research. All sections regenerated.
   - **Material-paste enrichment** — user provides new material (case study, battlecard, pitch deck, win-loss report). Incorporate into relevant sections; preserve other sections; note in `enriched_with` frontmatter.
   - **Targeted refresh** (deferred to v1.5) — partial section updates. v1 falls back to full refresh and notes the limitation.

3. **Update the profile in place** — write to the same path; update the `generated` date; preserve `migrated_from` if present.

4. **Show what changed:**
   > **Profile updated for [Company Name].**
   >
   > **What changed:**
   > - [New/updated sections]

---

## What This Skill Does NOT Do

- **No account research** — This profiles YOUR company, not your prospects. Use the `sales-account-research` skill for prospect companies.
- **No competitive deep-dive** — Differentiation is included via Force Management's 3-tier model, but a full competitive battlecard is a different task.
- **No internal data access** — Works from public sources. The user can paste internal docs (battlecards, decks, win-loss reports) to enrich the profile, but the skill doesn't access CRMs, internal wikis, or private data.
- **No messaging generation** — This produces the reference doc. Other skills (multi-thread email, follow-up email, business case, demo prep) use it to generate emails, business cases, talk tracks, etc.
- **No required AE interview in v1** — research-only by design. Honest Gaps section flags what couldn't be determined from public sources; v1.5 react-and-refine (future) is where the AE fills those gaps.
- **No multi-product within a single doc** — multi-product is supported via separate runs (one doc per product). Single doc supports multi-ICP within one product.

---

## Reference Files

This skill loads content from `references/` on demand. Reference files are authored separately so SKILL.md stays focused on orchestration.

| Reference | Purpose |
|---|---|
| `references/research-sources.md` | Per-section research source list and search query strategy. |
| `references/positioning-lens.md` | Dunford positioning lens — category, segments, alternatives, unique attributes. |
| `references/value-framework-lens.md` | Force Management value framework + 3-tier differentiation + buyer's problem stack with displacement signal in "before" field. |
| `references/strategic-narrative-lens.md` | Andy Raskin strategic narrative — 4 structured sub-fields. |
| `references/artifact-schema.md` | 9-section output schema, ICPs container shape, gap-probe schema, frontmatter spec, worked example. |
