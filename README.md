# Sales Skills

AI agent skills for enterprise and mid-market B2B sales. Works with Claude Code, Codex, Cursor, Windsurf, and any agent that supports the [Agent Skills spec](https://agentskills.io).

Built for AEs who actually sell -- not growth marketing, not SEO, not content. Discovery, deal management, account research, and the compounding knowledge layer that makes every call better than the last.

## Skills

| Skill | What it does |
|-------|-------------|
| **sales-extractor** | Paste a call transcript, demo, or internal deal chat. Auto-detects mode and runs five framework-grounded passes (Surface, Discovery-Depth, Forwarding-Test, Critique-simplified, Operational). Produces typed items with auditable provenance — buyer quotes, structured commitments, stakeholder mentions, framework tags, and a Framework Notes block per pass. **The foundational skill — everything else reads from these.** |
| **sales-account-research** | Build an account dossier from public sources -- news, earnings, job postings, executive signals. External research layer. |
| **sales-company-profile** | One-time setup. Generates a value framework for YOUR company -- problems you solve, differentiation, proof points, trigger events. |
| **sales-deal-review** | Score deal health across 6 evidence dimensions using actual buyer quotes from extractions. Honest assessment, not happy ears. |
| **sales-call-prep** | Generate a call plan with specific, ready-to-ask discovery questions for an upcoming conversation. Pulls from extractions, dossier, and company profile. |

## Install

Copy skills to your agent's skill directory:

```bash
# Clone
git clone https://github.com/kylelegare/sales_skills.git
cd sales_skills

# Copy to agent skills directory
cp -r skills/* ~/.agents/skills/

# Or for Claude Code specifically
cp -r skills/* ~/.claude/skills/
```

## How it works

Skills build a compounding knowledge layer at `~/.agents/sales/`:

```
~/.agents/sales/
  company-profile.md              # Your value framework (one-time setup)
  acme-corp/
    dossier.md                    # External research
    transcripts/
      2026-04-05-discovery.md     # Raw transcript (preserved as-is)
    extractions/
      2026-04-05-discovery.md     # Structured extraction
  global-payments/
    dossier.md
    transcripts/...
    extractions/...
```

Every call you process adds to the knowledge base. The deal review reads across all extractions for an account. Call prep (coming soon) pulls from extractions + dossier + company profile to generate questions. The data compounds.

## Methodology

Grounded in established B2B sales practice, not AI slop. The extractor's framework-grounded passes apply techniques widely used in modern complex/strategic sales:

- **Discovery depth** -- four-phase chain (business problem → cause analysis → negative impact → future state), three-bucket classification (problem / root-cause / desired-solution), specificity rating (problem descriptions unique enough that you can't swap account names without breaking the description)
- **Forwarding test** -- buyer language preserved verbatim, soundbite extraction in the "because of / now's the time / so that / instead of" structure, cost-of-inaction language, identification of the metric-that-matters
- **Critique (simplified for v1)** -- gaps for stage (what should have surfaced and didn't), glossed-over moments (where the buyer hinted and steered away), hypotheses worth probing on the next call
- **Operational signals** -- stakeholder mentions, commitments with owners and timeframes, trigger events, competitor mentions, happy-ears flags (seller-interpreted enthusiasm not backed by buyer commitment), commitment asymmetry, risk signals
- **Differentiation** -- three-tier classification (defensible / comparative / assumed) for value-framework assertions
- **Qualification overlays** -- MEDDPICC, SPICED, Gap Selling, Challenger views are not imposed on extractions, but the per-item structure supports any of them as a downstream lens

## Status

Early. Built for one AE (Kyle), being tested on real deals. Skills will evolve based on actual use.

## License

MIT
