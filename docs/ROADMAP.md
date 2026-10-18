# Meditrace Roadmap and Test Plan

This is a proposed plan, not a claim that these tasks are already complete.

## Phase 0 — Define scope
- [ ] Select the first target user and deployment setting.
- [ ] Decide static demo, mobile prototype, or full-stack pilot.
- [ ] Decide how records move between separate patient/provider devices.
- [ ] Define QR token, provider identity, expiry, revocation, and offline behavior.
- [ ] Use fictional data only.

## Phase 1 — End-to-end MVP
- [ ] Patient profile and chronological timeline.
- [ ] Consent QR simulation with scope and expiry visible.
- [ ] Doctor adds an encounter and prescription.
- [ ] Pharmacy records full/partial dispensing separately.
- [ ] AI-style summary references dates and source records.
- [ ] Expiry/revocation demonstration and mock-data warning.
- [ ] Automated tests for medication-event distinctions.

## Phase 2 — Privacy and reliability
- [ ] Local persistence, schema migrations, encrypted report storage.
- [ ] Secure key storage, backup/recovery, and deletion behavior.
- [ ] Provider identity verification and least privilege.
- [ ] QR expiry, revocation, replay defense, and audit trail.
- [ ] Offline synchronization and conflict handling, if cross-device sharing is required.
- [ ] Device-loss and cache-clearing tests.

## Phase 3 — Clinical readiness
- [ ] Clinician review of summary format.
- [ ] Synthetic/de-identified evaluation set.
- [ ] Evaluate factuality, omissions, source support, uncertainty, and language coverage.
- [ ] Accessibility and low-end device tests.
- [ ] Qualified privacy, legal, security, and clinical review before real-world pilot.

## Phase 4 — Later expansion
- [ ] Lab integrations and validated structured result extraction.
- [ ] Multilingual UX.
- [ ] Validated drug-interaction alerts.
- [ ] Trend charts, voice-to-note, wearable/telemedicine integrations if justified.
- [ ] Predictive features only after evidence, clinical validation, and governance.

## Acceptance tests

### Consent
- Invalid, expired, and revoked tokens are denied according to the chosen architecture.
- A QR payload contains no readable medical details.
- A provider sees only the data permitted for the consent purpose.
- Session expiry clears sensitive cached data according to policy.

### Medication records
- Prescription can exist without a dispensing event.
- Partial dispensing is supported.
- Substitution does not erase the original prescription.
- Patient-reported use is stored separately.
- Missing dispensing data never automatically becomes “patient did not take medicine.”

### AI summaries
- Material claims link to source records and dates.
- Missing or conflicting information is called out.
- AI does not silently alter the official record.
- Clinician can correct or reject a draft.
- No AI output is presented as a definitive diagnosis.

### Offline/sync
- Offline records persist locally.
- Retried sync does not duplicate events.
- Concurrent edits produce a defined conflict result.
- Backup restore verifies integrity.
- Disabling backup follows the documented deletion policy.

## Metrics to evaluate
| Metric | Method | Purpose |
|---|---|---|
| Time to review history | Timed task with and without summary | Test whether time is saved |
| Source-supported AI claims | Human-reviewed sample | Test grounding |
| Prescription/dispensing comparison accuracy | Known synthetic cases | Test core differentiator |
| Consent enforcement | Negative security tests | Test privacy boundary |
| Offline data integrity | Disconnect/reconnect tests | Test reliability |
| Summary usefulness | Clinician feedback rubric | Test relevance |
| Task completion | Usability sessions | Test accessibility |

Do not claim improved outcomes until appropriately measured.