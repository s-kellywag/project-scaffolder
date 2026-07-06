---
name: scaffold-project
description: "Scaffolds a new project or initiative from ideation to execution-ready. Gathers requirements interactively, ingests source materials (wiki pages, docs, issue tracker), synthesizes the problem space, and generates a full project folder with a draft charter page, resource plan, work breakdown, risk register, agent team spec, and kickoff prompt. Walks the owner through structured review at each stage."
user-invocable: true
---

# scaffold-project

A general-purpose project scaffolding agent. Turns a rough idea and a pile of scattered source material into an execution-ready project package — interactively, with a human review gate at every stage.

The workflow is **intake → ingest → synthesize → generate**. It is deliberately conversational: the agent never dumps a finished plan on you. It asks structured questions, ingests only what you point it at (plus a time-boxed discovery sweep), reflects back what it understood, and generates artifacts one at a time so you can correct course cheaply.

## When to Use

- Standing up a new project, workstream, or initiative
- Taking ownership of an unfamiliar area and needing to map it fast
- Preparing a planning review or a stakeholder-facing charter
- Building a project handoff package for a team (human or agent)

## Prerequisites

The skill is tool-agnostic. It expects three capabilities, which you wire to whatever your team uses:

| Capability | Examples | Used for |
|------------|----------|----------|
| **Issue / ticket tracker** | Jira, Linear, GitHub Issues, an internal tracker | Ingesting existing work items, generating a work-breakdown import |
| **Wiki / doc store** | Confluence, Notion, a shared drive, Markdown repo | Ingesting source docs, publishing a draft charter page |
| **Doc search** | Full-text search over the above | The time-boxed discovery sweep in Phase 2 |

Adapt the tool references in the phases below to your stack. Where the skill says "issue tracker," substitute your tool; where it says "wiki," substitute yours.

## Core Principles (read before running)

1. **Never dump everything at once.** Every review point is a single artifact, presented alone, with a specific question. Iterate one at a time.
2. **Never assume a rate or a number.** Capacity multipliers, team velocity, pass thresholds — if you don't know it, *ask*, and offer a clearly-labeled default the user can accept or override. Do not silently bake in a guess.
3. **Time-box ingestion.** Read what the user points at in full; discover the rest, but triage before deep-reading. Present a ranked list and let the user choose.
4. **Be explicit about what is real vs. drafted.** A generated import template is not created work items. A draft page is not a published page. Always ask "publish now?" / "create these now?" as a separate, explicit step — never leave ambiguity about side effects.
5. **Type-aware templates.** A deprecation is not a feature build. Pick the project type early (Phase 1) and adapt every downstream template to it. Do not force everything into a feature-build shape.
6. **Persist progress.** Save synthesized state to disk as you go so a long session can pause and resume without loss.

## Workflow

Seven phases, run interactively. Each has review prompts where the owner gives feedback before you proceed. **Do not rush. Pause for feedback at every review point.**

### Phase 1: Core Identity

Ask the user (use structured multiple-choice prompts where possible):

- **Project name** — what are we calling this?
- **Project type** — this controls which template sections are included downstream:
  - **Feature Build:** full plan — build-complete / ship dates, feature flags, enablement criteria, quality gates.
  - **Assessment:** no code dates, no feature flags. Milestone = a delivered document or decision. Focus on findings.
  - **Deprecation:** inventory, dependency mapping, migration plan, sunset criteria. No feature flags or enablement.
  - **Infrastructure:** technical milestones, integration points, performance targets.
- **One-line vision** — what is this, in plain user-oriented terms?
- **Target release / deadline** — a date, a release train, or "TBD."
- **Parent program** — the larger effort this rolls up to, or "standalone."
- **Where should the draft charter page live?** — the wiki space/section (default: a personal draft space).

### Phase 2: Problem Space Ingestion

Ask the user:
- "Point me at the problem" — issue/ticket IDs, doc links, doc-folder links, wiki page links, chat threads to paste.
- Any wiki spaces to scout (ask for the defaults your team lives in).
- Any keywords to search beyond the project name.

Then run discovery in **two tiers**:

