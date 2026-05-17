# Governance delta

## Purpose

The canon needs a governance process proportional to its claims. This document
extends the early governance draft with states, roles, quorum, acceptance,
rejection, errata, and security triggers.

## LIP lifecycle

```text
Draft -> Proposed -> Accepted -> Implemented -> Superseded
                         |              |
                         v              v
                     Rejected       Reverted
                         |
                         v
                     Withdrawn
```

## Roles

### Canon editor

Maintains textual consistency, version boundaries, and publication hygiene.
Does not unilaterally decide consequence-bearing changes.

### Runtime maintainer

Implements canon behavior in a runtime. May report implementation feasibility
and test evidence.

### Security reviewer

Reviews changes that affect admissibility, evidence, signatures, receipts,
execution boundaries, or ledger persistence.

### Witness set

A declared set of people or keys authorized to ratify a canon release.

## Acceptance process

A LIP may be Accepted when:

- motivation and scope are clear;
- normative language is precise;
- conformance impact is described;
- security impact is reviewed if needed;
- migration path exists;
- rejection alternatives were considered.

## Rejection process

A LIP may be Rejected when it:

- weakens evidence discipline;
- lets natural language execute;
- lets `if_doubt` release consequence;
- breaks the 9-slot form without a major-version process;
- lacks a conformance path;
- creates unbounded authority.

## Errata policy

Errata may correct spelling, broken links, or non-normative examples without a
version bump. Any change to MUST/SHOULD/MAY behavior requires a new canon version
or explicit amendment.

## Security review trigger

Security review is required when a change touches:

```text
confirmed_by
receipt verification
signature verification
ledger append/rollback
adapter execution
if_ok release
if_doubt simulation boundaries
runtime conformance claims
```
