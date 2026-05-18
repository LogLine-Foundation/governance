# LIP-0008 — LLM Tier Discipline and Dossier Discipline

Status: Proposed
Author: Dan Voulez
Created: 2026-05-18

## Sentence of stone

```
We do not ask Frontier to do Mini's job.
We do not ask Mini to do Operator's job.
We do not ask Operator to execute.
We do not ask Translator to authorize.
We do not ask any LLM to close evidence.

Each tier receives the smallest grammar it can honestly carry.
Frontier receives dossiers, not mess.
```

## 1. Problem

Before this LIP, "LLM" was a word that hid four different talents:

- constant cheap classification
- session / workorder operation
- natural-language-to-LogLine translation
- rare high-judgment decision

When those talents are mixed, the system collapses back into the anti-pattern:

```
throw the mess at the biggest model and pray
```

This violates the Minilab discipline. The objective is not to use the largest model possible. The objective is to use the **smallest sufficient talent**, with the **smallest sufficient grammar**, at the **right point in the pipeline**.

## 2. Doctrine

```
Mini LLMs operate.
Operators conduct.
Translators interpret.
Frontiers decide — on dossiers, not mess.

Each tier has a grammar.
Each grammar has an admission rule.
Rising tier is an efficiency failure, not a capability badge.
```

## 3. The four LLM tiers

| Tier | Typical size | Grammar | Mandate | Location | Call pattern |
|---|---|---|---|---|---|
| **Mini** | 1.5–3.5B, or hot local 7B where available | Operational Grammar | classify, extract, mark uncertainty, propose small candidates | dedicated LAB / Rust Mistral substrate | sustained, cheap, 24/7 |
| **Operator** | 9–14B local | Strong Grammar | conduct session, decompose goals, route workorders | local | per-session |
| **Translator** | 9–14B local, escalates when needed | Strong Grammar | natural language → LogLine candidate | local first, frontier-assisted if needed | per utterance |
| **Frontier** | external | Dossier only | yes/no or bounded judgment on already-prepared case | external API | rare |

## 4. Grammars by tier

```rust
pub enum GrammarKind {
    Operational,
    Strong,
    Dossier,
}
```

### Operational Grammar

Used by Mini LLMs. Intentionally simple:

- classify intent
- extract entities
- detect missing slot
- detect ambiguity
- suggest `if_doubt`
- produce tiny candidate fragment
- summarize receipt

Mini LLMs do **not** produce canonical LogLine closure. They produce low-cost signals.

### Strong Grammar

Used by Operators and Translators. It can produce:

- structured workorders
- full LogLine candidates
- translation candidates
- policy hints
- IR-facing intent structures
- ghost explanations

Strong Grammar is **not** execution. Strong Grammar compiles toward admissibility.

### Dossier Grammar

Used only at the Frontier boundary. Frontier does not receive the raw mess. Frontier receives:

- target decision
- evidence chain
- candidate alternatives
- summary
- cost envelope
- specific yes/no question
- known ghosts

Frontier answers the bounded question.

## 5. New core types

### LLM tier

```rust
#[derive(Debug, Clone, PartialEq, Eq, PartialOrd, Ord)]
pub enum LlmTier {
    Mini,
    Operator,
    Translator,
    Frontier,
}
```

### Capability manifest extension

```rust
use std::collections::BTreeSet;

#[derive(Debug, Clone)]
pub struct CapabilityManifest {
    pub substrate_id: String,
    pub supported_primitives: BTreeSet<String>,
    pub allowed_ingress_tiers: BTreeSet<LlmTier>,
    pub allowed_grammars: BTreeSet<GrammarKind>,
}
```

**Admission rule:** a substrate may only accept an ingress tier and grammar pair explicitly allowed by its `CapabilityManifest`.

Example:

```
ingress_via(Operational) requires tier in { Mini, Operator }
ingress_via(Strong)      requires tier in { Operator, Translator }
ingress_via(Dossier)     requires tier == Frontier
```

If not:

```
AdmissionRuling::Denied
reason: illegitimate tier/grammar pair
```