**Tier 1 — Full read (always):**
1. Fetch every user-provided source in full — docs, wiki pages, tickets.
2. For a doc link that could be a doc *or* a folder: try fetching it as a document first; if that fails, try listing it as a folder. Handle both gracefully — **never error out and stop.**
3. For a folder: enumerate contents, then fetch the **10 most recent** documents in full (time-box).
4. For wiki pages: fetch content and enumerate child pages.
5. For tickets: fetch full detail (description, relationships, status). Traverse parent/child hierarchies.

**Tier 2 — Discovery + triage (time-boxed):**
1. Search the doc store and tracker for: project name, feature keywords, component names, owner names.
2. For each discovered source **not** already in Tier 1: read only the **first ~500 characters** (enough for title + intro).
3. Present a ranked list with one-line summaries: "I found N additional sources. Here are the most relevant. Which should I read in full?"
4. Fetch the ones the user selects.
5. Index the rest as **"discovered, not read"** in the source table — available for later deep-read. Do not silently drop them.

**Synthesis** — compile everything into `docs/problem-space.md`:
- Architecture and key technical concepts
- Decisions already made (with source links)
- Open questions and unresolved discussions
- Risk surface (from ticket states, doc staleness, chat threads)
- Existing instrumentation / telemetry (if applicable)
- Related prior art
- Source document index (read + discovered-but-not-read)

**Review Prompt (wait for a response):**
"Here's what I found and synthesized from N docs, M wiki pages, and K tickets. Is this accurate? What's missing? Any additional sources I should check, or anyone I should search for by name?"

### Phase 3: People & Resources

Ask the user:
- **Leadership block:** Sponsor, Engineering lead, Project owner (default: user), QA lead, other stakeholders — all may be TBD.
- **Components / areas:** which systems, repos, or tracker components are involved?
- **Team size:** "How many people does the lead have available for this?"
- **Capacity rate:** "Do you have an effective-capacity estimate per person per week — accounting for meetings, reviews, and interrupts? Teams often use a discounted figure (a common baseline is ~60% of nominal, i.e. ~3 effective days per person-week), but this varies a lot. Do you have a number, or should I use that baseline as a clearly-labeled default?"

> This is a **conversational** ask, not a silent assumption. Offer the default, name it as a default, and let the user override. (See Core Principle 2.)

Then:
- Look up component/area IDs from your reference data.
- Calculate a **preliminary** capacity figure: team_size × capacity_rate × estimated_weeks. Label it preliminary.

### Phase 4: Work Breakdown & Planning

Ask the user:
- "What are the 3-5 big things this project delivers?" — or derive candidates from Phase 2 synthesis and propose them.
- For each: Essential or Candidate? Which platforms/surfaces?
- Rough sizing if known, or a placeholder for later refinement with the team.
- **Feature Build only:** feature-flag needs, enablement criteria (default, clearly labeled: a pass threshold you confirm with the user — do not hardcode a number).
- Known cross-team dependencies — which external teams?
- Any scope carried over from a prior release?
- **Explicitly ask:** "What should be merged or restructured here? Are any of these actually sub-items of another?"

Then:
- Generate a work-item breakdown from the feature list (to be refined with the team).
- Run a capacity check: total estimated effort vs. team capacity; flag deficit or surplus.
- Compile the cross-team dependency list.

**Review Prompt:** present the draft breakdown and get feedback. Revise before proceeding.

### Phase 5: Timeline & Milestones

Automatically:
1. Look up the current phase / date landmarks for the target release from your schedule source.
2. Calculate: days until next planning checkpoint, days until key milestones.
3. Map work items to milestones (type-appropriate: ship dates for Feature Build; deliverable dates for Assessment/Deprecation).
4. Optionally create reminders for planning-materials-due, status-due, milestone boundaries, and dependency follow-ups.

**Review Prompt:** present the timeline and ask: "Any dates wrong? Here's the timeline-pressure read: [assessment]."

### Phase 6: Generate Scaffold

Create the project folder. **Adapt structure to the project type from Phase 1.**

