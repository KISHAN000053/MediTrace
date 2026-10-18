# Meditrace — Product Overview

## 1. Vision

Build a patient-controlled, continuous healthcare record that follows a person across visits and care settings. Meditrace connects patient history, clinical encounters, prescriptions, pharmacy dispensing, lab reports, and AI-assisted summaries in one coherent timeline.

**Mission:** Reduce the effort required to reconstruct a patient's history and help care teams make better-informed decisions from more complete records.

## 2. The problem in plain English

A person's health information may be spread across paper prescriptions, lab PDFs, pharmacy receipts, different clinics, and the patient's memory. At a new visit, the patient may not remember the names or doses of previous medicines. A doctor may not know what was prescribed last time, what tests were performed, or whether symptoms have returned. A prescription also does not prove that the medicine was purchased, dispensed, or taken.

The result can be repeated explanations, missing context, avoidable administrative work, and difficulty understanding why a treatment did or did not help. Meditrace aims to make this history easier to carry, share, and review.

## 3. Product concept

Every patient has a digital medical record and can generate a QR-based sharing credential. The QR code is an access key governed by consent—not a public link to the patient's entire record. A provider must be authenticated and receive only the permissions needed for the task. Access expires and can be revoked.

The record can include:
- Patient-provided profile information and relevant emergency details.
- Allergies and other clinically important history.
- Visits, symptoms, clinician findings, diagnoses, and treatment plans.
- Prescriptions, including medicine, dosage, frequency, duration, and prescribing clinician.
- Pharmacy dispensing records, including what was supplied, when, and in what quantity.
- Lab reports and their metadata.
- Follow-up notes and treatment progress.

All records should be timestamped, attributable to their source, and displayed chronologically. Corrections should preserve an audit trail rather than silently erasing clinical history.

## 4. End-to-end workflows

### A. Patient onboarding and record ownership
1. Patient creates an account or initializes a local patient record.
2. Identity and account-recovery setup are completed according to the deployment model.
3. The app creates a patient identifier and a QR-based consent-sharing flow.
4. Patient adds existing prescriptions, allergies, and reports where appropriate.
5. Patient can view the timeline, manage sharing permissions, and control optional encrypted backup.

**Important design distinction:** A stable patient identifier and a time-limited sharing QR are not necessarily the same thing. Prefer a stable internal ID plus short-lived, revocable consent tokens rather than printing a permanent bearer credential on a card.

### B. Doctor consultation
1. Doctor signs in and is authenticated as an authorized provider.
2. Patient chooses to share records and presents a consent QR code.
3. System validates token, expiry, provider identity, and permissions.
4. Doctor reviews relevant diagnoses, allergies, past prescriptions, dispensing records, and lab reports.
5. Doctor records symptoms, findings, diagnosis, prescription, and follow-up instructions.
6. The new encounter is timestamped and added to the timeline.
7. The access session expires or is explicitly ended; cached sensitive data is cleared according to policy.

### C. Pharmacy visit
1. Pharmacist authenticates and scans the patient's current consent QR.
2. Pharmacist views only the prescription details needed for dispensing.
3. Pharmacist records which prescribed items were actually dispensed, along with quantity and date.
4. Partial fills, substitutions, unavailable items, and items not dispensed are represented explicitly.
5. The system stores the dispensing event separately from the original prescription.

**Terminology matters:** “Purchased” and “dispensed” are not always identical, and neither proves that the patient took the medicine. The data model should distinguish prescribed, dispensed/supplied, patient-reported use, and—if later supported—adherence estimates. Avoid accusing or blaming patients based on incomplete data.

### D. Laboratory report upload
1. Authorized lab staff verify the patient and consent.
2. They upload a report and enter test name, collection/report date, lab name, and relevant metadata.
3. The file is stored securely and linked to the patient timeline.
4. The patient and authorized clinicians can view the report according to permissions.

### E. Next visit and AI summary
1. The clinician opens a consent-authorized record.
2. The AI prepares a structured summary from available source records.
3. The summary may highlight recent visits, active medicines, allergies, recurring documented symptoms, test trends, prescription changes, and gaps between prescribed and recorded dispensing.
4. Each finding links back to its source and date; missing data is identified as missing rather than inferred.
5. The clinician checks the summary against the underlying records and makes the clinical decisions.

