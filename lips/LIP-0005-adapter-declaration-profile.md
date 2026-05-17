# LIP-0005 - Adapter Declaration Profile

Status: Draft
Type: Lateral canon extension
Scope: Adapter declarations, manifests, effect classes, and projections
Depends on: LIP-0004 Adapter Protocol
Primary lateral normative source: `source/adapter-declaration-profile.logline`
Does not change: the canonical nine slots

## Summary

This LIP defines how adapters declare themselves without becoming canon. It
provides a declaration profile that can be projected into JSON Schemas,
TypeScript interfaces, Rust traits, Python protocols, OpenAPI documents, MCP
manifests, CLI flags, or database schemas.

The declaration profile is lateral. The LogLine declaration remains the
institutional shape. JSON manifests and language types are auxiliary.

Core rule:

```text
LogLine declaration first.
Projection second.
Implementation elsewhere.
```

## Preserved canon

This LIP adds no runtime slot and no runtime crate.

The adapter declaration profile exists to describe how a world adapter claims:

```text
identity
kind
version
effect class
supported receipt scopes
supported projections
canon version
protocol version
```

## Adapter declaration LogLine

Every adapter declaration MUST be expressible as a LogLine.

Example:

```logline
adapter:http_inbound declare_adapter inbound_http_translation 2026-05-08T10:00:00Z confirmed_by receipt:sha256:author123 if_ok accept_adapter_declaration if_doubt quarantine_adapter if_not reject_adapter status declared
```

A JSON manifest MAY accompany the declaration, but the manifest is not the
canonical source of authority.

## Adapter kinds

Allowed adapter kinds for this profile:

```text
inbound
evidence
runtime_invocation_transport
release
receipt_sink
```

An implementation MAY use domain-specific subtypes, but it MUST preserve the
canonical kind.

## Effect classes

Adapters SHOULD declare one effect class:

```text
none
read
write
external_effect
irreversible_external_effect
```

Interpretation:

| Effect class | Meaning |
|---|---|
| `none` | Does not read or alter relevant external systems. |
| `read` | Reads external state. |
| `write` | Writes controlled storage or state. |
| `external_effect` | Produces an effect in another system. |
| `irreversible_external_effect` | Produces an effect that is hard or impossible to undo. |

Rule:

```text
Greater effect requires stronger evidence.
```

## Manifest profile

The JSON manifest is an auxiliary projection.

Minimum shape:

```json
{
  "adapter_id": "adapter:http_inbound",
  "adapter_version": "0.1.0",
  "adapter_kind": "inbound",
  "protocol_version": "adapter-protocol-v0",
  "canon_version": "0.2.0-draft",
  "effect_class": "none",
  "declaration_logline": "adapter:http_inbound declare_adapter inbound_http_translation 2026-05-08T10:00:00Z confirmed_by receipt:sha256:author123 if_ok accept_adapter_declaration if_doubt quarantine_adapter if_not reject_adapter status declared",
  "supports": [
    "http_request_to_logline",
    "translation_receipt",
    "external_input_digest"
  ],
  "receipt_scopes": [
    "translation_receipt"
  ]
}
```

The manifest MUST NOT add runtime slots.

The manifest MUST NOT claim canonical branch authority outside:

```text
if_ok
if_doubt
if_not
status
```

## Projections

Allowed projections:

```text
JSON Schema
TypeScript interfaces
Rust traits
Python protocols
OpenAPI specs
MCP manifests
CLI flags
HTTP endpoints
database schemas
```

Projection rule:

```text
Projections implement adapter contracts.
Projections do not redefine adapter contracts.
```

## Non-goals

This LIP does not implement:

```text
SDK
HTTP adapter
Supabase adapter
MCP adapter
GitHub adapter
Slack adapter
Cloudflare adapter
Gateway adapter
Minilab adapter
```

It only defines the declaration profile those adapters should obey if they are
created outside the canon.

## Acceptance criteria

This LIP is ready for initial merge when:

```text
source/adapter-declaration-profile.logline exists
spec/adapter-declaration-profile.md exists
spec/adapter-declaration.schema.json exists
an inbound adapter declaration example exists
a release adapter declaration example exists
effect classes are documented
JSON manifest is labeled auxiliary
projections are labeled non-normative
no SDK or concrete world adapter is implemented
no tenth slot is added
```
