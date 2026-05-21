# LIP-0004 - LogLine Adapter Protocol

Status: Proposed
Type: Lateral canon extension
Scope: Canonical contracts for external adapters
Primary lateral normative source: `source/adapter-protocol.logline`
Does not replace: `source/logline.canon.logline`
Does not change: the canonical nine slots

## Summary

This LIP defines the LogLine Adapter Protocol: a lateral canon layer that lets
external systems talk to LogLine engines without capturing, redefining, or
contaminating the canon.

Central rule:

```text
The canon does not create world-specific adapters.
The canon defines the canonical contracts any world adapter must obey.
```

LogLine does not implement HTTP, Supabase, Slack, GitHub, MCP, Cloudflare,
Gateway, Minilab, or any other specific system.

LogLine can and should define how any of those systems touches the engine:

```text
adapter translates
confirmed_by anchors evidence
runtime judges
if_ok releases
if_doubt simulates without mutation
receipt proves scope
adapter executes only after if_ok
```

## Preserved canon

This LIP adds no slots.

The canon remains:

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

The Adapter Protocol is lateral. It does not replace
`source/logline.canon.logline`.

Its lateral normative source is:

```text
source/adapter-protocol.logline
```

## Motivation

LogLine is generic enough to represent acts, evidence, doubts, releases,
rejections, simulations, and state closure.

To operate in the world, a LogLine engine must receive external input and may,
in some cases, release external effects. Those systems may include HTTP, MCP,
CLI, files, queues, databases, GitHub, Slack, Supabase, Cloudflare, workers,
local runtimes, remote runtimes, and internal systems.

Without a lateral protocol, each integration would invent its own way to
translate external input, resolve `confirmed_by`, invoke a runtime, persist
receipts, execute `if_ok`, handle `if_doubt`, report technical failure, and
prove scope.

This LIP defines that boundary.

## Non-goals

This LIP does not define:

```text
official HTTP adapter
official Supabase adapter
official Slack adapter
official GitHub adapter
official MCP adapter
official Cloudflare adapter
official Gateway adapter
official Minilab adapter
universal authentication model
mandatory database
mandatory queue
mandatory SDK
mandatory runtime
```

Terms such as passport, visa, admission, gateway, Supabase, Cloudflare, MCP,
and Minilab may appear as examples. They are illustrations, not canonical
products.

## Mental model

```text
world
  -> adapter
  -> LogLine candidate
  -> evidence resolution
  -> runtime invocation
  -> runtime judgment
  -> receipt
  -> release adapter, only after if_ok
  -> world
```

More explicitly:

```text
external input
  -> InboundAdapter translates input into LogLine candidate
  -> translation receipt may be emitted
  -> EvidenceAdapter resolves confirmed_by
  -> evidence receipt may be emitted
  -> RuntimeInvocationTransport invokes a LogLine runtime
  -> runtime invocation receipt may be emitted
  -> runtime judges candidate under canon
  -> release, doubt, rejection, or judgment receipt may be emitted
  -> ReleaseAdapter may execute released route only if if_ok was selected
  -> execution receipt may be emitted
  -> ReceiptSink may persist receipts at any receipt-producing phase
  -> persistence receipt may be emitted
```

`ReceiptSink` is not one stage before or after release. It is a transversal
persistence channel.

## Contract families

LIP-0004 defines five lateral families:

```text
inbound_adapter
evidence_adapter
runtime_invocation_transport
release_adapter
receipt_sink
```

### inbound_adapter

An inbound adapter translates external input into a LogLine candidate.

It does not judge. It does not execute external effects. It translates and
preserves traceability.

### evidence_adapter

An evidence adapter resolves, or attempts to resolve, what appears in
`confirmed_by`.

It may consult receipts, signatures, digests, declarations, identities, quorum,
or other evidence sources. It must not silently mutate the world.

### runtime_invocation_transport

A runtime invocation transport invokes a LogLine runtime by a specific medium:
CLI, HTTP sidecar, WASM, local binding, remote runtime, or test double.

Rule:

```text
transport invokes runtime;
transport does not judge.
```

### release_adapter

A release adapter executes a route released by the runtime.

It may execute external effects only after `if_ok`.

Under `if_doubt`, it must simulate, ask for evidence, or stop.

### receipt_sink

A receipt sink persists or emits receipts.

It can operate across phases:

```text
translation receipt
evidence receipt
runtime invocation receipt
release receipt
execution receipt
persistence receipt
```

It must preserve receipt hash and receipt scope.

## Lateral normative source

The first patch for this LIP adds:

```text
source/adapter-protocol.logline
```

That file defines the core adapter contracts as LogLines. This Markdown explains
the rules, but does not replace the LogLine source.

## Core normative rules

Adapters may translate, read, persist, invoke, observe, or execute. They may not
redefine the slots and may not create authority above:

```text
confirmed_by
if_ok
if_doubt
if_not
status
```

External input must become a LogLine candidate before runtime judgment.

`confirmed_by` must be resolvable, or fail audibly into doubt, contradiction,
or technical uncertainty.

`if_ok` releases a route; it is not automatically the external effect.

`if_doubt` must not mutate the external world by default:

```text
released = false
executed = false
```

Receipts prove scope, not universal truth.

Technical failure is not automatically `if_not`. A timeout, network error,
parse error, unavailable runtime, unavailable storage, invalid JSON, or missing
technical permission is adapter or transport uncertainty unless canonically
judged otherwise.

## Product examples

Examples of Gateway, Supabase, Minilab, MCP, Slack, GitHub, passport, visa, or
admission are illustrative only. They are not canonical adapters.

## Relationship to later LIPs

This LIP defines the protocol boundary.

LIP-0005 defines adapter declarations, manifests, effect classes, and
projections.

LIP-0006 defines adapter conformance cases and reporting.

## Acceptance criteria

This LIP is ready for initial merge when:

```text
source/adapter-protocol.logline exists
the five contract families exist
runtime_invocation_transport is used instead of runtime_transport
receipt_sink is described as transversal
if_ok is required before external effect
if_doubt does not mutate the world by default
adapter cannot redefine canon
receipts prove scope
product examples are explicitly non-canonical
no world-specific adapter is implemented
no mandatory SDK is introduced
no tenth slot is added
```
