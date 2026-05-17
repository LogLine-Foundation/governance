# LIP-0003 - LogLine Receipt Encoding and Content Addressing

Status: **Superseded**
Superseded by: LIP-0007 (three-layer hashing) and `logline.receipt.v0` in `LogLine-Foundation/canon`
Reason: the receipt model moved from a single `result_hash` plus embedded `result` / `evidence` / `transport` to a three-layer discipline (tuple / content / envelope) with strict separation of act, interpretation, and transport. See LIP-0007.
Author: LogLine Foundation draft
Target canon: receipt encoding profile (historical)
Created: 2026-05-07
Superseded: 2026-05-17

> **This document is preserved as audit history.**
> The current normative receipt format is `logline.receipt.v0` in `LogLine-Foundation/canon`,
> proved by `LogLine-Foundation/conformance`. Engines MUST NOT implement the receipt shape
> described below. Read it to understand what changed and why; do not implement it.

## Abstract

This LIP defines the canonical JSON receipt encoding profile for LogLine
receipts. It preserves the nine-slot LogLine body, keeps the existing
`canonical_tuple_digest` as the tuple identity algorithm, and adds a normative
profile for result and receipt content addressing.

Core law:

```text
A receipt does not contain a LogLine.
A receipt is a LogLine act with result, evidence, transport, and hashes.
```

## Motivation

LogLine already has a canonical tuple digest over exactly:

```text
who did this when confirmed_by if_ok if_doubt if_not status
```

`if_doubt` simulation receipts already carry a tuple digest and distinguish
simulation from execution. The canon now needs a receipt profile that lets
independent runtimes compare receipts by result identity, compare emissions by
historical identity, compose receipts, and use prior receipts as later evidence.

Without this profile, receipts risk becoming ad hoc JSON blobs whose fields are
hard to compare, hash, replay, or cite.

## Non-goals

This LIP does not:

- change the nine-slot LogLine body;
- add a tenth runtime slot;
- replace `canonical_tuple_digest`;
- switch the primary hash algorithm away from SHA-256;
- define Minilab Gateway behavior;
- define Supabase behavior;
- implement passports or visas as product features;
- require BLAKE3 in v0;
- require a ledger storage engine.

## Normative receipt shape

A LogLine receipt JSON object MUST keep these fields at the top level:

```text
receipt_version
who
did
this
when
confirmed_by
if_ok
if_doubt
if_not
status
```

The canonical slots MUST NOT be nested under `body.logline`.

A receipt MAY include:

```text
result
evidence
transport
hashes
```

When a receipt claims this encoding profile, `hashes` SHOULD include:

```text
json_canonicalization = jcs-rfc8785
tuple_hash_profile    = logline-length-prefixed-v0
algorithm             = sha256
tuple_hash
result_hash
receipt_hash
```

The receipt MUST NOT duplicate branch decision with authoritative parallel
fields such as `selected_route` or `decision`. Diagnostic traces MAY exist
inside `evidence`, but the authoritative branch structure remains
`if_ok`, `if_doubt`, `if_not`, and `status`.

## Hashes

### tuple_hash

`tuple_hash` MUST equal the existing `canonical_tuple_digest` over exactly the
nine slots.

It MUST use the existing LogLine length-prefixed tuple profile:

```text
logline-length-prefixed-v0
```

It MUST NOT include:

```text
result
evidence
transport
hashes
receipt_id
environment variables
CLI flags
stdout
stderr
adapter metadata
storage metadata
```

### result_hash

`result_hash` MUST hash the canonical JSON representation of `result`.

The JSON canonicalization profile is:

```text
jcs-rfc8785
```

The primary algorithm for v0 is:

```text
sha256
```

### receipt_hash

`receipt_hash` MUST hash the canonical JSON representation of the full receipt.

It MUST exclude only `hashes.receipt_hash` to avoid recursion.

It MAY include computed `hashes.tuple_hash` and `hashes.result_hash`.

It MUST include transport, evidence, responsibility, and dispatch context when
those fields are present in the receipt.

The JSON canonicalization profile is:

```text
jcs-rfc8785
```

The primary algorithm for v0 is:

```text
sha256
```

## Canonicalization profile

This LIP defines three explicit profiles:

```json
{
  "json_canonicalization": "jcs-rfc8785",
  "tuple_hash_profile": "logline-length-prefixed-v0",
  "algorithm": "sha256"
}
```

JCS / RFC 8785 applies to JSON `result_hash` and JSON `receipt_hash`.

`canonical_tuple_digest` remains the LogLine tuple algorithm and is not replaced
by JCS.

BLAKE3 is not primary in v0. Additional hashes such as BLAKE3 MAY be introduced
later for artifacts, large snapshots, or storage optimization, but they are not
required by this LIP.

## Receipt semantics

`result` describes what the act produced.

`evidence` describes how the act is known, bounded, scoped, or witnessed.

`transport` describes who or what emitted, transported, stored, or witnessed the
receipt.

`if_ok` is the route when evidence releases the real.

`if_doubt` is the route when evidence is insufficient, ambiguous, or simulated.

`if_not` is the route when the act is rejected, contradicted, or forbidden.

`status` closes the history.

Receipt scope is narrow:

```text
simulation receipt proves simulation only
release receipt    proves release only
execution receipt  proves adapter execution only
closure receipt    proves final status transition only
```

A receipt MUST NOT be generalized beyond its scope.

## Composition rules

A receipt MAY reference prior receipts in `evidence.input_receipts` using
`receipt_hash`.

A receipt MAY reference `result_hash` when only result identity is needed.

A receipt MAY become `confirmed_by` evidence for a later LogLine.

A receipt MUST NOT claim authority beyond the act, result, evidence, transport,
and hashes it actually records.

## Examples

The canonical examples for this profile are:

```text
examples/receipt.simulation.json
examples/receipt.passport.json
examples/receipt.admission.json
examples/receipt.execution.json
```

`receipt.passport.json` is an identity receipt example only. A passport
identifies; it does not authorize.

`receipt.admission.json` demonstrates scoped admissibility. The receipt persists
after admissibility expires.

`receipt.execution.json` demonstrates adapter execution. It does not prove the
prior release, except by referencing the release/admission receipt used as
evidence.

## Conformance

The conformance suite SHOULD test:

- same result object with different property order produces the same `result_hash`;
- same full receipt with different property order produces the same `receipt_hash`;
- changing `transport` changes `receipt_hash`;
- changing `result` changes `result_hash` and `receipt_hash`;
- changing only `transport` does not change `result_hash`;
- `tuple_hash` ignores `result`, `evidence`, and `transport`;
- `receipt_hash` excludes `hashes.receipt_hash`;
- simulation receipt has `executed = false` and `released = false`;
- receipt can reference an input receipt hash;
- receipt with nested `body.logline` is invalid for this profile.

## Acceptance criteria

This LIP can move from Proposed to Accepted when:

- `spec/receipt-encoding.md` exists;
- `spec/receipt-encoding.schema.json` exists;
- the four canonical receipt examples exist;
- receipt conformance cases exist;
- docs distinguish tuple hash, result hash, and receipt hash.

It can move from Accepted to Implemented when:

- a conforming runtime can validate the receipt fixtures;
- a conforming runtime can compute `tuple_hash`, `result_hash`, and
  `receipt_hash`;
- tests demonstrate that `canonical_tuple_digest` remains unchanged and covers
  exactly the nine-slot tuple;
- tests demonstrate that JSON property order does not change `result_hash` or
  `receipt_hash`.
