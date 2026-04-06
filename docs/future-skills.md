---
date: 2026-04-05
status: notes
---

# Future Skills — Ideas From pclub Course Analysis and Brainstorming

Possible future skills informed by Orlob's discovery and closing courses, Nasralla's Brief & Brilliant methodology, and gaps identified while building the current toolkit. Not committed — just captured for later decision.

## 1. Call Prep Skill

**Trigger:** "prep me for the Acme call Thursday", "what should I ask on the next call"

**What it does:** Generates a call plan with specific questions, ordered by Orlob's sequencing rules. Pulls from deal review gaps, dossier, company profile, and a discovery methodology reference doc.

**Key features:**
- Picks the right opening technique based on context (ROE for new, go-back-in-time for inbound, context-led for outbound)
- Generates questions following open-before-targeted sequencing
- Orders questions by progressive intrusion (least → most sensitive)
- Includes "summarize and transition" prompts between phases
- Outputs: opening play, 4-6 prioritized questions, suggested close/next-step prescription

**Dependencies:** Discovery methodology reference doc, deal review, dossier, company profile

**Architecture:** Skill at `~/.agents/skills/sales-call-prep/SKILL.md` with a reference doc at `references/discovery-methodology.md`

---

## 2. Discovery Methodology Reference Doc

**Not a skill — a reference doc loaded by other skills (call prep, deal review's "Focus for Next Call")**

**What it contains:**
- Orlob's 4-phase discovery architecture (business problem → cause analysis → negative impact → future state)
- Question templates for each phase (open diagnostic, targeted diagnostic, negative impact, future state)
- Recurring techniques: summarize-and-transition, permission points, softening language, "to what extent" and context-led templates
- The three-bucket classification (business problem vs root cause vs desired solution)
- The discovery prompter structure (5-part pain story + pass-the-torch)
- Inbound vs outbound opening technique decision tree

**Location:** `~/.agents/skills/sales-call-prep/references/discovery-methodology.md`

---

## 3. Closing Motion Skill

**Trigger:** "how do we close this deal", "what's left to get this signed", "closing motion for Acme", "are we going to close this quarter"

**What it does:** Reads extractions and deal review, maps the remaining path to signature, identifies risks, and generates the right closing motion questions.

**Key features from Orlob's closing course:**
- Qualify vendor of choice first (if competitive)
- Map the full series of steps → people → timeline → money (progressive intrusion)
- Spot-check for unmentioned risks: legal, procurement, paper process, uncovered stakeholders
- Pick the right closing motion question based on timing (tight vs good)
- Prescribe next steps using what/who/why framework
- "What's changed since last we spoke?" as ongoing confirmation
- Backstop meeting recommendations
- Micro-commitment from champion before handoff

**Output:** Closing motion checklist, recommended questions, risk spot-checks, suggested next step prescription

**Architecture:** Skill at `~/.agents/skills/sales-closing-motion/SKILL.md`, possibly with a reference doc for the closing methodology

---

## 4. Follow-Up Email Skill

**Trigger:** "write the follow-up for the Acme call", "draft an email after today's call"

**What it does:** Reads the latest extraction for an account, generates a follow-up email grounded in buyer language and commitments.

**Key features:**
- Uses buyer's exact words from extraction quotes
- References specific commitments from both sides
- Includes "what we heard" summary (builds toward Nasralla's business case)
- Suggests next step aligned with deal review gaps
- Passes the forwarding test — reads like it came from someone who was paying attention

---

## 5. Business Case Builder

**Trigger:** "build the business case for Acme", "write the one-pager"

**What it does:** Reads all extractions + company profile + dossier, generates a Nasralla-style 1-page business case.

**Key features:**
- Soundbite structure (because of → now's the time → so that → instead of)
- Problem written in buyer's language (from extraction quotes)
- Quantified impact using buyer's own numbers (metric that matters, cost of inaction)
- Required capabilities mapped to root causes (from extractions)
- Proof points matched from company profile
- Passes the forwarding test — champion can send to CFO without editing

**Dependencies:** Needs 3+ extractions to have enough material. Company profile must exist.

---

## 6. Demo Prep / Win the Demo Skill

**Trigger:** "prep the demo for Acme", "what should I show them"

**What it does:** Reads extractions (especially root causes, buying criteria, future state items) and generates a demo plan.

**Key features:**
- Maps capabilities to the buyer's specific root causes (not a generic feature tour)
- Sequences demo around buyer's priorities (most important problem first)
- Includes "targeted solution question" callbacks — the things you got them to agree they need
- Suggests what NOT to show (capabilities that don't map to their stated problems)
- Anti-pattern detection: flags if you're about to demo something that contradicts the buyer's stated root cause

---

## 7. Objection Handler / Risk Seeker

**Trigger:** "what objections should I expect", "what could go wrong with this deal"

**What it does:** Reads extractions + deal review, proactively identifies likely objections and risks, generates preparation material.

**Key features:**
- Maps common objections to company profile differentiation tiers (defensible vs comparative vs assumed)
- Identifies "happy ears" moments in extractions (seller interpreted enthusiasm that wasn't backed by buyer action)
- Surfaces commitment asymmetry (seller commits a lot, buyer commits little)
- Generates Orlob's risk-seeking spot-check questions
- Flags timeline slippage patterns across extractions

---

## 8. Discovery Prompter Generator

**Trigger:** "write me a discovery prompter for [persona/industry]", "create an opening story"

**What it does:** Uses company profile + proof points to generate Orlob's 5-part discovery prompter (pain statement → expand → negative impact → short solution → pass the torch).

**Key features:**
- Targeted to specific persona/industry
- Focuses on pain, not product (per Orlob's instructions)
- Generates the torch-pass question with time-horizon filter
- Can generate multiple versions for different buyer types

---

## Priority Ordering (Suggested)

1. **Discovery methodology reference doc** — Enables call prep and sharpens deal review. Low effort, high leverage.
2. **Call prep skill** — Most frequently needed. AEs prep before every call.
3. **Follow-up email skill** — Highest frequency downstream artifact. Every call needs a follow-up.
4. **Business case builder** — Highest impact downstream artifact. The thing that actually wins deals.
5. **Closing motion skill** — Critical for late-stage deals. Less frequent but high stakes.
6. **Demo prep** — Important but less frequent than call prep.
7. **Discovery prompter generator** — Nice to have. AEs tend to develop these organically.
8. **Objection handler** — Nice to have. More of a coaching tool.

---

## Infrastructure Ideas (Revisit After Skills Are Built)

Lessons from https://github.com/coreyhaines31/marketingskills — a 34-skill marketing plugin with 19K stars. Good patterns to steal once we have enough skills built and tested on real deals. Don't invest in this infrastructure until skills have stabilized through real use.

### Evals
- Each skill gets an `evals/evals.json` with 5-7 test cases
- Format: `{ prompt, expected_output, assertions[] }` — simple JSON, no fancy infra
- Assertions are specific checkable claims ("Checks for company-profile.md", "Tags buyer statements as root-cause when appropriate")
- Run with LLM-as-judge — give it the prompt + skill, get output, check assertions
- **Boundary evals** — at least one eval per skill tests that out-of-scope requests get redirected to the right skill (e.g., extractor doesn't try to write follow-up emails)

### References Directory
- Keep SKILL.md under ~500 lines as the workflow
- Deep knowledge goes in `references/` — loaded on demand
- Natural candidates: discovery methodology (Orlob's 4-phase framework), closing motion checklist, question templates, the root cause map exercise
- SKILL.md says "load references/discovery-methodology.md" when it needs the depth

### Hub Skill Pattern
- Their `product-marketing-context` is checked by every other skill as step 1
- Our `company-profile` is the equivalent but not every skill explicitly checks for it yet
- Consider making the check explicit: every skill's Phase 1 includes "load company profile if it exists"

### Auto-Sync Infrastructure
- GitHub Action + script keeps plugin manifest and README in sync when skills change
- Worth it once we have 10+ skills, not before

### Cross-Agent Compatibility
- They use `.agents/skills/` instead of `.claude/skills/` so skills work in Codex, Cursor, Windsurf
- Consider this if/when packaging as a plugin for distribution
- For now, `.claude/skills/` is fine — we're building for Kyle first

### Validation
- They have a bash script that checks: frontmatter format, description length, trigger phrases present, SKILL.md under 500 lines, name matches directory
- Nice guardrail once you have contributors or are publishing
