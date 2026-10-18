# Meditrace Feature Catalogue

This is the broad inventory of features and ideas captured from available project context. Status labels prevent a brainstorm from being mistaken for shipped functionality.

## Status legend

- **[VISION]** Part of the core product concept.
- **[EXISTING SPEC]** Appears in the repository's original requirements/design.
- **[PROPOSED]** A reasonable implementation candidate, not confirmed built.
- **[FUTURE / SPECULATIVE]** Explicitly later-stage or exploratory.
- **[OPEN DECISION]** Needs a decision before implementation.
- **[IMPLEMENTED]** Use only after confirming source code and tests.

## A. Patient experience

| Feature | Status | Notes |
|---|---|---|
| Patient digital medical profile | [VISION] | A longitudinal record, not just a single visit |
| Patient identity / stable patient ID | [VISION] | Keep stable internal ID distinct from share token |
| Patient QR sharing | [VISION] [EXISTING SPEC] | QR initiates consent-based sharing; should not expose raw medical data |
| Time-limited consent | [EXISTING SPEC] | Original spec: default 24 hours, configurable 1 hour–30 days |
| Revoke active access | [EXISTING SPEC] | Immediate revocation is hard when devices are fully offline; protocol must define it |
| Chronological timeline | [VISION] [EXISTING SPEC] | Encounters, prescriptions, reports, dispensing events |
| Allergy/history section | [PROPOSED] | Include provenance and last-confirmed date |
| Upload existing reports | [VISION] [EXISTING SPEC] | PDF storage and metadata |
| Download/view reports | [EXISTING SPEC] | Must honor permissions and local security |
| Offline read/write | [EXISTING SPEC] | Core local operation |
| Optional encrypted backup | [EXISTING SPEC] | Google Drive named as candidate provider |
| Restore after device loss | [EXISTING SPEC] | Key recovery and data recovery need careful design |
| Patient access/share history | [PROPOSED] | Show who accessed what, when, and why |
| Patient correction / dispute workflow | [PROPOSED] | Correct errors without silently deleting clinical audit history |
| Emergency access | [FUTURE / SPECULATIVE] | Requires explicit governance, scope, alerting and audit |

## B. Doctor and clinician experience

| Feature | Status | Notes |
|---|---|---|
| Scan patient consent QR | [VISION] [EXISTING SPEC] | Provider authentication remains necessary |
| Read prior medical history | [VISION] | Scoped to consent |
| Add consultation note | [VISION] [EXISTING SPEC] | Symptoms, findings, diagnosis, plan |
| Create prescription | [VISION] [EXISTING SPEC] | Medicine, dose, frequency, duration, quantity |
| Review lab reports | [VISION] | Link summary to original result |
| Review prior medication events | [VISION] | Distinguish prescription, dispensing, and reported use |
| AI-generated visit preparation summary | [VISION] | Concise and source-linked, clinician-reviewed |
| Recurring symptom / trend highlights | [VISION] | Only from recorded evidence; avoid unsupported inference |
| Drug interaction alert | [FUTURE / SPECULATIVE] | Needs validated drug knowledge source and clinical validation |
| Predictive disease/risk detection | [FUTURE / SPECULATIVE] | High-stakes; needs evidence, validation, governance |
| Personalized treatment suggestions | [FUTURE / SPECULATIVE] | Not for autonomous decisions; high clinical risk |
| Voice-to-medical-notes | [FUTURE / SPECULATIVE] | Consent, transcription quality, review and retention matter |
| Clinician correction of AI summary | [PROPOSED] | Keep AI draft and clinician edits attributable |

## C. Pharmacy features

| Feature | Status | Notes |
|---|---|---|
| Scan patient consent QR | [VISION] [EXISTING SPEC] | Access limited to dispensing purpose |
| View relevant prescription | [VISION] | Avoid broad access to unrelated notes |
| Medicine barcode scanning | [EXISTING SPEC] | Lookup catalogue availability must be evaluated |
| Medicine name/dosage/expiry/manufacturer lookup | [EXISTING SPEC] | Only as reliable as source catalogue |
| Record dispensed items | [VISION] [EXISTING SPEC] | Date and quantity |
| Partial fill / unavailable item | [PROPOSED] | Record item-level status explicitly |
| Substitution recording | [PROPOSED] | Preserve original prescribed item and substituted item |
| Bill / cost calculation | [EXISTING SPEC] | Original requirements include billing record |
| Distinguish prescribed from purchased/dispensed | [VISION] | Core differentiator |
| Track actual ingestion | [OPEN DECISION] | Cannot be inferred from pharmacy dispensing alone |

## D. Laboratory features

