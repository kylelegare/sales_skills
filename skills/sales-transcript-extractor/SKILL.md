---
name: sales-transcript-extractor
description: "Process sales call transcripts into structured, queryable extractions. Use when the user pastes a transcript, says 'process this transcript', 'extract from this call', 'I just had a call', 'debrief this transcript', 'here's the transcript', 'extract this', or shares meeting notes from Granola or Gong. Also use when the user says 'deep dive' on a previous call or asks to re-process a transcript more thoroughly."
---

# Sales Transcript Extractor

Process a raw sales call transcript into a structured extraction — a companion document that captures every meaningful piece of information in a queryable, reusable format. The raw transcript is preserved as-is. The extraction becomes the working document that downstream skills and ad-hoc questions pull from.

## Core Principles

1. **Pre-analytical extraction** — Capture what was said faithfully. Do not impose MEDDPICC, business case frameworks, or any sales methodology during extraction. Downstream skills apply frameworks to the extracted data. The extraction is the raw material, not the analysis.

2. **Buyer's words are sacred** — When a buyer uses specific terminology, codenames, or phrasing, capture their exact words. This is the most valuable thing in the extraction. A business case written in the buyer's language passes the forwarding test — it sounds like it came from inside their building.

3. **Quotes are the confidence signal** — When a direct quote is available, include it. The presence of a quote means the information is high-confidence and can be used directly in artifacts. The absence of a quote means the information is an interpretation and may need confirmation. No separate confidence field is needed.

4. **What was said AND why it matters** — Every extraction item captures both the information itself and a brief note on why it's significant. Is it new? Does it contradict something? Is it time-sensitive? Does it reveal something about a person or process?

5. **Extract signal, not noise** — A 45-minute call should produce 10-25 extraction items, not 50. Only extract information that a downstream skill or the AE would need to reference later. Small talk, filler, and generic pleasantries are not extracted.

## Workflow

### Phase 1: Receive and Identify

When the user provides a transcript:

1. **Check for deep dive mode** — If the user is requesting a deep dive or re-process of an existing transcript, skip to the Deep Dive section below.

2. **Identify the account** — Look for company names, people names, or context clues in the transcript. If the account is not obvious, ask:
   > "What account/company is this call with?"

3. **Identify call metadata** — Determine from the transcript or ask:
   - **Date** of the call (default to today if not specified)
   - **Call type** — discovery, demo, negotiation, check-in, executive meeting, qbr, etc. Infer from content if not stated.
   - **Participants** — Names and roles of speakers. If the transcript uses "Speaker 1" / "Speaker 2", ask:
     > "I see unnamed speakers. Can you tell me who was on this call and their roles? (Or I can work with Speaker 1/Speaker 2 and you can fill in names later.)"

4. **Create the account slug** — Lowercase, hyphenated version of the company name (e.g., "acme-corp", "global-payments", "meltwater").

### Phase 2: Store Raw Transcript

1. Create the directory structure if it doesn't exist:
   ```
   ~/.claude/sales/<account-slug>/transcripts/
   ~/.claude/sales/<account-slug>/extractions/
   ```

2. Save the raw transcript exactly as provided:
   ```
   ~/.claude/sales/<account-slug>/transcripts/YYYY-MM-DD-<call-type>.md
   ```
   If a file with that name already exists (multiple calls on the same day of the same type), append a number: `YYYY-MM-DD-<call-type>-2.md`

### Phase 3: Extract

Read through the transcript and extract every meaningful piece of information. For each item, produce a structured block following this format:

```markdown
### [Brief descriptive title]
**Speaker:** [Name, Role — or "Speaker 1" if unknown]
**Source:** [buyer / seller]
**Topics:** [comma-separated tags from the starter set, plus organic tags if needed]

> "[Direct quote when available — the buyer's exact words]"

**Summary:** [Faithful summary of what was communicated. Use when no clean quote is available, or to add context around a quote. If a quote is provided and speaks for itself, this field can be omitted.]

**Why it matters:** [Brief note — is this new information? Does it contradict something from a prior call? Is it time-sensitive? Does it reveal something about a person, process, or priority?]

**Buyer language:** [Specific terminology, codenames, internal project names, or phrasing the buyer used that differs from standard/seller language. Omit if nothing notable.]
```

**Topic tag starter set:** `problem`, `stakeholder`, `timeline`, `commitment`, `criteria`, `competition`, `outcome`, `process`, `risk`, `context`

- Use multiple tags when an item spans topics (e.g., a stakeholder mention that also reveals a risk)
- Add organic tags when something doesn't fit the starter set — but keep them descriptive, not analytical
- Tags describe what *kind* of information this is, not what it *means* for the deal

**Extraction guidelines:**

