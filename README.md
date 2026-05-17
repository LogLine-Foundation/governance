# LogLine Foundation — Governance

LogLine Improvement Proposals (LIPs) plus the governance process behind them.

```
canon defines.
conformance proves.
engine implements.
governance evolves.
```

This repository is **governance**. It does not define the canon. It does not own conformance. It does not implement the runtime. It records the **process** by which all of those change — debate, proposal, acceptance, supersession, errata.

## Contents

| path | role |
|---|---|
| `GOVERNANCE.md` | initial authority boundary for the LogLine canon |
| `governance-delta.md` | extended governance (LIP lifecycle, roles, quorum, supersession) |
| `lips/LIP-XXXX-*.md` | each LIP is one normative proposal |
| `lips/TEMPLATE.md` | template for new LIPs |
| `proposals/` | informal proposals — feed into LIPs once mature |

## LIP lifecycle (current)

```
Draft → Proposed → Accepted → Implemented → Superseded
                       ↓             ↓
                   Rejected      Reverted
                       ↓
                   Withdrawn
```

## Current LIPs

| id | title | status |
|---|---|---|
| LIP-0001 | Canon hardening and conformance | Accepted (sets the discipline this repo now formalizes) |
| LIP-0002 | If-doubt: auditable simulation | Proposed |
| LIP-0003 | LogLine receipt encoding and content addressing | **Superseded by LIP-0007** |
| LIP-0004 | LogLine adapter protocol | Proposed |
| LIP-0005 | Adapter declaration profile | Proposed |
| LIP-0006 | Adapter conformance | Proposed |
| LIP-0007 | Receipt three-layer hashing | Accepted (canon, in `LogLine-Foundation/canon`) |

## Authoring a new LIP

1. Copy `lips/TEMPLATE.md` to `lips/LIP-NNNN-short-title.md` (next available number).
2. Fill in: Status (start `Draft`), Author, Created, Abstract, Motivation, Specification, Non-goals, Compatibility, Reference.
3. Open a pull request.
4. Discussion happens on the PR.
5. On acceptance, change Status to `Accepted` (or `Proposed` first if a runtime needs to claim implementation feasibility).
6. If a LIP changes the canon, the corresponding file in `LogLine-Foundation/canon` is updated **in the same PR cycle** (in a sibling PR to the canon repo, linked from the LIP).

## Supersession

A LIP is never deleted. When superseded, its `Status:` header changes, a `Superseded by:` line is added, and the superseding LIP records the change in its `Acceptance trail`. See LIP-0003 ↔ LIP-0007 for the worked example.

## Related repos

- [`LogLine-Foundation/canon`](https://github.com/LogLine-Foundation/canon) — normative spec
- [`LogLine-Foundation/conformance`](https://github.com/LogLine-Foundation/conformance) — tests and machinery proving engine adherence
- [`LogLine-Foundation/engine`](https://github.com/LogLine-Foundation/engine) — Rust implementation of the canon
