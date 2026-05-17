# LogLine Canon Governance

This document defines the initial governance boundary for the LogLine canon
package. It is intentionally small until LIP-0001 is accepted or superseded.

## Authority

The canon is the normative source. Runtime implementations are adherence
claims, not sources of norm.

## Roles

| Role | Responsibility |
|---|---|
| Canon Editor | Maintains normative clarity and consistency. |
| Runtime Maintainer | Maintains a reference implementation without owning the canon. |
| Conformance Maintainer | Maintains corpus cases, manifests, and expected results. |
| Security Reviewer | Reviews proof, receipt, signature, and ledger implications. |
| Foundation Steward | Mediates long-term direction and unresolved conflicts. |

## Change Process

Substantive canon changes should use the LIP process:

```text
Draft -> Proposed -> Accepted -> Implemented -> Superseded
                     \-> Rejected
                     \-> Withdrawn
```

Editorial corrections that do not alter normative meaning may be recorded as
errata in `CHANGELOG.md`.

## Versioning

Published canon versions should be treated as immutable. Any substantive change
requires a new version or explicit errata.

## Runtime Boundary

The reference Rust runtime may influence the canon through LIPs and evidence,
but its behavior alone is not normative.