Example summary (illustrative, not a diagnosis): “Fever was documented at two visits in the past month. An antibiotic was prescribed on the first visit; the pharmacy record shows a partial dispensing event. No follow-up test result is recorded. Confirm medicine use and clinical progress with the patient.”

## 5. Core innovation: prescription vs. dispensing

| Record type | What it means | What it does not prove |
|---|---|---|
| Prescription | A clinician ordered a medicine and specified instructions | That the medicine was bought, supplied, or taken |
| Dispensing record | A pharmacy recorded supplying some or all of a medicine | That the patient took it as directed |
| Patient-reported use | The patient reports how they used the medicine | Perfect adherence or independently verified ingestion |
| Adherence estimate | A cautious calculation from available evidence | A definitive judgment about patient behavior |

Keeping these events separate helps clinicians ask better follow-up questions and avoid assuming that an unfilled prescription means refusal or negligence.

## 6. AI capabilities

### Initial AI scope
- Produce a concise, chronological visit-history summary.
- Extract active and recently recorded medicines from structured data.
- Summarize lab reports while retaining links to the originals.
- Highlight repeated documented symptoms and important changes.
- Compare prescriptions with recorded dispensing events.
- Flag missing information, contradictory entries, and records that need clinician verification.

### Later possibilities
- Clinician-configured drug-interaction alerts using a validated drug knowledge source.
- Trend visualizations for structured measurements such as blood pressure or glucose.
- Speech-to-note assistance with review before saving.
- Multilingual patient-facing explanations.
- Integration with wearables, telemedicine, insurance, and other clinical systems where lawful and technically appropriate.

### AI safety rules
- AI is decision support, not an autonomous diagnosing or prescribing authority.
- Do not invent facts or convert absence of a record into proof that an event did not happen.
- Show source records and dates for material claims.
- Distinguish recorded facts, patient reports, model-generated interpretations, and missing data.
- Require clinician review before AI-generated content becomes part of the clinical record or influences care.
- Evaluate factuality, omissions, bias, and performance across languages and populations before deployment.

## 7. Security, privacy, and consent

The repository's existing design is privacy-first and offline-first. It proposes local storage, optional encrypted backup, QR-based consent, role-based interfaces, and automatic session expiry. These are design intentions that must be implemented and tested.

Recommended controls:
- Keep sensitive clinical details out of QR payloads; use opaque, short-lived, revocable tokens.
- Authenticate providers separately; scanning a QR alone should not establish unrestricted access.
- Apply least privilege and purpose-specific permissions (for example, prescription dispensing versus lab upload).
- Encrypt records and backups; use platform secure storage for keys and design recovery carefully.
- Maintain tamper-evident access and change logs.
- Expire sessions, revoke consent promptly, and clear sensitive provider-side caches.
- Minimize data collection, define retention and deletion behavior, and document backup lifecycle.
- Threat-model lost phones, screenshots of QR codes, token replay, stolen credentials, malicious insiders, and compromised devices.
- Design for applicable Indian data-protection and health-record obligations with qualified legal/security review before real patient use.

**Architecture consideration:** The current requirements favor records stored locally on the patient's device and no central-server dependency. A remote doctor or pharmacy cannot automatically read or write that local record without a secure exchange path. A practical implementation must explicitly choose and document how records move between devices—such as encrypted export/import, a relay, or a consent-controlled synchronization service—and how conflicts, revocation, audit trails, and offline updates are handled. Do not promise seamless cross-provider synchronization until this is designed and tested.

## 8. Offline-first and rural accessibility

The existing design prioritizes:
- Local medical record storage, with SQLite proposed for structured records.
- Encrypted file storage for reports.
- Core read/write operations when internet access is unavailable.
- A queue for optional encrypted backup and later synchronization.
- Large, high-contrast controls and straightforward role-based screens.
- Support for low-end devices and intermittent connectivity.

Offline provider-to-patient updates are a separate engineering challenge from offline local storage. The product must make sync state visible, detect conflicting edits, and avoid silently losing or duplicating records.

## 9. Users and value

- **Patients:** One organized record, easier sharing, fewer lost paper records, and control over access.
- **Doctors:** Less manual history gathering and a clearer view of previous treatment and test results.
- **Pharmacists:** More legible prescriptions and a structured dispensing log.
- **Laboratories:** A clear way to attach reports to a patient timeline.
- **Clinics/hospitals:** Potentially improved continuity and less administrative duplication, depending on integration.

