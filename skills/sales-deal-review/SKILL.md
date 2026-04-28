---
name: sales-deal-review
description: "Scores deal health across 6 evidence-based dimensions using buyer quotes from call extractions. Triggers on 'review this deal', 'deal review for [account]', 'how's the deal looking', 'score this deal', 'where do we stand', 'deal health', 'what are we missing'. Can auto-run after new extractions. For processing transcripts, see sales-transcript-extractor. For external research, see sales-account-research."
---

# Sales Deal Review

Score deal health based on what buyers have actually said — not what the AE hopes is true. This skill reads all extractions for an account, evaluates 6 evidence-based dimensions, and produces an honest assessment grounded in direct quotes. No framework is imposed — but any framework (MEDDPICC, SPICED, Challenger, Gap Selling) can be derived from the output.

## Core Principles

1. **Evidence over opinion** — Every score is backed by what the buyer said, with direct quotes when available. If there's no evidence for a dimension, the score is "unknown" — not "needs work." The absence of evidence is the finding.

2. **Quote-grounded scoring** — A dimension is only scored as strong when you can point to a specific buyer quote that demonstrates it. Seller assertions ("I think they're bought in") don't count. Buyer statements do.

3. **Pre-framework, not anti-framework** — The 6 dimensions are universal building blocks. MEDDPICC's "M" maps to Problem + Impact. SPICED's "I" maps to Impact + Urgency. The AE's preferred framework can be layered on top, but the raw evidence comes first.

4. **Honest, not encouraging** — This skill exists to prevent deals from dying in the dark. A deal with 2 strong dimensions and 4 unknowns is a deal with 4 unknowns — not a deal that's "on track." Surface reality so the AE can act on it.

5. **Cumulative across calls** — The review reads ALL extractions for an account, not just the latest one. Evidence compounds. A problem mentioned in call 1, quantified in call 3, and confirmed by a second stakeholder in call 5 is stronger than one mentioned once.

## The 6 Dimensions

These are not a sales framework. They're the universal evidence categories that every framework draws from.

### 1. Problem
**What we're scoring:** Has the buyer articulated a specific business problem — a suffering metric the decision maker owns? Is it a surface complaint or a root cause? Is it their words or our projection? Does it pass the specificity rating?

**The specificity rating (Discovery-Depth):** If you removed the account name from your problem description, could you identify which deal this is? A problem description so unique and detailed it could only apply to one account = strong discovery. A generic description that could describe any company = weak discovery. Rate 1-5.

**Strong evidence looks like:**
- Buyer describes a specific suffering metric in their own words (direct quote) — e.g., "close rates dropped from 25% to 20%"
- Problem passes the specificity rating (unique, detailed, account-specific)
- Root causes have been explored — buyer has articulated WHY the problem is happening
- Problem is "above the line" — it's in the decision maker's top priorities, not a brush fire
- Buyer connects the problem to business outcomes they own

**Weak evidence looks like:**
- Seller described the problem and buyer said "yeah"
- Problem is generic ("we need to be more efficient") — fails the specificity rating
- Only root causes or symptoms surfaced, no connection to a suffering business metric
- Problem may be real but "below the line" — not in the decision maker's top 3-5 priorities
- Problem is assumed from industry trends, not stated by the buyer

### 2. Impact
**What we're scoring:** Can we quantify what this problem costs them? Do we have the "metric that matters" and a "cost of inaction"? Is the number from the buyer or from us? Have negative ripple effects been explored?

