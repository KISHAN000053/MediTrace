# Meditrace

**AI-assisted, QR-based healthcare records and medication tracking.**

> **Project knowledge hub:** Start with the [Knowledge Base Index](docs/README.md). This repository is being used as the durable project notebook so development can continue without relying on chat history.

[Interactive concept demo](demo/index.html) · [SIH / MediKiosk archive](docs/SIH_SUBMISSION.md) · [Feature matrix](docs/FEATURE_MATRIX.md) · [Architecture diagrams](docs/ARCHITECTURE.md) · [Archive checklist](docs/ARCHIVE_COMPLETENESS.md)

## The idea

Meditrace aims to connect patients, doctors, pharmacies, and potentially laboratories through a longitudinal digital medical record and patient-controlled QR sharing. Doctors can add consultation records and prescriptions; pharmacies can record what they actually dispense; and an AI assistant can prepare a concise, dated, source-linked summary for a clinician to review at a future visit.

The core distinction is essential: **prescribed is not the same as dispensed, and dispensed is not proof of ingestion.** Missing data means unknown; the app must not automatically accuse a patient of non-adherence.

## Main workflow

1. Patient maintains a medical timeline and chooses what information to share.
2. A consent-based QR/token allows an authenticated provider to request scoped, time-limited access.
3. Doctor reviews authorized history and records the encounter, diagnosis, prescription, and follow-up plan.
4. Pharmacy records full or partial dispensing as a separate event.
5. Authorized lab reports can be attached to the timeline.
6. AI drafts a source-linked summary of previous visits, medication events, symptoms, and relevant tests.
7. A qualified clinician verifies the summary and remains responsible for clinical decisions.

## Product and SIH documentation

- [Knowledge base index](docs/README.md)
- [Product overview](docs/PRODUCT_OVERVIEW.md)
- [Project history and provenance](docs/PROJECT_HISTORY.md)
- [SIH / MediKiosk submission archive](docs/SIH_SUBMISSION.md)
- [Feature matrix](docs/FEATURE_MATRIX.md)
- [Architecture and data model](docs/ARCHITECTURE.md)
- [User-flow diagrams](docs/DIAGRAMS_AND_USER_FLOWS.md)
- [Feature catalogue](docs/FEATURE_CATALOG.md)
- [Decisions and open questions](docs/DECISIONS_AND_OPEN_QUESTIONS.md)
- [Security and clinical safety](docs/SECURITY_AND_SAFETY.md)
- [Roadmap and acceptance tests](docs/ROADMAP.md)
- [Worked examples](docs/EXAMPLES.md)
- [Hackathon and fundraising pitch](docs/PITCH.md)
- [Archive completeness checklist](docs/ARCHIVE_COMPLETENESS.md)
- [Fictional sample patient data](examples/sample-patient.json)
- [Interactive browser demo](demo/index.html)
- [Original requirements](.kiro/specs/healthcare-record-system/requirements.md)
- [Original technical design](.kiro/specs/healthcare-record-system/design.md)

## Technology direction

The original specification describes local SQLite storage, encrypted report files, QR consent/session handling, barcode scanning, and optional encrypted Google Drive backup. It names AES-256-GCM, PBKDF2-SHA-256, and TLS 1.2+ as design choices that need implementation and security review. Broader options discussed include Flutter or React Native; Node.js or Django; PostgreSQL or MongoDB; and medical NLP/LLMs. These are options, not proof of an implemented stack.

## Important architecture question

The original design leans local-first, while the product vision expects doctors and pharmacies on separate devices to access and update records. A secure exchange/sync design is needed if those devices must share records. Key ownership, provider identity, consent expiry/revocation while offline, synchronization conflicts, audit, backup, and recovery must be resolved before production use.

## Demo status and safety

The [browser demo](demo/index.html) is an illustrative, rule-based local prototype using fictional data. It does **not** implement real authentication, secure QR authorization, encryption, backend exchange, or medical AI. The repository contains specifications and documentation; do not assume each described feature is implemented or clinically validated.

Medical records are highly sensitive. Production deployment requires appropriate privacy, security, legal, and clinical review. Never commit real patient data, passwords, API keys, or private contact details.

## Project pitch

**Meditrace is an AI-assisted, QR-based healthcare ecosystem that connects patient history, doctor consultations, pharmacy dispensing, and lab reports, then drafts concise, source-linked summaries for clinician review to support continuity of care.**
