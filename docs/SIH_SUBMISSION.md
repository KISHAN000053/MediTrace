# Smart India Hackathon (SIH) / MediKiosk Archive

**Evidence status:** Recoverable context confirms SIH/hackathon work, professional PPT improvement, project documentation, and application-form wording for a project referred to as MediKiosk / Meditrace. The exact official SIH problem-statement ID, sponsor, final submitted deck, and source brief were not available. Do not invent them.

## Naming
- Broader product vision: **Meditrace**.
- SIH-related context also used **MediKiosk**.
- **OPEN DECISION:** establish whether MediKiosk is a prototype name, kiosk/module, or earlier name. Do not silently treat these as confirmed synonyms in external submissions.

## Problem framing (draft, not official SIH wording)
Patient medical information is often fragmented across paper prescriptions, test reports, pharmacy bills, and different clinics. Patients may forget medicine names or lose records. A new doctor may have to reconstruct treatment history from scattered documents. A prescription alone does not show whether a pharmacy dispensed the medicine, whether a partial fill occurred, or what the patient reports taking. These information gaps make continuity of care harder and consume consultation time.

**Replace this paragraph with the exact official SIH problem statement and ID once recovered. This is our framing, not an official quote.**

## Proposed solution
Meditrace is an AI-assisted, QR-based healthcare record ecosystem intended to connect patients, doctors, pharmacies, and potentially laboratories. A patient maintains a longitudinal medical timeline and shares a limited, time-bound view with an authorized provider through a consent-based QR workflow. Doctors add consultation notes, diagnoses, prescriptions, and follow-up plans. Pharmacies record what they actually dispense as a separate event. Reports can be linked to the timeline. At a later visit, AI drafts a concise summary grounded in dated source records for clinician review.

## End-to-end demo narrative
1. A fictional patient has a medical timeline and internal patient ID.
2. The patient selects recipient, purpose, scope, and expiry for sharing.
3. The app presents a short-lived consent QR/token; it must not expose readable medical information.
4. An authenticated doctor scans it and receives only permitted records.
5. The doctor adds a fictional encounter and prescription.
6. A pharmacist records full, partial, or no dispensing based on the actual pharmacy event.
7. The timeline keeps prescription, dispensing, and patient-reported use separate.
8. At a later visit, AI drafts a dated, source-linked summary, flags missing/conflicting data, and asks the clinician to verify it.
9. Access expires or is revoked according to the defined consent design.

## Differentiation
- QR-based patient-controlled sharing, not a public QR link to a medical file.
- A chronological, portable health record rather than isolated documents.
- Explicit distinction between prescribed medicine, pharmacy-dispensed medicine, and patient-reported use.
- AI-generated consultation summaries with source references and clinician review.
- Offline-first direction for unreliable connectivity, subject to a designed cross-device exchange model.

## SIH pitch — short
Meditrace is an AI-assisted healthcare continuity platform connecting patient records, doctor consultations, pharmacy dispensing, and lab reports through patient-controlled QR sharing. It maintains a chronological medical timeline and distinguishes medicines prescribed from those actually dispensed. Before a follow-up consultation, its AI assistant drafts a concise, source-linked history summary for clinician review, helping providers understand previous treatment without reconstructing it from scattered papers.

## SIH pitch — extended
Medical history can be fragmented across paper prescriptions, reports, bills, and multiple providers. Meditrace proposes a patient-controlled digital medical timeline shared through a consent-based QR flow. During a consultation, a doctor reviews authorized history and records the new visit. A pharmacy separately records the medicine and quantity dispensed, preserving the difference between what was prescribed and what was supplied. Laboratory reports can be linked to the same timeline. At a later visit, an AI assistant prepares a concise summary of prior visits, medication events, recurring symptoms, and relevant test trends, citing dates and source records and highlighting missing information. The clinician remains responsible for interpretation and decisions. The concept aims to improve continuity and reduce manual record-gathering while respecting consent, privacy, and clinical safety.

## Suggested slide order
1. Title, team, institute, exact official SIH problem-statement ID.
2. Problem and current workflow pain points.
3. Target users and user journey.
4. Proposed solution and differentiators.
5. System architecture and trust boundaries.
6. Patient → doctor → pharmacy workflow.
7. Prescription vs dispensing vs reported use.
8. AI summary example with source/date citations.
9. Prototype screenshots or live demo.
10. Technology stack and implementation plan.
11. Privacy, security, offline behavior, and risk mitigation.
12. Feasibility, pilot plan, measurable evaluation, expected impact.
13. Limitations, dependencies, and future scope.
14. Team roles and references.

Use the official SIH template and slide-count rules if supplied. Do not imply the concept is clinically validated.

## Demo scenario for judges
Use fictional data only. A doctor prescribes 10 units of “Example Medicine A”; the demo pharmacy records dispensing of 4 units. Safe summary: “A prescription for 10 units is recorded. A pharmacy dispensing event for 4 units is recorded. The available record does not establish whether the remaining quantity was obtained elsewhere or whether any medicine was taken.”

## Questions to resolve
- Official SIH problem-statement ID, sponsor, and exact wording?
- Is the submission name Meditrace, MediKiosk, or another name?
- Required judging criteria, PPT template, slide limit, and deadline?
- Which features existed in the prototype at submission time versus were only proposed?
- Team responsibilities, deployment assumptions, data source, and evaluation plan?
- What measurable outcomes can be tested rather than merely claimed?

## Provenance
This is reconstructed from accessible project context, not copied from the official final submission. Context indicates SIH/MediKiosk work included PPT improvement, documentation, and form-description writing, but the referenced original SIH source document was not found. Recover the original problem statement, slide deck, and final application response before treating this as a verbatim archive.