---
title: "PULSE Instrument Cluster"
subtitle: "Functional Safety Concept, preliminary (ISO 26262)"
author: "Ahmed Abdelghany"
date: "2026-09-03"
---

# Document control

| Field | Value |
|-------------------------|---------------------------------------------------------------------------|
| Document ID | PULSE-SAF-001 |
| Title | Functional Safety Concept, preliminary |
| Version | 0.9.2 |
| Status | Skeleton for sprint 1 review; not safety-reviewed |
| Date | 2026-09-03 |
| Author | Ahmed Abdelghany |
| Reviewer | Safety manager, not yet assigned |
| Review gate | Sprint 1 review for structure; sprint 2 review for content |
| Parent | PULSE-SyRS-001 |
| Related | PULSE-StRS-001, PULSE-SAD-001, PULSE-SEC-001, PULSE-RTM-001 |

## Change log

| Version | Date | Author | Change |
|--------|----------|--------------|--------------------------------------------------------------------|
| 0.9 | 2026-09-03 | A. Abdelghany | First skeleton: item definition, safety-relevant signals with freshness proposals, preliminary HARA, safety mechanisms including the minimum-risk manoeuvre, interference argument outline, qualification gap list, SR-1 to SR-11 with status |
| 0.9.1 | 2026-09-03 | A. Abdelghany | Safety-mechanism placement and manoeuvre state-machine diagrams added |
| 0.9.2 | 2026-09-03 | A. Abdelghany | SR-9 evidence updated for the regression suite |

# 1. Purpose and disclaimer

The PULSE demonstrator is not a qualified safety item and no ASIL is claimed. This document establishes compliance readiness: the artefacts and design decisions a production programme would need, produced as a by-product of the build rather than retrofitted later.

Every rating in the hazard analysis is a proposal by the project engineer and has not been reviewed by a safety manager. Ratings are recorded so that a reviewer has something concrete to disagree with.

# 2. Item definition (SR-1)

| Field | Content |
|----------------------|------------------------------------------------------------------------------|
| Item name | PULSE cluster and driver-monitoring function |
| Purpose | Present vehicle state to the driver on a strip display; detect driver drowsiness and distraction from a driver-facing camera; on sustained drowsiness, trigger a minimum-risk manoeuvre |
| Functions | F1 display of road speed, engine speed, gear, fuel, range, tyre pressures, indicators. F2 display of motorsport telemetry. F3 driver-state estimation and alerting. F4 minimum-risk manoeuvre trigger and status display |
| System boundary | As PULSE-SAD-001 section 3.1: catalogue, broker, providers, both HMIs, orchestration |
| Assumed vehicle context | Passenger car or race car with a single driver; cluster is the primary source of speed and gear information; a separate motion controller executes braking on a real vehicle (on the desk rig the simulator stands in) |
| Interfaces crossing the boundary | Camera frames in (V4L2); display surface out (DisplayPort); vehicle bus in (SocketCAN, planned); autonomy stack in (planned); Android surface out |
| Operational situations | Highway cruise, urban driving, track driving, standstill; day and night (night is backlog for the camera) |
| Assumptions on other elements | The vehicle bus delivers correct signals. The motion controller executes a commanded stop safely. The display panel renders what it is sent |

# 3. Safety-relevant signals (SR-3, SR-5)

Signals whose corruption or loss could mislead the driver. Each carries a proposed freshness criterion: a value older than the criterion is invalid and shall be shown as degraded, not as its last value.

| Signal | Why safety-relevant | Publish rate | Proposed freshness | Degraded presentation |
|----------------------------|---------------------------|----------|------------|-----------------------|
| Vehicle.Speed | Speed misjudgement; legal and collision consequences | 10 Hz | 500 ms | Numeral replaced by dashes, dial greyed |
| Vehicle.Powertrain.Transmission.SelectedGear | Unexpected direction of travel at low speed | 10 Hz | 500 ms | Gear glyph replaced by "?" |
| Vehicle.Powertrain.CombustionEngine.Speed | Over-rev, engine damage; secondary | 10 Hz | 500 ms | Dial greyed |
| Vehicle.Body.Lights.Hazard.IsSignaling | Driver must know the vehicle is signalling a stop | 10 Hz on change | 2 s | Indicator shown as unknown |
| Vehicle.Driver.Monitoring.IsActive | Driver may assume monitoring when there is none | 10 Hz | 1 s | "Monitoring unavailable" banner |
| Vehicle.Driver.Monitoring.AlertState | Missing alert is a false negative; false alert triggers the manoeuvre | 10 Hz | 1 s | Treated as NO_FACE (monitoring lost), never as NONE |
| Vehicle.Driver.Monitoring.InterventionState | Driver must know the vehicle is about to brake | 10 Hz | 500 ms | Banner "vehicle state unknown" |
| Vehicle.Driver.Monitoring.InterventionCountdown | As above | 10 Hz | 500 ms | Countdown hidden, banner stays |

Freshness is not yet enforced in either HMI (SR-4, SR-5 status below). The broker timestamps every datapoint, so the criterion can be evaluated client-side without a protocol change.

