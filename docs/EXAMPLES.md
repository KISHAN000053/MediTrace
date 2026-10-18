# Meditrace Worked Examples

All names, dates, measurements, and events in this file are fictional examples for product design and testing. They are not medical advice.

## Example 1 — The core journey

**Patient:** Asha Example (fictional)

### Visit 1 — Doctor
The doctor records a visit for fever and cough and prescribes:
- Medicine A, prescribed course.
- Medicine B, as documented in the fictional prescription.

### Pharmacy
The pharmacist records:
- Medicine A: partially dispensed.
- Medicine B: no dispensing event recorded at this pharmacy.

### Visit 2 — Follow-up
A safe Meditrace summary could say:

> “At the visit on 12 October, fever and cough were documented. The clinician prescribed Medicine A and Medicine B. The pharmacy record shows a partial dispensing event for Medicine A; no dispensing event for Medicine B is recorded in this Meditrace timeline. Confirm what the patient obtained and used, as records may be incomplete.”

It should **not** say the patient refused or failed to take Medicine B.

## Example 2 — Prescription versus dispensing

| Event | Record | Safe interpretation |
|---|---|---|
| 09:00 | Doctor prescribes 10 tablets | Intended treatment is documented |
| 12:00 | Pharmacy records 4 tablets supplied | Partial dispensing is documented |
| Next day | No further pharmacy event | No further event is recorded here; supply elsewhere is possible |
| Follow-up | Patient reports taking 1 tablet daily | Patient-reported use; reconcile with clinician instructions |

The system should retain all events and let the clinician clarify the actual course.

## Example 3 — Recurring symptom

Records:
- 01 Oct: patient reports headache.
- 06 Oct: headache documented at a second visit.
- 20 Oct: patient reports headache again.

Safe summary:
> “Headache is documented on three occasions (1, 6, and 20 October). Review the linked notes for context. The record alone does not establish a cause.”

Unsafe summary:
> “Patient has a neurological disease.”

## Example 4 — Lab values

Suppose three fictional lab records contain values for the same test but one uses a different unit. The system must not blindly connect them into a trend. It should first verify test identity, unit, reference range, dates, and source. Keep the original report available.

## Example 5 — Consent expiry

1. Patient grants a doctor read/write access to consultation data for one hour.
2. Doctor adds the encounter while the session is valid.
3. Session expires.
4. Further reads/writes are denied according to the documented exchange architecture.
5. The system records the expiry/access outcome without exposing the medical payload.

If a provider device is offline, the design must specify whether it can validate expiry locally and how revocation is enforced after connectivity returns.

## Example 6 — Wrong or copied QR

A QR is photographed and sent to another person. Expected behavior:
- Token cannot be used without the correct provider authentication and audience/scope.
- Token expires and can be revoked.
- Invalid access attempts are rejected and logged where feasible.
- The QR payload itself reveals no diagnosis or medication list.

## Example 7 — AI summary with conflicting records

One note says a medicine was stopped; a later prescription shows the same medicine again. The AI should surface the conflicting dates and link both records rather than silently decide which is current. A clinician must reconcile it.

## Example 8 — Lab report uploaded to the wrong patient

The product should support an error-report/correction workflow, preserve an audit trail, restrict exposure, and notify appropriate responsible users. Never silently move or erase clinical records without a trace.

## Example 9 — Lost device

Expected design questions:
- Is the local record encrypted?
- Can access to a lost device be revoked?
- Is backup available and tested?
- Can the patient recover the encryption key safely?
- What data remains on provider devices?
- What happens to offline cached copies?

These must be decided before a real patient pilot.

## Example 10 — Demonstration script (3–5 minutes)

1. Introduce the fragmented-record problem.
2. Show a fictional patient's timeline.
3. Generate a time-limited consent QR simulation.
4. Switch to doctor role and add a consultation/prescription.
5. Switch to pharmacy role and record partial dispensing.
6. Return to doctor and generate the summary.
7. Show the exact source events behind the summary.
8. Explain that the summary is assistive and that missing dispensing data is not proof of non-adherence.
9. Demonstrate expiry/revocation.
10. End with the roadmap: real authentication, secure cross-device exchange, clinical review, and validation.

## Suggested fictional test fixture

See [sample-patient.json](../examples/sample-patient.json). It contains fictional names and clearly labelled demonstration events only.
