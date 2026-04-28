---
name: sales-call-prep
description: "Generates specific discovery questions and a call plan for an upcoming sales conversation. Triggers on 'prep me for the [account] call', 'what should I ask', 'generate questions for [account]', 'call prep', 'prep for my meeting with [person/company]', 'what are the gaps in this deal', 'help me prepare for tomorrow's call', or 'I have a call with [account] on Thursday'. Loads deal review gaps, dossier, company profile, and discovery methodology to produce questions the AE can actually ask. For processing transcripts after a call, see sales-transcript-extractor. For deal health scoring, see sales-deal-review. For external research, see sales-account-research."
---

# Sales Call Prep

Generate a call plan with specific, ready-to-ask discovery questions for an upcoming conversation. Every question is grounded in what's known (from extractions and research) and what's missing (from deal review gaps). Questions follow proven methodology — the depth chain, question formulas, humbling disclaimers, "to what extent" patterns — so they sound natural, not robotic.

## Core Principles

1. **Questions come from gaps, not templates** — Don't generate a generic question list. Read the deal review gaps and the dossier, then produce questions that fill specific holes in your knowledge of this account. A deal with strong Problem but Unknown Urgency needs urgency questions, not more problem questions.

2. **Ready to say out loud** — Every question should pass the "would I actually say this in a conversation?" test. Include softening language, humbling disclaimers, and natural transitions. The AE should be able to read these verbatim or adapt them naturally.

3. **Sequenced, not random** — Questions follow a logical order: open before targeted, least intrusive before most intrusive, build on what the prospect just said. The AE isn't going to ask all of them — they'll pick the best 3-5 — but the ordering helps them navigate the conversation.

4. **Grounded in buyer language** — When prior extractions contain buyer quotes and terminology, use those words in the questions. "You mentioned your close rates dropped from 28% to 19%..." is better than "Regarding the performance issue you discussed..."

## Workflow

### Phase 1: Gather Context

1. **Identify the account** — from the user's request, or ask.

2. **Load everything available** for this account:
   - `~/.agents/sales/<account-slug>/extractions/` — all extraction files (what you've learned from conversations)
   - `~/.agents/sales/<account-slug>/dossier.md` — external research
   - `~/.agents/sales/<product-slug>/company-profile.md` — what you sell (product-aware path produced by the evolved sales-company-profile skill). If the user has multiple products, ask which applies. **Fallback:** `~/.agents/sales/company-profile.md` (legacy flat path from the pre-evolution version) — read if the product-aware path doesn't exist.

3. **Assess the deal stage** by scanning what exists:
   - **No extractions, no dossier** — This is a first call with no prep. Generate an opening plan with context-led questions if dossier exists, or a discovery prompter approach if going in cold.
   - **No extractions, dossier exists** — First call but research is done. Use dossier findings for context-led openers.
   - **1-2 extractions** — Early stage. Focus on deepening: cause analysis, negative impact, stakeholder mapping.
   - **3+ extractions** — Mid/late stage. Focus on gaps: buying criteria, process, closing motion qualification.

4. **Identify the gaps** — Read the most recent extraction's feedback summary for flagged gaps. If no extraction exists, all dimensions are gaps.

5. **Load the discovery methodology** — Read [references/discovery-methodology.md](references/discovery-methodology.md) for question patterns and sequencing rules.

6. **Get call context from the user** if helpful:
   > "Anything specific you want to accomplish on this call, or should I work from the gaps?"

   The user might say "I need to get to the economic buyer" or "I want to quantify the impact" or "just prep me." All valid.

### Phase 2: Generate the Call Plan

**Output format:**

```
## Call Prep: [Account Name]
**Call type:** [Discovery / Follow-up / Demo / Executive / etc.]
**Based on:** [X extractions + dossier + company profile, or "cold — no prior data"]
**Key gaps to address:** [2-4 specific gaps from deal review]

### Opening Play

[How to open this specific call. Pick the right technique based on context:
- First call, inbound → Go-Back-In-Time sequence
- First call, outbound → Context-led question from dossier research
- Follow-up call → "What we heard" summary + permission to ask a few more questions
- Executive meeting → Lead with insight, not questions]

[Write out the actual opening — the words the AE would say, including softening language and transitions.]

### Questions to Ask

[4-8 specific questions, ordered by priority and sequenced for natural conversation flow. Each question includes:]

**1. [Gap this addresses]: [The actual question]**
*Why this matters:* [1 sentence — what evidence this unlocks and why it's missing]
*If they answer well, follow up with:* [A deeper question or natural transition]
*If they deflect, try:* [An alternative angle or softer version]

**2. [Gap this addresses]: [The actual question]**
...

### Closing the Call

[How to close based on where the deal is:
- Early stage → Secure specific next step with date and attendees
- Mid stage → "What do you think you need?" future state question
- Late stage → Closing motion questions (vendor of choice, series of steps)]

[Write out the actual close — including "Does that feel fair?" or "strong prescription loosely held" language.]

### Watch For

[2-3 specific things to listen for on this call, based on what's known:
- A signal that would change the deal (e.g., "if they mention a competing evaluation, probe immediately")
- A thread from a prior call to follow up on
- A stakeholder name or organizational dynamic to map]
```

### Phase 3: Output the Plan

Output directly in conversation. This is a prep doc the AE reads before their call — not stored as a persistent file.

If the user asks for a shorter version ("just give me the questions"), skip the structure and output the numbered question list with the gap labels.

---

## Question Generation Rules

When generating questions, load and follow [references/discovery-methodology.md](references/discovery-methodology.md). Key rules:

1. **Match question type to the gap:**
   - Problem gap → Problem formula (context + insight + open question + disclaimer)
   - Root cause gap → Open diagnostic ("What's your opinion on why...") then targeted ("To what extent is [root cause] an issue?")
   - Impact gap → Negative impact questions ("What are the ripple effects..." / "To what extent has this impacted [area]?")
   - Urgency gap → Time-based questions ("What's compelling you to solve for this now instead of waiting?")
   - People gap → Stakeholder questions ("Who else does this challenge impact?" / "To what extent does your boss need to be part of this?")
   - Process gap → Closing motion questions ("What still needs to happen from here?")
   - Approach gap → Future state questions ("What do you think you need?" then targeted solution questions)

2. **Always use softening before tough questions:**
   - Humbling disclaimers for sensitive topics (budget, decision authority, competition)
   - Permission points before shifting topic ("Do you mind if I ask about...")
   - Negative disposition endings for direct questions ("...or would you say that's not really an issue?")

3. **Use buyer language when available:**
   - If extractions contain buyer quotes, weave their exact words into questions
   - "You mentioned [their phrase] — to what extent has that been impacting [area]?"

4. **Include transitions between questions:**
   - Recap what you expect to hear, then transition: "Great. Now that we're aligned on that..."
   - "That's interesting. Can you help me understand..." (support response, not shift response)

5. **Don't overload** — 4-8 questions is plenty. The AE will adapt in the moment. Better to have 5 great questions than 15 mediocre ones.

---

## What This Skill Does NOT Do

- **No transcript processing** — Use sales-transcript-extractor for that.
- **No external research** — Use sales-account-research for that. This skill reads from existing research.
- **No deal scoring** — Use sales-deal-review for that. This skill reads from deal review gaps.
- **No artifact generation** — This doesn't produce emails, proposals, or business cases.
- **No coaching** — This doesn't evaluate past call performance. It prepares for the next one.
