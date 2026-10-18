# Meditrace Architecture & Diagrams

**Status:** A synthesis of the existing offline-first specification and the broader product vision. Diagrams describe intended behavior, not a guarantee that every component has been implemented.

## 1. Product at a glance

```mermaid
flowchart LR
    P[Patient] -->|time-limited consent QR| M[Meditrace record & consent layer]
    D[Doctor] <-->|review history / add encounter and prescription| M
    PH[Pharmacist] <-->|view prescription / record dispensing| M
    L[Laboratory] -->|upload report with consent| M
    M --> T[Chronological medical timeline]
    T --> AI[AI summarization assistant]
    AI -->|source-linked draft summary| D
    M -. optional encrypted backup .-> B[(Encrypted backup)]
```

## 2. Intended component architecture [EXISTING SPEC + PROPOSED]

```mermaid
flowchart TB
  subgraph App["Mobile application / provider interface"]
    UI["Role-based UI\nPatient · Doctor · Pharmacy · Lab"]
    APP["Application services\nRecord · Consent · Session · Barcode · PDF · Sync"]
    DAL["Data access layer\nValidation · Encryption · Cache · Audit"]
    DB[("Local SQLite\nstructured records")]
    FILES[("Encrypted files\nreports / attachments")]
    QUEUE["Pending backup / sync queue"]
    UI --> APP --> DAL
    DAL --> DB
    DAL --> FILES
    DAL --> QUEUE
  end
  QUEUE -. "optional encrypted backup" .-> CLOUD[("Cloud backup provider\nGoogle Drive proposed in original spec")]
  PROVIDER["Separate provider device / clinic system"] <-->|"secure record exchange required"| EXCHANGE["[OPEN DECISION] Exchange / relay / encrypted export"]
  EXCHANGE <--> APP
```

**Important architecture tension:** The original specification says there is no central-server dependency and records are stored locally on the patient's device. But a doctor or pharmacy using another device cannot magically read or update that local database. Multi-device sharing requires a deliberate exchange design: encrypted export/import, a relay service, a synchronization service, or another reviewed approach. The product must decide how keys, revocation, offline changes, conflicts, and audit trails work. Do not promise seamless real-time sharing until this path exists and has been tested.

## 3. Consultation sequence

```mermaid
sequenceDiagram
    actor Patient
    participant App as Patient Meditrace
    participant Doctor
    participant Consent as Consent/session manager
    participant Record as Authorized record store
    participant AI as Summary assistant
    Patient->>App: Choose provider, scope and duration
    App-->>Patient: Show consent QR / sharing token
    Doctor->>Consent: Scan token + authenticate
    Consent->>Consent: Validate expiry, scope, audience and revocation
    Consent-->>Doctor: Grant least-privilege session
    Doctor->>Record: Read permitted history
    Record-->>Doctor: Timeline + source records
    Doctor->>Record: Add encounter and prescription
    Record-->>App: New signed/attributed timeline event via chosen exchange
    Doctor->>AI: Request summary from authorized records
    AI-->>Doctor: Draft with dates, source links and missing-data notes
    Doctor->>Record: Review and save clinician-approved notes if desired
    Consent->>Consent: Expire or revoke session
```

## 4. Pharmacy workflow and the three medication facts

```mermaid
flowchart TD
  RX["Doctor creates prescription"] --> PRES["Prescription event: intended treatment"]
  PRES --> QR["Patient grants pharmacy scoped access"]
  QR --> VERIFY["Pharmacist authenticates and verifies prescription"]
  VERIFY --> DISP["Dispensing event: supplied item, quantity, date"]
  DISP --> TIMELINE["Both events appear separately in timeline"]
  PAT["Patient report of use"] --> USE["Patient-reported use event"]
  TIMELINE --> AI["AI compares available records"]
  USE --> AI
  AI --> NOTE["Summary highlights known facts + uncertainty"]
  NOTE --> DOC["Doctor verifies with patient"]
```

- A **prescription** is not a purchase.
- A **dispensing record** is not proof of ingestion.
- Missing dispensing data is not proof of non-adherence.
- Patient-reported use is useful but must be labelled as self-reported.

## 5. Consent and trust boundaries

```mermaid
flowchart LR
  subgraph PatientBoundary["Patient-controlled boundary"]
    KEY["Patient identity / record key"]
    CONSENT["Consent settings"]
    QR["Short-lived opaque QR token"]
    RECORD["Medical records"]
    KEY --> RECORD
    CONSENT --> QR
  end
  QR --> AUTH["Provider authentication + token validation"]
  AUTH --> POLICY["Scope · expiry · revocation · role"]
  POLICY -->|allowed fields only| PROVIDER["Doctor / pharmacy / lab"]
  PROVIDER --> AUDIT["Audit event: who, when, what purpose"]
  AUDIT --> RECORD
  BAD["Expired / revoked / invalid token"] --> DENY["Deny access; no record payload"]
```

