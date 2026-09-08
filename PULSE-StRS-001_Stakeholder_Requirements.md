---
title: "PULSE Instrument Cluster"
subtitle: "Stakeholder Requirements Specification and User Stories"
author: "Ahmed Abdelghany"
date: "2026-09-03"
---

# Document control

| Field | Value |
|-------------------------|---------------------------------------------------------------------------|
| Document ID | PULSE-StRS-001 |
| Title | Stakeholder Requirements Specification and User Stories |
| Version | 1.1 |
| Status | Draft frozen for review |
| Date | 2026-09-03 |
| Author | Ahmed Abdelghany |
| Reviewer | Cockpit Electronics / HMI Platform Group (sponsor) |
| Review gate | Sprint 1 review, week 3, 4 September 2026 |
| Classification | Internal, competency project |
| Related | PULSE-SyRS-001, PULSE-SAD-001, PULSE-SAF-001, PULSE-SEC-001, PULSE-RTM-001 |

## Change log

| Version | Date | Author | Change |
|--------|----------|--------------|--------------------------------------------------------------------|
| 0.1 | 2026-08-25 | A. Abdelghany | First draft, single combined document |
| 1.0 | 2026-09-03 | A. Abdelghany | Split into the document set; stakeholder needs added; stories traced to needs and system requirements; frozen for sprint 1 review |
| 1.1 | 2026-09-03 | A. Abdelghany | Document-set map, delivery plan and needs-to-epics diagrams added; section 3.1 added |

## Document set

This document is the top of the PULSE specification chain. The chain follows the left leg of the Automotive SPICE V-model so a reviewer from any OEM programme can find what they expect where they expect it.

| ID | Document | Answers |
|----------------|------------------------------------|------------------------------------------------|
| PULSE-StRS-001 | Stakeholder Requirements and User Stories (this document) | Why are we building this, for whom, and what does "done" look like to them |
| PULSE-SyRS-001 | System Requirements Specification | What the system shall do, numbered and verifiable |
| PULSE-SAD-001 | System Architecture Description | How the system is built, its components, interfaces and deployment |
| PULSE-SAF-001 | Functional Safety Concept, preliminary | ISO 26262 readiness artefacts |
| PULSE-SEC-001 | Cybersecurity Concept, preliminary | ISO/SAE 21434 and UNECE R155 readiness artefacts |
| PULSE-RTM-001 | Requirements Traceability Matrix | Which story, requirement, verification and evidence belong together; generated, never hand-edited |

![The specification chain: who realises whom, and how the traceability matrix is derived from the set.](diagrams/doc-set.png)

Source: `docs/diagrams/doc-set.drawio`.

# 1. Purpose

This document records who is asking for the PULSE instrument cluster, why, and what they need from it. It is written before the system requirements so the system requirements have something to be traced back to. Every user story in section 6 names the stakeholder need it serves and the system requirements that realise it.

# 2. Stakeholder brief

**Requesting stakeholder:** Cockpit Electronics / HMI Platform Group.

**Sponsor question:** "For our next-generation digital cluster, do we build the HMI on Linux (AGL/Flutter) or on Android Automotive, and what does that choice actually cost us?"

## 2.1 Business drivers

- Cockpit programmes are being asked to commit to an HMI toolkit years before hardware is fixed. That decision is currently made on vendor claims, not measured evidence.
- The industry is converging on a hybrid cockpit: one signal broker feeding a safety-relevant Linux cluster and an Android infotainment surface. The group needs in-house competence in that pattern before it lands in a production programme.
- HMI work is repeatedly re-done when the underlying bus or ECU changes. The group wants a UI layer that is decoupled from CAN, so a change of vehicle platform does not restart the HMI effort.

## 2.2 Why a motorsport cluster

A racing UI is deliberately the worst case: high refresh rate, dense simultaneous signals, wide dynamic range, and hard legibility demands. If the architecture holds here, it holds for a passenger-car cluster.

