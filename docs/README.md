# Meditrace Knowledge Base

This directory is the durable project notebook. If you clone the repository after clearing chats, start here.

## Status labels
- **VISION / CORE VISION** — product intent, not necessarily implemented.
- **EXISTING SPEC / SPECIFIED** — present in the original specification; still verify implementation.
- **PROPOSED** — candidate feature or implementation.
- **FUTURE IDEA** — possible later expansion.
- **EXPERIMENTAL DEMO** — illustrative local model, not production healthcare software.
- **OPEN DECISION** — unresolved; do not silently assume a choice.

## Start here
1. [Repository README](../README.md)
2. [Product overview](PRODUCT_OVERVIEW.md)
3. [Project history and provenance](PROJECT_HISTORY.md)
4. [SIH / MediKiosk submission archive](SIH_SUBMISSION.md)
5. [Feature matrix by role](FEATURE_MATRIX.md)
6. [Architecture and diagrams](ARCHITECTURE.md)
7. [Additional user-flow diagrams](DIAGRAMS_AND_USER_FLOWS.md)
8. [Feature catalogue](FEATURE_CATALOG.md)
9. [Decisions and open questions](DECISIONS_AND_OPEN_QUESTIONS.md)
10. [Security and clinical safety](SECURITY_AND_SAFETY.md)
11. [Roadmap and test plan](ROADMAP.md)
12. [Worked examples](EXAMPLES.md)
13. [Hackathon and fundraising pitch](PITCH.md)
14. [Archive completeness checklist](ARCHIVE_COMPLETENESS.md)
15. [Interactive mock demo](../demo/index.html)
16. [Fictional patient JSON](../examples/sample-patient.json)

## Original specifications preserved
- [Healthcare record requirements](../.kiro/specs/healthcare-record-system/requirements.md)
- [Healthcare record technical design](../.kiro/specs/healthcare-record-system/design.md)

## Repository map
```text
MediTrace/
├── README.md
├── .kiro/specs/healthcare-record-system/
│   ├── requirements.md
│   └── design.md
├── docs/
│   ├── README.md
│   ├── PRODUCT_OVERVIEW.md
│   ├── PROJECT_HISTORY.md
│   ├── SIH_SUBMISSION.md
│   ├── FEATURE_MATRIX.md
│   ├── ARCHITECTURE.md
│   ├── DIAGRAMS_AND_USER_FLOWS.md
│   ├── FEATURE_CATALOG.md
│   ├── DECISIONS_AND_OPEN_QUESTIONS.md
│   ├── SECURITY_AND_SAFETY.md
│   ├── ROADMAP.md
│   ├── EXAMPLES.md
│   ├── PITCH.md
│   └── ARCHIVE_COMPLETENESS.md
├── examples/sample-patient.json
└── demo/index.html
```

## Important archive limitation
This is a structured reconstruction from the original specifications and recoverable project context, not a guaranteed verbatim export of every historical chat. The exact official SIH problem statement, original reference file, final SIH slide deck, and exact final application responses were not available. See [Archive Completeness](ARCHIVE_COMPLETENESS.md). Do not delete the only copy of those artifacts until recovered or explicitly marked unrecoverable.

Use synthetic data only. Never commit secrets, real patient records, passwords, API keys, or private contact information.
