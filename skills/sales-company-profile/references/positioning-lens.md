# Positioning Lens (Dunford)

The positioning lens. Establishes WHERE the product sits before talking about value: category, market segment, competitive alternatives, unique attributes, and the translation from attributes to buyer-relevant value.

This pass owns the **Positioning section** (Section 1 of the artifact). Runs first in the synthesis phase because positioning frames everything that follows — problem stack, value chain, differentiation all depend on knowing what category the product is in and who it competes against.

## Source Material

Draws primarily from:
- **Comparison / "vs" pages** on the company website (the company's own framing of who they compete against)
- **Analyst categorization** (Gartner Magic Quadrant, Forrester Wave, IDC MarketScape references)
- **G2 / Gartner / TrustRadius reviews** — how buyers categorize the product (often differs from marketing's category)
- **Homepage hero + About page** — the company's stated positioning category
- **Customers/logos page** — segment patterns visible in who buys

## What This Pass Extracts

For the Positioning section, produces:

1. **Category** — how buyers categorize the product. Not the marketing-preferred category; the buyer-perspective category. *"What product category would a buyer Google-search to find this?"*

2. **Market segments served** — specific segments within the category. Names + why each segment specifically. (For Glia: "community banks under $5B" + "credit unions $500M-$5B" — distinct segments with shared characteristics.)

3. **Competitive alternatives** — what buyers are actually choosing between. NAMED specifically, not "incumbents" or "legacy solutions." Includes:
   - Direct competitors (named products)
   - Indirect competitors (different category but solving same problem)
   - Status quo / DIY (when buyers might just keep doing it manually or build internally)

4. **Unique attributes** — what makes this product a contender in the category. Attributes are *what the product is or does that competitors can't or don't*. Not vendor-claimed superlatives ("best AI"); concrete attributes (e.g., "deterministic / non-generative virtual agent for customer-facing interactions").

5. **Attribute-to-value translation** — for each unique attribute, the chain: attribute → operational value to buyer → business outcome they care about. This is the bridge from "what the product is" to "why a buyer cares."

## Quality Standards

A good positioning section passes these tests:

- **Buyer-categorization test** — could a buyer place this product in the named category from the doc alone? If the category is jargon ("AI-powered intelligent customer engagement orchestration platform"), the test fails.

- **Named-alternatives test** — are competitive alternatives NAMED specifically? Generic ("legacy solutions", "incumbents", "the status quo") fails. Specific (TalkDesk, Genesys, Five9, NICE) passes.

- **Specificity test** — could the section text be transposed onto a different company's positioning doc? If yes, it's too generic. Positioning should be specific enough that swapping the company name breaks the section.

- **Attribute-value linkage** — for each unique attribute, is the value translation present and buyer-relevant? "Best-in-class AI" without explaining how that translates to outcomes the buyer cares about fails.

- **Honest about category contention** — if the company is genuinely a category contender (not the leader), say so. "We're in the X category, where [leader] dominates and we differentiate via [angle]" is more honest than positioning the product as a category-creator when it isn't.

## Prompt-Shaping Guidance

When constructing the synthesis prompt for this pass:

**Be told to:**
- Read research corpus with category-and-alternatives focus
- Surface the buyer-perspective category, not the marketing-preferred one
- Name competitive alternatives specifically; if the research surfaces "we replaced manual spreadsheets" as the alternative, name "spreadsheets" (not "incumbents")
- For each unique attribute, articulate the value translation explicitly: attribute → operational change → business outcome
- Flag if the company's own claimed category differs materially from the buyer-categorization (e.g., marketing says "platform" but reviews say "phone system replacement") — both go in the doc, with the discrepancy noted

**Be told to NOT:**
- Use marketing-grade category names if buyer reviews use a different framing
- Output vendor-claim superlatives ("best", "leading", "industry-defining") without buyer-evidence
- List capabilities under "unique attributes" — attributes are *what makes the product a contender in the category*, not "things the product does"
- Synthesize a positioning that contradicts the company's actual stated positioning without flagging the contradiction
- Speculate beyond research (if the category isn't clear from sources, output `[insufficient public data]` and add a high-severity gap-probe)

## Example Output

```markdown
## Positioning

**Category:** Customer engagement platform for community banks and credit unions (the buyer-perspective category — analysts place this under "Banking-specific CCaaS" / "FI-focused customer engagement").

**Market segments served:**
- **Community banks** ($1B-$10B asset range) — buyers value FI-vertical product depth and the no-per-license pricing model that lets them expand to multiple departments without nickel-and-diming.
- **Credit unions** ($500M-$5B asset range) — same value drivers; "member" terminology and FI-vertical compliance posture matter especially in CU language.

**Competitive alternatives** (what buyers actually evaluate against):
- **TalkDesk** — generic CCaaS; FI-vertical depth is shallow; pricing is per-license which adds friction for multi-department deployments.
- **Genesys** — enterprise-grade CCaaS; powerful but heavy; common incumbent in community-bank stacks where it's been hard to displace.
- **NICE CXone** — large-enterprise CCaaS; over-built for community-bank scale.
- **Status quo (Microsoft Teams + manual processes)** — many community banks use Teams as their backbone with no contact-center platform, leaving call routing and reporting as manual work.

**Unique attributes** (what makes the product a contender in this category):
- **Deterministic, non-generative virtual agent for customer-facing interactions** — guaranteed-no-hallucination behavior that competitors using generative-AI VAs cannot offer.
- **No-per-license pricing model** — flat platform pricing, no nickel-and-diming on expansion to additional departments or use cases.
- **FI-vertical depth** — banking-specific knowledge bases, FIS / Jack Henry / Fiserv core integrations as first-class concerns, not afterthoughts.

**Attribute-to-value translation:**
- *Deterministic virtual agent* → no hallucination risk in customer-facing AI → preserves community bank's reputation and compliance posture (the load-bearing concern for CCO and risk committee approval).
- *No-per-license pricing* → predictable cost across multi-department expansion → CFO-friendly, no surprise renewal hikes.
- *FI-vertical depth* → less custom integration work for IT → faster time-to-value, less drag on CIO's roadmap.
```

(Example uses Glia as the worked case; the lens applies universally to any company.)

## Pass-Failure Handling

If research can't determine the category, alternatives, or attributes (rare for any company with a public website):
- Output what IS knowable
- Mark unknown sub-fields with `[insufficient public data]`
- Add a high-severity gap-probe to the Honest Gaps section: *"Positioning category and competitive alternatives could not be reliably determined from public sources. Recommended action: name the 2-3 categories buyers consider you in, and the 3-5 alternatives they actually evaluate against."*
