# Sales Skills

AI agent skills for enterprise and mid-market B2B sales. Works with Claude Code, Codex, Cursor, Windsurf, and any agent that supports the [Agent Skills spec](https://agentskills.io).

Built for AEs who actually sell -- not growth marketing, not SEO, not content. Discovery, deal management, account research, and the compounding knowledge layer that makes every call better than the last.

## Skills

| Skill | What it does |
|-------|-------------|
| **sales-extractor** | Paste a call transcript, demo, or internal deal chat. Auto-detects mode and runs five framework-grounded passes (Surface, Orlob, Nasralla, Bredvick-simplified, Operational). Produces typed items with auditable provenance — buyer quotes, structured commitments, stakeholder mentions, framework tags, and a Framework Notes block per pass. **The foundational skill — everything else reads from these.** |
| **sales-account-research** | Build an account dossier from public sources -- news, earnings, job postings, executive signals. External research layer. |
| **sales-company-profile** | One-time setup. Generates a value framework for YOUR company -- problems you solve, differentiation, proof points, trigger events. |
| **sales-deal-review** | Score deal health across 6 evidence dimensions using actual buyer quotes from extractions. Honest assessment, not happy ears. |
| **sales-transcript-extractor** *(DEPRECATED 2026-04-26)* | Single-pass predecessor to `sales-extractor`. Kept in place during the v1 soak window for fallback only. Will be removed once `sales-extractor` is proven on real deals. **Do not use for new work.** |

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

Grounded in real sales frameworks, not AI slop:

- **Orlob / pclub** -- Discovery depth chain, No Logo Test, cause analysis, negative impact, closing motion
- **Nasralla / Brief & Brilliant** -- Buyer language, chiseling process, business case architecture, soundbites
- **Force Management** -- 3-tier differentiation (defensible / comparative / assumed), value framework
- **MEDDPICC, SPICED, Gap Selling, Challenger** -- Not imposed on extractions, but available as overlay views in deal review

## Status

Early. Built for one AE (Kyle), being tested on real deals. Skills will evolve based on actual use.

## License

MIT