```
{project-name}/
├── README.md                      # Project instructions and context
├── KICKOFF_PROMPT.md              # Lead-agent activation prompt
├── brief/
│   └── project-brief.md           # Scope, constraints, people, deliverables, critical path, success criteria
├── plans/
│   ├── project-plan.md            # Workstreams, timeline, milestones
│   ├── resource-plan.md           # Capacity model, team + individual capacity, sequencing
│   ├── feature-flag-plan.md       # (Feature Build only)
│   └── reporting-plan.md          # (If applicable)
├── docs/
│   ├── problem-space.md           # Synthesized from Phase 2
│   ├── architecture-reference.md  # Technical quick-reference
│   └── source-index.md            # Source document index (read + discovered)
├── feedback/
│   └── opens-and-risks.md         # Prioritized risks (P0/P1/P2), blockers, open questions
├── agents/
│   └── team-spec.md               # Agent/team roles, first tasks, context requirements
├── tracker/
│   └── work-breakdown.csv         # Bulk-import template matching the work-breakdown names
├── wiki/
│   ├── draft-charter-page.html    # Draft charter/sponsor page (wiki storage format)
│   └── draft-area-summary.html    # Area summary page (feature areas → tech areas → team/objectives/risks/questions)
└── data/                          # Empty, for future data
```

**Key generation actions:**

- **Populate the work-breakdown import template** (`tracker/work-breakdown.csv`) as a WBS hierarchy:
  - Columns: `WBS Code | Item ID | Prerequisites | Prefix | Feature/Area | Title | Description | Notes | Assignee | Effort (Days) | Priority | Component | Milestone | Category | Program`
  - WBS `1` = the umbrella item, `1.x` = major items, `1.x.y` = sub-items; deeper nesting allowed.
  - Milestone-marker rows for checkpoints; spacer rows between groups.
  - Effort values come from the resource plan estimates.
  - **Names must match the draft charter page exactly** — item titles here = the Feature/Use-Case column on the charter page.
- **Generate the draft charter page** (`wiki/draft-charter-page.html`) in your wiki's storage format:
  - Leadership block (Sponsor, Eng lead, Owner, QA, stakeholders)
  - Status line (ON TRACK / AT RISK / OFF TRACK / PLANNING) + executive summary
  - Feature-summary table — columns adapt to project type:
    - **Feature Build:** Feature/Use Case | Current Phase | Target Build Complete | Target Ship | Target Enabled | Scope Changes | Platform Support | Feature Flag | Essential
    - **Assessment/Deprecation:** Feature/Use Case | Current Phase | Target Deliverable Date | Scope Changes | Platform Support
  - Per-feature health matrix (Owner, Eng lead, Status, summary)
  - QA summary skeleton (full for Feature Build; "N/A — assessment phase" otherwise)
  - Achievements section, Key Risks section