- **Order items chronologically** — follow the conversation flow. This preserves context and makes it easy to trace back to the raw transcript.
- **Prioritize buyer statements over seller statements** — Extract what the buyer revealed. Only extract seller statements when they contain a commitment ("I'll send you the ROI model by Friday"), a teaching moment that landed ("Oh, I never thought of it that way"), or context needed to understand a buyer response.
- **Distinguish pain from injury** — When extracting problem statements, note whether the problem is localized pain (one person feels it, they've been living with it) or widespread injury (debilitating, multiple people affected, must be fixed). This is not a separate field — just include it naturally in the "Why it matters" note when relevant.
- **Capture commitment asymmetry** — When extracting commitments, note whether the commitment is from the buyer or the seller. Track both. A pattern where the seller has many commitments and the buyer has few is a signal of weak champion engagement.
- **Don't over-extract** — If the buyer says "yeah, that sounds right" in response to a summary, that's not a separate extraction item. It confirms the prior statement. If the buyer says "actually, it's more like $3M, not $2M" — that's a correction worth extracting.

### Phase 4: Write Extraction File

Assemble the extraction into a markdown file with YAML frontmatter:

```markdown
---
date: [YYYY-MM-DD]
account: [account-slug]
call_type: [discovery / demo / negotiation / check-in / executive / qbr / other]
participants:
  - name: [Name]
    role: [Role/Title]
    source: [buyer / seller]
  - name: [Name]
    role: [Role/Title]
    source: [buyer / seller]
duration: [estimated duration if known]
raw_transcript: ../transcripts/[matching filename]
---

# Extraction: [Account Name] — [Call Type], [Date]

## Items

[All extraction items, separated by ---]

## Feedback Summary

[See Phase 5]
```

Save to:
```
~/.claude/sales/<account-slug>/extractions/YYYY-MM-DD-<call-type>.md
```

### Phase 5: Feedback Summary

After writing the extraction file, generate a feedback summary. This is the thing that makes processing a transcript worth doing — it shows you what you captured and what to do next.

The feedback summary is:
- Written at the bottom of the extraction file (stored for later reference)
- AND output directly in the conversation (so the AE sees it immediately)

**Structure:**

**Captured:** [Total count] items — [X] with direct quotes, [Y] stakeholder mentions, [Z] commitments with deadlines, [notable counts of other types]

**Notable:**
- [Highlight anything new, changed, time-sensitive, or particularly significant]
- [If a specific dollar figure, date, or stakeholder name appeared for the first time, call it out]
- [If something contradicts prior information from this account, flag it]

**Gaps — questions for the next call:**
Compare what was extracted against this lightweight checklist. For any area with no extracted items, surface it as a question — not as criticism, but as a prompt for the next conversation:

- **Problems:** Did we surface specific problems with quantified impact? Or are we still at surface level?
- **Stakeholders:** Do we know who's involved in the decision? Who has power? Who might block?
- **Timeline:** Is there a specific date or event driving urgency? Or is timing vague?
- **Commitments:** Did both sides commit to next steps? Or is it one-sided?
- **Decision criteria:** Do we know what they need in a solution? What they're evaluating against?
- **Competition:** Are they looking at alternatives? Is the status quo the real competitor?
- **Budget:** Is there funding allocated, or does it need approval?
- **Outcomes:** Do we know what success looks like for them — both the company and the individual?

Only surface gaps that are reasonable to expect given the call context. A first discovery call won't have budget details — don't flag that. A third call with no stakeholder map beyond one contact — flag that.

**Suggested next actions:**
- [1-3 specific, actionable next steps based on what was extracted and what's missing]

### Phase 6: Confirm Completion

After outputting the feedback summary, confirm where files were saved:

> **Extraction complete.**
>
> Raw transcript: `~/.claude/sales/<account>/transcripts/<filename>`
> Extraction: `~/.claude/sales/<account>/extractions/<filename>`
>
> [Feedback summary output here]

---

## Deep Dive Mode

When the user requests a deeper analysis of a previously processed transcript ("deep dive on the Acme call", "re-process the last transcript", "I think we missed something in the discovery call"):

1. **Find the transcript** — Look in `~/.claude/sales/` for the matching account and call. If ambiguous, ask which call to re-process.

2. **Read both files** — Read the raw transcript AND the existing extraction to understand what was already captured.

3. **Re-extract with a deeper lens** — Process the raw transcript again, specifically looking for:
   - **Dropped threads** — Topics the buyer raised or hinted at that weren't followed up on
   - **Subtle signals** — Hesitation, qualifiers ("sort of", "I think", "probably"), or energy shifts that suggest something unsaid
   - **Implications not explored** — Statements that imply something important but weren't unpacked ("we tried something similar last year" — what happened?)
   - **Stakeholder clues** — Names, roles, or organizational dynamics mentioned in passing
   - **Contradictions within the call** — Moments where the buyer said one thing early and something different later
   - **Things the standard extraction correctly judged as noise, but that in aggregate might matter**

4. **Produce a supplementary extraction** — Don't replace the original. Save as:
   ```
   ~/.claude/sales/<account-slug>/extractions/YYYY-MM-DD-<call-type>-deep-dive.md
   ```

   Use the same item format. In the "Why it matters" field, note why the standard pass may have missed this — was it subtle, buried in a tangent, or only meaningful in context?

5. **Output a deep dive summary** — Compare the supplementary findings to the original extraction. Highlight what's new and why it matters.

---

## What This Skill Does NOT Do

- **No coaching** — This skill does not evaluate seller performance. A coaching skill can do its own pass on the raw transcript.
- **No framework scoring** — This skill does not score against MEDDPICC, SPICED, or any methodology. A scoring skill reads from these extractions.
- **No cross-transcript synthesis** — This skill processes one transcript at a time. Conflicts between transcripts are not resolved here.
- **No artifact generation** — This skill does not produce follow-up emails, business cases, or other deliverables. Those are separate skills that read from the extractions.
- **No external integrations** — Input is pasted text. No Gong API, no Salesforce sync.
