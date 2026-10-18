# Meditrace Security, Privacy & Clinical Safety

This document is a design checklist, not a certification or legal opinion. Health records are highly sensitive. Do not deploy to real patients based only on a prototype or this document.

## 1. Security principles

- **Patient control:** Clear consent for purpose, provider, data scope, and duration.
- **Least privilege:** A pharmacist needs prescription/dispensing information, not necessarily every consultation note.
- **Minimization:** Collect only data needed for the stated purpose.
- **Confidentiality:** Encrypt sensitive records and files at rest and in transit; protect keys separately.
- **Integrity:** Attribute records to their author/source, timestamp events, preserve amendments, and detect tampering where feasible.
- **Availability:** Plan encrypted backup, restore, offline access, and lost-device recovery.
- **Accountability:** Maintain access/change logs and an incident response process.
- **Accessibility:** Support low-end devices, large controls, readable language, and intermittent connectivity.

## 2. QR threat model

Potential threats:
- Someone photographs or forwards a QR.
- A token is replayed or used after expiry.
- An attacker guesses or enumerates token values.
- A QR is shown to the wrong provider.
- A provider device caches data after access ends.
- An offline device cannot learn that consent was revoked.
- A malicious app or website tricks a user into sharing a token.

Recommended controls:
1. Put no readable medical details in the QR.
2. Use high-entropy opaque, short-lived tokens with explicit scope and audience.
3. Authenticate the provider independently; a QR scan is not proof of professional identity.
4. Validate expiry and revocation in a trusted component.
5. Rate-limit failed attempts where a service exists.
6. Show patient the recipient, requested data, purpose, and expiry before sharing.
7. Record access and changes in an audit trail.
8. Expire sessions, clear sensitive caches, and protect local storage.
9. Test replay, screenshot, wrong-recipient, expired-token, revoked-token, and device-loss scenarios.
10. Document what happens during prolonged offline use.

## 3. Local storage, backup, and keys

The original design proposes local SQLite and encrypted report files, with optional encrypted cloud backup. It names AES-256-GCM, PBKDF2-SHA-256, and TLS 1.2+. These are design notes, not evidence of a secure implementation.

- Prefer platform secure storage/keystore for key material.
- Never hard-code keys or secrets in the app or repository.
- Use vetted libraries and current recommendations for KDF parameters, random nonces, and authenticated encryption.
- Avoid custom cryptographic protocols.
- Ensure encryption covers attachments, temporary files, exports, backups, logs, and cached previews where relevant.
- Decide whether backup is ciphertext-only and where decryption keys live.
- Test restore, key loss, key rotation, disabled-backup deletion, and account recovery.
- Avoid logging clinical payloads, tokens, passwords, or encryption keys.

## 4. Consent and audit

A consent grant should record, at minimum:
- Patient/record identifier.
- Recipient/provider identity or audience.
- Purpose and permitted actions/data categories.
- Issued time, expiry, and revocation time.
- Token/session identifier that is not itself a reusable secret.
- Relevant access/change events.

Do not silently erase historical clinical events. Correct mistakes with attributable amendments and a defined correction process. Define retention and deletion policy, including encrypted backups and exported copies.

## 5. AI clinical safety

### Permitted direction
- Summarize facts already present in the record.
- Highlight repeated documented symptoms or recorded trends.
- Compare prescriptions with recorded dispensing events.
- Point out missing or conflicting information for the clinician to verify.
- Explain that evidence is incomplete when it is incomplete.

### Avoid
- Autonomous diagnosis or prescribing.
- Treating missing records as proof that a medicine was not obtained or taken.
- Inventing dose, diagnosis, date, allergy, or test result.
- Presenting model-generated inference as a confirmed fact.
- Hiding the original report or source note.
- Silently writing generated text into the official record.

### Required UX
- Clearly label AI-generated drafts.
- Show source record/date for material claims.
- Distinguish recorded fact, patient report, inference, and missing data.
- Let a clinician correct or reject a summary.
- Record model/version and review status where appropriate.
- Provide a fallback if AI fails or is unavailable.
- Evaluate factuality, omissions, language performance, bias, and robustness using approved test cases.

## 6. Example of safe versus unsafe wording

**Unsafe:** “Patient did not take prescribed antibiotic.”

**Safer:** “The record contains a prescription for an antibiotic on 12 October. No dispensing event is currently recorded in Meditrace. Confirm with the patient; the medicine may have been obtained elsewhere or the record may be incomplete.”

**Unsafe:** “Diabetes is worsening.”

**Safer:** “Recorded glucose values increased across the three available results. Verify units, dates, testing conditions, and clinical context before drawing a conclusion.”

## 7. Privacy and legal readiness

Before a real-world pilot, obtain qualified review of applicable Indian data-protection and health-record requirements, professional rules, consent, retention, patient rights, breach response, vendor/LLM data handling, and any medical-device/software obligations. Do not claim legal compliance or certification until independently verified.

## 8. Incident response checklist

- Provide a way to report suspected exposure or wrong-patient linkage.
- Revoke affected credentials/tokens.
- Preserve relevant audit evidence without spreading sensitive payloads.
- Determine affected people, data categories, and time range.
- Follow applicable notification and remediation obligations.
- Patch the cause and add a regression test.
- Review lessons learned and update the threat model.