- **Generate the area summary page** (`wiki/draft-area-summary.html`) — the operational companion to the charter. It breaks the program into **Feature Areas → Technology Areas** with per-area detail:
  1. **Metadata table** — Owner, Eng lead (with headcount), target release and date.
  2. **Info panel** — links to the charter page and any KB page.
  3. **Executive Summary** — a *bulleted* list (not paragraphs) for a cold reader. Lead with WHAT before WHO. Ordered bullets: Program (what + target), Deliverables, Hardest constraint (sub-bullets lead with the technology-area name), Immediate risks (same), Resourcing snapshot (headcount by area, unmapped people, pending assignments), Staffing gaps (format: "role/person — status (timing)"). Keep sub-bullets short. **Write this section LAST**, after everything else is populated.
  4. **Feature Area sections** (one heading per area, 3-5 areas) — each with a metadata table (Lead, headcount, tracker component) and a single 6-column table, one row per technology area:
     `Technology Area | Team | Objective | Target | Key Risks | Open Questions`
     - **Technology Area:** bold name + a one-line description with linked repos/components. Tag internal tooling explicitly.
     - **Team:** named individuals with roles (not just role titles).
     - **Objective:** what the team is doing now, and the target state.
     - **Target:** a date or timeframe; use a `~` prefix for estimates.
     - **Key Risks:** color-coded (🔴 blocking / one-way-door, 🟡 significant but manageable, 🟢 low). Each risk must state its **consequence**, not just a label.
     - **Open Questions:** answerable questions, as bullets — these become kickoff agenda items.
     - **Row ordering:** sort by risk severity (red at top), then by nearest target date.
  5. **Cross-Cutting Concerns** — same table shape for areas spanning multiple feature areas.
  6. **Release Timeline & Targets** — table mapping release milestones to program milestones with dates.
  7. **How Targets Were Calculated** — in a collapsible section:
     - **Capacity model** table: headcount → gross capacity/week → deductions (itemized) → net capacity/week → duration → total available.
     - **Effort estimates** table: per-item estimate, size, rationale.
     - **Timeline derivation:** narrative — total effort / net capacity = weeks, adjusted for dependencies, ramp, holidays.
     - **Assumptions:** utilization %, parallelism, ramp, scope boundaries, dependency assumptions.
     - **Risks to timeline** table: risk → impact (weeks) → likelihood → mitigation.
  8. **Last Updated** date.

  **Best practices for the area summary:**
  - One row per technology area. If a row needs >4-5 bullets in any column, split the area.
  - Name people, not roles.
  - Mark TBD explicitly. Never leave a cell blank.
  - Open questions must be answerable ("What is the timeline for X?" not "Is X good?").
  - Risks must state consequences ("sole owner of X, no backup" — not just "flight risk").
  - Link to sources in every technology-area description.

- **Generate README.md** with project-specific context, IDs, milestone dates, and program context.
- **Generate the kickoff prompt** configured for the project's current phase.

After generation, ask two **explicit, separate** questions (see Core Principle 4):
- "Would you like me to publish the draft charter page to the wiki now, or review locally first?"
- "The work-breakdown template is ready at `tracker/work-breakdown.csv`. Would you like me to create these work items in the tracker now, or leave the template for you to import once components are confirmed?"

### Phase 7: Iterative Review

**Walk through EACH artifact ONE AT A TIME.** Present it, get feedback, revise if needed, then move on. **Do not dump all artifacts at once.** (This is the single most important behavior in the skill — see Core Principle 1.)

1. **Problem-space synthesis** — "Is this accurate? What's missing?"
2. **Work breakdown** — "Does this make sense? What should be merged, split, or restructured?"
3. **Draft charter page** — "Leadership block correct? Feature table accurate? Status summaries right?"
4. **Area summary page** — section by section: "Feature-area groupings right? Tech areas correctly assigned? Team mapping accurate? Risks properly sized? Open questions cover the unknowns? Does the exec summary read for a cold audience? Are target calculations defensible?"
5. **Resource plan** — "Do these estimates pass the sniff test? Any constraints I'm missing?"
6. **Risk register** — "What am I missing? Severities correct?"
7. **Work-breakdown template** — "Names match the plan? Anything to add or rename?"
8. **Cross-team dependencies & flags** — "Who's missing? Who are the contacts?" (Feature Build: flags, gates, inspections.)
9. **Kickoff prompt** — "Is the scope right for the lead agent? Anything to add or constrain?"
10. **Team asks & first-meeting agenda** — "Anything else you need from this meeting?"
11. **Final summary** — files created (with paths), what's ready now, what's pending (needs team input / component confirmation / sponsor), suggested next steps, any reminders created.

## Defaults Applied Unless Overridden

Every default below is **offered and confirmed**, never silently assumed (Core Principle 2).

- Enablement criteria: a pass threshold you confirm (Feature Build only).
- Capacity rate: ~60% of nominal (~3 effective days/person-week) — a labeled baseline, confirm with user.
- Milestone structure: from your schedule source.
- Charter draft space: a personal draft space.
- Owner: the invoking user.
- Agent team: sized to project phase (small + lead pre-launch; scale up at launch).

## Usage Examples

```
/scaffold-project
/scaffold-project for a new data-platform assessment
/scaffold-project and ingest these tickets: PROJ-1024 PROJ-1099
/scaffold-project with docs: https://wiki.example.com/x/AbCdEf
```
