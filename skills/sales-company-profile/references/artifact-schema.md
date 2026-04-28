# Artifact Schema

The contract every `sales-company-profile` artifact must conform to. File location, frontmatter, body structure (9 sections), per-section field schemas, ICPs container shape, gap-probe schema, and one worked example.

This file is the single source of truth for the artifact format. If a lens reference describes an output the schema doesn't accommodate, that's a schema-drift bug — update both files in the same commit.

---

## File Location and Naming

```
~/.agents/sales/<product-slug>/company-profile.md
```

Where:
- `<product-slug>` is the lowercase, hyphenated product name. For single-product companies, defaults to the slugified company-name (e.g., Glia → `glia`).
- Multi-product is supported via separate runs / separate docs (one doc per product), not via multi-product within one file.
- Old flat-path location (`~/.agents/sales/company-profile.md`) is the **migration source** for users upgrading from prior version. After migration, archived to `~/.agents/sales/.archive/company-profile-pre-evolution-<date>.md`.

---

## Frontmatter Schema

Required and optional fields:

```yaml
---
company: <company name>                              # required
product: <product name>                              # required (= company name for single-product)
product_slug: <slug>                                 # required
website: <url>                                       # required
generated: YYYY-MM-DD                                # required
sources_consulted:                                   # required (list of sources actually read)
  - website-homepage
  - website-product
  - website-case-studies
  - g2-reviews
  - gartner-peer-insights
  - ceo-interview
  # ... etc
sources_failed: []                                   # required (empty array if none failed)
passes_run:                                          # required (in execution order)
  - positioning
  - value-framework
  - strategic-narrative
passes_failed: []                                    # required (empty array if none failed)
icps_detected:                                       # required (list of ICP names; single-element if single-ICP)
  - community-banks
  - credit-unions
icp_detection_signals:                               # required (which signals supported the multi-ICP detection)
  community-banks: [case-study-segmentation, customers-page-cluster, pricing-tier]
  credit-unions: [case-study-segmentation, customers-page-cluster]
gap_count_by_severity:                               # required (auto-computed at composition time)
  high: <int>
  medium: <int>
  low: <int>
no_logo_rating: <1-5>                                # optional (subjective specificity rating, populated if synthesis can self-assess)
migrated_from: <old-path>                            # optional (only set when migrating from old flat path)
enriched_with: []                                    # optional (sources of user-paste enrichment in Update Mode — e.g., ['battlecard.pdf', 'win-loss-q3-2026.docx'])
source_conflicts: []                                 # optional (notes when sources disagreed materially — see research-sources.md priority rules)
---
```

**Field semantics:**

- `sources_consulted` and `sources_failed` together document the research surface — downstream consumers can use `sources_failed` for trust calibration ("no G2 reviews available" is a known limitation).
- `passes_run` and `passes_failed` document synthesis-pipeline outcomes. If a pass failed, the relevant section will have partial content + gap-probes flagging it.
- `icps_detected` enables metadata-level queries from downstream skills ("does this product serve community banks?") without loading the body. Critical for AI-readability.
- `gap_count_by_severity` lets a downstream skill quickly assess artifact quality ("this profile has 5 high-severity gaps — treat its outputs cautiously").
- `migrated_from` traces lineage when a profile was migrated from the old flat path; useful for debugging path issues.

---

## Body Structure (9 sections)