## 2.3 Stakeholders

| Role | Interest | Represented by |
|------------------|----------------------------------------------|------------------------------------|
| Sponsor | An evidence-based toolkit decision and a costed hardware envelope | Cockpit Electronics / HMI Platform Group |
| HMI developer | Never touching bus formats; a stable signal contract | Project engineer |
| Platform engineer | Upstream-compatible signal catalogue; reproducible builds | Project engineer |
| Integrator | Swapping data sources without HMI changes | Future CAN feeder programme |
| Safety manager | Compliance-readiness artefacts that a production programme can pick up | Not yet assigned; see PULSE-SAF-001 |
| Security manager | Same, for ISO/SAE 21434 and R155 | Not yet assigned; see PULSE-SEC-001 |
| Presenter | A demo that runs on any machine in under five minutes | Project engineer |
| New engineer | Bring-up from documentation alone | Whoever inherits the repository |

# 3. Scope

**In scope:** a working instrument-cluster demonstrator on a standards-based software-defined-vehicle data stack, implemented twice on the same design, with measured comparison between the two implementations, on desk hardware. Edge-AI driver monitoring and the drowsiness minimum-risk manoeuvre are in scope as the closed-loop showcase of the same signal path.

**Out of scope:** production ASIL / functional-safety qualification, real vehicle integration, certification, in-vehicle validation. PULSE-SAF-001 and PULSE-SEC-001 define compliance readiness for those activities, not the activities themselves.

**Funded scope (twelve weeks, four sprints):** requirements and architecture baseline; signal layer and Flutter cluster on live broker data; virtual then physical CAN feeder; Autoware planning bridge; measurement, documentation and handover.


## 3.1 Delivery plan

Five work packages across four three-week sprints, each closing with a demo that is the acceptance gate for that sprint's release. The second row of each column lists what this specification expects to land in that sprint, so the sponsor can check each gate against the requirement status columns in PULSE-SyRS-001, PULSE-SAF-001 and PULSE-SEC-001.

![Work packages, gates, and what each sprint delivers against the numbered requirements.](diagrams/delivery-plan.png)

Source: `docs/diagrams/delivery-plan.drawio`.

# 4. Stakeholder needs

Stakeholder needs are the reasons the system exists. They are deliberately few and deliberately not testable in themselves; the system requirements in PULSE-SyRS-001 make them testable.

| ID | Need | Driver |
|--------|----------------------------------------------------|----------------------------------------|
| SN-1 | Decide the cluster toolkit on measured evidence, with application cost separated from platform cost | Toolkit decisions are made years before hardware is fixed |
| SN-2 | Build in-house competence in the hybrid cockpit pattern: one broker, a Linux cluster, an Android surface | The pattern will land in a production programme |
| SN-3 | An HMI decoupled from the vehicle bus so a platform change does not restart HMI work | HMI rework on every bus or ECU change |
| SN-4 | A demonstrator presentable to a non-technical audience in under five minutes | Sponsor reviews and stakeholder demos |
| SN-5 | Competence that outlives the project: a new engineer brings the stack up from documentation alone | Key-person risk |
| SN-6 | Compliance readiness: the safety and security artefacts a production programme would ask for, produced while building rather than retrofitted | ISO 26262, ISO/SAE 21434, UNECE R155 and R156 |
| SN-7 | A closed loop, not a dashboard light: perception feeds a vehicle reaction through the same signal path the cluster renders | Autonomy programmes need take-over readiness, not only display |

# 5. Constraints and assumptions

