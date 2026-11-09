# ROV Inspection Software QA Portfolio — Pulse RemoteBridge

Real production QA work by **Abdelrahman Ali**, SQA Engineer — redacted for public sharing. Company and product names below have been changed; the system design, workflows, test artifacts, and defect data are drawn directly from work I actually did.

> **A note on anonymization:** I test proprietary offshore inspection software under an internal-use classification. The product names, company name, and any individuals referenced in this repository are fictionalized or generalized. The testing approach, documentation structure, test coverage, and defect-tracing work are real.

---

## The system under test

**Pulse RemoteBridge** is a remote-bridge application: it extends an existing offshore pipeline/structure inspection suite ([**Pulse Inspection Suite**](https://github.com/abd0ali/pulse-inspection-suite-qa-portfolio), a companion project in this portfolio) so an onshore office can operate a live inspection session on a vessel at sea as if the operator were physically on board — without any inspection-specific logic being duplicated. RemoteBridge issues the same commands the underlying applications already expose, over a secure network link.

| App | Role |
|---|---|
| **RemoteBridge** | The office-side orchestrator client. Defines the office↔vessel connection, pushes vessel configuration, remotely initializes cameras, drives overlay/recording/event logging, and manages inspection data transfer back to the office. |
| **SiteSync (Office / Vessel)** | Registers vessels, maps project/pipe catalog data between the office and vessel databases, and holds the active vessel session. |
| **DataLink** | Captures live sensor readings on the vessel and streams them to the office via RemoteBridge. |
| **MissionHub** | Executes the camera, overlay, and recording commands RemoteBridge issues remotely. |
| **StreamCap** | Runs per camera on the vessel; captures, displays, records video, and burns in the overlay. |
| **Broadcast Server / Client** | Streams live video from the vessel to the office for real-time remote viewing. |

**Typical test environment:** 1 office PC (RemoteBridge, SiteSync Office, Broadcast Client), 1 vessel/logging PC (SiteSync Vessel, MissionHub, DataLink), 1–3 StreamCap PCs (one per camera), bridged over a virtual LAN / site-to-site VPN. SQL Server backend. No physical sensors on-site for testing — sensor strings are simulated with a lightweight IP/port sender tool.

## My role

- Reverse-engineered an undocumented remote-operations application into a **14-stage operational sequence** matching how it's actually deployed and used, and built every test artifact around that same sequence — so defect tracking, retest, and regression all share one mental model of the system
- Designed and executed **manual, smoke, regression, negative/edge, and database-validation** suites covering all 14 stages
- Logged and workflow-sequenced defects so retest and regression happen in one deliberate pass through the application, not a scattershot re-check
- Built this as the second project in a growing formal QA documentation practice that didn't exist before I introduced it — designed to extend to each new system, not be rebuilt from scratch each time

## What's in this repository

| File | What it is |
|---|---|
| [`docs/Master-Test-Plan.docx`](docs/Master-Test-Plan.docx) | Scope, system overview, test levels, environment, the full 14-stage operational sequence, entry/exit criteria, risk register |
| [`docs/Test-Strategy.docx`](docs/Test-Strategy.docx) | Testing principles, per-component strategy, coverage priorities, feature coverage map across all 14 stages |
| [`docs/Test-Procedures.docx`](docs/Test-Procedures.docx) | 14 self-contained step-by-step SOPs, one per operational stage, each with preconditions, steps, and verification |
| [`test-cases/Test-Case-Repository.xlsx`](test-cases/Test-Case-Repository.xlsx) | 87 test cases (Smoke, Regression, Negative/Edge, DB Validation) plus the full defect log, both organized by the same 14-stage sequence |

## A few things I'm proud of here

- Took a remote-operations application with no formal documentation and organized its entire test surface around the actual order it's deployed and used — not an arbitrary feature list.
- Kept every artifact — test plan, procedures, cases, and defects — cross-referenced to the same 14-stage sequence, so a new tester (or a hiring manager) can trace from "what does Stage 10 cover" straight through to "which test cases and which open defects live there."
- Companion project to [Pulse Inspection Suite](https://github.com/abd0ali/pulse-inspection-suite-qa-portfolio) — same house documentation style, same QA discipline, applied to a second, architecturally different system.
- All of it built and run manually, release after release, with no dedicated test-automation budget — the discipline is in the process, not the tooling.
