# Meditrace Diagrams and User Flows

These diagrams describe intended behavior, not proof of implementation. GitHub renders Mermaid in Markdown.

## Patient journey
```mermaid
flowchart TD
 A[Patient creates account] --> B[Identity verification design]
 B --> C[Internal patient ID and medical timeline]
 C --> D[Patient selects provider, purpose, scope, expiry]
 D --> E[Consent QR / short-lived token]
 E --> F[Provider authenticates and scans]
 F --> G{Consent valid and in scope?}
 G -- No --> H[Deny access and audit event]
 G -- Yes --> I[Show permitted records]
 I --> J[Session expires or is revoked]
```

## Care loop
```mermaid
flowchart LR
 PAT[Patient history] --> DOC[Doctor reviews history]
 DOC --> ENC[New encounter and prescription]
 ENC --> PHARM[Pharmacy verifies prescription]
 PHARM --> DISP[Dispensing event recorded]
 PAT --> REPORTED[Patient reports actual use]
 DISP --> TL[Longitudinal timeline]
 REPORTED --> TL
 ENC --> TL
 LAB[Lab report] --> TL
 TL --> SUM[Source-linked AI summary draft]
 SUM --> REVIEW[Clinician verifies]
 REVIEW --> PLAN[Clinical decision and follow-up]
 PLAN --> TL
```

## Four role views
```mermaid
flowchart TB
 subgraph Patient
 P1[Profile and medical timeline]
 P2[Consent QR and sharing controls]
 P3[Reports and medication history]
 end
 subgraph Doctor
 D1[Authorized history]
 D2[Encounter, diagnosis, prescription]
 D3[AI summary review]
 end
 subgraph Pharmacy
 F1[Verify relevant prescription]
 F2[Record full / partial / no dispense]
 end
 subgraph Admin_or_Clinic
 A1[Provider onboarding]
 A2[Roles and audit]
 A3[Configuration and support]
 end
 P2 --> D1
 D2 --> F1
 F2 --> P3
 D3 --> D2
 A1 --> D1
```

## Trust boundaries
```mermaid
flowchart LR
 subgraph PatientDevice[Patient-controlled device]
 PR[Local records and consent UI]
 KEY[Key store / OS security]
 end
 subgraph ProviderDevice[Provider device]
 AUTH[Provider authentication]
 VIEW[Scoped record view]
 end
 subgraph Exchange[Optional exchange service]
 TOK[Opaque short-lived token validation]
 SYNC[Encrypted event sync, if chosen]
 end
 PR --> TOK
 AUTH --> TOK
 TOK --> VIEW
 PR <--> SYNC
 SYNC <--> VIEW
```

The exact data exchange architecture remains open. If the product must work across separate devices, “local-only” cannot mean no exchange mechanism exists. Define encryption/key ownership, recipient authentication, consent revocation, offline behavior, synchronization, conflict resolution, and auditability.

## AI summary example — safe wording
**Available records:** One encounter dated 12 October; one prescription for 10 units; one pharmacy event recording 4 units dispensed.

**Appropriate summary:** “A prescription for 10 units is recorded. A pharmacy dispensing event for 4 units is recorded. The available data does not establish whether the remaining quantity was obtained elsewhere or whether any medicine was taken.”

**Avoid:** “The patient was non-compliant” or “The patient did not take the medicine.” The records do not support those conclusions.
