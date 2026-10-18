# Meditrace Knowledge Base

> **Purpose:** This directory is the durable project notebook. If you clone the repository after deleting chats, start here.

## Status legend

- **[EXISTING SPEC]** Already written in the repository before the expanded knowledge base.
- **[VISION]** Agreed product concept / core intent.
- **[PROPOSED]** A candidate implementation or feature; not proof that it exists in code.
- **[EXPERIMENTAL DEMO]** Illustrative model that runs locally but is not production healthcare software.
- **[OPEN DECISION]** Requires a product, technical, clinical, or legal decision.

## Start here

1. [Repository README](../README.md) — quick introduction.
2. [Product overview](PRODUCT_OVERVIEW.md) — end-to-end vision and feature map.
3. [Architecture and diagrams](ARCHITECTURE.md) — system boundaries, workflows, data flow, and Mermaid diagrams.
4. [Feature catalogue](FEATURE_CATALOG.md) — core, planned, and speculative features.
5. [Project decisions and open questions](DECISIONS_AND_OPEN_QUESTIONS.md) — what is known versus unresolved.
6. [Hackathon and fundraising pitch](PITCH.md) — long and short pitch versions.
7. [Examples and demo scenarios](EXAMPLES.md) — worked examples, expected behavior, edge cases.
8. [Security and clinical safety](SECURITY_AND_SAFETY.md) — privacy, QR threat model, AI safeguards, and release gates.
9. [Implementation roadmap](ROADMAP.md) — MVP sequencing, test strategy, and acceptance checklist.
10. [Interactive mock demo](../demo/index.html) — open in a browser; uses fictional sample data and local browser state only.

## Existing specifications (preserved)

- [Requirements](../.kiro/specs/healthcare-record-system/requirements.md) **[EXISTING SPEC]**
- [Technical design](../.kiro/specs/healthcare-record-system/design.md) **[EXISTING SPEC]**

Those files remain the original detailed requirements/design. The newer documents organize the broader product vision and explicitly flag decisions that still need validation.

## Repository tree

```text
MediTrace/
├── README.md
├── .kiro/specs/healthcare-record-system/
│   ├── requirements.md             # existing requirements
│   └── design.md                   # existing technical design
├── docs/
│   ├── README.md                   # this index
│   ├── PRODUCT_OVERVIEW.md
│   ├── ARCHITECTURE.md
│   ├── FEATURE_CATALOG.md
│   ├── DECISIONS_AND_OPEN_QUESTIONS.md
│   ├── SECURITY_AND_SAFETY.md
│   ├── ROADMAP.md
│   ├── EXAMPLES.md
│   └── PITCH.md
├── examples/
│   └── sample-patient.json         # entirely fictional example
└── demo/
    └── index.html                 # self-contained illustrative prototype
```

## What "complete" means here—and what it cannot mean yet

This repository records all Meditrace information currently available in the project context and accessible connected memory, plus the existing specs. The accessible long-term conversation search did not return older Meditrace-specific chat records, so this is **not a verified verbatim export of every historical chat**. Do not infer missing past decisions. Add future decisions to `DECISIONS_AND_OPEN_QUESTIONS.md` and commit them here.

## Contribution rule

When a feature or decision changes, update the relevant feature catalogue, decision log, architecture, examples, and requirements as needed. Label ideas as **[VISION]**, **[PROPOSED]**, **[EXPERIMENTAL DEMO]**, **[IMPLEMENTED]** only after checking code/tests, or **[OPEN DECISION]**. Never store credentials, real patient data, or private personal contact information in this knowledge base.
