# Value Framework Lens (Force Management)

The value framework lens. Captures the substance of what the product DOES for buyers — problems solved, value chain per persona, three-tier differentiation. This pass owns the largest share of the artifact body and formalizes content the existing skill already produces (lightly), now grounded in framework standards.

## Owned Sections

This pass owns three sections of the artifact:

1. **Buyer's Problem Stack** (Section 4) — including the **"before" sub-field** that captures the displacement signal (what buyers were doing manually that got replaced). The displacement signal lives here, NOT in a separate JTBD lens.
2. **Per-persona value mappings inside the ICPs container** (Section 5 body) — for each persona × ICP combination: capability → operational change → metric that moves → dollar impact → how-to-say-it.
3. **Differentiation 3-tier** (Section 6) — defensible / comparative / assumed.

## What This Pass Extracts

### Buyer's Problem Stack (3-5 problems)

Each problem block:

- **Problem name** — in buyer language, not marketing language. From G2 reviews, customer testimonials, case-study language. *"Tellers panicking on Monday mornings"* beats *"Operational inefficiency in branch operations."*
- **Before** — what the buyer's world looked like without the product. The displacement signal. **Specific and named** when possible: "we used to run forecasts in spreadsheets that took 4 hours a week" beats "manual processes." Pulled verbatim from case-study before/after framing or G2 "switched from X" patterns.
- **Consequence** — what it costs them. In dollars / time / risk / opportunity terms. Quantify from case studies where possible.
- **Who feels it** — which persona/role owns this problem.

Rank the 3-5 problems by how often they appear in case studies and reviews as the primary buying reason.

### Per-persona value mappings (inside ICPs container)

Per persona × ICP block:

- **Capability** — what the product does for this persona
- **Operational improvement** — what changes in their day-to-day
- **Metric that moves** — which KPI improves (named, not "efficiency")
- **Dollar impact** — quantified from case studies (e.g., "customers report 30-40% reduction in X" with named customer + timeframe when available)
- **How to say it** — one sentence an AE could say in a conversation that connects the dots

Only include personas with evidence from case studies or reviews. Don't invent personas.

### Differentiation 3-tier

- **Defensible — only we can do this** — capabilities competitors *genuinely cannot* replicate. If the company doesn't have any, say so explicitly. Inflating comparative-into-defensible is a quality failure.
- **Comparative — we do this better** — capabilities where there's a meaningful advantage but competitors have some version. Include WHY the company is better (technical reason, vertical depth, etc.), not just the claim.
- **Assumed — table stakes** — capabilities everyone in the category has. Not differentiators. The AE needs to know they exist because buyers will ask.

Plus: **How buyers describe the difference** — pulled verbatim from reviews and case studies. Often more compelling than vendor's own differentiation claims.

## Quality Standards

- **Value chain traceability** — each persona block can be traced from capability → dollar impact without missing links. If "metric that moves" is unstated, the chain is broken.
- **Honest tiering** — defensible should be rare. If everything is "defensible," the tiering is wrong (re-tier or call out the gap honestly).
- **Specific "before" field** — "manual processes" / "old way of doing things" fails. "Spreadsheets + Slack DMs + 4-hour Friday forecasting calls" passes.
- **Buyer-language problem names** — passes the read-aloud test (would a buyer recognize this problem-name as their own?).
- **Quantification when available** — pull numbers from case studies. If unquantified, flag in the gap-probe.
- **Persona evidence** — every persona block must trace to a named customer / role in case studies, or to a G2 review segment.

## Prompt-Shaping Guidance

