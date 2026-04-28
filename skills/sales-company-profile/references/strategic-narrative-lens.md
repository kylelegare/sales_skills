# Strategic Narrative Lens (Andy Raskin)

The strategic narrative lens. Captures the long-form "why this matters now" story for higher-ticket strategic sales. Output is structured into 4 explicit sub-fields, NOT flowing prose. This pass owns Section 3 of the artifact and contributes to Soundbite assembly during composition.

## Owned Section

The Strategic Narrative section (Section 3) is structured as 4 sub-fields, each 1-3 sentences:

1. **World is changing because** — what's different now in the market, regulation, technology, or buyer behavior
2. **Change-the-game move buyers must make** — what new approach or capability is required to win in the changed world
3. **Promised land buyers are reaching for** — the desired future state buyers are heading toward (the value they're chasing, not the product they're buying)
4. **Cost of staying still** — what happens to buyers who don't adapt; the strategic-level cost of inaction

This is structured output, not flowing prose. Each sub-field is independently consumable by downstream tactical skills (e.g., a multi-thread email skill might pull just the "cost of staying still" as a closing line).

## Source Material

Strategic narrative content is the hardest to derive from public sources. Primary sources:

- **CEO / founder interviews** (recent, within 12 months) — extract market-direction claims and "what we're really fighting against" language
- **Exec podcast appearances** — CEO/founder talking about the market, not just the product
- **Conference keynotes** — strategic narrative arcs delivered to industry audiences
- **Recent funding announcement narratives** — "why we raised, what's next" framing
- **Annual investor letters / 10-K MD&A** (if public) — Management Discussion & Analysis is often the most honest strategic narrative
- **Founder LinkedIn posts** about market direction (filter for substance)
- **Recent product-launch press releases** that frame the "why now"

## What This Pass Extracts

### World-changing (sub-field 1)

What's different in the buyer's world that creates urgency or opportunity?

Look for:
- Regulatory events (new laws, compliance windows, deprecations)
- Technology shifts (new capabilities, deprecation of old approaches)
- Market structure changes (consolidation, new entrants, customer-behavior shifts)
- Macro pressure (cost pressure, talent shortage, demographic shifts)

Output: 1-3 sentences naming the specific change. Avoid generic "AI is changing everything" framing — must be company-specific or category-specific.

### Change-the-game move buyers must make (sub-field 2)

Given the changed world, what new approach or capability do buyers need?

Look for:
- The strategic shift CEO/founder argues for
- The "if you keep doing it the old way, you'll lose" framing
- The specific behavioral or organizational change required

Output: 1-3 sentences naming the required move.

### Promised land (sub-field 3)

What's the future state buyers are reaching for? The value they're chasing — NOT the product they're buying.

The key constraint here: promised land is **buyer-relevant outcome**, not vendor-self-serving framing. "Buyers reach a state where they..." beats "Buyers buy our product to..."