```markdown
# <Company Name> — Sales Profile

## TL;DR
<3-5 sentences. Composed at Phase 4 from positioning category + ICPs detected + top problems + defensible differentiation + cost of staying still. Forwarding-test should pass.>

## 1. Positioning
<per positioning-lens.md output structure>

## 2. Soundbite
<Nasralla 4-sentence: because of / now's the time / so that / instead of. Composed in Phase 4 from positioning + value-framework + strategic-narrative outputs.>

## 3. Strategic Narrative
<per strategic-narrative-lens.md — 4 structured sub-fields:
- World is changing because:
- Change-the-game move buyers must make:
- Promised land buyers are reaching for:
- Cost of staying still:>

## 4. Buyer's Problem Stack
<per value-framework-lens.md — 3-5 problems, each with name / before / consequence / who-feels-it. The "before" field carries the displacement signal.>

## 5. ICPs
<container — see ICPs Container schema below>

## 6. Differentiation 3-tier
<per value-framework-lens.md — defensible / comparative / assumed + how-buyers-describe-the-difference>

## 7. Proof Points
<list of customer results mapped to problem-solved>

## 8. Trigger Events
<list of trigger-events paired to which problem from stack each activates>

## 9. Honest Gaps + Suggested Probes
<structured gap-probe items — see Gap-Probe schema below>
```

The TL;DR is a top-of-doc summary, not a numbered section. Sections 1-9 are the substance.

---

## Per-Section Schemas

### Section 1: Positioning

```markdown
## 1. Positioning

**Category:** <buyer-perspective category, 1 sentence>

**Market segments served:**
- <segment 1> — <why this segment>
- <segment 2> — <why this segment>

**Competitive alternatives:**
- **<Named competitor 1>** — <their positioning / how buyers see the choice>
- **<Named competitor 2>** — <their positioning>
- **<Status quo / DIY>** — <when relevant>

**Unique attributes:**
- **<Attribute 1>** — <what makes this product a contender; not vendor-claimed superlative>
- **<Attribute 2>** — <same>

**Attribute-to-value translation:**
- <Attribute> → <operational value> → <business outcome>
- <Attribute> → <operational value> → <business outcome>
```

### Section 2: Soundbite

```markdown
## 2. Soundbite

**Because of** <market change creating urgency>,
**now's the time to** <approach the product uniquely enables>,
**so that** <positive business outcome>,
**instead of** <negative outcome of doing nothing or old approach>.

**One-sentence version:** <absolute shortest framing>
```

### Section 3: Strategic Narrative

```markdown
## 3. Strategic Narrative

**World is changing because:** <1-3 sentences>

**Change-the-game move buyers must make:** <1-3 sentences>

**Promised land buyers are reaching for:** <1-3 sentences, in buyer-outcome terms>

**Cost of staying still:** <1-3 sentences>
```

If thin-source: each sub-field outputs `[insufficient public data]` + high-severity gap-probe in Section 9.

### Section 4: Buyer's Problem Stack

```markdown
## 4. Buyer's Problem Stack

### Problem 1: <Name in buyer language>
**Before:** <displacement signal — what they were doing manually / what got fired; specific and named>
**Consequence:** <quantified cost when possible>
**Who feels it:** <persona / role>

### Problem 2: <Name>
[same structure]

### Problem 3: <Name>
[same structure]

[3-5 problems, ranked by frequency in case studies + reviews as primary buying reason]
```

### Section 5: ICPs Container

```markdown
## 5. ICPs

### ICP: <ICP Name>

**Firmographic profile:**
- **Industry:** <industry / vertical>
- **Size:** <revenue / employee / asset-size range>
- **Geography:** <if applicable>
- **Other defining traits:** <e.g., regulatory environment, tech-stack archetype>

**Detection signals (research basis):**
- <signal 1 supporting this ICP>
- <signal 2>

**Personas:**

#### Persona: <Title / Role>
**Capability:** <what the product does for this persona>
**Operational improvement:** <what changes day-to-day>
**Metric that moves:** <named KPI>
**Dollar impact:** <quantified from case studies, with named customer + timeframe when available>
**How to say it:** <one-sentence AE talk track>

#### Persona: <Title / Role>
[same structure]

[2-4 personas per ICP, only with case-study/review evidence]

---

### ICP: <Next ICP Name>
[same structure]
```

For single-ICP products: one ICP block. For multi-ICP: N ICP blocks separated by `---`.

### Section 6: Differentiation 3-tier