## 10. Illustrative scenario

A clinician prescribes an antibiotic, a blood-pressure medicine, and a supplement. A pharmacy records dispensing the antibiotic and blood-pressure medicine but does not record dispensing the supplement. At the next visit, Meditrace summarizes the prescription and the available dispensing events, clearly noting that the supplement has no dispensing record. The doctor asks the patient what happened and decides the next step based on the clinical context.

The system must not conclude that the patient definitely did not take a medicine solely because no pharmacy record exists; they may have obtained it elsewhere or the data may be incomplete.

## 11. Suggested technology (options, not confirmed implementation)

The existing design specification names:
- Mobile app: role-based interface.
- Local data: SQLite for structured records and encrypted file storage for PDFs.
- Encryption: authenticated encryption such as AES-GCM, with carefully managed keys.
- Optional backup: encrypted backup storage; Google Drive is mentioned in the current design as a possible provider.
- QR and barcode scanning: consent-token QR flow and medicine barcode lookup.
- Potential broader stack options previously considered: Flutter or React Native; Node.js or Django; PostgreSQL or MongoDB; a medical summarization model/LLM with source-grounded outputs.

These are candidates to evaluate, not a final architecture decision. Choose based on the actual MVP, deployment model, security review, offline requirements, cost, and team skill.

## 12. Suggested MVP

Prioritize a demo that proves the core workflow rather than attempting every future feature:
1. Patient timeline with manual entry for visits, allergies, prescriptions, and reports.
2. Provider roles with demo authentication.
3. QR consent flow with scoped permissions and visible expiry.
4. Doctor adds a consultation and prescription.
5. Pharmacy records full or partial dispensing separately from prescription.
6. Timeline shows the prescription and dispensing events distinctly.
7. AI creates a source-linked summary from the demo data.
8. Audit log, expiry/revocation demonstration, and clear offline/sync status.

Use synthetic or properly de-identified demo data. Do not use real patient data in a hackathon demo without a proper lawful basis, security controls, and informed authorization.

## 13. Roadmap

- **Phase 1 — Demonstration:** Patient record, QR consent, doctor visit, prescription, dispensing record, and source-linked AI summary.
- **Phase 2 — Reliability:** Encrypted local storage, backup/recovery, session expiry/revocation, audit trail, conflict handling, and usability testing.
- **Phase 3 — Pilot readiness:** Identity verification, threat model, security review, privacy/legal review, clinical workflow validation, and limited supervised pilot.
- **Phase 4 — Expansion:** Lab integrations, multilingual UX, validated interaction alerts, structured health trends, voice-to-note, telemedicine, and interoperable integrations.

## 14. Success measures to test

- Time a clinician needs to understand a returning patient's history.
- Percentage of demo visits with a complete, source-linked summary.
- Correctness of prescription-versus-dispensing comparisons.
- Rate of AI claims supported by cited records.
- QR consent expiry and revocation success rate.
- Record loss/duplication during offline edits and synchronization.
- Patient and clinician usability, trust, and accessibility feedback.

## 15. Positioning

**One-line pitch:** Meditrace is an AI-powered QR healthcare ecosystem that connects patients, doctors, pharmacies, and laboratories through a patient-controlled medical timeline and generates source-linked summaries for better-informed consultations.

**Investor framing:** Meditrace addresses fragmented medical records and poor continuity of care with a privacy-first record layer, consent-controlled sharing, prescription-versus-dispensing tracking, and AI-assisted clinical summaries. The opportunity is to reduce the effort required to understand a patient's history while building trust through patient control and transparent data provenance.

**Hackathon framing:** Demonstrate one complete patient journey: consent QR → doctor consultation → prescription → pharmacy dispensing log → AI summary at the next visit.

## 16. Current status and open decisions

The repository currently contains product requirements and a design document for an offline-first healthcare record system. This overview captures the product vision and proposals discussed; it does not imply that every described feature is implemented.

Open decisions include:
- Whether the first product is fully local/offline or uses a secure sync service for multi-provider access.
- How identity verification and provider credentialing work.
- The precise QR token format, revocation behavior, and offline expiry enforcement.
- Key recovery and encrypted-backup deletion semantics.
- The supported medicine catalogue and source of drug information.
- How AI summaries are evaluated, sourced, logged, and approved.
- The first target deployment setting and relevant compliance obligations.