# 4. Preliminary hazard analysis and risk assessment (SR-2)

Ratings follow ISO 26262-3 severity S0 to S3, exposure E0 to E4, controllability C0 to C3. The ASIL column is the table lookup, not a claim.

| ID | Malfunction | Operational situation | Effect | S | E | C | ASIL proposal | Safety goal |
|-----|-----------------|---------------|-----------------|---|---|---|-------|------------------------------|
| H-1 | Speed displayed too low | Any driving | Driver exceeds intended speed | S1 | E4 | C2 | A | SG-1 The displayed speed shall not be lower than the actual speed by more than a defined tolerance |
| H-2 | Speed display frozen at last value | Any driving | Driver trusts a stale value; worse than blank | S2 | E4 | C2 | B | SG-2 A stale speed shall be recognisable as stale within its freshness criterion |
| H-3 | Wrong gear indicated (D shown, R engaged) | Manoeuvring at standstill | Vehicle moves in an unexpected direction | S2 | E3 | C2 | A | SG-3 Gear indication shall match the engaged gear or show unknown |
| H-4 | Display blank | Any driving | Loss of information, driver aware | S1 | E4 | C1 | QM | SG-4 Loss of display shall be obvious, not plausible |
| H-5 | Hazard or stop indication missing during the manoeuvre | Highway cruise, manoeuvre active | Driver surprised by braking | S2 | E2 | C2 | QM | SG-5 Manoeuvre state shall be displayed before actuation begins |
| H-6 | Drowsiness not detected (false negative) | Highway cruise, drowsy driver | No alert, no manoeuvre | S3 | E2 | C2 | A | SG-6 Sustained drowsiness shall raise an alert within the hold time |
| H-7 | Drowsiness falsely detected, manoeuvre executed | Highway cruise, attentive driver | Unintended braking in traffic | S3 | E4 | C2 | C | SG-7 The manoeuvre shall not begin without a driver-recovery window and a visible countdown |
| H-8 | Alert shown but manoeuvre state stale | Manoeuvre in progress | Driver cannot predict vehicle behaviour | S2 | E2 | C2 | QM | SG-5 as above |

H-7 is the finding to carry forward: the actuation side of the loop, not the display, would carry the highest ASIL in a production programme. That is why the manoeuvre is specified as a safety mechanism with a recovery window (SM-3) rather than as a feature.

# 5. Safety mechanisms

![Where each safety mechanism sits on the signal path, and which hazards it addresses. Ratings are proposals.](diagrams/safety-mechanisms.png)

Source: `docs/diagrams/safety-mechanisms.drawio`.

## SM-1 Staleness detection and degraded display (SR-4, SR-5)

Each HMI shall track the broker timestamp of every safety-relevant signal and compare it to the freshness criterion in section 3 on every frame. Loss of the broker connection shall be treated as all signals stale at once. The degraded presentation in section 3 shall replace the value. Current state: the Flutter service reconnects on broker loss but keeps displaying the last value. Planned sprint 2.

## SM-2 Fail visibly (SR-6)

A rendering fault shall not present a plausible but wrong value. Proposed approach for the demonstrator: a heartbeat glyph on the cluster that the renderer toggles every frame from the signal timestamp, so a frozen renderer is visible within one second; and a self-check that the drawn speed numeral equals the state value. A production programme would need a qualified renderer or an output-verifying supervisor (DD-1 in PULSE-SAD-001). Planned sprint 3.

## SM-3 Minimum-risk manoeuvre on sustained drowsiness (SR-7)

![SM-3 as a state machine. Trigger, recovery window, escalation, abort and resume, with the current parameters.](diagrams/mrm-state-machine.png)

Source: `docs/diagrams/mrm-state-machine.drawio`.

| Element | Specification | Current value |
|--------------------|---------------------------------------------|-----------------------------------|
| Trigger condition | AlertState DROWSY sustained beyond the alert hold time, while in cruise mode | Hold 3.0 s (`DMS_ALERT_HOLD_S`), PERCLOS over 30 s window above 0.40, or microsleep over 1.5 s |
| Driver-recovery window | A countdown displayed on the cluster during which the alert clearing aborts the manoeuvre | 5 s (`SIM_STOP_COUNTDOWN_S`) |
| Escalation | Hazard lights on, cruise disengaged, controlled braking to standstill | 22 km/h per second, about 6 m/s squared |
| Abort condition | AlertState leaves DROWSY during the countdown | Immediate return to NONE |
| Resume | Driver attentive (AlertState NONE) for a sustained period after standstill | 5 s (`SIM_RESUME_AFTER_S`) |
| Status display | InterventionState and InterventionCountdown published every tick and rendered as a banner | Implemented |

Implemented in the simulator's cruise mode (FR-20). On a real vehicle the escalation moves to a motion controller; the trigger and status signal contract stays.

## SM-4 Range validation at the broker (shared with CS-5)

The broker rejects values outside the catalogue's datatype, allowed list or range, so a faulty provider cannot drive an impossible value onto the display. Implemented by KUKSA; a client-side check is added in sprint 2.

# 6. Freedom from interference (SR-8)

