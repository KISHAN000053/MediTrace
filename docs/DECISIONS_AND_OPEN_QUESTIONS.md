# Meditrace Decision Log & Open Questions

This document is intentionally candid. It distinguishes details explicitly present in the repository/current project context from assumptions that still need a decision.

## 1. Confirmed project direction in available context

| Topic | Current understanding | Status |
|---|---|---|
| Product name | Meditrace | [VISION] |
| Core idea | AI-powered QR-based healthcare record ecosystem | [VISION] |
| Main actors | Patients, doctors, pharmacies; labs are also in the existing spec | [VISION / EXISTING SPEC] |
| Core pain point | Doctors repeatedly reconstruct patient history from paper and fragmented records | [VISION] |
| QR purpose | Patient uses QR to initiate sharing/access to medical record | [VISION] |
| Medication distinction | Prescribed items and medicines actually purchased/dispensed must be separate | [VISION] |
| AI purpose | Summarize previous records and surface useful trends/gaps for clinician review | [VISION] |
| Existing technical preference | Offline-first, privacy-first, local patient data, optional encrypted backup | [EXISTING SPEC] |
| Local database candidate | SQLite | [EXISTING SPEC] |
| Backup candidate | Encrypted Google Drive backup | [EXISTING SPEC] |
| QR session | Default 24 hours; configurable 1 hour to 30 days in original requirements | [EXISTING SPEC] |
| Crypto named in design | AES-256-GCM; PBKDF2-SHA-256; TLS 1.2+ | [EXISTING SPEC, requires security review] |
| Broader stack options | Flutter/React Native; Node.js/Django; PostgreSQL/MongoDB; medical NLP/LLM | [OPTIONS DISCUSSED, not a final choice] |
| Hackathon title direction | Meditrace — AI-Powered QR Healthcare Records and Medication Tracking | [PITCH COPY] |
| Hackathon explanation | Plain-English explanation covering fragmented records, QR sharing, pharmacy dispensing, and AI summaries | [PITCH COPY] |

## 2. Core product rules to preserve

1. A prescription means a clinician ordered something; it does not prove purchase or use.
2. A pharmacy dispensing record means a pharmacy recorded supplying something; it does not prove ingestion.
3. Missing dispensing data is a gap in available records, not proof of non-adherence.
4. QR code is a consent/access mechanism, not a public URL containing medical information.
5. AI outputs should link to source records and be reviewed by a qualified clinician.
6. Medical records are sensitive; use synthetic data in public demos.
7. Clearly label features as implemented only after checking source code and tests.
8. Preserve patient control and least-privilege access.
9. Offline-first local storage and multi-device access are different requirements; sharing needs an explicit secure data exchange design.

## 3. High-impact architecture decisions

### D1 — How do separate devices exchange records?
**[OPEN DECISION — BLOCKS REAL MULTI-PROVIDER WORKFLOW]**

The original design prefers local-only storage and no central server. The product vision expects doctors and pharmacies to use their own devices. Options:
- Encrypted patient-mediated export/import.
- Secure relay/synchronization service that stores ciphertext.
- A provider-side system that exchanges encrypted record bundles.
- A carefully designed hybrid approach.

Decide: key ownership, device authorization, revocation behavior while offline, conflict resolution, audit logs, duplicate detection, deletion semantics, and recovery.

### D2 — What exactly does the QR contain?
**[OPEN DECISION — SECURITY CRITICAL]**

Recommended direction: opaque, short-lived, high-entropy credential or reference; no readable diagnosis, medicine list, or direct unprotected patient ID. Define token audience, allowed actions, expiration, nonce/replay defense, revocation, and signature/MAC strategy. If there is no server, define how validation and revocation work securely.

### D3 — What is the identity verification model?
**[OPEN DECISION]**

Patient account verification, provider credential verification, pharmacy identity, lab identity, account recovery, and lost-device handling are not yet fully decided. A role selector alone is not authentication.

### D4 — What is the canonical medication vocabulary?
**[OPEN DECISION]**

Choose a reliable medicine catalogue and coding strategy. Support brand/generic names, strengths, forms, units, dose, route, frequency, duration, and substitution. Avoid relying on unverified barcode data alone.

### D5 — How will AI be grounded and evaluated?
**[OPEN DECISION]**

Define structured input, source IDs, date handling, active-medication logic, missing-data language, evaluation set, clinician review, model/data retention, provider policy, cost/latency limits, and fallback when AI is unavailable. Never let the AI silently change clinical records.

### D6 — Which deployment and compliance path?
**[OPEN DECISION]**

Choose target users and pilot setting. Obtain qualified review of applicable Indian data-protection, health-record, medical-device/software, clinical, and professional obligations before using real patient data. This document is not legal advice.

### D7 — What is the actual MVP platform?
**[OPEN DECISION]**

The original spec describes a mobile application. The repository currently has requirements/design and documentation; no production app implementation was verified during the knowledge-base expansion. Choose a framework only after agreeing on demo scope and device needs.

### D8 — How should emergency access work?
**[OPEN DECISION / FUTURE]**

If considered, define who may invoke it, which data is exposed, justification, alerting, duration, audit, patient notification, and retrospective review. Do not add a silent universal bypass.

### D9 — How is backup deletion and key recovery handled?
**[OPEN DECISION]**

The original requirements expect disabling backup to delete cloud copies. This needs verification against provider versions/trash/replicas, backup retention, and encryption-key loss. Patient-controlled keys can protect privacy but can also make recovery impossible if lost.

### D10 — Product and business model
**[OPEN DECISION]**

No validated pricing, paying customer, market size, revenue model, partner commitment, or fundraising target is recorded in accessible context. Candidate customers include clinics, hospitals, pharmacies, labs, and patient users; validate through interviews before claiming traction.

## 4. Risks / assumptions register

| Risk or assumption | Why it matters | Mitigation / next evidence |
|---|---|---|
| Patient QR copied or photographed | Unauthorized access | Short expiry, audience binding, authentication, revocation |
| Provider device is shared or stolen | Exposure of medical data | Secure storage, session expiry, cache clearing, device threat model |
| Offline token cannot be revoked immediately | Old access may persist | Define offline limits and maximum data/cache lifetime |
| Two devices edit same record | Data loss or inconsistency | Versioning, append-only events, conflict UI and tests |
| AI invents a clinical fact | Patient safety | Source-linked claims, schema validation, abstention and review |
| Dispensing data is incomplete | False adherence conclusion | Label missing data; ask patient; show provenance |
| Patient record is lost with phone | Loss of continuity | Tested encrypted backup/recovery and recovery UX |
| Report uploaded to wrong patient | High-impact privacy/clinical error | Identity confirmation and correction workflow |
| Drug catalogue is stale | Wrong medicine metadata | Source/versioning and review |
| Compliance assumed from encryption alone | Legal and operational gaps | Qualified review and documented controls |
| Scope balloons before MVP | No complete demo | Prioritize one end-to-end workflow |

## 5. History and knowledge completeness note

The current project context included the Meditrace vision and the recent hackathon/fundraising description conversation. The accessible long-term conversation search did not return older Meditrace-specific chat records. Therefore, this repository should not claim to contain a verbatim export of every prior project chat. Existing requirements and design files were preserved and used as source material. If a local chat archive or other notes contain additional Meditrace decisions, add them here with dates and provenance rather than guessing.

## 6. Decision template

Append decisions using this format:

```text
### YYYY-MM-DD — Short decision title
Status: Proposed / Accepted / Rejected / Superseded
Context:
Options considered:
Decision:
Reason:
Consequences:
Owner:
Follow-up / evidence:
```
