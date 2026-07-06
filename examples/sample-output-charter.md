# Sample Generated Output — Beacon Charter (fictional)

This is an example of the `wiki/draft-charter-page` artifact the agent generates from [`sample-intake.md`](sample-intake.md), shown here as rendered Markdown for readability. In a real run this is emitted in your wiki's storage format (e.g. Confluence storage HTML). Everything below is invented.

---

# Beacon — Project Charter (DRAFT)

**Status:** 🟡 PLANNING

## Leadership

| Role | Name |
|------|------|
| Sponsor | Dana Ortiz (Director) |
| Eng Lead | Marcus Feld |
| Owner | *(project owner)* |
| QA Lead | TBD |
| Design | Priya Anand |

## Executive Summary

- **Program:** Beacon delivers a single opt-in surface for users to see and control every notification the app sends. Target GA: November cut, Autumn release train. Rolls up to Trust & Controls.
- **Deliverables:** unified preference center (mobile + web), server-authoritative preference storage, and scheduled Quiet Hours.
- **Hardest constraint:**
  - *preferences-store* — item 2 depends on the Platform team's storage layer, which has a live concurrent-write bug (NW-4102). This is the critical path.
- **Resourcing snapshot:** 4 engineers on Marcus Feld's team; effective capacity 2.5 days/person-week (owner-provided). Preliminary capacity ~100 person-days; essential scope ~85. QA lead unassigned.
- **Staffing gaps:** QA lead — unassigned (current); needed before acceptance-gate definition.

## Feature Summary

| Feature / Use Case | Current Phase | Target Build Complete | Target Ship | Target Enabled | Scope Changes | Platform | Feature Flag | Essential |
|--------------------|---------------|----------------------|-------------|----------------|---------------|----------|--------------|-----------|
| Unified preference center UI | Planning | ~Oct | ~Nov | ~Nov | — | mobile, web | `beacon_prefs_v1` | ✅ |
| Server-authoritative preference storage | Planning | ~Oct | ~Nov | ~Nov | — | backend | `beacon_prefs_v1` | ✅ |
| — cross-device sync (sub-item) | Planning | ~Oct | ~Nov | ~Nov | folded under storage | backend, mobile | `beacon_prefs_v1` | ✅ |
| Quiet Hours (scheduled DND) | Planning | ~Oct | ~Nov | ~Nov | — | mobile | `beacon_prefs_v1` | ✅ |
| Digest batching | Deferred | — | — | — | over capacity; candidate | backend | — | ⬜ |

## Health & Status

| Feature | Owner | Eng Lead | Status | Summary |
|---------|-------|----------|--------|---------|
| Preference center UI | *(owner)* | Marcus Feld | 🟢 | Design ready (Priya); build not started. |
| Preference storage | *(owner)* | Marcus Feld | 🔴 | Blocked by Platform bug NW-4102 (concurrent-write race). Cannot start integration until fixed. |
| Quiet Hours | *(owner)* | Marcus Feld | 🟡 | Design v2 accepted; no engineer assigned yet. |

## QA Summary

- **Acceptance gate:** 95% of QA acceptance scenarios passing (owner-confirmed).
- **QA lead:** TBD — gate cannot be finalized until assigned.

## Key Risks

- 🔴 **Storage dependency (NW-4102).** Server-authoritative storage is on the critical path and blocked by a Platform-owned concurrent-write bug. *Consequence:* item 2 cannot integrate; slips cascade to UI and Quiet Hours. *Mitigation:* escalate to Platform for a fix date this week.
- 🟡 **Thin capacity margin.** Essential scope (~85 person-days) fits ~100 available with little slack. *Consequence:* any scope creep or the digest-batching candidate breaks the plan. *Mitigation:* hold digest batching out of v1.
- 🟡 **Unassigned QA lead.** *Consequence:* acceptance gate undefined, no test plan. *Mitigation:* assign before build-complete.

## Achievements

- Preference storage model decided: per-user, server-authoritative (NW-4088).
- Quiet Hours design v2 accepted.

---

*Companion `area-summary` page and `tracker/work-breakdown.csv` are generated alongside this charter. Draft only — not published, no work items created, pending explicit confirmation.*