Argument outline for the mixed-criticality cockpit, to be completed in sprint 4:

- Spatial: the cluster and any infotainment surface run as separate processes; on the target, separate Wayland surfaces with the cluster surface always on top.
- Temporal: the cluster's render loop does not depend on any infotainment process; broker fan-out is per-stream, so a slow subscriber does not delay another.
- Communication: infotainment is a subscriber only (CS-4 write authorisation); it cannot publish a safety-relevant signal.
- Evidence to produce: a stress test in which an infotainment process saturates CPU and the cluster's frame time is measured.

# 7. Documented deviations (SR-10)

Held in PULSE-SAD-001 section 8, decisions DD-1 to DD-11. The ones that would block a later ASIL argument are DD-1 (unqualified renderer), DD-3 (container runtime), DD-7 (desk window system) and DD-9 (actuation in the simulator).

# 8. Qualification gap list (SR-11)

| Component | What exists | What a production programme would still need |
|--------------------|------------------------------|--------------------------------------------------|
| Linux kernel and board support | Ubuntu on x86-64; Raspberry Pi 5 planned | Safety-certified OS or a hypervisor partition for the cluster; qualified BSP |
| KUKSA databroker | Open-source Rust broker | Safety argument for the broker as a QM element with monitoring, or a qualified alternative; tool qualification not applicable |
| VSS toolchain | vss-tools at a pinned version | Tool confidence level assessment for the catalogue compiler |
| Flutter engine and renderer | Upstream Flutter, unqualified | Qualified renderer or output-verifying supervisor (SM-2) |
| Compose on Android Automotive | AOSP emulator | Not a candidate for the safety-relevant surface; infotainment only |
| Driver monitor | MediaPipe model on CPU | Evaluated dataset with known false-positive and false-negative rates; infrared camera; per-driver calibration; SOTIF (ISO 21448) analysis for the perception function |
| Simulator and actuation | Python simulator | Real motion controller with its own safety concept; PULSE only provides the trigger and status signals |
| Toolchain | Dart, Kotlin, Python, Bash | Qualified compilers where the safety-relevant code runs; coding guideline compliance |

# 9. Safety requirements

| ID | Requirement | Verification | Status (W3) | Evidence |
|-------|-----------------------------------|----------|--------------|----------------------------------|
| SR-1 | An item definition shall be written for the cluster and driver-monitoring function, naming the system boundary, the assumed vehicle context, and the interfaces crossing that boundary (ISO 26262-3). | I | Partial | Section 2 of this document; not yet safety-reviewed |
| SR-2 | A preliminary hazard analysis and risk assessment (HARA) shall be recorded for the displayed functions, with severity, exposure and controllability rated per malfunction and a resulting ASIL proposal. | I | Partial | Section 4; eight hazards rated; review in S2 |
| SR-3 | Signals whose corruption or loss could mislead the driver (road speed, gear, warning indicators, drowsiness alert) shall be identified as safety-relevant and listed explicitly. | I | Implemented | Section 3 |
| SR-4 | The system shall detect loss of a safety-relevant signal, including broker disconnection and stale values, and shall indicate the degraded state rather than continuing to display the last known value. | T | Planned S2 | SM-1; Flutter service reconnects but shows last value today |
| SR-5 | Every safety-relevant signal shall carry a freshness criterion; a value older than its criterion shall be treated as invalid. | T | Partial | Criteria proposed in section 3; enforcement with SM-1 in S2 |
| SR-6 | Safety-relevant display elements shall fail visibly. A rendering fault shall not present a plausible but wrong value. | T | Planned S3 | SM-2 |
| SR-7 | The minimum-risk manoeuvre triggered by sustained driver drowsiness shall be specified as a safety mechanism: trigger condition, driver-recovery window, escalation, and abort condition. | D | Implemented | SM-3; implemented in `emulator/telemetry_sim.py` cruise mode; demonstrated live |
| SR-8 | Freedom from interference shall be argued for the mixed-criticality cockpit: infotainment content shall not be able to degrade or obscure the safety-relevant cluster surface. | A | Planned S4 | Section 6 outline |
| SR-9 | A safety-relevant requirement shall be traceable to the verification that demonstrates it, and each verification result shall be reproducible from the repository. | T | Partial | PULSE-RTM-001 generated from this table; regression suite runs before each demo and commits its report to `docs/evidence/`; safety-specific tests (SR-4 to SR-6) still to come in S2 and S3 |
| SR-10 | Any decision that would block a later ASIL argument, such as the choice of graphics stack, operating system or toolkit, shall be recorded as a documented deviation with its rationale. | I | Implemented | PULSE-SAD-001 section 8, DD-1 to DD-11 |
| SR-11 | A qualification gap list shall be maintained: for each component, what a production programme would still need (qualified toolchain, certified OS, evidence of a safety-qualified renderer). | I | Implemented | Section 8 |

# 10. Verification and traceability

Safety requirements are traced to user stories US-13, US-15 and US-17 in PULSE-StRS-001 and to their verification in PULSE-RTM-001. Test cases for SR-4, SR-5 and SR-6 are written in sprint 2 as part of the regression suite so that each demo re-runs them.