**Be told to:**
- Pull problem names verbatim from G2 / Gartner / TrustRadius reviews when possible
- Always populate the "before" field with the displacement signal — search case-study before/after framing, G2 "switched from" patterns
- For each persona, build the full value chain (don't skip metric-that-moves or how-to-say-it)
- Be honest about the differentiation tiering — defensible should be the smallest tier, not the largest
- Capture "how buyers describe the difference" verbatim — this is high-signal language

**Be told to NOT:**
- Use marketing-grade problem names ("digital transformation drag") when buyer-language exists ("we can't get reports out of the system without a database engineer")
- Inflate comparative-into-defensible to make positioning look stronger
- Invent personas without case-study or review evidence
- Output "manual processes" or "old way of doing things" as the "before" field — that's a quality fail
- Speculate on dollar impact when not present in case studies

## Example Output (excerpts)

### Buyer's Problem Stack — example problem block

```markdown
### Problem 2: Tellers panicking on Monday mornings (cross-department queue collision)

**Before:** Loan-servicing department has one phone number serving both external customers AND internal teller help-desk requests. On busy Monday mornings, tellers calling for help (e.g., a teller standing in front of a customer asking for a payoff amount) sit in queue behind external customers, panicking visibly while their face-to-face customer waits.

**Consequence:** Visible employee distress, customer-facing. Drives teller turnover (anecdotal), drags branch CSAT, creates "I had to wait while my teller was on the phone" customer complaints. Quantified at one community bank: 8-10K calls/month in CEC + 5-8K each across 3 other departments, with sub-minute resolution on internal asks blocked by 5+ minute wait times.

**Who feels it:** Day-to-day call center managers (Jen Welch types); branch managers; ultimately customers face-to-face with stressed tellers.
```

### Per-persona value mapping — example persona block

```markdown
### Persona: Director, Customer Engagement Center (community-bank ICP)

**Capability:** Cross-department queue routing with internal-vs-external call distinction at the platform level; AI heads-up panel surfaces prior call context before agent picks up.

**Operational improvement:** Tellers calling internally route to a separate queue from external customers; reps see prior-context summary, eliminating "let me look this up" delays; internal routing reportable like external (current Teams-based internal-help has zero visibility).

**Metric that moves:** Internal-help-desk average response time; teller-initiated internal-call abandonment rate; CSAT on customer-facing-while-teller-blocked interactions.

**Dollar impact:** [Calibrate from real case studies. Placeholder for the lens; the synthesis prompt should populate from research, e.g., "X bank reduced average internal-help response from 4 minutes to <60 seconds, freeing roughly 200 hours/month of teller productivity."]

**How to say it:** "Your tellers stop panicking in the queue because internal asks route separately and your reps see the call's context before they pick up — that 'standing in front of a customer waiting for help' moment goes away."
```

### Differentiation — example tier

```markdown
## Differentiation

### Defensible — Only We Can Do This
- **Deterministic non-generative virtual agent for customer-facing interactions** — guaranteed-no-hallucination contractual obligation. Competitors using generative-AI VAs cannot offer this guarantee architecturally; it's not a tuning problem, it's a model-class difference.

### Comparative — We Do This Better
- **FI-vertical depth (Fiserv / FIS / Jack Henry core integrations as first-class)** — competitors offer integrations but require custom work; FI-vertical-only-customer focus drives our integrations to be deeper and pre-tested. Why we're better: we don't sell to non-FIs, so all our integration investment goes into this stack.
- **No-per-license pricing model** — competitors typically charge per-seat, creating expansion friction. Our flat platform pricing makes multi-department rollouts predictable. Why we're better: pricing-model choice, not technical superiority.

### Assumed — Table Stakes
- Voice + digital channels integrated
- Co-browse / screen-share for digital sessions
- Contact-center reporting + dashboards
- CRM integrations (Salesforce, etc.)

### How Buyers Describe the Difference
- "I don't have to worry about hallucinations because [their virtual agent] just literally can't" (G2 review, community bank)
- "Pricing was the surprise — we'd been bracing for the per-license hit and it just wasn't there" (case study quote, mid-size CU)
```

## Pass-Failure Handling

If research is too thin to populate a section:
- Output partial content with `[insufficient public data]` markers in unfilled fields
- Add gap-probes for each missing area:
  - **Problem stack thin:** *"Buyer's Problem Stack could not be reliably populated from public sources. Recommended: list the 3-5 problems your product solves, in buyer language, with what they were doing before."*
  - **Per-persona value chain incomplete:** *"For [persona], the capability-to-dollar value chain is missing [specific link]. Recommended: provide what changes operationally, what metric moves, and rough dollar impact from a real customer."*
  - **Differentiation unclear:** *"Defensible vs. comparative differentiation could not be cleanly tiered from public sources. Recommended: name the 1-2 capabilities competitors literally cannot replicate (defensible) vs. capabilities you do better but they have versions of (comparative)."*
