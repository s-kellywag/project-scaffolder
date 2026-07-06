# Sample Intake — "Beacon" (fictional)

A worked, fully fictional example of Phases 1-4 of a `scaffold-project` run. Everything here is invented for illustration: the company (Northwind), the product (Beacon), the people, the tools, and the tickets are not real.

---

## Phase 1 — Core Identity

> **Agent:** Let's establish the basics. What are we calling this project?

**Owner:** Beacon.

> **Agent:** What type of project is this? (Feature Build / Assessment / Deprecation / Infrastructure)

**Owner:** Feature Build.

> **Agent:** One-line vision, in plain user terms?

**Owner:** "Give users a single opt-in place to see and control every notification the app can send."

> **Agent:** Target release or deadline?

**Owner:** Autumn release train — target GA in the November cut.

> **Agent:** Parent program, or standalone?

**Owner:** Rolls up to the "Trust & Controls" program.

> **Agent:** Where should the draft charter page live?

**Owner:** My personal draft space for now.

---

## Phase 2 — Problem Space Ingestion

> **Agent:** Point me at the problem — tickets, docs, wiki pages, folders, threads.

**Owner:** Tickets NW-4021 and NW-4088. This wiki folder of prior notifications research: `wiki://folder/trust-controls-research`. And search the tracker for "notification center."

> **Agent:** Which wiki spaces should I scout, and any keywords beyond the project name?

**Owner:** The `TRUST` and `PLATFORM` spaces. Keywords: "notification preferences," "digest," "quiet hours."

**Agent (Tier 1 — full read):**
- Fetched NW-4021 (parent epic: "Unified notification controls") and NW-4088 (spike: "Preference storage model").
- Enumerated the research folder (14 docs); read the 10 most recent in full.

**Agent (Tier 2 — discovery + triage):** "I found 6 additional sources. Ranked by relevance:"

| # | Source | One-line (from first ~500 chars) |
|---|--------|----------------------------------|
| 1 | wiki: "Quiet Hours design v2" | Design doc for scheduled do-not-disturb; supersedes v1. |
| 2 | NW-3760 | Closed ticket — legacy per-channel toggle, useful prior art. |
| 3 | wiki: "Digest batching RFC" | Proposal to batch low-priority notifications hourly. |
| 4 | NW-4102 | Open bug — preference writes race under concurrent edits. |
| 5 | wiki: "Notifications metrics glossary" | Definitions for open-rate, opt-out-rate. |
| 6 | chat export: "notif sync thread" | Unresolved discussion on cross-device sync. |

> **Agent:** Which should I read in full?

**Owner:** 1, 3, and 4. Index the rest.

**Agent:** Synthesized into `docs/problem-space.md`. Key findings:
- **Decisions already made:** preference storage will be per-user, server-authoritative (NW-4088).
- **Open questions:** cross-device sync conflict resolution (chat thread, unresolved); digest batching in scope for v1? (RFC not accepted).
- **Risk surface:** NW-4102 concurrent-write race is a live bug on the storage model this project depends on.

> **Agent:** Here's what I synthesized from 12 docs, 3 wiki pages, and 3 tickets. Accurate? Missing anything? Anyone to search for by name?

**Owner:** Accurate. Also search for docs by "Priya Anand" — she led the Quiet Hours work.

---

## Phase 3 — People & Resources

> **Agent:** Leadership block — Sponsor, Eng lead, Owner, QA lead, stakeholders? (TBD is fine.)

**Owner:** Sponsor: Dana Ortiz (Director). Eng lead: Marcus Feld. Owner: me. QA: TBD. Stakeholder: Priya Anand (design).

> **Agent:** Components / areas involved?

**Owner:** `notifications-service`, `preferences-store`, and the `mobile-client` notification module.

> **Agent:** How many people does Marcus have available?

**Owner:** Four engineers.

> **Agent:** Do you have an effective-capacity estimate per person per week? A common baseline is ~60% of nominal — about 3 effective days per person-week — but it varies. Do you have a number, or should I use that baseline as a labeled default?

**Owner:** Use the baseline, but Marcus's team runs meeting-heavy — call it 2.5 days.

**Agent:** Noted — capacity rate **2.5 effective days/person-week (owner-provided, not default)**. Preliminary capacity: 4 people × 2.5 × ~10 weeks = **~100 effective person-days**. Flagged as preliminary.

---

## Phase 4 — Work Breakdown & Planning

> **Agent:** What are the 3-5 big things Beacon delivers? Or I can propose from the synthesis.

**Owner:** Propose.

**Agent (proposed, for confirmation):**

| # | Item | Essential? | Platforms |
|---|------|-----------|-----------|
| 1 | Unified preference center UI | Essential | mobile, web |
| 2 | Server-authoritative preference storage | Essential | backend |
| 3 | Quiet Hours (scheduled DND) | Essential | mobile |
| 4 | Digest batching | Candidate | backend |
| 5 | Cross-device sync | Candidate | backend, mobile |

> **Agent:** Feature-flag needs, and an enablement threshold? I won't hardcode a pass rate — what's your gate?

**Owner:** One flag, `beacon_prefs_v1`. Gate on 95% of QA acceptance scenarios passing.

> **Agent:** Known cross-team dependencies?

**Owner:** The Platform team owns `preferences-store`; NW-4102 (their bug) blocks item 2.

> **Agent:** What should be merged or restructured? Are any of these sub-items of another?

**Owner:** Fold cross-device sync (5) under storage (2) as a sub-item — don't track it separately yet.

**Agent (capacity check):** essential items (1-3 + sync sub-item) estimate ~85 person-days against ~100 available — **fits, thin margin**. Digest batching (candidate) would push to ~115 — **over capacity**; recommend deferring unless a person is added. Cross-team risk: **item 2 is blocked by Platform's NW-4102** — flagged red in the risk register.

> **Agent:** Here's the draft breakdown. Feedback before I generate the scaffold?

**Owner:** Looks right. Generate it.

---

*Continues into Phase 5 (timeline) and Phase 6 (generation). See [`sample-output-charter.md`](sample-output-charter.md) for the charter this intake produces.*