| Feature | Status | Notes |
|---|---|---|
| Scan patient consent QR | [EXISTING SPEC] | Scope should permit only required upload/metadata |
| Upload PDF report | [EXISTING SPEC] | Encrypted file storage |
| Record test name, date and lab | [EXISTING SPEC] | Include collection and report dates separately where possible |
| Link report to timeline | [VISION] [EXISTING SPEC] | Patient and authorized clinician can view |
| Extract test values with AI/OCR | [FUTURE / SPECULATIVE] | Preserve original report; validate units/reference ranges |
| Trend charts for structured values | [FUTURE / SPECULATIVE] | Handle unit changes and lab-specific reference ranges |

## E. AI and medical intelligence

| Feature | Status | Notes |
|---|---|---|
| Concise patient-history summary | [VISION] | Core AI capability |
| Summarize prior diagnoses/visits | [VISION] | Ground claims in dated records |
| Summarize prescriptions | [VISION] | Show active/previous and avoid guessing current status |
| Compare prescription and dispensing events | [VISION] | Missing data is not proof of non-adherence |
| Identify recurring documented symptoms | [VISION] | Highlight patterns, not diagnosis |
| Summarize lab reports | [VISION] | Link original files/results |
| Identify missing/inconsistent records | [PROPOSED] | Show uncertainty |
| Generate source links/citations | [PROPOSED] | Needed for verification |
| Drug interaction checks | [FUTURE / SPECULATIVE] | Validated source, versioning, clinical oversight |
| Predictive disease detection | [FUTURE / SPECULATIVE] | Research/validation required |
| Risk scoring | [FUTURE / SPECULATIVE] | Explainability, calibration, fairness and governance required |
| Personalized prevention suggestions | [FUTURE / SPECULATIVE] | Evidence-based, clinician-reviewed |
| Multilingual patient explanations | [FUTURE / SPECULATIVE] | Validate translations of medical instructions |
| LLM-powered conversational interface | [FUTURE / SPECULATIVE] | Permissions, hallucination and audit controls |

## F. Platform, security, and accessibility

| Feature | Status | Notes |
|---|---|---|
| Role-specific UI | [EXISTING SPEC] | Patient, doctor, pharmacist, lab technician |
| Large controls / high contrast | [EXISTING SPEC] | Original spec specifies minimum 48×48 dp buttons |
| Low-end-device support | [EXISTING SPEC] | Original design targets constrained devices |
| Offline-first local store | [EXISTING SPEC] | SQLite proposed |
| Encryption at rest / encrypted attachments | [EXISTING SPEC] | Review key management before production |
| Encrypted optional cloud backup | [EXISTING SPEC] | Original design names Google Drive |
| Session expiry and cache clearing | [EXISTING SPEC] | Ensure logout and role switching clear sensitive cache |
| Audit trail | [PROPOSED] | Who accessed/changed data, when, purpose |
| Secure provider verification | [PROPOSED] | Credentials and provider roles must be validated |
| End-to-end encryption | [VISION / PROPOSED] | Exact threat model and key exchange remain open |
| Blockchain records | [FUTURE / SPECULATIVE] | Not needed for MVP without a clear requirement |
| Wearable integration | [FUTURE / SPECULATIVE] | Consent, device accuracy and provenance |
| Telemedicine | [FUTURE / SPECULATIVE] | Clinical, privacy and regulatory work |
| Insurance integration | [FUTURE / SPECULATIVE] | High sensitivity; purpose limitation needed |
| Interoperability standards | [FUTURE / PROPOSED] | Evaluate FHIR and Indian health ecosystem requirements with experts |
| Emergency mode | [FUTURE / SPECULATIVE] | Must define break-glass access and post-event audit |

## G. Startup / ecosystem direction

- **[VISION]** Connect patients, doctors, pharmacies, and labs through a common record journey.
- **[VISION]** Reduce time clinicians spend reconstructing patient history.
- **[VISION]** Improve record continuity and make treatment events easier to review.
- **[PROPOSED]** Start with one end-to-end demo journey before broad integration.
- **[OPEN DECISION]** Initial target: individual patients, a clinic, a pharmacy partner, a campus/college pilot, or rural health setting.
- **[OPEN DECISION]** Business model: B2B clinic subscription, provider tools, institutional deployment, or another model. No pricing/revenue model has been confirmed in available context.
- **[OPEN DECISION]** Go-to-market and fundraising milestones need customer interviews and evidence; do not claim market validation yet.

## MVP priority (suggested)

1. Synthetic patient record and chronological timeline.
2. Consent QR simulation with visible scope and expiry.
3. Doctor adds visit and prescription.
4. Pharmacy records full/partial dispensing separately.
5. AI-like demo summary references source events (real LLM integration comes later).
6. Access history, expiry/revocation demo, and clear mock-data warning.
7. Tests for medication-event distinctions and consent rules.

Do not build speculative prediction or autonomous treatment recommendations before the basic record exchange and safety foundations are reliable.
