# Meditrace Feature Matrix

Status labels are deliberate: **CORE VISION**, **SPECIFIED**, **PROPOSED**, **FUTURE IDEA**, **OPEN DECISION**. They do not mean implemented.

## Patient app

| Feature | Status | Notes |
|---|---|---|
| Account/profile and identity verification | CORE VISION | Identity verification design required |
| Unique internal patient identifier | CORE VISION | Separate from share token |
| Patient QR sharing | CORE VISION | Short-lived opaque token; never a public record URL |
| Choose provider, purpose, scope, expiry | PROPOSED | Least privilege and provider authentication |
| Expiry and revocation | PROPOSED | Offline revocation limits need design |
| Longitudinal medical timeline | CORE VISION | Preserve source, author, timestamp, amendments |
| Diagnoses, symptoms, allergies, history | CORE VISION | Structured fields plus notes |
| Prescriptions and follow-up plans | CORE VISION | Preserve originals and amendments |
| Lab reports and attachments | CORE VISION | Original file plus searchable metadata |
| Pharmacy dispensing history | CORE VISION | Depends on participating pharmacy records |
| Patient-reported medication use | PROPOSED | Label as self-reported, not verified |
| Download/export record | PROPOSED | Format and encryption need decisions |
| Emergency/break-glass access | FUTURE IDEA | Audit, policy, and notifications required |
| Multilingual interface | FUTURE IDEA | Validate clinical terminology |
| Wearables | FUTURE IDEA | Integrate only validated/relevant measurements |
| Telemedicine | FUTURE IDEA | Separate workflow and legal review |

## Doctor / clinician portal

| Feature | Status | Notes |
|---|---|---|
| Scan QR and open authorized profile | CORE VISION | Authenticate provider and validate consent |
| Review timeline | CORE VISION | Filter by dates, encounters, medicines, reports |
| Add consultation, symptoms, findings | CORE VISION | Author and timestamps |
| Add diagnosis and recommendations | CORE VISION | Clinician-authored |
| Create prescription | CORE VISION | Medicine, strength, dose, route, frequency, duration, quantity |
| Review dispensing history | CORE VISION | Dispensing is not proof of ingestion |
| AI consultation summary | CORE VISION | Source-linked draft; human review |
| Trends across visits | PROPOSED | Show units, dates, reference ranges, provenance |
| Recurring-pattern flags | PROPOSED | Decision support, not diagnosis |
| Clinician approve/correct/reject AI draft | PROPOSED | Audit changes; AI cannot silently change official records |
| Drug interaction alerts | FUTURE IDEA | Requires validated data source |
| Predictive risk / disease detection | FUTURE IDEA | Requires clinical validation and governance |
| Voice-to-medical notes | FUTURE IDEA | Consent and review required |

## Pharmacy portal

| Feature | Status | Notes |
|---|---|---|
| Scan patient sharing QR | CORE VISION | Validate pharmacy role and scope |
| View relevant active prescription | CORE VISION | Show issuer, date, status, amendments |
| Record full dispensing | CORE VISION | Item, quantity, date, pharmacy identity |
| Record partial dispensing | CORE VISION | Keep prescribed/supplied quantities separate |
| Record substitution | PROPOSED | Preserve original item and reason/authorization |
| Record not dispensed / unavailable | PROPOSED | Explicit event; absence of event means unknown |
| Duplicate/expired prescription flags | PROPOSED | Requires reliable status and policy |
| Repeat-fill tracking | PROPOSED | Do not infer ingestion from refills |
| Barcode medicine lookup | SPECIFIED / PROPOSED | Barcode scanning appears in original technical design; workflow needs review |

## Lab, hospital, and admin

| Feature | Status | Notes |
|---|---|---|
| Attach lab report | CORE VISION | Lab, test, collection/result dates, original file |
| Hospital/clinic integrations | FUTURE IDEA | Interoperability and permissions required |
| Provider onboarding | PROPOSED | Verify organization and staff |
| Role-based access | SPECIFIED | Scope resources as well as roles |
| Audit trail | SPECIFIED / PROPOSED | Access, exports, edits, consent, admin actions |
| Consent management | SPECIFIED / PROPOSED | Issue, expire, revoke, audit, recover |
| Backup/restore | SPECIFIED | Original design mentions optional encrypted Google Drive backup |
| Local storage | SPECIFIED | Original design describes SQLite and encrypted report files |
| Admin analytics | FUTURE IDEA | Aggregate/de-identify; avoid unnecessary patient visibility |

## AI and analytics

| Feature | Status | Guardrail |
|---|---|---|
| Summarize previous visits | CORE VISION | Cite source records and dates |
| Summarize prescription/dispensing events | CORE VISION | Never infer ingestion |
| Recurring symptoms/history overview | CORE VISION | Describe patterns in records, not diagnosis |
| Trends in labs/measurements | PROPOSED | Preserve units, dates, ranges, source |
| Missing information detection | PROPOSED | Say “not found in available records,” not “did not happen” |
| Medication adherence estimation | PROPOSED | Use dispensing and self-report cautiously; show uncertainty |
| Drug interaction warnings | FUTURE IDEA | Validated database, freshness, clinician workflow |
| Predictive risk scoring | FUTURE IDEA | Clinical validation, fairness assessment, governance |
| Personalized treatment suggestions | FUTURE IDEA | Not autonomous prescribing; review required |
| Preventive recommendations | FUTURE IDEA | Evidence-based and appropriate |

## Platform and infrastructure

| Feature / option | Status | Notes |
|---|---|---|
| Offline-first local storage | SPECIFIED | Cross-device updates conflict with fully local-only design; resolve |
| SQLite | SPECIFIED | Existing design candidate for local DB |
| Encrypted report files | SPECIFIED | Key lifecycle and backups need design |
| AES-256-GCM | SPECIFIED DESIGN CHOICE | Needs correct implementation and review |
| PBKDF2-SHA-256 | SPECIFIED DESIGN CHOICE | Reassess current key-derivation guidance before production |
| TLS 1.2+ | SPECIFIED DESIGN CHOICE | Secure transport configuration required |
| Optional encrypted Google Drive backup | SPECIFIED | Decide key ownership, deletion, recovery, sync semantics |
| Flutter / React Native | PROPOSED OPTIONS | No final selection confirmed |
| Node.js / Django | PROPOSED OPTIONS | No final selection confirmed |
| PostgreSQL / MongoDB | PROPOSED OPTIONS | No final selection confirmed |
| Medical NLP / LLM | PROPOSED OPTIONS | Privacy, source grounding, evaluation unresolved |
| Blockchain medical records | FUTURE IDEA | Justify tradeoffs; not assumed necessary |

## Data semantics — never blur these
1. **Prescribed:** a clinician ordered treatment.
2. **Dispensed:** a pharmacy recorded supplying a quantity.
3. **Patient-reported use:** patient says they took or did not take it.
4. **Clinically verified use:** only if a suitable verification process exists; often not directly established by an app.

Missing data means **unknown**, not automatically “not purchased,” “not taken,” or “non-compliant.”