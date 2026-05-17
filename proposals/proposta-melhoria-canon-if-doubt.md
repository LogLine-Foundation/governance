# Proposta de melhoria do cânon - if_doubt como simulação auditável

## Tese

A próxima melhoria forte do LogLine não é aumentar a anatomia. É dar estatuto
normativo ao que já está implícito na forma:

```text
confirmed_by collapses evidence. if_ok releases the real. if_doubt simulates the possible. if_not records the false. status closes the history.
```

O cânon atual já tem um desenho bom: 9 slots, pivô em `confirmed_by`, rotas
`if_ok` / `if_doubt` / `if_not`, digest canônico e ciclo de status. A melhoria
natural é fazer `if_doubt` carregar uma semântica positiva e verificável.

## Regra proposta

```text
if_doubt is where possible worlds may be tested without becoming facts.
```

Em português operacional:

```text
if_doubt é o lugar onde mundos possíveis podem ser testados sem virarem fatos.
```

Dúvida não é erro. Dúvida é ensaio governado.

## Por que isso importa

Sem essa regra, `if_doubt` pode virar um fallback frouxo. Com essa regra, ele se
torna o espaço canônico para:

- simular uma consequência;
- pedir evidência;
- suspender sem liberar;
- gerar uma LogLine hipotética;
- deixar rastro do motivo da dúvida;
- impedir que uma LLM transforme possibilidade em fato.

## Mudança mínima no cânon

Adicionar as leis:

```text
if_doubt MAY simulate, ask, suspend, dispatch, or clarify.
if_doubt MUST NOT execute consequence.
if_doubt MUST NOT silently convert doubt into ok.
if_doubt MUST emit or preserve a trace when it affects routing.
```

## Novo artefato: simulation receipt

Quando o runtime simula uma consequência em `if_doubt`, ele deve emitir um
receipt com escopo explícito:

```json
{
  "receipt_kind": "simulation",
  "branch": "doubt",
  "reason": "receipt_missing",
  "simulated_route": "ask_or_suspend",
  "executed": false,
  "released": false
}
```

Esse receipt prova que houve simulação. Não prova execução.

## Novo artefato: doubt trace

Toda dúvida relevante deve deixar rastro mínimo:

```json
{
  "branch": "doubt",
  "reason": "receipt_missing",
  "simulated_route": "ask_or_suspend",
  "released": false
}
```

## Classe oficial: ambiguous

O conformance suite deve aceitar três classes:

```text
valid
invalid
ambiguous
```

`ambiguous` é uma classe nobre. Não é meio inválido. É material que deve ir para
`if_doubt` porque ainda não pode ser liberado nem rejeitado honestamente.

## CLI prioritário

A implementação mais importante é esta:

```bash
logline conformance run conformance/cases.json
```

O segundo comando é:

```bash
logline canon check --schema spec/logline-canon.schema.json source/logline.canon.json
```

Ordem correta:

1. validar schema;
2. validar semântica;
3. rodar conformance;
4. emitir relatório ou receipt.

## Roadmap recomendado

1. Incorporar `spec/if-doubt-simulation.md`.
2. Aceitar LIP-0002 como proposta.
3. Aplicar backlog de LIP-0001: schema, conformance, runtime declaration.
4. Implementar `doubt_trace` no runtime walk.
5. Implementar `simulation_receipt` sem execução de consequência.
6. Adicionar CI com `fmt`, `clippy`, `test`, schema parse e fixture shape.
7. Só depois falar em publicar essa evolução como cânon estável.

## Definição honesta de sucesso

A proposta estará implementada quando um runtime conseguir produzir um relatório
como este:

```json
{
  "runtime": "logline-runtime-rs",
  "canon_version": "0.2.0-draft",
  "conformance_status": "partial",
  "supports": [
    "valid_invalid_ambiguous",
    "if_doubt_trace",
    "simulation_receipt"
  ]
}
```

E quando os casos de `if_doubt` provarem que simulação não executa consequência.