## 6. Dossier as first-class type

```rust
pub struct Dossier {
    pub dossier_id: String,
    pub target_decision: DecisionRequest,
    pub evidence_chain: Vec<EvidenceRecord>,
    pub alternatives: Vec<Candidate>,
    pub summary: String,
    pub cumulative_cost: CostEnvelope,
    pub frontier_question: String,
    pub known_ghosts: Vec<GhostRecord>,
}

pub enum FrontierVerdict {
    Yes {
        reason: String,
        signed_at: String,
    },
    No {
        reason: String,
        alternative_suggestion: Option<String>,
    },
}
```

**Rule:** `Dossier` is the only admissible shape crossing the Frontier boundary.

**Forbidden:**

- raw chat history
- unbounded workspace context
- entire repo dumps
- ambiguous "please solve this"
- provider miracle prompt

**Allowed:**

- specific yes/no decision
- prepared alternatives
- receipt chain
- known ghosts
- scope
- cost envelope
- bounded question

## 7. Receipt chain

The target chain for one consequential utterance:

```
1. Mini operational receipt
2. Translator receipt
3. Pocket runtime receipt
4. Canon receipt
5. Admission receipt
6. Plan receipt
7. Optional Frontier dossier receipt
8. Execution receipt
9. Evidence closure receipt
```

Translation is **not** invisible preprocessing: the LLM Translator produces an auditable act, with pocket runtime confirmation, before the Engine validates/canonizes.

## 8. Three hashes as talent boundaries

Receipt v0 having three hashes is no longer "technical paranoia." It expresses talent boundaries:

```
tuple_hash
  human act boundary
  9 slots only
  what act is being asserted

content_hash
  translator interpretation boundary
  9 slots + AUX
  what semantic additions the translator introduced

envelope_hash
  transport boundary
  sender / receiver / sent_at / route metadata
  how the act moved
```

Each hop crosses exactly one boundary.

The current canon in `LogLine-Foundation/canon/logline.receipt.v0` already uses these names. This LIP records the doctrinal meaning of that naming choice.

## 9. Entropy as policy input

LLM cost is not only money. LLM cost is **entropy**.

Entropy can be measured by:

- translation confidence
- rate of `if_doubt`
- divergence between N translations of the same input
- slot disagreement
- AUX disagreement
- perplexity / uncertainty score if available
- pocket runtime rejection rate
- escalation rate by tier

Policy can then say:

```
PolicyClass A:
  requires low entropy
  requires local deterministic validation
  no Frontier needed unless contradiction

PolicyClass B:
  allows moderate entropy
  requires Pocket Runtime clean pass

PolicyClass C:
  allows higher entropy
  requires human confirmation or Tower gate

PolicyClass D:
  denies material execution
  may produce ghost/dossier only
```

"LLM caro" stops being vague. It becomes:

- high entropy
- high escalation
- high disagreement
- high ghost rate

The Pocket Runtime can reject or ghost when entropy crosses threshold.

## 10. Health metric

The institutional health metric is:

```
% of acts resolved at the lowest legitimate tier
```

**Good:**

- Mini resolves classification.
- Operator resolves routing.
- Translator emits clean candidate.
- CR admits or ghosts.
- No Frontier needed.

**Bad:**

- Frontier called for ordinary classification.
- Operator asked to parse trivial entities.
- Translator asked to decide authority.
- Mini asked to produce Strong Grammar.

Escalation is not shameful. But it is a measurable efficiency failure.

## 11. Anti-patterns

Stop immediately if implementation introduces:

- Frontier receives raw mess.
- Mini LLM emits canonical closure.
- Operator sits behind dispatcher.
- Translator approves execution.
- Pocket Runtime becomes optional.
- Strong Grammar bypasses Engine.
- Operational Grammar mutates provider state.
- Dossier lacks receipt chain.
- Frontier verdict is treated as evidence closure.
- Tier escalation is invisible.

## 12. Minimal spike

**GOLDEN-004-llm-tier-discipline-no-frontier-mess**

