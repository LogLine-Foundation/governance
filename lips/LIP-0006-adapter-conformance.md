# LIP-0006 - Adapter Conformance

Status: Draft
Type: Lateral canon extension
Scope: Adapter conformance cases and reporting
Depends on: LIP-0004 Adapter Protocol, LIP-0005 Adapter Declaration Profile
Primary lateral normative source: `source/adapter-conformance.logline`
Does not change: the canonical nine slots

## Summary

This LIP defines how adapters may claim conformance to the Adapter Protocol
without claiming conformance to the whole LogLine canon.

Core rule:

```text
Adapter conformance is lateral.
Runtime conformance is canonical.
Do not confuse them.
```

## Conformance levels

Adapters MAY report:

```text
none
partial
full
failed
unknown
```

An adapter MUST NOT report `full` without a conformance report.

An adapter MUST NOT claim full LogLine canon conformance merely because it
passes adapter protocol cases.

## Minimum conformance expectations

The initial adapter conformance suite SHOULD test:

```text
adapter does not redefine canon
external input becomes LogLine candidate
confirmed_by is resolved or fails audibly
runtime invocation transport invokes without judging
release adapter requires if_ok before external effect
if_doubt does not mutate world by default
receipt proves scope
receipt sink preserves hash and scope
product examples do not become canonical adapters
adapter manifest is auxiliary
projection does not redefine protocol
technical failure is not automatic if_not
```

## Conformance report

A conformance report MAY be encoded as JSON, but it SHOULD be receiptable under
LIP-0003.

Minimum report shape:

```json
{
  "profile": "adapter-protocol-v0",
  "adapter_id": "adapter:http_inbound",
  "adapter_version": "0.1.0",
  "canon_version": "0.2.0-draft",
  "protocol_lips": [
    "LIP-0004",
    "LIP-0005",
    "LIP-0006"
  ],
  "status": "partial",
  "cases": [
    {
      "case_id": "external_input_becomes_logline_candidate",
      "observed": "pass"
    }
  ]
}
```

## Technical failures

Technical failures MUST be reported as adapter, transport, storage, or
projection failures. They MUST NOT be silently converted into canonical `if_not`.

Examples:

```text
timeout
network_error
parse_error
runtime_unavailable
storage_unavailable
invalid_json
missing_technical_permission
```

## Non-goals

This LIP does not implement a conformance runner.

It defines the conformance surface and cases that a future runner can execute.

## Acceptance criteria

This LIP is ready for initial merge when:

```text
source/adapter-conformance.logline exists
spec/adapter-conformance.md exists
conformance/adapter-cases.json exists
adapter conformance is described as lateral
full conformance requires a report
technical failures are distinguished from canonical if_not
no conformance runner is implemented
no world-specific adapter is implemented
no tenth slot is added
```
