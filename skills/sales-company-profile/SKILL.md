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

[Detailed identification + migration logic filled in Unit 5.]

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

[Detailed research + ICP detection logic filled in Unit 5.]

### Phase 3: Synthesis (3 sequential framework passes)

For each lens in **[positioning, value-framework, strategic-narrative]** (in order):

- Read `references/<lens>-lens.md` for that pass's quality standards, prompt-shaping guidance, and example output structure.
- Construct a focused synthesis prompt: research corpus + lens guidance + accumulated artifact state + (for value-framework) confirmed ICP set.
- Receive structured output for that lens.
- Append to in-flight artifact-state.

**Pass-failure handling:** if a pass returns empty or errors, log to `passes_failed` in the artifact's frontmatter and continue with remaining passes. Partial-output-with-honest-frontmatter is more valuable than a hard abort.

[Detailed synthesis loop logic filled in Unit 5.]

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

[Detailed composition + gap detection logic filled in Unit 5.]

### Phase 5: Write & Report

Write the artifact to `~/.agents/sales/<product-slug>/company-profile.md`.

If migrating from old flat path: archive old to `~/.agents/sales/.archive/company-profile-pre-evolution-<date>.md`.

Report to user:
- Path written
- TL;DR (positioning category, ICPs detected, top 3 problems, defensible differentiation)
- Gap count by severity (high / medium / low)
- Suggested next step (downstream tactical use today; v1.5 react-and-refine when available)

[Detailed write + report logic filled in Unit 5.]

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
