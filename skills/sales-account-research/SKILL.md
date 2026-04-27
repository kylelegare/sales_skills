---
name: sales-account-research
description: "Researches a company from public sources and builds an account dossier for sales preparation. Triggers on 'research [company]', 'look up [company]', 'prep me for [company]', 'update the dossier', or mentions of upcoming meetings at unresearched companies. Layers on specialized FI data for credit unions and community banks. External research only — does not read transcripts or extractions. For transcript processing, see sales-transcript-extractor. For deal scoring, see sales-deal-review."
---

# Sales Account Research

Build and maintain a living account dossier — the external research layer for any account. The dossier captures what you can learn about a company without ever talking to them. It lives alongside transcript extractions (what you learn from conversations) and together they give downstream skills the full picture.

## Core Principles

1. **The dossier is the external research layer** — It captures publicly available information about the company. It does NOT synthesize transcript data. Extractions are the internal knowledge layer. They stay separate.

2. **Research compounds over time** — The first research pass is broad. Subsequent updates are targeted — refresh what's changed, add what's new, keep what's still valid. The dossier gets richer, not replaced.

3. **Optimize for the AE's next conversation** — Every piece of research should help the AE walk into a call with context. If a data point doesn't help you have a better conversation, it probably doesn't belong.

4. **Use the buyer's world, not yours** — Research how THEY talk about their business. Their press releases, their earnings calls, their job postings tell you what they care about and the language they use to describe it.

## Workflow

### Phase 1: Identify the Account

1. **Get the company name** — from the user's request, or ask if unclear.

2. **Create the account slug** — lowercase, hyphenated (e.g., "acme-corp", "hubspot", "global-payments").

3. **Check for existing dossier** — Look for `~/.agents/sales/<account-slug>/dossier.md`
   - If it exists and the user said "update" or "refresh" — skip to the **Update Mode** section below.
   - If it exists and the user wants new research — ask whether to update the existing dossier or start fresh.
   - If it doesn't exist — continue with full research.

4. **Create directory structure** if needed:
   ```
   ~/.agents/sales/<account-slug>/
   ```
   (The transcripts/ and extractions/ subdirectories are created by the extraction skill when needed.)

### Phase 2: Research

Gather information from publicly available sources. Adapt depth to what's findable — a Fortune 500 has 10Ks and earnings calls, a Series B startup has a Crunchbase page and blog posts. Don't force sections that don't have meaningful data.

Run these research passes. Use web search and web fetch to execute the queries listed.

#### 2.1 What's Changing at This Company Right Now?

The most important research area. Changes create urgency and open doors. This is the "because of [change]" — the trigger for every deal.

**Search queries to run:**
- `"[Company Name]" news [current year]`
- `"[Company Name]" earnings OR revenue OR funding [current year]`
- `"[Company Name]" acquisition OR merger OR partnership`
- `"[Company Name]" new CEO OR new CRO OR new VP OR leadership change`
- `"[Company Name]" layoffs OR restructuring OR reorganization`
- `"[Company Name]" product launch OR expansion OR pivot`

**What to capture:**
- Revenue trends — growing, flat, declining? Did they miss or beat guidance?
- Leadership changes — new executives bring new strategies. Who's new and what's their mandate?
- Funding or M&A — a funding round means growth mode. An acquisition means integration mode.
- Restructuring — layoffs or reorgs signal cost pressure and changing priorities.
- Product launches or market expansion — signals where they're placing bets.

#### 2.2 What Are Their Executives Publicly Saying Matters?

Their leaders tell you their priorities — in their own language. Internal codenames and strategic themes make your messaging sound like it came from inside.

**Search queries to run:**
- `"[Company Name]" CEO OR CRO OR CFO earnings call transcript [current year]` (public companies)
- `"[Company Name]" annual report OR investor presentation [current year]`
- `"[Company Name]" [executive name] interview OR keynote OR conference`
- Visit their company blog for executive thought leadership
- Search LinkedIn for posts by their key executives (note themes, not specific content)

**What to capture:**
- Named strategic initiatives or programs (e.g., "Project Sundance", "Path to $1B", "Customer First transformation")
- Metrics they're tracking publicly (NRR, ARR growth rate, customer count targets)
- Problems they've acknowledged publicly
- Their own language for their priorities — the exact phrases executives use

#### 2.3 What Are They Investing In?

Job postings are the most honest signal of a company's priorities. They can't fake hiring.

**Search queries to run:**
- Visit `[Company website]/careers` or `[Company website]/jobs`
- `"[Company Name]" jobs site:linkedin.com OR site:greenhouse.io OR site:lever.co`
- `"[Company Name]" hiring OR recruiting [function relevant to your sale]`

