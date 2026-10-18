# Meditrace Project History and Provenance

## Why this exists
The project owner wants GitHub to be the durable source of truth so project chats can be cleared without losing context. This is a reconstructed archive, not a verbatim export of every historical chat.

## Recoverable product vision
- AI-powered, QR-based healthcare record ecosystem.
- Connect patients, doctors, and pharmacies; lab and hospital participation are part of broader product direction.
- Patient gets a unique QR identity / consent-based access flow.
- Doctor scans QR, reviews history, adds symptoms, diagnosis, prescription, recommendations, and notes.
- Pharmacy records medicines actually dispensed separately from medicines prescribed.
- Later, AI reviews available timeline and creates a concise consultation summary.
- Goal: help clinicians understand prior treatment without reconstructing it from scattered paperwork.

## Example used to explain the concept
A doctor prescribes antibiotics, vitamin supplements, and blood-pressure medicine. Pharmacy records show antibiotics and blood-pressure medicine dispensed, but no supplement dispensing event. The system can tell the doctor that the available pharmacy record does not show supplement dispensing. It must not claim the patient definitely did not obtain or take the supplement elsewhere.

## Project naming / SIH
- Broader product vision uses **Meditrace**.
- SIH-related work also used **MediKiosk**.
- SIH work included professional PPT improvement, documentation, and application-form/idea-description wording.
- Exact official problem-statement ID, sponsor, final slide deck, and final form were not recovered. See [SIH submission archive](SIH_SUBMISSION.md).

## Technical options discussed
- Mobile frontend: Flutter or React Native.
- Backend: Node.js or Django.
- Data stores discussed: PostgreSQL, MongoDB; original design instead describes local SQLite for local-first use.
- AI/NLP: medical summarization models, LLMs, healthcare NLP.
- Security themes: encrypted records, role-based access, consent-controlled sharing, secure QR access.
- Original spec names SQLite, encrypted report files, barcode scanning, consent/session management, optional encrypted Google Drive backup, AES-256-GCM, PBKDF2-SHA-256, and TLS 1.2+.

## Future ideas captured in project context
- Wearable integration.
- Blood-test auto-analysis.
- Telemedicine.
- Insurance integration.
- Multilingual support.
- Blockchain-based medical records.
- Emergency mode access.
- Voice-to-medical-notes.
- AI health prediction.
- Drug interaction alerts, risk scoring, personalized treatment suggestions, preventive recommendations, richer health trends.

These are future ideas, not confirmed implementation commitments. Each requires feasibility, privacy, security, clinical validation, and legal review.

## Known gaps
- Exact official SIH problem statement and ID.
- Original SIH reference document and final PPT.
- Verbatim historical chats and every prior decision.
- Confirmed relationship between Meditrace and MediKiosk names.
- Verified implementation status of each feature in source code/tests.
- Final decision on local-only versus cross-device synchronized records.
- Final stack, hosting, data exchange, identity verification, clinical validation plan.

## Archive protocol
When a feature or decision comes up, add it to the relevant feature/decision file, date it, label it confirmed/proposed/rejected, record why, and link implementation PRs/tests. Store official source documents in docs/reference only if licensing and privacy permit. Never commit real patient data, passwords, API keys, or private personal contact details.