Look for:
- Customer aspiration language in case studies
- Mission-statement substance (when it's substantive, not boilerplate)
- "Where the industry is heading" claims from CEO/founder

Output: 1-3 sentences naming the future state in buyer-outcome terms.

### Cost of staying still (sub-field 4)

What happens to buyers who don't adapt? Strategic-level cost of inaction — bigger than tactical pain.

Look for:
- Competitive-pressure framing (laggards lose to adapters)
- Cost-of-inaction language in case studies (often quantified)
- Risk language (regulatory, reputational, talent-flight)

Output: 1-3 sentences naming the cost. Quantify when public sources allow.

## Quality Standards

- **Specificity test (per sub-field):** does each sub-field survive without the company name? If you transposed it onto a different company in a different category, would it still read as their narrative? **If yes, the sub-field is too generic.** Real strategic narrative is company-specific or category-specific, not industry-trend-piece content.

- **Buyer-relevant promised land:** sub-field 3 ("promised land") MUST be in buyer-outcome terms, not vendor-self-serving terms. "Buyers reach a future where their customers feel taken care of without leaning on retiring branch staff" passes. "Buyers use our AI-powered platform to drive engagement" fails.

- **Structured, not prose:** the 4 sub-fields stand alone. Don't blur them into a paragraph; downstream skills consume them as separate fields.

- **CEO/founder-language ground:** prefer language pulled from CEO/founder primary sources over inferred-from-product-pages. Marketing-page strategic narratives are often hollower than what the CEO actually says in a podcast interview.

- **Honest about thinness:** if public sources don't support a real strategic narrative, do NOT fabricate. Use the thin-source fallback (below).

## Thin-Source Fallback

Strategic narrative is the section most likely to fail on public-source-only research, especially for:
- Early-stage / Series A companies (no exec podcast tour yet)
- Private companies that don't publish investor letters
- Quiet companies whose CEOs don't do interviews
- Companies where founders post mostly product-launch noise on LinkedIn

When sources are genuinely thin:

1. **Attempt to construct from what IS available** — mission statement, blog posts about market direction, product release narratives, founder LinkedIn posts (filtered for substance)

2. **If still thin, output 4 sub-field stubs marked `[insufficient public data]`** rather than fabricating narrative:

```markdown
## Strategic Narrative

**World is changing because:** [insufficient public data — see Honest Gaps]

**Change-the-game move buyers must make:** [insufficient public data — see Honest Gaps]

**Promised land buyers are reaching for:** [insufficient public data — see Honest Gaps]

**Cost of staying still:** [insufficient public data — see Honest Gaps]
```

3. **Add a high-severity gap-probe** to the Honest Gaps section:
   ```yaml
   - gap: "Strategic narrative cannot be reliably derived from public sources for this company"
     framework_area: strategic-narrative
     research_says: "Limited CEO interviews, no recent investor letters, blog content is primarily product-feature-focused. Public corpus does not articulate a strategic-level world-changing thesis."
     probe: "Provide 2-3 paragraphs explaining how your market is changing, what new approach buyers need to take, where buyers are heading, and what staying still costs them."
     severity: high
   ```

This is how the lens stays honest when public sources don't deliver.

## Prompt-Shaping Guidance

**Be told to:**
- Output 4 sub-fields explicitly, each 1-3 sentences, with the named labels (world-changing / change-the-game / promised land / cost of staying still)
- Ground each sub-field in CEO/founder language when available (cite the source in a comment if useful for traceability)
- Make each sub-field specific enough to fail the no-company-name transposition test
- For promised land: phrase in buyer-outcome terms, not vendor-product terms
- If sources are thin, use the fallback format with `[insufficient public data]` markers + high-severity gap-probe — do NOT fabricate

**Be told to NOT:**
- Output flowing prose — the 4 sub-fields are structured
- Include generic trend-piece content ("AI is transforming everything") without company-specific framing
- Use vendor-self-serving promised-land language ("buyers buy our platform to...")
- Speculate beyond research; thin-source fallback is preferable to invention
- Pad the section to look substantive when sources don't support depth

## Example Output (well-grounded company)

```markdown
## Strategic Narrative

**World is changing because:** Community banks and credit unions are facing a generational transition — long-tenured branch staff are retiring, customer expectations have been reset by mobile-first banks like Chime and SoFi, and traditional FIs cannot hire their way to relationship-banking depth at scale. Customer engagement that used to live in the branch now has to live in the contact center and digital channels — but the platforms most FIs run on were designed for transaction-handling, not relationship-handling.

**Change-the-game move buyers must make:** Treat the contact center and online-banking session as the new branch. That means consolidating fragmented vendor stacks into a single platform that handles voice, digital, and AI together; building AI experiences customers actually trust (not generative chatbots that hallucinate); and giving the contact-center team the tooling to handle relationship-banking work at scale.

**Promised land buyers are reaching for:** A future where their members get the same relationship feel — the one that has historically required walking into a branch and being recognized — through any channel, at any time, without that experience depending on whether a senior branch employee is still on staff. The community-bank brand promise survives the demographic transition.

**Cost of staying still:** Lose the demographic war. Younger customers default to digital-first competitors who solve their problems in 90 seconds. Older customers feel abandoned when their longtime branch teller retires and the replacement is a generic call-center experience. Within 5-10 years, the FI's relationship-banking advantage — its core moat — erodes faster than its asset base.
```

(Worked example uses Glia. The lens applies universally.)

## Example Output (thin-source company)

```markdown
## Strategic Narrative

**World is changing because:** [insufficient public data — see Honest Gaps]

**Change-the-game move buyers must make:** [insufficient public data — see Honest Gaps]

**Promised land buyers are reaching for:** [insufficient public data — see Honest Gaps]

**Cost of staying still:** [insufficient public data — see Honest Gaps]
```

(Honest, not fabricated. The high-severity gap-probe in the Honest Gaps section gives the user a path to fill these in.)
