# project-scaffolder

An open-source Claude Code skill that turns a rough idea and a pile of scattered source material into an **execution-ready project package** — interactively, with a human review gate at every stage.

It is a *prompt-engineering artifact*: a carefully structured system prompt for an agent that scaffolds projects the way an experienced program manager would — by asking the right questions in the right order, ingesting only what matters, reflecting understanding back before acting, and generating deliverables one at a time so a human can steer cheaply.

## What it does

The workflow is **intake → ingest → synthesize → generate**:

1. **Intake** — a structured interview establishes the project's identity: name, *type* (feature build / assessment / deprecation / infrastructure), one-line vision, target date, parent program. The type drives every downstream template so a deprecation is never forced into a feature-build shape.
2. **Ingest** — the agent reads what you point it at (tickets, docs, wiki pages, doc folders) in full, then runs a **time-boxed discovery sweep** across your doc store and tracker. Discovered-but-unread sources are triaged (title + first ~500 chars) and presented as a ranked list — *you* choose what gets a deep read. Nothing is silently dropped or silently over-read.
3. **Synthesize** — everything collapses into a `problem-space.md`: architecture, decisions already made (with links), open questions, risk surface, prior art, and a source index that distinguishes read from discovered.
4. **Generate** — a full project folder: charter page, area summary, resource/capacity model, work-breakdown import template, risk register, agent-team spec, and a kickoff prompt. Every artifact is reviewed **one at a time**.

## Architecture note

The skill is **tool-agnostic by design**. It assumes three capabilities and lets you wire each to whatever your team uses:

| Capability | You provide | Examples |
|------------|-------------|----------|
| Issue / ticket tracker | ingest + bulk-create | Jira, Linear, GitHub Issues |
| Wiki / doc store | ingest + publish | Confluence, Notion, a Markdown repo |
| Doc search | the discovery sweep | full-text search over the above |

The prompt is the product. There is no runtime, no dependency to install — the "code" is the structured instruction set in [`skill/SKILL.md`](skill/SKILL.md), which a Claude Code agent (or any capable agentic LLM harness) executes. The design encodes hard-won behavioral guardrails as **Core Principles** at the top of the prompt (never dump everything at once; never assume a rate; time-box ingestion; be explicit about real vs. drafted side effects; type-aware templates; persist progress). Those principles exist because early real-world use surfaced exactly those failure modes — see [`EVAL.md`](EVAL.md).

## Install / run

This is a [Claude Code](https://claude.com/claude-code) skill.

1. Copy the skill into your Claude Code skills directory:
   ```bash
   mkdir -p ~/.claude/skills/scaffold-project
   cp skill/SKILL.md ~/.claude/skills/scaffold-project/SKILL.md
   ```
2. Invoke it from a Claude Code session:
   ```
   /scaffold-project
   /scaffold-project for a new data-platform assessment
   /scaffold-project and ingest these tickets: PROJ-1024 PROJ-1099
   ```

## Adapt it to your stack

The skill references a generic "issue tracker," "wiki," and "doc search." To make it yours:

- **Wire the tools.** Point the ingestion/publish steps at your MCP servers or CLIs (e.g. a Jira MCP, a Confluence/Notion MCP, GitHub CLI). Substitute those tool names in Phases 2, 3, and 6.
- **Set your defaults.** Capacity rate, enablement thresholds, draft space, and milestone source are all *confirmed, not assumed* — edit the "Defaults Applied" section to match your team's conventions.
- **Trim the template.** The area-summary page is opinionated. Keep the columns your stakeholders read; drop the rest.

See [`examples/`](examples/) for a fully fictional end-to-end run: a sample intake and the generated charter output.

## Files

```
project-scaffolder/
├── README.md
├── EVAL.md                         # eval rubric: grading scaffold output good / acceptable / wrong
├── LICENSE                         # MIT
├── skill/
│   └── SKILL.md                    # the core artifact — the scaffolding agent prompt
├── examples/
│   ├── sample-intake.md            # a fictional Phase 1-4 intake transcript
│   └── sample-output-charter.md    # the charter the agent would generate from it
└── templates/
    └── work-breakdown.csv          # the WBS import template shape
```

## License

MIT — see [`LICENSE`](LICENSE). Author: Sally Kellaway.
