---
title: "PULSE Instrument Cluster"
subtitle: "Sprint 2 Backlog"
author: "Ahmed Abdelghany"
date: "2026-09-08"
---

# Document control

| Field | Value |
|-------------------------|---------------------------------------------------------------------------|
| Document ID | PULSE-PLAN-002 |
| Title | Sprint 2 Backlog |
| Version | 0.2 |
| Status | Draft for the sprint 1 review; frozen after review feedback is merged |
| Date | 2026-09-08 |
| Author | Ahmed Abdelghany |
| Reviewer | Cockpit Electronics / HMI Platform Group (sponsor) |
| Sprint window | Week 4 to week 6, 7 September to 25 September 2026 |
| Review gate | Week 6 demo: signal layer and Flutter cluster verified on live broker data |
| Related | PULSE-StRS-001, PULSE-SyRS-001, PULSE-SAF-001, PULSE-SEC-001 |

## Change log

| Version | Date | Author | Change |
|--------|----------|--------------|--------------------------------------------------------------------|
| 0.1 | 2026-09-03 | A. Abdelghany | First draft from the delivery plan and the week-3 status columns; review feedback section left open |
| 0.2 | 2026-09-08 | A. Abdelghany | Document-control fields corrected so the review gate and related documents resolve. Item 7 reduced to Partial: the databroker digest is pinned, the Flutter SDK version is not. Sprint goal and out-of-scope wording clarified |

# 1. Sprint goal

Close work package 02. An automated suite verifies the signal layer and the Flutter cluster against live broker data, and runs before every demo. Every desk-only shortcut that has a sprint 2 requirement attached is removed. The week 6 demo shows the same cluster as week 3, now with contract tests, degraded-state display, encrypted transport and pinned upstream versions behind it.

# 2. Capacity

| Item | Value |
|---|---|
| Engineers | 2 at 40 h a week |
| Hours in sprint | 240 |
| Committed below | 196 |
| Reserve for review feedback and risk | 44 |

# 3. Backlog

Ordered by priority. Every item names the requirement it moves and the status it should reach by the week 6 gate. Estimates are engineering hours including verification.

| # | Item | Moves | To status | Est. h | Verification |
|---|-------------------------------------------------|--------------|-------------|------|------------------------------|
| 1 | Regression suite in CI shape: broker up, signal contract, VSS regeneration, live telemetry, HMI purity, secrets, Flutter launch. Run recorded under `docs/evidence/` before every demo | CS-11, SR-9, FR-11, FR-3 | Implemented | 16 | Suite passes on a clean checkout |
| 2 | Signal contract test against the running broker: every path in PULSE-SAD-001 appendix A subscribes and receives a typed datapoint | FR-11, FR-13, IR-2 | Implemented | 12 | Test in the suite |
| 3 | UI smoke test per build: cluster launches, connects, renders a known injected value, exits clean; Compose cluster on the emulator | FR-12, FR-14 | Implemented | 20 | Test in the suite |
| 4 | Staleness detection in the Flutter cluster: freshness criterion per safety-relevant signal from PULSE-SAF-001 section 3, degraded presentation, broker loss shows all stale | SR-4, SR-5 | Implemented | 32 | Test: kill the broker, dashes appear within the criterion |
| 5 | Same staleness handling in the Compose cluster | SR-4, SR-5 | Implemented | 16 | Test on the emulator |
| 6 | Mutual TLS on the databroker with clearly marked development certificates; start-up prints a warning whenever `--insecure` is used; both clients and both providers connect over TLS | CS-3, IR-5 | Implemented | 24 | Suite: plaintext client refused unless the development flag is set |
| 7 | Pin the Flutter SDK version and record it in the README; the databroker image digest is already pinned | NFR-8, DD-10, DD-11 | Partial | 6 | Inspection |
| 8 | Software bill of materials per component (CycloneDX) and a vulnerability check in the suite | CS-8 | Implemented | 14 | Suite |
| 9 | Driver-monitor privacy test: no file written, no socket other than the broker | CS-6 | Implemented | 6 | Suite |
| 10 | HMI-side range and enumeration check as defence in depth, with a test that an out-of-range publish is refused by the broker | CS-5 | Implemented | 10 | Suite |
| 11 | Catalogue checksum in the suite, so a tampered `vss_agl.json` fails the run | CS-8, T-4 | Implemented | 4 | Suite |
| 12 | Merge the driver-monitoring branch into `main` after review; one baseline for sprint 2 | NFR-5 | Done | 4 | Onboarding guide re-run on the merged tree |
| 13 | Software architecture description for the Flutter cluster and the providers (SWAD), one page each, as promised in PULSE-SAD-001 section 1 | SR-10 | Implemented | 16 | Review |
| 14 | Safety and security review of PULSE-SAF-001 and PULSE-SEC-001 with the assigned managers; ratings confirmed or changed | SR-1, SR-2, CS-1 | Reviewed | 8 | Review minutes |
| 15 | Virtual CAN feeder spike: `vcan0` with kuksa-can-provider and the AGL DBC, publishing three signals, to de-risk sprint 3 | FR-8 | Partial | 8 | Demonstration |

# 4. Out of scope this sprint

Six items stay in sprints 3 and 4, as the delivery plan in PULSE-StRS-001 section 3.1 already schedules them: physical CAN hardware, the Raspberry Pi 5 baseline, per-client write authorisation (CS-4), security event logging (CS-7), the fail-visible renderer (SR-6) and the Autoware bridge (FR-22).

# 5. Risks carried into the sprint

| Risk | Response |
|---|---|
| TLS in the Dart and Kotlin gRPC stacks costs more than estimated | Land the broker side first; a client that cannot do TLS yet keeps the development flag and the start-up warning |
| Staleness handling changes the visual design | Degraded presentation is specified in PULSE-SAF-001 section 3; no new design work, only the agreed dashes, greying and banner |
| Safety and security managers not assigned in time | Item 14 slips to sprint 3 without blocking the gate; the documents stay marked "not reviewed" |

# 6. Review feedback, 4 September

To be filled in during the sprint 1 review. Each comment gets a line here and, where it changes a requirement, a change-log row in the owning document.

| # | Comment | Raised by | Action | Target sprint |
|---|---------|-----------|--------|---------------|
| | | | | |

# 7. Definition of done for the week 6 gate

1. Regression suite passes on a clean checkout and its report is committed under `docs/evidence/`.
2. The cluster shows degraded state within the freshness criterion when the broker is stopped, on both HMIs.
3. Broker and all four clients run over mutual TLS; a plaintext start prints the warning.
4. PULSE-SyRS-001, PULSE-SAF-001 and PULSE-SEC-001 status columns updated and PULSE-RTM-001 regenerated with zero dangling references.
5. Demo rehearsed on the strip display and timed under five minutes.