**Input:**

```
"atualiza o host runtime do lab512 pra v0.2"
```

**Expected path:**

```
 1. Surface receives natural language.
 2. Mini LLM attempts Operational classification.
 3. Mini emits operational receipt:
      intent_kind = update_runtime
      target = lab512
      component = host_runtime
      uncertainty = medium/high
 4. Pocket Runtime checks candidate fragments.
 5. Local Translator emits Strong Grammar / LogLine candidate.
 6. Pocket Runtime validates shape and forces status=candidate.
 7. Engine emits canonical receipt.
 8. CR evaluates admission.
 9. Because this is protected operational power:
      returns NeedsApproval or Ghost.
10. CP prepares Dossier only if escalation needed.
11. Frontier, if called, receives Dossier only.
12. No dispatch occurs.
```

**Expected outcome:**

```json
{
  "material_execution_observed": false,
  "frontier_received_raw_mess": false,
  "lowest_tier_attempted": "Mini",
  "translator_used": true,
  "pocket_runtime_passed": true,
  "engine_canon_receipt": true,
  "admission_result": "NeedsApproval",
  "dispatch_allowed": false
}
```

## 13. Implementation order

### PR 1 — Types

- Add `LlmTier`
- Add `GrammarKind`
- Extend `CapabilityManifest`
- Add `Dossier`
- Add `FrontierVerdict`

Tiny patch. High leverage. Lands in `LogLine-Foundation/constitutional-runtime`.

### PR 2 — Admission rules

Admission checks tier + grammar legitimacy:

- Operational cannot Execute.
- Strong can propose / compile.
- Dossier can cross Frontier boundary only.

Lands in `LogLine-Foundation/constitutional-runtime/src/admission.rs` (or a new `tier_admission.rs`).

### PR 3 — Pocket Runtime packaging

Create `logline-pocket-runtime` crate in `LogLine-Foundation/engine`. Import the 9 slot validators. Expose:

```rust
fn check(draft: LogLineDraft) -> PocketRuntimeRuling
```

### PR 4 — Spike

- Mock Mini
- Mock Translator
- Real Pocket Runtime
- Real Engine receipt
- Real CR admission
- **No dispatch**

End-to-end proof that the 9-step receipt chain works without ever calling Frontier and without any side effect.

## 14. Final invariant

```
The bigger the model, the smaller the mess it should receive.
```

Mini gets noisy small tasks because it is cheap and constant.
Operator gets structured session context.
Translator gets utterances plus constraints.
Frontier gets a dossier.

The Frontier is not a miracle worker. It is a **constitutional reviewer**.

## Compact doctrine

```
Split labour by talent.

Humans sign consequence.
Mini LLMs operate simple grammar.
Operators conduct Strong Grammar.
Translators turn language into LogLine candidates.
Pocket Runtime lowers entropy.
Engine canonizes.
Runtime admits.
Tower authorizes.
LABs execute.
Receipts close.
Frontier receives dossiers, not mess.
Receipts beat stories.
```

## Artifact Honesty Receipt

```
Phase:
  LIP-0008 draft from thesis articulation.

Scope claimed:
  Converted the tiered-LLM thesis into a proposed constitutional spec.

Files changed:
  This file only.

Commands run:
  None.

Tests run:
  None.

Evidence produced:
  Planning / specification text only.

What is proven:
  The doctrine is internally coherent with the Minilab architecture,
  character model, LLM translator flow, three-hash receipt model
  (LIP-0007), and the existing Operational Grammar / Strong Grammar
  modules already present in LogLine-Foundation/constitutional-runtime.

What is not proven:
  Code exists for LlmTier, Dossier, CapabilityManifest extension,
  tier-aware admission, or the end-to-end receipt chain.

Known ghosts:
  Actual code patch not applied.
  Operational Grammar and Strong Grammar wiring to LLM tiers still
  needs runtime receipts.

Next required gate:
  Implement PR 1:
    LlmTier
    GrammarKind
    Dossier
    FrontierVerdict
    CapabilityManifest tier/grammar fields
```
