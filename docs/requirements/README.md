# Requirements

Requirement documents for the Sauce Scramble demo shop. **Start with 00.**

| # | Document | What it covers | Main readers | Status |
| --- | --- | --- | --- | --- |
| 00 | [Consolidated requirements](00-consolidated-requirements.md) | Goals, roles, summaries, data model, scope, open questions, build order, traceability | Everyone | Draft v0.1 |
| 01 | [Product features and user stories](01-product-features-user-stories.md) | Epics E1–E6, user stories with acceptance criteria, domain terms, seed data, out of scope | Developers, testers, trainers | Draft v0.1 |
| 02 | [UI/UX requirements](02-ui-ux-requirements.md) | Vision, design principles, visual direction, sitemap, flows, screens S1–S16, accessibility, tone, Streamlit limits, deliverables | UI/UX designer, developers | Draft v0.1 |
| 03 | [Non-functional requirements](03-non-functional-requirements.md) | Authentication, security (RLS, secrets), privacy and child safety, data integrity, performance, accessibility, reliability, code quality, logging, deployment, scalability limits, constraints and assumptions | Developers, testers, trainers | Draft v0.1 |

## ID conventions

| Prefix | Meaning | Defined in |
| --- | --- | --- |
| `E1`–`E6` | Epic | 01 |
| `US-101`… | User story (`US-<epic><number>`) | 01 |
| `AC1`… | Acceptance criterion within a story | 01 |
| `S1`–`S16` | Screen | 02 |
| `F1`–`F9` | User flow | 02 |
| `NFR-<AREA>-nn` | Non-functional requirement, e.g. `NFR-SEC-01` (areas: AUTH, SEC, PRIV, DATA, PERF, UX, REL, MAINT, LOG, DEP, SCALE) | 03 |
| `C-01`…, `A-01`… | Constraint, assumption | 03 |
| `Q1`… | Open question | 00 |
| `M0`–`M8` | Build milestone | 00 |

When a document changes, update its status and check the traceability table in 00.
