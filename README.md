# Meditrace

**AI-powered, QR-based healthcare records and medication tracking.**

> **If you clone this repository to recover the project context, start with the [Knowledge Base Index](docs/README.md).** It links the product overview, architecture diagrams, feature catalogue, decision log, security guidance, roadmap, examples, pitch, and interactive mock demo.

[Open interactive concept demo](demo/index.html) · [Architecture diagrams](docs/ARCHITECTURE.md) · [Feature catalogue](docs/FEATURE_CATALOG.md) · [Open decisions](docs/DECISIONS_AND_OPEN_QUESTIONS.md)

Meditrace helps patients maintain a continuous medical history and share the right information with healthcare providers through patient-controlled, time-limited QR consent. It is designed with privacy, offline access, and the needs of people in areas with unreliable internet in mind.

> **Core idea:** One patient-controlled digital medical history that connects consultations, prescriptions, pharmacy dispensing, lab reports, and AI-generated summaries—so each new healthcare provider does not have to reconstruct the patient's story from scratch.

## The problem

Healthcare information is often scattered across paper prescriptions, test reports, pharmacy bills, and separate clinic systems. Patients may forget medicine names or lose records. Doctors may not know what treatment was tried previously, whether symptoms keep recurring, or whether a prescribed medicine was actually dispensed. This makes continuity of care harder and can waste consultation time.

## The solution

Meditrace gives a patient a digital medical record and a QR-based way to grant a healthcare provider access. With the patient's consent, authorized providers can review relevant history and add records. Pharmacies can record medicines actually dispensed, keeping that information distinct from what the doctor prescribed. AI can turn the timeline into a concise, reviewable summary for the next consultation.

## How it works

1. **Patient record:** The patient creates and maintains a longitudinal record containing diagnoses, allergies, prescriptions, medicines, visit notes, and lab reports.
2. **Consent-based QR sharing:** The patient generates a QR code for a defined access period and permission set. Providers scan it to request or establish authorized access; sessions expire and can be revoked.
3. **Doctor consultation:** A doctor reviews the history, records symptoms and findings, adds a diagnosis, and creates a prescription and follow-up plan.
4. **Pharmacy dispensing:** A pharmacist verifies the relevant prescription and records what was actually dispensed, including date and quantity where available.
5. **Lab reports:** Authorized lab staff can link reports to the patient's timeline, with test name, date, and lab details.
6. **AI consultation summary:** Meditrace summarizes previous visits, medicines prescribed and dispensed, test reports, recurring symptoms, and relevant changes. The doctor reviews the source records and remains responsible for clinical decisions.

## What makes Meditrace different?

- **Prescribed vs. dispensed tracking:** A prescription records what a clinician intended. A dispensing record records what a pharmacy supplied. These are not treated as the same event.
- **Patient-controlled sharing:** QR codes are a consent mechanism, not a public medical-record link. Access should be scoped, time-limited, authenticated, auditable, and revocable.
- **Continuity across visits:** A chronological timeline makes previous treatment and test results easier to understand.
- **Offline-first direction:** The existing product specification prioritizes local record storage and core offline use, with optional encrypted backup.
- **AI as a summarization assistant:** The AI organizes recorded facts and highlights possible gaps for clinician review; it does not replace a doctor or independently diagnose a patient.

## Intended users

- **Patients:** Keep records together, control sharing, and avoid relying only on paper files.
- **Doctors and other clinicians:** Review a structured history and add consultation notes, diagnoses, prescriptions, and plans.
- **Pharmacists:** Verify relevant prescriptions and record medicines dispensed.
- **Laboratories:** Attach test reports and metadata to the correct patient record.
- **Hospitals and clinics:** Improve record continuity and reduce manual searching, subject to integration and governance requirements.

## Product documentation

- [Knowledge base index](docs/README.md)
- [Product overview](docs/PRODUCT_OVERVIEW.md)
- [Architecture and diagrams](docs/ARCHITECTURE.md)
- [Feature catalogue](docs/FEATURE_CATALOG.md)
- [Decisions and open questions](docs/DECISIONS_AND_OPEN_QUESTIONS.md)
- [Security and clinical safety](docs/SECURITY_AND_SAFETY.md)
- [Roadmap and test plan](docs/ROADMAP.md)
- [Worked examples](docs/EXAMPLES.md)
- [Hackathon and fundraising pitch](docs/PITCH.md)
- [Fictional sample data](examples/sample-patient.json)
- [Interactive concept demo](demo/index.html)
- [Original requirements](.kiro/specs/healthcare-record-system/requirements.md)
- [Original technical design](.kiro/specs/healthcare-record-system/design.md)

## Proposed technical direction

The existing design specification describes a mobile application with a role-based interface, local SQLite storage, encrypted file storage for reports, consent/session management, barcode scanning, and optional encrypted cloud backup (Google Drive is named as a possible backup provider). Potential frontend choices discussed for a broader product include Flutter or React Native; potential backend choices include Node.js or Django; PostgreSQL or MongoDB were also considered. These are options, not a claim that all have already been implemented.

## Responsible use and privacy

Medical data is highly sensitive. Production use requires strong identity verification, least-privilege role-based access, patient consent, secure key management, encryption in transit and at rest, audit logs, safe recovery, backup deletion, and clear policies for emergencies and revoked access. QR codes should contain only short-lived opaque credentials—not readable medical details. AI output must be traceable to source records, communicate uncertainty, and be checked by a qualified clinician. Do not use Meditrace as a substitute for emergency care or professional medical judgment.

## Status

The repository contains original product requirements/design, product documentation, and an explicitly illustrative browser demo. The demo does not implement real authentication, QR authorization, encryption, backend exchange, or medical AI. Features described as proposed/future must not be assumed implemented, clinically validated, compliant, or production-ready until verified in source code and tests.

## Pitch

**Meditrace is an AI-powered, QR-based healthcare ecosystem that helps patients, doctors, pharmacies, and laboratories maintain a connected medical history. It distinguishes prescribed medicines from medicines actually dispensed and creates concise, source-linked summaries for clinician review to support better-informed consultations and continuity of care.**
