---
name: sales-company-profile
description: "Generate a deep company profile that helps AI understand what you sell, who buys it, and why it matters. Use when the user says 'set up my company profile', 'analyze my company', 'here's my company website', 'create a company profile', 'what do I sell', or when any other sales skill lacks context about the user's own product/company. Also triggers on 'update my company profile' or 'refresh the profile'. This is a one-time setup skill that makes every other sales skill work better."
---

# Sales Company Profile Generator

Generate a consultant-grade company profile from public sources — your website, case studies, reviews, press releases, job postings. This profile becomes the foundational reference doc that every other sales skill pulls from. Without it, AI doesn't know what you sell. With it, every transcript extraction, account research, follow-up email, and business case is contextualized to YOUR product and market.

## Core Principles

1. **This is a value framework, not a website summary** — Don't regurgitate marketing copy. Extract the underlying value architecture: what problems you solve, how your product creates economic value, what makes you genuinely different, and what proof exists. Force Management calls this the "Value Framework." We're building one from public sources.

2. **Buyer's language over marketing language** — Wherever possible, capture how real buyers and reviewers describe the product, not how marketing describes it. G2 reviews, customer quotes in case studies, and testimonial language are more valuable than homepage hero copy.

3. **Three tiers of differentiation** — Following Force Management's model: defensible (only you can do this), comparative (you do this better), and assumed (table stakes everyone has). Most companies only articulate the assumed tier. The defensible tier is where deals are won.

4. **Honest about what's producible** — Some sections will be richer than others depending on what's publicly available. A public company with earnings calls and 50 case studies produces a different profile than a Series A startup with a landing page. Don't fake depth. Note gaps and move on.

## Workflow

### Phase 1: Get the Input

1. **Get the company URL** — from the user's request, or ask:
   > "What's your company website? I'll analyze it to build your profile."

2. **Check for existing profile** — Look for `~/.agents/sales/company-profile.md`
   - If it exists and the user wants to update — skip to **Update Mode** below.
   - If it doesn't exist — continue with full analysis.

3. **Ask for any additional context** the user wants to provide:
   > "Anything specific you want me to know that might not be on the website? (Optional — I can work with just the URL.)"

   The user might paste a pitch deck, a competitive battlecard, internal positioning docs, or just give verbal context. All of this enriches the profile.

### Phase 2: Research

Scrape and analyze public sources systematically. Run these searches and reads in parallel where possible.

#### 2.1 The Website Deep Read

This is the primary source. Read thoroughly, not just the homepage.