**Security rule:** The QR should not expose readable medical details. A QR scan alone must not grant unrestricted access; provider authentication and server/device-side validation are required. If fully offline validation is supported, revocation and expiry semantics need special attention.

## 6. Suggested conceptual data model

```mermaid
erDiagram
  PATIENT ||--o{ ENCOUNTER : has
  PATIENT ||--o{ ALLERGY : records
  PATIENT ||--o{ LAB_REPORT : has
  PATIENT ||--o{ CONSENT_GRANT : controls
  ENCOUNTER ||--o{ PRESCRIPTION : creates
  PRESCRIPTION ||--o{ PRESCRIPTION_ITEM : contains
  PRESCRIPTION_ITEM ||--o{ DISPENSE_EVENT : may_have
  PATIENT ||--o{ PATIENT_REPORTED_USE : reports
  PATIENT ||--o{ AUDIT_EVENT : relates_to
  ENCOUNTER ||--o{ CLINICAL_NOTE : includes
  AI_SUMMARY }o--o{ SOURCE_RECORD : cites
```

### Core entity definitions

| Entity | Key fields (illustrative) | Notes |
|---|---|---|
| Patient | patient_id, display_name, created_at | Use a stable internal ID; protect identity fields |
| Encounter | encounter_id, patient_id, clinician_id, occurred_at, status | Append-only clinical event preferred |
| Prescription | prescription_id, encounter_id, prescriber_id, issued_at, status | Not the same as dispensing |
| Prescription item | item_id, medicine_code/name, dose, route, frequency, duration, quantity | Preserve original instructions and amendments |
| Dispense event | dispense_id, item_id, pharmacy_id, dispensed_at, quantity, substitution, status | Supports partial fill and no-fill outcomes |
| Patient-reported use | report_id, item_id, reported_at, usage_description, source | Clearly mark as patient-reported |
| Lab report | report_id, patient_id, test_name, lab_name, collected_at, reported_at, file_ref | Store file encrypted; retain source |
| Consent grant | consent_id, patient_id, audience/provider, scope, issued_at, expires_at, revoked_at | Never trust client-only expiry checks |
| Audit event | event_id, actor_id, action, patient_id, timestamp, purpose | Avoid logging unnecessary medical payloads |
| AI summary | summary_id, created_at, model_version, source_ids, review_status | Draft versus clinician-approved must be clear |

This is a conceptual schema, not a migration or deployed database.

## 7. Data lifecycle

```mermaid
flowchart TD
  IN[Capture record] --> VALIDATE[Validate fields, identity and source]
  VALIDATE --> ATTR[Attribute actor, timestamp and provenance]
  ATTR --> ENCRYPT[Encrypt data / attachment]
  ENCRYPT --> LOCAL[Persist locally]
  LOCAL --> AUDIT[Append audit event]
  LOCAL --> QUEUE{Backup/sync enabled?}
  QUEUE -->|No| READY[Available on this device]
  QUEUE -->|Yes| PENDING[Queue encrypted transfer]
  PENDING --> ONLINE{Network available?}
  ONLINE -->|No| PENDING
  ONLINE -->|Yes| SYNC[Transfer under chosen sync protocol]
  SYNC --> VERIFY[Verify integrity and reconcile conflicts]
  VERIFY --> READY
```

## 8. AI summary data flow

```mermaid
flowchart LR
  RX[Prescriptions] --> G[Evidence gathering]
  DX[Diagnoses / visit notes] --> G
  LAB[Lab reports / measurements] --> G
  DISP[Dispensing events] --> G
  USE[Patient-reported use] --> G
  G --> FILTER[Permission check + source/date selection]
  FILTER --> SUM[Generate structured draft]
  SUM --> CHECK[Check source support / uncertainty]
  CHECK --> UI[Display citations and missing data]
  UI --> CLINICIAN[Clinician reviews and corrects]
```

## 9. Technology candidates

**From existing design:** local SQLite, encrypted report files, QR consent/session management, barcode scanning, role-based interfaces, pending backup queue, optional encrypted Google Drive backup, AES-256-GCM and PBKDF2-SHA-256 named in the design. Cryptographic details must be reviewed before production; prefer platform-supported password/key derivation and secure key storage, and never implement cryptography casually.

**Broader options discussed:** Flutter or React Native for mobile; Node.js or Django for backend if a service is chosen; PostgreSQL or MongoDB for a centralized/shared data layer; medical NLP/LLM for summarization. These are alternatives to evaluate, not a selected final stack.

## 10. Diagrams are not implementation evidence

A component appearing in this file means it is part of the intended design. Check source code, tests, and deployment configuration before describing it as implemented. At the time this knowledge base was expanded, the repository tree contained the original requirements/design specs and documentation, but no verified production application source tree.
