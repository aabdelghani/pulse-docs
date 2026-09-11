---
title: "PULSE Instrument Cluster"
subtitle: "Sprint 2 Backlog"
author: "Ahmed Abdelghany"
date: "2026-09-11"
---

# Document control

| Field | Value |
|-------------------------|---------------------------------------------------------------------------|
| Document ID | PULSE-PLAN-002 |
| Title | Sprint 2 Backlog |
| Version | 0.3 |
| Status | Draft for the sprint 1 review; frozen after review feedback is merged |
| Date | 2026-09-11 |
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
| 0.3 | 2026-09-11 | A. Abdelghany | Week 4 checkpoint added as section 8: per-item state verified against the repository, hours position, decisions requested. Sprint 1 review recorded as held with no comments |

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

The review was held on 4 September as planned. No comments were raised and no requirement changed as a result, so this table is empty by outcome rather than by omission.

| # | Comment | Raised by | Action | Target sprint |
|---|---------|-----------|--------|---------------|
| - | No comments raised at the review | - | None | - |

# 7. Definition of done for the week 6 gate

1. Regression suite passes on a clean checkout and its report is committed under `docs/evidence/`.
2. The cluster shows degraded state within the freshness criterion when the broker is stopped, on both HMIs.
3. Broker and all four clients run over mutual TLS; a plaintext start prints the warning.
4. PULSE-SyRS-001, PULSE-SAF-001 and PULSE-SEC-001 status columns updated and PULSE-RTM-001 regenerated with zero dangling references.
5. Demo rehearsed on the strip display and timed under five minutes.

# 8. Progress at the close of week 4

Every state below was checked against the repository on 11 September, not
self-reported. Where an item is partly done, the gap is named.

| # | Item | Est. h | Target at the gate | State at week 4 | Checked by |
|----|--------------------------|------|--------------|------------------|--------------------------------------|
| 1 | Regression suite | 16 | Implemented | Done, but runs by hand | Nine checks and six committed reports under `docs/evidence/`. No CI workflow exists, so nothing runs it automatically |
| 2 | Signal contract test | 12 | Implemented | Partly done | The check resolves 28 appendix A paths in the compiled tree. It does not subscribe and receive a typed datapoint, which is what the item asks for |
| 3 | UI smoke test per build | 20 | Implemented | Barely started | `flutter-launch` asserts the process stays up for four seconds. No injected value, no assertion on what is rendered, nothing on Compose |
| 4 | Flutter staleness display | 32 | Implemented | Not started | No freshness or staleness logic in hand-written HMI source |
| 5 | Compose staleness display | 16 | Implemented | Not started | As above |
| 6 | Mutual TLS on the broker | 24 | Implemented | Not started | `scripts/start-databroker.sh` still passes `--insecure` |
| 7 | Pin the Flutter SDK | 6 | Partial | Half done | Broker pinned by tag and digest. No FVM pin and no SDK version recorded in the README |
| 8 | SBOM and vulnerability check | 14 | Implemented | Not started | No SBOM file and no check |
| 9 | Driver-monitor privacy test | 6 | Implemented | Not started | No check. The claim holds by inspection but nothing proves it |
| 10 | HMI-side range check | 10 | Implemented | Not started | Broker-side validation only |
| 11 | Catalogue checksum | 4 | Implemented | Not started | No check |
| 12 | Merge the monitoring branch | 4 | Done | Done | Merged into `main` on 4 September |
| 13 | Architecture write-ups | 16 | Implemented | Not started | No SWAD documents exist |
| 14 | Safety and security review | 8 | Reviewed | Not held | Both concepts still carry "not safety-reviewed" and "not security-reviewed" |
| 15 | Virtual CAN feeder spike | 8 | Partial | Not started | No `vcan0` or provider work. The only matches in the tree are diagram labels |

## 8.1 Hours

| Position | Hours |
|--------------------------------------|------|
| Committed in section 3 | 196 |
| Delivered by the close of week 4 | about 33 |
| Remaining | about 163 |
| Capacity left, weeks 5 and 6 | 160 |

The sprint reserve of 44 hours was held for review feedback and risk. The review
raised no feedback, so that reserve was available. Week 4 spent it on work that
was not in this backlog, so the position is now roughly break-even with no
slack at all. One illness, one hard defect, or one more week like the last, and
the gate is missed.

## 8.2 What week 4 actually produced

None of it was on this backlog, and all of it was worth doing. Said plainly so
that it is not mistaken for progress against work package 02.

- Every download on the published documentation site returned 404. The page was
  built for one directory layout and published into another. All fourteen now
  serve, and each document is available as PDF or Word.
- The specification set was revised for readability, versioned and dated.
- A defect was found and closed: the odometer published kilometres into a signal
  the catalogue defines in metres, and the display divided by nothing, so the two
  errors cancelled and the dial looked correct. A new suite check, `unit-sanity`,
  fails the build if it recurs.

Against the backlog, week 4 moved item 1 by one check and item 7 by part of a
decision. Nothing else.

## 8.3 Decisions requested

Each one is cheap this week and expensive in week 6.

| # | Decision | Why it cannot wait |
|----|-----------------------------------------|-----------------------------------------------|
| 1 | Cut item 5, Compose staleness | There is no Java, Gradle or Android SDK on the machine, so it would be written blind and untested. Cutting it is what returns slack to the sprint |
| 2 | Amend definition of done item 2 | It says degraded state on both HMIs. If item 5 is cut, that wording must change before the gate, not at it |
| 3 | Fund parity or narrow the benchmark claim | FR-13 is Partial: Flutter subscribes to 28 signals, Compose to 17. The comparison in the deck assumes both consume the same data, and that is no longer true |
| 4 | Settle the Flutter SDK pin | Commit the 3.47 upgrade, or revert and pin through FVM. Six hours of work behind a decision open since sprint 1 |
| 5 | Book the safety and security review | Item 14 needs two external calendars and there are two weeks left. It slips by default |
| 6 | Order the target hardware | Work package 03 gates at week 9. Section 7.2 of PULSE-SAD-001 specifies a Raspberry Pi 5 with 8 GB; if a 16 GB board is bought instead, that line changes |

## 8.4 Proposed change to this plan

Add a checkpoint at the end of week 5, one hour, against this same table. A
three-week sprint whose first status point is the gate itself has no room to
recover. If week 5 goes the way week 4 did, that should be known on 18
September rather than on the morning of the demo.
