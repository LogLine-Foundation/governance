# LIP-0002 - if_doubt as auditable simulation

Status: Proposed  
Author: LogLine Foundation draft  
Target canon: if_doubt addendum  
Created: 2026-05-06

## Abstract

This LIP promotes `if_doubt` from a suspension route into a canonical,
auditable simulation branch. It defines the rule that possible worlds may be
examined without becoming facts, and it requires a receipt or trace whenever a
simulation affects runtime routing.

## Motivation

The current canon correctly prevents release without evidence, but it
underspecifies what the
runtime may do with doubt. Without a stronger rule, `if_doubt` risks becoming a
loose fallback, a hidden manual path, or a place where generated language
pretends to be action.

This LIP makes doubt productive while preserving discipline.

## Normative rule

```text
if_doubt is where possible worlds may be tested without becoming facts.
```

## Required behavior

A conforming runtime:

1. MUST NOT execute external consequence from `if_doubt`.
2. MUST NOT silently convert a doubt route into an ok route.
3. SHOULD emit a doubt trace when routing enters `if_doubt`.
4. SHOULD emit a simulation receipt when it performs a simulation.
5. MAY generate a possible-world LogLine, provided it remains epistemically marked.
6. MUST leave `released = false` for simulated consequences.

## Receipt shape

```json
{
  "receipt_kind": "simulation",
  "canon_version": "0.2.0-draft",
  "logline_digest": "sha256:...",
  "branch": "doubt",
  "reason": "receipt_missing",
  "simulated_route": "ask_or_suspend",
  "executed": false,
  "released": false
}
```

## Conformance tests

The conformance suite MUST include cases for:

- `confirmed_by = none` with `if_doubt = suspend`, expected ambiguous.
- `if_doubt = simulate`, expected simulated and unreleased.
- unknown doubt route, expected invalid or ambiguous according to canon version.
- simulation receipt that claims `executed = true`, expected invalid.
- promotion from simulated to released without new evidence, expected invalid.

## Backward compatibility

Existing LogLines remain valid when their routes are valid under the canon. The
new obligations apply only to runtimes claiming support for this addendum or
LIP-0002 support.

## Acceptance criteria

This LIP can move from Proposed to Accepted when:

- the normative rule is added to the textual spec;
- the JSON schema accepts the receipt and trace shapes;
- `conformance/cases.json` contains valid, invalid, and ambiguous fixtures;
- a runtime declaration can report partial support.

It can move from Accepted to Implemented when:

- the CLI can run the conformance suite;
- a runtime emits a doubt trace for `if_doubt` routing;
- a runtime emits a simulation receipt for simulation routes;
- tests demonstrate that `if_doubt` cannot execute consequence.