```markdown
## 6. Differentiation

### Defensible — Only We Can Do This
- **<Capability>** — <why competitors literally cannot replicate; technical or structural reason>

### Comparative — We Do This Better
- **<Capability>** — <why we're better; technical, vertical-depth, pricing-model, etc.>

### Assumed — Table Stakes
- <capability> (assumed across category)
- <capability>

### How Buyers Describe the Difference
> "<verbatim quote from G2 / case study>" — <attribution>
> "<verbatim quote>" — <attribution>
```

### Section 7: Proof Points

```markdown
## 7. Proof Points

### <Customer name or industry> — <problem solved>
- **Company:** <size / industry / context>
- **Problem:** <what they were dealing with>
- **Result:** <specific outcome with numbers + timeframe>
- **Quote:** "<direct customer quote when available>"
- **Best used when:** <type of prospect / situation this proof point matches>

### <Next customer> — <problem solved>
[same structure]

[Include all proof points from case studies. More is better; this is a lookup table for downstream skills.]
```

### Section 8: Trigger Events

```markdown
## 8. Trigger Events

- **<Trigger>** — <what happens to the buyer> → Activates **Problem #<N>** → Typical urgency: <timeframe>
- **<Trigger>** — <what happens> → Activates **Problem #<N>** → Typical urgency: <timeframe>

[5-8 triggers]
```

### Section 9: Honest Gaps + Suggested Probes

```markdown
## 9. Honest Gaps + Suggested Probes

The following could not be reliably determined from public sources. v1.5 react-and-refine (when available) will use these probes to fill the gaps with your input.

```yaml
- gap: <short description of what's missing>
  framework_area: positioning | value-framework | strategic-narrative | proof | triggers | icps | other
  research_says: <what research found, if anything; "nothing relevant" if truly absent>
  probe: <specific question for the AE in v1.5; should be answerable in 1-3 sentences or a short list>
  severity: high | medium | low
- gap: ...
  ...
```
```

**Severity calibration:**
- **high** — gap blocks downstream tactical-skill use (e.g., positioning category unknown, no proof points found, strategic narrative entirely thin)
- **medium** — gap reduces output quality but doesn't block (e.g., some personas missing, dollar impact unquantified for known personas)
- **low** — polish-level gap (e.g., one trigger-event from 5-8 list missing, secondary buyer-language source unconsulted)

---

## Worked Example (sanitized — Glia × community-banks + credit-unions)