**Pages to read:**
- Homepage (positioning, hero message, value prop)
- Product/platform pages (what it does, how it works)
- Pricing page (if public — business model, packaging, tiers)
- Case studies / customer stories (every one available — these are the richest source)
- About page (company story, mission, team)
- Customers / logos page (who uses it)
- Comparison pages or "vs. competitor" pages (how they position against alternatives)
- Blog (recent posts — what topics they write about)
- Careers page (what they're hiring for)

**What to extract from each:**
- From homepage: the core promise in one sentence, who it's for, primary CTA
- From product pages: capabilities described, technical approach, integrations
- From case studies: who bought (industry, size, persona), what problem, what result (with numbers), what timeline
- From pricing: model (per seat, usage, platform fee), tiers and what differentiates them
- From comparisons: who they compare against, what dimensions they compete on, what they claim as advantages
- From blog: what thought leadership topics they push, what narrative they're building
- From careers: what functions are growing, what tech stack, what stage of growth

#### 2.2 The Buyer's Voice

Search for how real buyers and users describe the product — not marketing, real people.

**Search queries to run:**
- `"[Company Name]" site:g2.com` (G2 reviews)
- `"[Company Name]" site:gartner.com peer insights` (Gartner reviews)
- `"[Company Name]" site:trustradius.com` (TrustRadius reviews)
- `"[Company Name]" review OR "switched from" OR "we use"` (organic mentions)

**What to extract:**
- How buyers describe what the product does (often different from marketing)
- What they praise most (the real value drivers)
- What they complain about (the real objections you'll face)
- What they compare it to (the real competitive set)
- The language they use — specific phrases, not star ratings

#### 2.3 Market and Competitive Context

**Search queries to run:**
- `"[Company Name]" competitor OR alternative OR "compared to"`
- `"[Company Name]" vs [likely competitor names]`
- `"[Company Name]" funding OR acquisition OR revenue` (company stage/trajectory)
- `"[Company Name]" CEO OR founder interview OR podcast` (executive perspective)

**What to extract:**
- Named competitors and how the company positions against them
- Company trajectory — growth stage, recent funding, market position
- Executive vision — what the CEO/founder says about where the market is going

### Phase 3: Analyze and Write the Profile

Synthesize everything into a structured profile. This is where the skill earns its value — don't just summarize what you read. Analyze it through the lens of "what does an AE need to sell this effectively?"

Save to `~/.agents/sales/company-profile.md`:

```markdown
---
company: [Company Name]
website: [URL]
generated: [YYYY-MM-DD]
sources:
  - [list of sources analyzed]
---

# [Company Name] — Sales Profile

## Company Soundbite

The 4-sentence anchor for every conversation. Every other section builds on this.

**Because of** [a significant change in the market/buyer's world that creates urgency],
**now's the time to** [the approach your product uniquely enables] by [timeframe/trigger],
**so that** [the positive business outcomes buyers achieve],
**instead of** [the negative outcomes of doing nothing / using the old approach].

[Write the soundbite in plain language. No jargon. A champion should be able to repeat this in an internal meeting and have it land.]

[Also include a 1-sentence version — the absolute shortest way to describe what this company does and why it matters.]

## Buyer's Problem Stack

The 3-5 problems your product solves, ranked by urgency and frequency. Each problem is described in buyer language (from reviews and case studies), not marketing language.

### Problem 1: [Name in buyer's words]
**Before:** [What the buyer's world looks like without your product — the painful current state]
**Consequence:** [What it costs them — in dollars, time, risk, or opportunity. Quantify from case studies where possible.]
**Who feels it:** [Which persona/role owns this problem]

### Problem 2: [Name]
[Same structure]

### Problem 3: [Name]
[Same structure]

[Include 3-5 problems. Rank by how often they appear in case studies and reviews as the primary buying reason.]

## The Value Chain

How your product creates measurable economic value. Not features — the causal chain from capability to dollar impact.

### For [Persona 1, e.g., VP Sales]:
**Capability:** [What the product does]
**Operational improvement:** [What changes in their day-to-day]
**Metric that moves:** [Which KPI improves]
**Dollar impact:** [Quantified from case studies — e.g., "customers report 30-40% reduction in X"]
**How to say it:** [One sentence an AE could say in a conversation that connects the dots]

### For [Persona 2, e.g., CFO]:
[Same structure — different value chain for different buyers]

[Include 2-4 personas. Only include personas where you have evidence from case studies or reviews.]

## Differentiation

Three tiers, following Force Management's framework. Be honest — defensible differentiation is rare and valuable. Don't inflate comparative into defensible.

### Defensible — Only We Can Do This
[Capabilities or approaches that competitors genuinely cannot replicate. These are your strongest positioning weapons. If you don't have any, say so — that's important for the AE to know.]

### Comparative — We Do This Better
[Capabilities where you have a meaningful advantage but competitors have some version. Include WHY you're better, not just that you claim to be.]

### Assumed — Table Stakes
[Capabilities everyone in the category has. These are not differentiators. Don't lead with them. But the AE needs to know they exist because buyers will ask.]

### How Buyers Describe the Difference
[From reviews and case studies — what do actual customers say makes this product different? This is often more compelling than your own differentiation claims.]

## Proof Points

Specific customer results with enough context to be deployable in a conversation. Organized by problem solved so the AE can match proof to prospect.

### [Customer Name / Industry] — [Problem Solved]
- **Company:** [Size, industry, relevant context]
- **Problem:** [What they were dealing with]
- **Result:** [Specific outcome with numbers and timeframe]
- **Quote:** [Direct customer quote if available]
- **Best used when:** [What type of prospect/situation this proof point is most relevant for]

[Include all proof points available from case studies. More is better here — this is a lookup table.]

## Trigger Events

What changes in a company's world make them a buyer? Paired to which problem from the stack each trigger activates.

- **[Trigger]** — [What happens] → Activates **Problem [#]** → Typical urgency: [timeframe]
- **[Trigger]** — [What happens] → Activates **Problem [#]** → Typical urgency: [timeframe]

[Include 5-8 triggers. Draw from case studies (what preceded the purchase), from the market change referenced in the soundbite, and from patterns visible in who buys.]
```

**Important:** Omit sub-sections that don't have real data behind them. A proof points section with 2 strong entries is better than one with 5 where 3 are fabricated. Note gaps explicitly — "No public case studies found for enterprise segment" is useful information for the AE.

### Phase 4: Confirm Completion

After writing the profile:

> **Company profile created for [Company Name].**
>
> Saved to: `~/.agents/sales/company-profile.md`
>
> **Soundbite:**
> [The 4-sentence soundbite]
>
> **Top 3 problems you solve:**
> 1. [Problem 1]
> 2. [Problem 2]
> 3. [Problem 3]
>
> **Defensible differentiation:**
> - [What only you can do]
>
> **Gaps in the profile:**
> - [Anything that couldn't be determined from public sources — e.g., "No pricing info public", "Limited case studies for enterprise segment"]
>
> You can enrich this profile anytime by pasting internal docs (pitch decks, battlecards, win/loss reports) and asking me to update it.

---

## Update Mode

When the user says "update my company profile" or "refresh the profile":

1. **Read the existing profile** at `~/.agents/sales/company-profile.md`

2. **Determine what to update:**
   - If the user provided new material (case study, battlecard, pitch deck) — incorporate it into the relevant sections
   - If the user wants a full refresh — re-run the research and update all sections
   - If the user mentions a specific change ("we just launched a new product" or "we got a new case study") — update only the relevant sections

3. **Update the profile in place** — Add new information, revise changed sections, keep what's still valid. Update the `generated` date.

4. **Show what changed:**
   > **Profile updated for [Company Name].**
   >
   > **What changed:**
   > - [New/updated sections]

---

## What This Skill Does NOT Do

- **No account research** — This profiles YOUR company, not your prospects. Use the sales-account-research skill for prospect companies.
- **No competitive deep-dive** — Differentiation is included, but a full competitive analysis with battlecards is a different task.
- **No internal data access** — This works from public sources. The user can paste internal docs to enrich the profile, but the skill doesn't access CRMs, internal wikis, or private data.
- **No messaging generation** — This produces the reference doc. Other skills use it to generate emails, business cases, talk tracks, etc.