| ID | Statement |
|--------|--------------------------------------------------------------------------------------------|
| C-1 | Development and demonstration occur on desk hardware. No vehicle is available. |
| C-2 | The signal catalogue is fixed to a released version (COVESA VSS 6.0); upgrades are a deliberate, reviewed change. |
| C-3 | Both HMI implementations must be reachable by one engineer; toolkit familiarity cannot be assumed for either. |
| C-4 | The demonstrator must be presentable to a non-technical audience in under five minutes. |
| C-5 | Two engineers at 40 hours a week for twelve weeks, 960 hours including 120 hours contingency. |
| A-1 | A future programme will supply real vehicle data via a CAN feeder. |
| A-2 | Target deployment hardware is not yet selected; the design must not presuppose it. |
| A-3 | The sponsor accepts emulator-derived Android Automotive measurements as directionally sound until target hardware exists. |

# 6. User stories

Each story carries acceptance criteria and a trace to the stakeholder need it serves and the system requirements in PULSE-SyRS-001 that realise it. The Trace column is machine-read by the traceability generator; keep its format as a comma-separated list of IDs.

![Which stakeholder needs each epic serves, derived from the Trace column of its stories.](diagrams/needs-epics.png)

Source: `docs/diagrams/needs-epics.drawio`.

## Epic A: Vehicle data platform

| ID | Story | Acceptance criteria | Trace |
|------|--------------------------------------------|----------------------------------|----------------|
| US-1 | As an HMI developer, I want every vehicle value available as a named VSS signal, so that I never touch bus formats or hardware decoding. | Cluster code contains no CAN/DBC references; all reads go through broker subscriptions. | SN-3, FR-1, FR-11 |
| US-2 | As a platform engineer, I want domain signals added as VSS overlays, so that we stay compatible with the upstream catalogue. | Overlay files build with the standard tooling; upstream spec is unmodified. | SN-2, FR-2, FR-3 |
| US-3 | As an integrator, I want producers and consumers to meet only at the broker, so that either side can be swapped without touching the other. | Simulator replaced by another provider with zero HMI changes. | SN-3, FR-5, FR-8 |

## Epic B: Telemetry

| ID | Story | Acceptance criteria | Trace |
|------|--------------------------------------------|----------------------------------|----------------|
| US-4 | As a demonstrator, I want a realistic drive cycle without a vehicle, so that the cluster behaves plausibly on a desk. | Speed, RPM, gear, fuel and temperature evolve consistently with each other. | SN-4, FR-6 |
| US-5 | As an evaluator, I want the same cycle to replay identically, so that measurements of the two HMIs are comparable. | Two runs produce the same signal sequence. | SN-1, FR-7, NFR-7 |

## Epic C: Cluster HMI

| ID | Story | Acceptance criteria | Trace |
|------|--------------------------------------------|----------------------------------|----------------|
| US-6 | As a driver, I want speed, RPM, gear, fuel and tyre pressures at a glance, so that I can read the vehicle state in under a second. | Primary values legible in a sub-second glance on the strip display. | SN-4, FR-9, NFR-2 |
| US-7 | As a race engineer, I want lap, DRS and ERS state on the cluster, so that motorsport telemetry is visible live. | Lap number and time, DRS and ERS render and update from broker signals. | SN-4, FR-10 |
| US-8 | As a stakeholder, I want the same design built in Flutter and Compose from one signal source, so that the platform comparison is fair. | Both clusters run side by side from the identical broker feed. | SN-1, SN-2, FR-12, FR-13 |
| US-9 | As a presenter, I want the cluster full-screen on the strip display but windowed elsewhere, so that the demo works on any machine. | Full-screen on the 2560 x 720 panel; graceful window fallback. | SN-4, FR-14, IR-3, IR-4 |

## Epic D: Evidence

| ID | Story | Acceptance criteria | Trace |
|------|--------------------------------------------|----------------------------------|----------------|
| US-10 | As the sponsor, I want application cost separated from platform cost, so that the toolkit decision and the hardware bill are decided on evidence. | CPU, memory and artefact size reported per app and per platform, reproducible from the repository. | SN-1, FR-15, FR-16, FR-17, NFR-3 |
| US-11 | As a new engineer, I want to bring the stack up from documentation alone, so that the competence outlives the project. | Clean-machine bring-up succeeds using only the README and onboarding guide. | SN-5, NFR-5 |