```markdown
---
company: Glia
product: Glia
product_slug: glia
website: https://www.glia.com
generated: 2026-04-28
sources_consulted:
  - website-homepage
  - website-product
  - website-pricing
  - website-case-studies
  - website-comparison-pages
  - website-customers
  - website-blog
  - website-careers
  - g2-reviews
  - gartner-peer-insights
  - trustradius
  - ceo-interview-2026-q1
  - funding-announcement-2025-series-d
  - reddit-r-banking
sources_failed:
  - 10k-mda  # private company, not public
passes_run:
  - positioning
  - value-framework
  - strategic-narrative
passes_failed: []
icps_detected:
  - community-banks
  - credit-unions
icp_detection_signals:
  community-banks: [case-study-segmentation, customers-page-cluster, pricing-tier]
  credit-unions: [case-study-segmentation, customers-page-cluster, distinct-review-segment]
gap_count_by_severity:
  high: 1
  medium: 3
  low: 4
no_logo_rating: 4
---

# Glia — Sales Profile

## TL;DR

Glia is a customer-engagement platform built specifically for community banks and credit unions, competing primarily with TalkDesk, Genesys, and the status-quo Microsoft Teams + manual processes stack. Its defensible differentiation is a deterministic, non-generative virtual agent for customer-facing interactions (contractually guaranteed no hallucination) plus FI-vertical depth (Fiserv / FIS / Jack Henry as first-class integrations); its commercial differentiator is a flat platform pricing model that competitors with per-license pricing can't easily replicate. The strategic narrative — community-bank brand promise survives the demographic transition — is grounded in a CEO interview and the recent Series D funding announcement. Honest gaps: dollar-impact quantification thin for several personas; pricing details not public; one ICP-specific persona missing.

## 1. Positioning

**Category:** Customer engagement platform for community banks and credit unions (analyst categorization places it under "FI-focused CCaaS" / "Community-bank customer engagement").

**Market segments served:**
- **Community banks** ($1B-$10B asset range) — value FI-vertical depth, no-per-license pricing for multi-department expansion
- **Credit unions** ($500M-$5B asset range) — same value drivers, plus member-terminology + CU-specific compliance posture

**Competitive alternatives:**
- **TalkDesk** — generic CCaaS; FI-vertical depth shallow; per-license pricing
- **Genesys** — enterprise-grade CCaaS; common community-bank incumbent; heavy
- **NICE CXone** — over-built for community-bank scale
- **Status quo (Teams + manual processes)** — many community banks operate without a contact-center platform

**Unique attributes:**
- **Deterministic non-generative virtual agent for customer-facing interactions** — contractually guaranteed no hallucination
- **No-per-license pricing model** — flat platform pricing
- **FI-vertical depth** — Fiserv / FIS / Jack Henry core integrations as first-class

**Attribute-to-value translation:**
- *Deterministic VA* → no hallucination risk in customer-facing AI → preserves community-bank reputation and compliance posture
- *No-per-license pricing* → predictable cost across multi-department expansion → CFO-friendly, no surprise renewal hikes
- *FI-vertical depth* → less custom integration work for IT → faster time-to-value, less drag on CIO's roadmap

## 2. Soundbite

**Because of** the demographic transition where long-tenured branch staff are retiring and younger customers default to mobile-first banks,
**now's the time to** consolidate fragmented customer-engagement vendor stacks into a single FI-purpose-built platform with hallucination-free customer-facing AI,
**so that** community-bank brand promise — relationship banking — survives across digital and voice channels at scale,
**instead of** losing the demographic war to mobile-first neobanks while customer-facing AI hallucinates and erodes regulator trust.

**One-sentence version:** Customer engagement built specifically for community banks and credit unions, with a deterministic virtual agent that won't hallucinate.

## 3. Strategic Narrative

**World is changing because:** Community banks and credit unions face a generational transition — long-tenured branch staff retiring, customer expectations reset by mobile-first banks. The contact center and online banking session are the new branch, but FI platforms were built for transactions, not relationships.

**Change-the-game move buyers must make:** Treat the contact center and OLB session as the new branch. Consolidate fragmented vendor stacks into one platform; build customer-trustable AI (not hallucination-prone generative chatbots); equip the contact center for relationship work.

**Promised land buyers are reaching for:** A future where members get the same relationship feel — being recognized, helped quickly, problem solved — through any channel, regardless of whether a senior branch employee is still on staff. Community-bank brand promise survives.

**Cost of staying still:** Lose the demographic war. Younger customers default to digital-first competitors. Older customers feel abandoned. Within 5-10 years, the FI's relationship-banking moat erodes faster than its asset base.

## 4. Buyer's Problem Stack

### Problem 1: Tellers panicking on Monday mornings
**Before:** Loan-servicing has one phone number for both external customers AND internal teller help-desk requests. Tellers calling for help (e.g., asking for a loan payoff) sit in queue behind external customers.
**Consequence:** Visible employee distress, customer-facing. Drives teller turnover. Drags branch CSAT.
**Who feels it:** Day-to-day call center managers; branch managers; ultimately face-to-face customers.

[Problems 2-5 omitted in this excerpt for brevity.]

## 5. ICPs

### ICP: Community Banks

**Firmographic profile:**
- **Industry:** Community-bank charter
- **Size:** $1B-$10B asset range
- **Geography:** US, multi-state with regional concentration
- **Other defining traits:** Operational risk committee approval is the gating step; CIO/CCO involvement late-stage

**Detection signals:**
- Case studies clearly segmented (community-bank-named cases distinct from CU cases)
- Customers/logos page has separate "Community Banks" cluster
- Pricing tiers customer-profile-tied (asset-size brackets)

**Personas:**

#### Persona: Director, Customer Engagement Center
**Capability:** Cross-department queue routing with internal-vs-external distinction; AI heads-up panel surfaces prior-call context.
**Operational improvement:** Tellers route to separate queue from external customers; reps see prior context before pickup.
**Metric that moves:** Internal-help-desk average response time; teller-initiated internal-call abandonment rate.
**Dollar impact:** [thin-source — see Honest Gaps]
**How to say it:** "Your tellers stop panicking in queue — internal asks route separately, reps see context before they pick up."

[Additional personas omitted in this excerpt.]

---

### ICP: Credit Unions

**Firmographic profile:**
- **Industry:** Federally / state-chartered credit unions
- **Size:** $500M-$5B asset range
- **Geography:** US
- **Other defining traits:** "Member" not "customer" terminology; field-of-membership constraints; CU-specific regulatory posture

**Detection signals:**
- Case studies segmented (CU-named cases distinct)
- Customers/logos page has separate "Credit Unions" cluster
- Distinct G2 review segment ("CU users" cluster with different praise/complaint patterns)

**Personas:**

[Personas listed; member-relationship language preserved.]

## 6. Differentiation

### Defensible — Only We Can Do This
- **Deterministic non-generative virtual agent** — contractual no-hallucination guarantee. Architectural, not tunable.

### Comparative — We Do This Better
- **FI-vertical depth (Fiserv / FIS / Jack Henry)** — competitors offer integrations, but FI-only-customer focus drives our integrations to be deeper and pre-tested.
- **No-per-license pricing model** — competitors typically charge per-seat. Pricing-model choice, not technical superiority.

### Assumed — Table Stakes
- Voice + digital channels integrated
- Co-browse / screen-share
- Contact-center reporting + dashboards
- CRM integrations (Salesforce, etc.)

### How Buyers Describe the Difference
> "I don't have to worry about hallucinations because their virtual agent literally can't" — G2 review, community bank, 2026
> "Pricing was the surprise — we'd been bracing for the per-license hit and it just wasn't there" — case study quote, mid-size CU

## 7. Proof Points

[Multiple customer cases mapped to problems; omitted for brevity.]

## 8. Trigger Events

- **TalkDesk renewal hits price hike** (2026 has been a notable wave) → Activates **Problem #N (cost / vendor-trust)** → Typical urgency: 6-12 months pre-contract-end
- **CIO transition / new CIO with Glia exposure from prior role** → Activates **Problem #N (platform-consolidation appetite)** → Typical urgency: 12-18 months
- **Regulatory pressure on contact-center recording / AI governance** → Activates **Problem #N (compliance posture)** → Typical urgency: ad-hoc

[5-8 triggers total]

## 9. Honest Gaps + Suggested Probes

```yaml
- gap: Dollar impact thin for community-bank Director-CEC persona
  framework_area: value-framework
  research_says: Public case studies cite operational improvements but rarely quantify dollar impact for the contact-center director role specifically
  probe: For your community-bank wins, what's the typical dollar-impact for the contact-center director? (e.g., reclaimed FTE hours, reduced agent overtime, CSAT impact translated to retention)
  severity: medium

- gap: Pricing details not public
  framework_area: positioning
  research_says: Pricing page describes the no-per-license model conceptually but doesn't disclose dollar amounts
  probe: What's the typical entry-tier ARR for a community bank? For a credit union? Helps the AE calibrate budget conversations.
  severity: low

# ... additional gaps
```
```

(Worked example uses Glia for concreteness. The schema applies universally.)