**Strong evidence looks like:**
- Buyer provides specific numbers ("this costs us $2M a year in churn") — the "metric that matters" is identified
- Cost of inaction is quantified on a monthly basis (what it costs them every month they don't act)
- Negative impact / ripple effects explored — second-order consequences beyond the primary problem (e.g., problem is declining close rates → negative impact is higher customer acquisition costs, team morale issues, investor concern)
- Impact has been validated by someone with budget authority
- Multiple types of impact identified (revenue + time + risk + downstream effects)

**Weak evidence looks like:**
- Seller projected ROI and buyer didn't push back
- Impact is directional but not quantified ("it's a big problem")
- Only the primary problem explored — no ripple effects or second-order consequences surfaced
- Impact comes from a case study, not from the buyer's own situation
- No cost of inaction — buyer can rationalize delaying indefinitely

### 3. Urgency
**What we're scoring:** Is there a forcing function — a date, event, or consequence that makes "later" unacceptable? Or is this a nice-to-have with no deadline? Is the timeline buyer-driven or seller-driven?

**Strong evidence looks like:**
- Buyer names a specific date or event driving the timeline ("we need this before Q3 board meeting") — buyer-driven urgency
- Consequence of inaction is articulated ("if we don't fix this by renewal, we lose the account")
- Cost of inaction is quantified monthly — the buyer feels the bleed every period that passes
- Urgency has been confirmed in multiple conversations
- The problem is "above the line" — in the decision maker's top priorities, not a brush fire they're managing around

**Weak evidence looks like:**
- Seller proposed a timeline and buyer agreed — seller-driven urgency is not real urgency
- Buyer said "soon" or "this quarter" without a specific driver
- No consequence of inaction articulated — they can live with this indefinitely
- Timeline keeps slipping across conversations (a red flag: urgency may be manufactured)
- Problem may be real but it's a "brush fire" — the executive is a park ranger managing thousands of acres, and this isn't a raging forest fire

### 4. Approach
**What we're scoring:** Does the buyer believe our approach is the right way to solve their problem? Have they seen how it works and connected it to their situation? Or are we still one of many options?

**Strong evidence looks like:**
- Buyer describes how our solution fits their situation (in their words)
- Buyer has connected specific capabilities to their specific problems
- Buyer has articulated why our approach is better than alternatives they've considered
- Buyer is using our language or, better, has adapted it into their own

**Weak evidence looks like:**
- Buyer sat through a demo and said "looks good"
- No comparison to alternatives has happened
- Buyer hasn't connected capabilities to their specific problems
- All enthusiasm is from the seller side

### 5. People
**What we're scoring:** Do we know who's involved in the decision? Who has power? Who champions us? Who might block? Or are we single-threaded with one contact?

**Strong evidence looks like:**
- Multiple stakeholders identified by name and role
- We know who makes the final decision (and it's not just our contact)
- Champion has been tested — they've done something for us that cost them political capital (shared internal docs, set up meetings with leadership, advocated in internal meetings, pushed back on procurement)
- Potential blockers identified and a plan exists to address them
- Organizational dynamics are understood (who reports to whom, who has budget authority)
- We have access to the economic buyer (the person who owns the suffering metric), not just an end-user or evaluator

**Weak evidence looks like:**
- Only one contact in the account — single-threaded
- We assume our contact is the decision-maker because they said so
- Champion hasn't been tested — they're friendly but haven't taken any action on our behalf. Friendliness is not championship.
- No visibility into who else is involved or who might block
- Procurement, legal, or IT involvement is unknown
- No one on the buyer side has "sold internally" for us — we're doing all the selling ourselves

### 6. Process
**What we're scoring:** Do we know how they buy? What steps remain between now and signature? Who's involved in each step? What could derail it? Or are we guessing at their internal process?

**Strong evidence looks like:**
- Buyer has described the full series of steps between now and signature (not just the next one — you asked twice)
- For each step: we know WHO is involved and HOW LONG it takes
- We know what approvals are needed and from whom
- Paper process is mapped — legal, security review, procurement, who signs, how many signatures
- Specific next steps are committed with dates and owners — and there's a backstop meeting on the calendar
- We've proactively spot-checked for common risks the buyer didn't mention (legal review, procurement, uncovered stakeholders)
- We know whether we're vendor of choice (if competitive)

**Weak evidence looks like:**
- Next steps are vague ("we'll circle back") — open loops with nothing scheduled
- Only the next step is known, not the full series after that
- No visibility into internal approval process
- Commitments are one-sided (seller commits, buyer doesn't)
- Legal, procurement, or security review hasn't been discussed — could surprise us
- We don't know who else they're evaluating or whether we're vendor of choice
- No micro-commitment from champion to re-engage if things go sideways during handoff

## Workflow

### Phase 1: Gather Evidence

1. **Identify the account** — from the user's request, or ask:
   > "Which account do you want to review?"

2. **Read all extractions** — Load every file from `~/.agents/sales/<account-slug>/extractions/`. These are the evidence base.

3. **Read the dossier** (if it exists) — Load `~/.agents/sales/<account-slug>/dossier.md` for external context. The dossier provides background but is NOT evidence of buyer intent — only buyer statements from extractions count as evidence.

4. **Read the company profile** (if it exists) — Look for the product-aware path first: `~/.agents/sales/<product-slug>/company-profile.md` (produced by the evolved sales-company-profile skill; if the user has multiple products, ask which applies). **Fallback:** `~/.agents/sales/company-profile.md` (legacy flat path from the pre-evolution version of the skill). The company profile gives context on what you sell — helps assess whether buyer-stated problems align with your value chain, but does NOT substitute for buyer evidence.

### Phase 2: Score Each Dimension

For each of the 6 dimensions:

1. **Scan all extractions** for items tagged with relevant topics:
   - Problem → items tagged `problem`
   - Impact → items tagged `outcome`, plus any items with quantified business impact
   - Urgency → items tagged `timeline`, plus items with deadlines or forcing functions
   - Approach → items tagged `criteria`, `competition`, plus items where buyer evaluates solutions
   - People → items tagged `stakeholder`, plus items revealing organizational dynamics
   - Process → items tagged `process`, `commitment`, plus items about next steps and evaluation

2. **Assess evidence strength** using this scale:

   | Score | Meaning | Evidence Required |
   |-------|---------|-------------------|
   | **Strong** | Buyer has clearly articulated this in their own words | Direct quote(s) from buyer, validated across calls or stakeholders |
   | **Emerging** | Some evidence exists but it's incomplete or unvalidated | Buyer mentioned it but hasn't elaborated, or only one source |
   | **Weak** | Only seller-side assertions or vague signals | Seller projected it and buyer didn't disagree, or generic statements |
   | **Unknown** | No evidence in any extraction | Nothing found — this hasn't been discussed or explored |

3. **Pull the evidence** — For each scored dimension, identify the 1-3 strongest pieces of evidence. Include direct quotes when available. Note which call they came from (date and type).

### Phase 3: Write the Review

Output the review directly in the conversation. This is NOT stored as a file — it's a point-in-time assessment that the AE reads and acts on. (The evidence lives in the extractions; the review is a lens on that evidence.)

**Output format:**

```
## Deal Review: [Account Name]
**Based on:** [X] calls from [earliest date] to [latest date]
**Reviewed:** [today's date]

### Evidence Scorecard

| Dimension | Score | Key Evidence |
|-----------|-------|-------------|
| Problem | [Strong/Emerging/Weak/Unknown] | [1-sentence summary with quote snippet] |
| Impact | [Strong/Emerging/Weak/Unknown] | [1-sentence summary with quote snippet] |
| Urgency | [Strong/Emerging/Weak/Unknown] | [1-sentence summary with quote snippet] |
| Approach | [Strong/Emerging/Weak/Unknown] | [1-sentence summary with quote snippet] |
| People | [Strong/Emerging/Weak/Unknown] | [1-sentence summary with quote snippet] |
| Process | [Strong/Emerging/Weak/Unknown] | [1-sentence summary with quote snippet] |

### What's Strong

[For dimensions scored Strong or Emerging — expand on the evidence. Include full quotes, note which call they came from, and explain why this evidence is solid. This is the foundation the deal is built on.]

### What's Missing

[For dimensions scored Weak or Unknown — be specific about what's not there. Not "we need to understand their timeline better" but "no one on the buyer side has named a date, event, or consequence that makes this urgent. The only timeline reference was the seller suggesting Q3 and the buyer saying 'that could work.'"]

### Contradictions and Risk

[Anything that doesn't add up across calls. Timeline shifts, conflicting statements from different stakeholders, enthusiasm that isn't backed by action. These are the things that kill deals quietly.]

### Focus for Next Call

[2-4 specific, actionable things to address in the next conversation. Each one tied to a gap or risk identified above. Not generic advice — specific questions to ask or things to validate, grounded in what's missing from the evidence.]

- **[Dimension]:** [Specific question or action] — because [why this matters based on the evidence gap]
```

### Phase 4: Framework Overlay (Optional)

If the user asks for a specific framework view ("show me the MEDDPICC", "where are we on SPICED"), map the 6 dimensions to the requested framework:

**MEDDPICC mapping:**
- **M**etrics → Impact
- **E**conomic Buyer → People (specifically: who has budget authority?)
- **D**ecision Criteria → Approach
- **D**ecision Process → Process
- **P**aper Process → Process (specifically: procurement, legal, security review)
- **I**dentified Pain → Problem
- **C**hampion → People (specifically: has anyone acted on our behalf?)
- **C**ompetition → Approach (specifically: who else are they evaluating?)

**SPICED mapping:**
- **S**ituation → Problem (the current state)
- **P**ain → Problem + Impact (the problem and what it costs)
- **I**mpact → Impact + Urgency (quantified consequence + timeline)
- **C**ritical Event → Urgency (the forcing function)
- **E**xpected Decision → Process + Approach
- **D**ecision → People + Process

**Forwarding-Test Business Case mapping:**
- "Because of" → Urgency (the change creating urgency)
- Problem definition → Problem + Impact
- Required capabilities → Approach
- Positive business outcomes → Impact (the quantified upside)
- Proof → mapped to company profile proof points that match the buyer's stated problems

Present the framework view using the same evidence — just reorganized into the framework's categories. Don't invent new information.

---

## Auto-Run Mode

If the user says "auto-run deal reviews" or "run this after every extraction":

After processing a transcript (via the sales-transcript-extractor skill), automatically run a deal review if:
- There are 2+ extractions for the account (a single call isn't enough for a meaningful review)
- The most recent extraction is from today (just processed)

Output a condensed version:
- The scorecard table
- Only dimensions that changed (improved or weakened) since the last review
- The "Focus for Next Call" section

This keeps the AE in a loop without overwhelming them after every call.

---

## What This Skill Does NOT Do

- **No CRM updates** — This doesn't write to Salesforce. The AE uses the review to update their own CRM notes.
- **No coaching** — This doesn't evaluate seller performance. A coaching skill can read from the same extractions.
- **No forecasting** — This doesn't predict win probability. It tells you what evidence exists and what's missing. The AE's judgment determines the forecast.
- **No artifact generation** — This doesn't produce follow-up emails, business cases, or proposals. Those are separate skills.
- **No cross-deal comparison** — This reviews one deal at a time. Portfolio-level analysis is a different tool.
