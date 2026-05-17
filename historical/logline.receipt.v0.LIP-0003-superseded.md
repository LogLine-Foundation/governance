# LogLine Receipt Encoding

Status: Draft  
Introduces: LIP-0003 LogLine Receipt Encoding and Content Addressing

## 1. Core law

```text
A receipt does not contain a LogLine.
A receipt is a LogLine act with result, evidence, transport, and hashes.
```

## 2. Top-level shape

A receipt JSON object MUST keep the canonical LogLine slots at top level:

```text
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

The receipt MUST also include:

```text
receipt_version
```

The canonical slots MUST NOT be nested under `body.logline`.

A receipt MAY include:

```text
result
evidence
transport
hashes
```

## 3. Branch authority

The authoritative branch structure is:

```text
if_ok
if_doubt
if_not
status
```

A receipt MUST NOT introduce authoritative parallel branch fields such as:

```text
selected_route
decision
```

Diagnostic traces MAY exist inside `evidence`. They are evidence about the walk,
not replacement branch authority.

## 4. Hash profile

Receipts claiming this profile SHOULD declare:

```json
{
  "json_canonicalization": "jcs-rfc8785",
  "tuple_hash_profile": "logline-length-prefixed-v0",
  "algorithm": "sha256"
}
```

## 5. tuple_hash

`hashes.tuple_hash` MUST equal `canonical_tuple_digest` over exactly:

```text
who did this when confirmed_by if_ok if_doubt if_not status
```

It MUST use:

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

## 6. result_hash

`hashes.result_hash` MUST hash the JCS / RFC 8785 canonical JSON form of
`result`.

The v0 algorithm is:

```text
sha256
```

Changing JSON object property order MUST NOT change `result_hash`.

Changing result content MUST change `result_hash`.

## 7. receipt_hash

`hashes.receipt_hash` MUST hash the JCS / RFC 8785 canonical JSON form of the
full receipt, excluding only `hashes.receipt_hash`.

The receipt hash MAY include `hashes.tuple_hash` and `hashes.result_hash` once
computed.

Changing transport, evidence, responsibility, dispatch context, result, or the
nine slots MUST change `receipt_hash`.

Changing only `hashes.receipt_hash` MUST NOT change `receipt_hash`.

## 8. Semantics

`result` describes what the act produced.

`evidence` describes how the act is known or scoped.

`transport` describes who or what emitted, transported, stored, or witnessed the
receipt.

`if_ok` is the route when evidence releases the real.

`if_doubt` is the route when evidence is insufficient or ambiguous.

`if_not` is the route when the act is rejected, contradicted, or forbidden.

`status` closes the history.

## 9. Receipt scope

```text
simulation receipt proves simulation only
release receipt    proves release only
execution receipt  proves adapter execution only
closure receipt    proves final status transition only
```

A receipt MUST NOT be generalized beyond its scope.

## 10. Composition

A receipt MAY reference prior receipts in:

```text
evidence.input_receipts
```

using `receipt_hash`.

A receipt MAY reference `result_hash` when only result identity is needed.

A receipt MAY become `confirmed_by` evidence for a later LogLine.

## 11. Algorithm boundaries

JCS / RFC 8785 applies to JSON `result_hash` and JSON `receipt_hash`.

`canonical_tuple_digest` remains the LogLine tuple algorithm and is not replaced
by JCS.

BLAKE3 is not primary in v0.

Additional hashes MAY be introduced later for artifacts or snapshots, but this
profile requires only SHA-256.