## Epic E: Driver monitoring and closed loop

| ID | Story | Acceptance criteria | Trace |
|------|--------------------------------------------|----------------------------------|----------------|
| US-12 | As a driver, I want the cluster to warn me when I am drowsy or distracted, so that I regain attention before anything else happens. | Alert banner appears within the alert hold time of sustained eye closure or off-road head pose; clears when attention returns. | SN-7, FR-18, FR-19, FR-21 |
| US-13 | As a safety engineer, I want sustained drowsiness to trigger a defined minimum-risk manoeuvre with a recovery window, so that detection has a vehicle-level consequence. | Countdown shown on the cluster; recovery aborts it; otherwise hazards on and controlled stop; automatic resume after sustained attention. | SN-7, FR-20, SR-7 |
| US-14 | As a privacy officer, I want camera frames to stay on the device, so that driver monitoring never becomes surveillance. | No frame leaves the process or is written to disk; only derived state is published. | SN-6, CS-6 |

## Epic F: Compliance readiness

| ID | Story | Acceptance criteria | Trace |
|------|--------------------------------------------|----------------------------------|----------------|
| US-15 | As a safety manager, I want an item definition, hazard analysis and safety-relevant signal list for the cluster, so that a production programme starts from artefacts instead of from scratch. | PULSE-SAF-001 exists with item definition, preliminary HARA and signal list, reviewed at a sprint gate. | SN-6, SR-1, SR-2, SR-3, SR-11 |
| US-16 | As a security manager, I want a threat analysis and trust-boundary description for the signal path, so that the desk-only shortcuts are known and removable. | PULSE-SEC-001 exists with TARA and trust boundaries; each shortcut has a requirement that removes it. | SN-6, CS-1, CS-2, CS-3 |
| US-17 | As a reviewer, I want every requirement traced to a story and a verification, so that I can see what the prototype covers at each sprint gate. | PULSE-RTM-001 regenerates from the document set with no dangling references. | SN-6, SR-9 |

# 7. Acceptance at the sprint 1 review

The week 3 review on 4 September 2026 is the acceptance gate for the sprint 1 release. It is accepted when the following are shown:

1. The first prototype live: the simulated drive cycle published through the broker and rendered on the Flutter cluster, on the 2560 x 720 strip display.
2. A walkthrough of this document and PULSE-SyRS-001 with the sponsor, with review comments recorded into the sprint 2 backlog.
3. PULSE-RTM-001 regenerated on the day, showing per-requirement status at week 3.
4. The sprint 2 plan: completion of the signal layer and Flutter cluster verification, start of the virtual CAN feeder.

# 8. Glossary

| Term | Meaning |
|---------------|-------------------------------------------------------------------------------------|
| AAOS | Android Automotive OS, Android running as the vehicle's own operating system rather than a projected phone |
| AGL | Automotive Grade Linux, the Linux Foundation's shared automotive Linux base |
| Broker | The Eclipse KUKSA databroker, which serves the VSS tree over gRPC |
| DMS | Driver monitoring system, here a camera-based edge-AI service |
| DRS, ERS | Drag reduction system and energy recovery system, motorsport telemetry rendered by the cluster |
| HARA | Hazard analysis and risk assessment, ISO 26262-3 |
| MRM | Minimum-risk manoeuvre, the controlled stop triggered by sustained drowsiness |
| Overlay | A VSS mechanism for adding or changing signals without forking the upstream specification |
| Strip display | A 2560 x 720 automotive-aspect panel; on the desk rig a Corsair Xeneon Edge |
| TARA | Threat analysis and risk assessment, ISO/SAE 21434 |
| VSS | COVESA Vehicle Signal Specification, the standard signal catalogue |
