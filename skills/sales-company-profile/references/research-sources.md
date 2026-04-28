# Research Sources

The catalog of public sources the skill consults during Phase 2 (Research), what to extract from each, and which output section each feeds. The skill loads this reference and runs parallel web fetches across the listed sources.

## Philosophy

- **Buyer-voice over marketing-voice** — wherever signals conflict, prefer how real buyers describe the product. G2 reviews + customer quotes in case studies > homepage hero copy.
- **Specificity over coverage** — better to deeply read 5 high-signal sources than skim 20 low-signal ones.
- **Honest about absence** — if a source category yields nothing, log it in `sources_failed` and continue. Do not invent content to fill the gap.
- **Truncate aggressively** — long sources (full 10-K MD&A, hour-long podcast transcripts) get summarized, not loaded verbatim. Aim for signal density, not raw token count.

---

## Source Catalog

Each source listed with: (a) what to read, (b) what to extract, (c) what NOT to extract.

### Website Pages (primary source — read thoroughly)

**Pages to read:**
- **Homepage** — extract: core promise in one sentence, who it's for, primary CTA. NOT: hero animation copy, vague aspirational language.
- **Product / platform pages** — extract: capabilities described, technical approach, integrations. NOT: feature laundry lists without context.
- **Pricing page** (if public) — extract: pricing model (per seat / usage / platform fee), tier differentiators, who each tier targets. NOT: enterprise-tier handwaving.
- **Case studies / customer stories** — read every one available. Extract: who bought (industry, size, role), problem (in buyer language when quoted), result (with numbers and timeframe), **before-state** (what they were doing before — the displacement signal), trigger that preceded the purchase. NOT: vendor-self-congratulation.
- **About page** — extract: company story, mission, team. NOT: founder origin myths unrelated to product positioning.
- **Customers / logos page** — extract: named customers, **logo clusters** (segments grouped together — important multi-ICP signal), industries represented. NOT: "Fortune 500 trust us" without specifics.
- **Comparison / "vs" pages** — extract: named competitors, dimensions they compete on, claimed advantages. **Critical for Positioning section.** NOT: comparison-page strawmen.
- **Blog** (recent 3-6 months) — extract: thought-leadership themes, market-direction commentary, customer-success patterns, narrative they're building. NOT: SEO-only content with no perspective.
- **Careers page** — extract: roles being hired, tech stack signals, geographic expansion, function-level investment direction. NOT: generic "great culture" boilerplate.

### Buyer Voice (high-signal — extract verbatim phrases)

**Primary sources:**
- **G2** — `site:g2.com "<company>"` — pros, cons, "switched from" patterns, segment ratings.
- **Gartner Peer Insights** — `site:gartner.com peer insights "<company>"` — enterprise-skewed reviews, evaluation criteria language.
- **TrustRadius** — `site:trustradius.com "<company>"` — depth-of-use details, "best fit for" language.

**Secondary sources:**
- **Reddit** — relevant subreddits (industry-specific: r/SaaS, r/devops, r/sysadmin, r/banking, r/CreditUnions, etc.). High signal for IT/dev tools; medium for general B2B.
- **LinkedIn** — customer testimonial posts, employees-of-customer-company posting about products they use.

**Tertiary (defer unless primary/secondary thin):**
- Capterra, Software Advice — broader review spread, lower depth.
- Product Hunt — early-stage products only.
- Hacker News threads about the company.

