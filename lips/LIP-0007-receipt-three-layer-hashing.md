# LIP-0007 — LogLine Receipt Three-Layer Hashing

Status: Accepted (canon)
Supersedes: LIP-0003
Author: Dan Voulez
Created: 2026-05-17

## Abstract

The LogLine receipt format moves from a single `result_hash` model with embedded `result` / `evidence` / `transport` fields to a three-layer hash discipline: **tuple_hash** (the pure act, the nine slots only), **content_hash** (the interpreted act — slots plus AUX fields invented by the decomposer), and **envelope_hash** (the transported package — content plus transport metadata, computed by the sender at each boundary crossing). `result`, `evidence`, and `transport` are no longer permitted at the receipt top level.

## Motivation

LIP-0003 placed `result`, `evidence`, and `transport` inside the receipt itself. This conflated three distinct concerns:

- **The act** — what happened, as the nine canonical slots.
- **The interpretation** — what an AI or operator added when decomposing a natural-language input into nine slots: free-form AUX fields.
- **The transport** — who sent the receipt to whom, when, by which channel.

Conflating them produced ambiguous content addressing:

- A receipt's identity changed every time it crossed a transport boundary (transport fields were inside the hashed body). This broke content-addressed citation: the same act, sent twice, produced two distinct identifiers.
- A receipt could not be distinguished from a *receipt of a receipt*: results that referenced prior receipts could not be compared by identity because the act and the outcome shared the same hash.
- Evidence, which by doctrine is *attached*, not *embedded*, leaked into the act identity.

## Specification

The normative shape is defined by `logline.receipt.v0` in `LogLine-Foundation/canon`. The hash discipline:

```
tuple_hash    = sha256(jcs(pick: 9 slots))                          → pure act
content_hash  = sha256(jcs(all_except: [id, hashes]))               → interpreted act:
                                                                       receipt_version + 9 slots + AUX + json_canonicalization
envelope_hash = sha256(jcs({content, transport}))                   → transported package
                                                                       lives ONLY on the Envelope wrapper
```

Top-level fields `result`, `evidence`, `transport` are **forbidden** in v0 receipts. They are not AUX. The schema in `LogLine-Foundation/conformance/schemas/logline.receipt.v0.schema.json` rejects them.

## Non-goals

This LIP does not:

- change the nine-slot LogLine body;
- replace JCS-RFC8785 as the canonicalization profile;
- replace SHA-256 as the hash algorithm;
- define how evidence stores or transport channels work;
- define a ledger storage engine.

## Compatibility

Receipts encoded under LIP-0003 are not automatically conformant. Engines may convert them by:

1. Extracting the nine slots verbatim.
2. Recomputing `tuple_hash` over the nine slots only.
3. Recomputing `content_hash` over the new receipt without the legacy `result` / `evidence` / `transport` fields. Any AUX that was previously placed *inside* one of those fields must be promoted to a top-level AUX field (with renaming if it collides with reserved names).
4. Emitting `envelope_hash` only on the next transport boundary, not in the receipt at rest.

`result` and `evidence` from LIP-0003 receipts are lost on conversion. They were embedded; they cannot be reconstructed. Adopters who need to preserve them must record them as separate records content-addressed by the new receipt's `id`.

## Reference

- `LogLine-Foundation/canon/logline.receipt.v0`
- `LogLine-Foundation/conformance/schemas/logline.receipt.v0.schema.json`
- `LogLine-Foundation/conformance/vectors/{valid,invalid}/`
- `LogLine-Foundation/conformance/hash-profiles/jcs-rfc8785.md`

## Acceptance trail

- 2026-05-17: discussed in working session; canon and conformance repos published with the three-layer model.
- 2026-05-17: LIP-0003 marked Superseded; this LIP authored as the formal record of the change.