**What to capture:**
- What functions are they hiring for? (Sales = growth mode. Engineering = build mode. CS = retention focus.)
- Tech stack mentioned in job descriptions (tells you what they're using and what they might replace)
- Are they building internally something you could sell them? (A posting for "data engineer to build internal analytics pipeline" = they're trying to DIY what you sell)
- Seniority of open roles (hiring a VP means they don't have one — that's a gap you can sell into)

#### 2.4 Company Overview

Foundational context — what does this company actually do?

**Search queries to run:**
- `"[Company Name]"` (their website, Wikipedia, Crunchbase, G2/Gartner profiles)
- `"[Company Name]" revenue OR employees OR customers` (sizing)

**What to capture:**
- What they do in plain language (not their marketing tagline)
- Business model — how they make money (subscription, transactional, marketplace, etc.)
- Size — revenue, employee count, customer base (whatever is public)
- Headquarters and geographic presence
- Market position — leader, challenger, or niche player?

Keep this tight. If the interesting stuff was already captured in 2.1-2.3, the overview can be brief.

#### 2.5 Industry Context

Only what's relevant to YOUR conversation with them.

**Search queries to run:**
- `[Industry] trends [current year]`
- `[Industry] challenges OR disruption [current year]`
- `"[Company Name]" competitors OR competitive landscape`

**What to capture:**
- Market trends their executives are likely thinking about
- Regulatory changes they have to respond to
- Competitive moves that pressure them
- Technology shifts in their space

#### 2.6 Key People (Lightweight)

Note executives and leaders found during research. This is NOT a deep stakeholder profile — just who showed up and what's publicly available.

**What to capture:**
- Names and titles of executives relevant to your sale
- How long they've been in role (new = new priorities, tenured = established approach)
- Background that's relevant (came from a competitor? from a customer of yours?)
- Any public content they've authored — talks, posts, articles

Don't go deep. Stakeholder intelligence builds over time through conversations, not through web research.

#### 2.7 Financial Institution Layer (Conditional)

**Only if the account is a credit union or community bank**, add specialized FI data:

**For Banks (FDIC-regulated):**
- Use FDIC BankFind Suite (https://banks.data.fdic.gov/) for asset size, charter type, regulator, location
- If a script exists at `~/.agents/skills/financial-institution-research/scripts/fdic_api.py`, use it:
  ```bash
  python3 ~/.agents/skills/financial-institution-research/scripts/fdic_api.py "[Bank Name]"
  ```

**For Credit Unions (NCUA-regulated):**
- Search for charter number, asset size, member count via https://mapping.ncua.gov/ or web search
- Field of membership (who can join — community, employer, association)
- Charter type (Federal or State)

**For both:**
- Service area demographics (population, income, major employers)
- Asset size context: <$100M (small), $100M-$1B (medium), $1B+ (large)
- Regulatory environment relevant to what you're selling

### Phase 3: Write the Dossier

Save the dossier to `~/.agents/sales/<account-slug>/dossier.md` with this structure:

```markdown
---
account: [account-slug]
company: [Full Company Name]
industry: [Industry]
last_researched: [YYYY-MM-DD]
sources_checked:
  - [list of sources consulted]
---

# [Company Name] -- Account Dossier

## What's Changing
[Recent news, events, leadership changes, funding, earnings -- dated, most recent first. Focus on changes that create urgency or shift priorities.]

## Strategic Priorities
[What their executives are saying matters. Named initiatives, public metrics, language they use. Include direct quotes from earnings calls or executive posts when available.]

## Hiring Signals
[What roles they're hiring, what it suggests about their priorities and investments.]

## Company Overview
[What they do, size, business model, market position. Keep brief if covered above.]

## Industry Context
[Relevant market trends, competitive pressures, regulatory factors.]

## Key People
[Names, roles, background, relevant public content they've authored.]

## FI-Specific Data
[Only if applicable -- FDIC/NCUA data, charter info, field of membership, service area.]

## Conversation Starters
[2-4 specific, research-backed openers. Each follows the context-led question format: an observation from research + why it's relevant + a question that ties to a problem you solve. Not generic. Tied to what you found above.]

Format each as:
- **Observation:** [What you noticed in your research -- a press release, earnings trend, hiring signal, industry shift]
- **Opener:** "[Name], I noticed [observation]. [Why it's relevant or 'I'll make it clear why this matters in a second']. [Question that ties to a problem area you solve]."

Example: "Emily, I noticed you just announced expansion into the enterprise segment -- you're hiring 12 AEs and a new VP of Sales. Companies at that inflection point often find that what worked for SMB motion doesn't translate to enterprise. How are you thinking about that transition?"

## Questions to Explore
[3-5 questions worth asking based on the research -- gaps in your understanding, signals that could indicate a fit, things you want to validate.]
```

**Omit sections that have no meaningful data.** A clean dossier with 4 strong sections is better than one padded with filler.

### Phase 4: Confirm Completion

After writing the dossier, confirm:

> **Dossier created for [Company Name].**
>
> Saved to: `~/.agents/sales/<account-slug>/dossier.md`
>
> **Key findings:**
> - [2-3 most significant or actionable findings]
>
> **Best conversation starters:**
> - [The top 1-2 from the dossier]

---

## Update Mode

When the user says "update the research" or "refresh the dossier":

1. **Read the existing dossier**

2. **Run targeted research** -- Don't redo everything. Focus on:
   - Recent news since `last_researched` date
   - Leadership changes
   - New job postings
   - Any earnings or funding events

3. **Update the dossier in place** -- Add new information, update changed information, keep what's still valid. Update the `last_researched` date and `sources_checked`.

4. **Show what changed:**
   > **Dossier updated for [Company Name].**
   >
   > **What's new:**
   > - [Changes or new findings since last research]

---

## What This Skill Does NOT Do

- **No transcript processing** -- Use the sales-transcript-extractor skill for that. This skill reads from extractions but doesn't create them.
- **No artifact generation** -- This skill doesn't write follow-up emails, business cases, or proposals. It provides the research that those skills pull from.
- **No CRM integration** -- No Salesforce sync. The dossier is a local file.
- **No deep stakeholder profiling** -- Key people are noted during research, but detailed stakeholder intelligence builds through conversations and extractions over time.