**What to extract across all buyer-voice sources:**
- How buyers describe the product (often different from marketing's framing)
- What they praise most (real value drivers)
- What they complain about (real objections you'll face)
- What they compare it to (real competitive set, often broader than marketing's claimed competitors)
- **Specific phrases verbatim** — not star ratings, not summaries

**What NOT to extract:**
- 5-star "great product" reviews with no specifics
- Star ratings as proxies for content
- Vendor-prompted testimonials (often visually distinct from organic reviews)

### Market & Competitive Context

**Search queries:**
- `"<company>" competitor OR alternative OR "compared to"`
- `"<company>" vs <likely competitor names>` (run after homepage read identifies likely competitors)
- `"<company>" funding OR Series OR acquisition OR revenue`
- `"<company>" CEO OR founder interview OR podcast`
- `"<company>" Gartner Magic Quadrant OR Forrester Wave OR IDC MarketScape` (analyst categorization)

**What to extract:**
- Named competitors and how the company positions against them
- Company trajectory — funding stage, recent rounds, market position, growth signals
- Executive vision — what the CEO/founder says about market direction
- **Analyst categorization** — what category analysts place the product in (critical for Positioning)

**What NOT to extract:**
- Funding announcements without context (TechCrunch headline-only)
- Stock-price commentary
- Acquisition rumors without sourcing

### Strategic Narrative Sources (new for v2)

**For Andy Raskin's strategic narrative (Phase 3 strategic-narrative lens):**

**Primary:**
- **CEO / founder interviews** — recent (within 12 months). Extract: market-direction claims, customer-pain framing, "what we're really fighting against" language, change-the-game articulation.
- **Exec podcast appearances** — search for `"<CEO name>" podcast OR interview` filtered to recent. Same extraction as above.
- **Conference keynotes** — search for `"<CEO name>" keynote OR talk` recent. Pull the strategic narrative arc.

**Secondary:**
- **Recent funding announcement narratives** — the "why we raised, what's next" framing in TechCrunch / company blog post. High signal for Raskin's "world-changing" + "promised land."
- **Investor letters / 10-K MD&A** (if public) — Management Discussion & Analysis section is often the most honest strategic narrative a company produces. Read sparingly (long); summarize by themes.
- **Annual product or vision keynotes** (if any).

**Tertiary:**
- **Founder LinkedIn posts** about market direction (filter for substance, not "we're hiring" or product-launch noise).
- **Recent product-launch press releases** that frame the "why now" alongside the launch.

**What to extract:**
- "World is changing because" claims — what's different now in the market / regulatory / tech landscape
- "Change-the-game" claims — what new approach or capability is required
- "Promised land" claims — desired future state buyers are reaching for
- "Cost of staying still" claims — what happens to buyers who don't adapt

**What NOT to extract:**
- Generic trend-piece content without company-specific framing ("AI is changing everything" with no Glia-specific angle)
- Self-congratulatory growth metrics
- Mission-statement copy without strategic substance

### Displacement Signal Sources (new for v2)

For the Buyer's Problem Stack "before" field — what buyers were doing manually that got replaced.

**Primary:**
- **Case-study before/after framing** — extract verbatim "we used to..." / "previously we..." / "before [product] we..." passages from case studies.
- **G2 review filter for displacement language** — search for review snippets containing "switched from X", "we used to", "previously", "before we had".
- **Customer testimonial pre/post narratives** — many testimonials describe the before-state explicitly; extract.

**What to extract:**
- Named alternatives (the product they fired, often a competitor or a manual process)
- Specific pain of the before-state (not "it was hard" but "we were spending 4 hours a week on...")
- Trigger that drove them to look for an alternative

**What NOT to extract:**
- Vague "old-fashioned" / "outdated" language without specifics
- Inferred displacement (always extract verbatim, never speculate)

### Trigger Events Sources (extends existing)

**For Trigger Events section:**

**Primary:**
- **Recent customer-win press releases** — extract: what triggered the customer's decision (re-org? regulatory event? leadership change? funding round? tech-stack migration?).
- **Customer-success blog posts** — same extraction.

**Secondary:**
- **Job postings at customer companies** (if accessible) — function-level hiring signals that often precede vendor evaluations.
- **News re: customer companies** — leadership changes, mergers, regulatory shifts, RIFs.

**What to extract:**
- Specific events that preceded purchase
- Patterns across multiple customer wins (e.g., "many of our customers came in after their CFO changed")
- Urgency language ("we needed to act because...")

**What NOT to extract:**
- Generic "digital transformation" framing as a trigger
- Trends without customer-specific instantiation

---

## Per-Section Source Mapping

For each output section, the primary sources to consult during research:

| Output Section | Primary Sources | Secondary Sources |
|---|---|---|
| **1. Positioning** (Dunford) | Comparison/"vs" pages, analyst categorization, customer-language-about-category from G2 reviews | Homepage hero, About page |
| **2. Soundbite** (composed in Phase 4) | (no direct research; assembled from positioning + value-framework + strategic-narrative outputs) | — |
| **3. Strategic Narrative** (Raskin) | CEO/founder interviews, exec podcast appearances, conference keynotes, recent funding announcements, annual investor letters | Founder LinkedIn posts, blog thought-leadership |
| **4. Buyer's Problem Stack** (Force Mgmt) | Case studies, G2/Gartner/TrustRadius reviews | Customer testimonials, blog customer-success posts |
| **5. ICPs container** (multi-ICP) | Case studies (segmentation patterns), customers/logos page (logo clusters), pricing tiers (if customer-profile-tied), comparison page ("for X / for Y" content) | Explicit ICP statements (rare but valuable when found) |
| **5b. Per-persona value mappings** | Case studies (named roles, quoted personas), G2 segment ratings | Career-page hires (signal for what personas the company is built around) |
| **6. Differentiation 3-tier** | Comparison/"vs" pages, G2/Gartner/TrustRadius pros & cons | Customer testimonials about "what makes them different" |
| **7. Proof Points** | Case studies, customer logos page, customer testimonials | Press releases (filtered for substance) |
| **8. Trigger Events** | Customer-win press releases, case-study contexts ("after their re-org..."), recent hires/leadership changes | Industry news re: customer companies |
| **Displacement signal** (in Problem Stack "before" field) | Case-study before/after framing, G2 "switched from X" patterns, customer testimonial pre/post narratives | (no secondary — verbatim only from primary) |

---

## Priority & Conflict Rules

When sources conflict (common — marketing claims one ICP, reviews show another):

1. **Buyer-voice wins over marketing-voice** for: who actually buys, what problems they actually have, what differentiation actually matters.
2. **Marketing-voice wins over buyer-voice** for: official product positioning category, official pricing model, official feature naming.
3. **Recent wins over older** when sources are time-stamped (e.g., a 2026 CEO interview supersedes a 2023 blog post for strategic narrative).
4. **Specific wins over generic** — a named customer's specific testimonial beats a "trusted by" logo wall.
5. **When in doubt, log both as a conflict in the artifact's frontmatter** under `source_conflicts` and let the lens resolve during synthesis.

---

## Failure Handling

If a source category returns no relevant content (zero G2 reviews, no public funding info, no comparison page, etc.):
- Log it in the artifact's frontmatter `sources_failed` array
- Do NOT invent content to fill the gap
- Do NOT degrade the output silently — the absence may itself be a signal (e.g., "no public case studies for enterprise segment" tells the reader something)

If a source returns content that exceeds reasonable token limits (long earnings call, hour-long podcast):
- Summarize by themes, not verbatim
- Pull out 3-5 key quotes that anchor the strategic narrative or buyer-voice signals
- Note in `sources_consulted` which sources were summarized vs. read in full

---

## Source Catalog Summary

| Tier | Source Categories |
|---|---|
| **Primary (always consult)** | Website (homepage, product, pricing, case studies, customers, comparison, blog, careers), G2, Gartner Peer Insights, TrustRadius, named-competitor searches |
| **Secondary (consult when relevant)** | Reddit (industry-specific), LinkedIn customer testimonials, CEO/founder interviews, exec podcasts, recent funding announcements, conference keynotes, analyst categorization (MQ/Wave) |
| **Tertiary (only when primary/secondary thin)** | Capterra, Software Advice, Product Hunt, Hacker News threads, founder LinkedIn posts |
