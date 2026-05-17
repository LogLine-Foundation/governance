---
lip: 0001
title: Hardening do Canon e Conformidade Independente
status: Proposed
type: Normative / Process
author: LogLine Foundation
created: 2026-05-06
canon-package-target: living package
---

# LIP-0001 - Hardening do Canon e Conformidade Independente

## 1. Resumo

Esta LIP propoe transformar o canon LogLine em uma base normativa, auditavel e
implementavel por multiplos runtimes.

A proposta preserva o nucleo doutrinario do LogLine, especialmente a estrutura
de nove posicoes e a centralidade de `confirmed_by`, mas desloca a autoridade
primaria do runtime de referencia para o canon publicado.

O objetivo e permitir que uma equipe externa, em outra linguagem e com outro
runtime, implemente o mesmo canon e consiga demonstrar conformidade sem depender
da interpretacao interna do runtime Rust.

## 2. Principio Diretor

**LogLine e canon. O runtime e uma implementacao aderente, nao a origem da
norma.**

A norma deve ser estavel, citavel, versionada e testavel. O runtime pode
evoluir, otimizar, experimentar e especializar, mas nao deve ser tratado como
fonte primaria da verdade normativa.

## 3. Motivacao

O canon LogLine ja apresenta uma forma conceitualmente forte: compacta,
semantica e orientada a evidencia. Porem, sua adocao por terceiros nao deve
depender de pastas historicas, zips antigos ou comportamento interno do runtime
de referencia.

Essa dependencia cria tres riscos:

1. **Ambiguidade normativa** - implementadores externos podem nao distinguir
   claramente entre regra do canon, decisao editorial, comportamento acidental
   do runtime e convencao de exemplo.
2. **Baixa auditabilidade** - sem schema, gramatica formal e corpus de
   conformidade, a aderencia permanece mais declarativa do que verificavel.
3. **Acoplamento institucional** - se o runtime Rust for percebido como a
   propria norma, outras implementacoes passam a ser traducoes subordinadas, e
   nao participantes legitimas do ecossistema.

Esta LIP propoe a separacao explicita entre norma, implementacao, prova e
governanca.

## 4. Objetivos

Esta LIP tem os seguintes objetivos:

- Separar formalmente o que e canon do que e implementacao.
- Publicar artefatos normativos minimos para adocao por terceiros.
- Criar uma base de conformidade objetiva, com casos validos e invalidos.
- Elevar `confirmed_by` de campo doutrinario para mecanismo verificavel em
  camadas.
- Instituir governanca editorial independente do ciclo tecnico do runtime.
- Permitir que implementacoes externas provem aderencia a uma versao especifica
  do canon.

## 5. Nao Objetivos

Esta LIP nao pretende:

- Redefinir o nucleo de nove posicoes do LogLine.
- Substituir o runtime Rust como implementacao de referencia.
- Introduzir dependencia obrigatoria de infraestrutura pesada de identidade,
  blockchain ou autoridade externa.
- Exigir prova criptografica plena para todos os usos do LogLine desde a
  primeira versao.
- Transformar o canon em um formato excessivamente academico ou inacessivel a
  operacao cotidiana.

## 6. Tese Normativa

A melhoria do canon deve preservar sua elegancia operacional, mas precisa
produzir autonomia documental suficiente para que o ecossistema sobreviva fora
do runtime de referencia.

Uma implementacao deve poder responder, com evidencia:

> Implementei LogLine contra a versao X do canon, rodei o corpus de
> conformidade, produzi receipts e consigo demonstrar aderencia sem consultar
> comportamento interno do runtime Rust.

Essa frase define o alvo institucional desta LIP.

## 7. Distribuicao Normativa Minima

A versao endurecida do canon deve publicar, no minimo, os seguintes artefatos:

| Artefato | Funcao | Resultado esperado |
|---|---|---|
| `spec/logline-canon.md` | Texto normativo com uso explicito de MUST, SHOULD e MAY. | Leitura oficial, citavel e estavel. |
| `spec/grammar.ebnf` | Gramatica formal da forma textual `.logline`. | Parsers independentes consistentes. |
| `spec/logline-canon.schema.json` | Schema formal do canon em JSON. | Validacao por ferramentas externas. |
| `conformance/` | Corpus de casos que passam e falham, com motivo esperado. | Prova objetiva de aderencia. |
| `docs/canon-vs-runtime.md` | Explicacao institucional da separacao entre norma e runtime. | Doutrina coerente e ensinavel. |
| `CHANGELOG.md` | Historico de mudancas do canon. | Rastreabilidade editorial independente do runtime. |
| `GOVERNANCE.md` | Papeis, rito de alteracao, quorum e processo de decisao. | Governanca madura e previsivel. |
| `lips/` | Diretorio de propostas formais de melhoria. | Evolucao publica, argumentada e auditavel. |

## 8. Arquitetura Editorial Proposta

O canon deve ser mantido em um repositorio proprio ou, no minimo, em uma raiz
editorial claramente separada do runtime.

Estrutura sugerida:

```text
logline-canon/
  README.md
  LICENSE
  CHANGELOG.md
  GOVERNANCE.md
  CONTRIBUTING.md
  CODE_OF_CONDUCT.md
  SECURITY.md

  spec/
    logline-canon.md
    grammar.ebnf
    logline-canon.schema.json

  conformance/
    valid/
    invalid/
    ambiguous/
    README.md

  examples/
    health/
    finance/
    notarial/
    operations/
    governance/

  lips/
    LIP-0001-canon-hardening-and-conformance.md
    TEMPLATE.md

  docs/
    canon-vs-runtime.md
    evidence-model.md
    release-discipline.md
```

Se o canon permanecer no mesmo repositorio do runtime, a raiz editorial deve
deixar claro que o runtime e uma implementacao:

```text
logline/
  canon/
  runtime-rs/
  tools/
  docs/
```

A pasta do runtime deve evitar nomes editorialmente ambiguos como se fosse "o
LogLine" em si. Um nome como `runtime-rs` comunica melhor seu papel:
implementacao Rust aderente ao canon.

## 9. Regras de Versionamento

Cada versao publicada do canon deve ser tratada como imutavel.

Mudancas devem seguir estas regras:

1. Correcoes editoriais sem impacto normativo podem ser publicadas como errata.
2. Correcoes substantivas exigem nova versao do canon.
3. Mudancas incompativeis exigem secao explicita de breaking changes.
4. O changelog do canon deve ser separado do changelog do runtime.
5. O runtime deve declarar a versao do canon a qual afirma aderir.
6. A conformidade deve ser testada contra uma versao especifica, nunca contra
   "a versao atual" de forma vaga.

Exemplo de declaracao de runtime:

```json
{
  "runtime": "logline-runtime-rs",
  "runtime_version": "0.3.0",
  "canon_version": "0.1",
  "conformance_suite": "conformance",
  "status": "partial"
}
```

## 10. Modelo de Conformidade

A conformidade deve ser demonstravel por execucao de corpus, nao apenas por
afirmacao documental.

O diretorio `conformance/` deve conter casos classificados em pelo menos tres
grupos:

```text
conformance/
  valid/
    minimal.logline
    confirmed-with-receipt.logline
    jurisdictional-origin.logline

  invalid/
    missing-confirmed-by.logline
    invalid-nine-position-shape.logline
    malformed-authority.logline

  ambiguous/
    weak-evidence-reference.logline
    unresolved-origin-scope.logline
```

Cada caso deve declarar:

- entrada;
- versao do canon alvo;
- resultado esperado;
- motivo esperado;
- severidade, quando aplicavel;
- se o erro e sintatico, estrutural, semantico ou probatorio.

Formato sugerido para manifesto de caso:

```json
{
  "case_id": "invalid-missing-confirmed-by-001",
  "canon_version": "0.1",
  "input": "invalid/missing-confirmed-by.logline",
  "expected": "fail",
  "reason": "confirmed_by is required for admissible LogLine entries",
  "layer": "semantic",
  "severity": "error"
}
```

## 11. Modelo de Prova e Admissibilidade

O canon afirma a centralidade da evidencia. Esta LIP propoe elevar
`confirmed_by` para um modelo verificavel em camadas, preservando simplicidade
operacional.

| Camada | Evolucao proposta | Beneficio |
|---|---|---|
| Basica | Receipts estruturados com `id`, `issuer`, `digest` e relacao com a LogLine. | Rastro minimo de admissibilidade. |
| Intermediaria | Assinaturas Ed25519 e suporte inicial a `did:key`. | Prova forte sem infraestrutura pesada. |
| Persistente | Ledger append-only com `prev_digest` entre entradas. | Historico verificavel, nao apenas declarativo. |
| Institucional | Export de receipts de `release_to_tower`. | Portabilidade da prova para auditoria externa. |

A adocao deve ser incremental. Uma implementacao pode declarar conformidade
sintatica e estrutural sem implementar a camada criptografica completa, desde
que seu nivel de conformidade seja declarado com precisao.

## 12. Niveis de Conformidade

Esta LIP propoe quatro niveis de conformidade:

### Nivel 0 - Parse

A implementacao consegue ler a forma `.logline` e produzir representacao
interna sem perda estrutural.

Requisitos:

- Implementar `spec/grammar.ebnf`.
- Rejeitar entradas sintaticamente invalidas.
- Preservar as nove posicoes do canon.

### Nivel 1 - Schema

A implementacao consegue validar a representacao canonica em JSON.

Requisitos:

- Implementar `spec/logline-canon.schema.json`.
- Validar campos obrigatorios.
- Rejeitar estruturas incompativeis com o canon.

### Nivel 2 - Semantica

A implementacao aplica regras semanticas minimas do canon.

Requisitos:

- Validar `who`, `did`, `this`, `when`, `confirmed_by` e demais posicoes
  conforme a versao alvo.
- Distinguir erro sintatico de erro semantico.
- Rodar o corpus `valid/` e `invalid/` com resultados esperados.

### Nivel 3 - Prova

A implementacao produz e verifica receipts.

Requisitos:

- Gerar receipts estruturados.
- Associar cada receipt a LogLine correspondente.
- Validar digest.
- Opcionalmente verificar assinatura, quando presente.

### Nivel 4 - Ledger

A implementacao mantem historico persistente verificavel.

Requisitos:

- Registrar entradas em ledger append-only.
- Encadear entradas com `prev_digest`.
- Exportar receipts auditaveis.
- Permitir replay ou verificacao independente do historico.

## 13. Receipt Minimo Proposto

Formato inicial sugerido:

```json
{
  "receipt_id": "rec_01HX...",
  "canon_version": "0.1",
  "logline_digest": "sha256:...",
  "issued_at": "2026-05-06T12:00:00Z",
  "issuer": {
    "kind": "runtime",
    "id": "logline-runtime-rs",
    "version": "0.3.0"
  },
  "claim": {
    "kind": "conformance_result",
    "case_id": "valid-minimal-001",
    "expected": "pass",
    "observed": "pass"
  },
  "evidence": {
    "command": "logline validate conformance/valid/minimal.logline",
    "exit_code": 0,
    "stdout_digest": "sha256:..."
  },
  "signature": {
    "alg": "Ed25519",
    "key": "did:key:z...",
    "value": "base64url..."
  }
}
```

A assinatura pode ser opcional nos niveis iniciais, mas o formato deve permitir
sua inclusao sem quebrar compatibilidade.

## 14. Governanca Proposta

Deve ser criado um `GOVERNANCE.md` com, no minimo:

- papeis editoriais;
- autoridade para aceitar mudancas normativas;
- quorum de aprovacao;
- rito de alteracao;
- politica de versionamento;
- distincao entre decisao editorial do canon e decisao tecnica do runtime.

Papeis sugeridos:

| Papel | Responsabilidade |
|---|---|
| Canon Editor | Zela pela clareza normativa e consistencia textual. |
| Runtime Maintainer | Mantem uma implementacao aderente, sem controlar sozinho a norma. |
| Conformance Maintainer | Mantem corpus, fixtures e criterios de conformidade. |
| Security Reviewer | Avalia impactos de prova, assinatura, ledger e admissibilidade. |
| Foundation Steward | Media conflitos institucionais e decisoes de longo prazo. |

## 15. Processo LIP

Mudancas substantivas no canon devem passar por LIP.

Cada LIP deve conter:

- titulo;
- autor ou autores;
- status;
- motivacao;
- escopo;
- proposta normativa;
- impacto em compatibilidade;
- impacto em runtimes;
- impacto no corpus de conformidade;
- plano de migracao;
- criterios de aceitacao.

Status sugeridos:

```text
Draft -> Proposed -> Accepted -> Implemented -> Superseded
                     \-> Rejected
                     \-> Withdrawn
```

A primeira LIP do projeto deve instituir ou ratificar o proprio processo de LIPs,
para que a evolucao do canon ja comece usando o rito que propoe.

## 16. Melhorias de Runtime em Apoio ao Canon

O runtime de referencia deve evoluir para servir a norma, nao substitui-la.

| Tema | Mudanca | Observacao |
|---|---|---|
| CLI | Ajuda completa, versao, subcomandos consistentes e validacao de schema. | Melhora adocao sem alterar a doutrina. |
| `who` | Parse de `authority` e `jurisdiction` em `AccountableOrigin`. | Alinha responsabilidade com semantica mais forte. |
| `confirmed_by` | Tipos de evidencia assinavel e verificacao real. | Aproxima discurso e mecanismo. |
| `status` | Ledger persistente, receipts e opcao de export. | Fecha o ciclo de prova. |
| Tooling | CI, lint, MSRV e empacotamento limpo. | Da previsibilidade a terceiros. |

## 17. Roadmap Sugerido

| Fase | Janela | Entregas |
|---|---:|---|
| Fase 1 | 0-2 semanas | Higiene do repositorio, artefatos basicos, CI minima, limpeza de exemplos e disciplina de release. |
| Fase 2 | 2-6 semanas | Spec normativa, schema, EBNF, corpus de conformidade e reorganizacao editorial. |
| Fase 3 | 6-10 semanas | Ledger persistente, receipts e modelo inicial de prova criptografica. |
| Fase 4 | 10-14 semanas | Governanca formal, LIP, exemplos setoriais e narrativa publica da Foundation. |

## 18. Quick Wins Imediatos

1. Adicionar `LICENSE`, `.gitignore`, `CHANGELOG.md` e `SECURITY.md`.
2. Criar workflow de CI com `fmt`, `clippy` e `test`.
3. Publicar `logline-canon.schema.json`.
4. Fazer o CLI validar schema antes da semantica.
5. Eliminar duplicacao entre `examples/` e `crates/status/examples/`.
6. Renomear a pasta local do runtime para `runtime-rs` ou equivalente.
7. Criar `docs/canon-vs-runtime.md`.
8. Criar `conformance/README.md` com formato dos casos.
9. Criar `lips/TEMPLATE.md`.
10. Declarar explicitamente a versao do canon suportada pelo runtime.

## 19. Criterios de Aceitacao

Esta LIP pode ser considerada implementada quando:

1. Uma equipe externa consegue implementar parser e validador sem consultar o
   runtime Rust.
2. O corpus de conformidade cobre casos validos, invalidos e ambiguos com motivo
   esperado.
3. O schema JSON permite validacao automatizada independente.
4. A gramatica EBNF define a forma textual `.logline` sem depender de exemplos
   informais.
5. Toda mudanca normativa passa por rito proprio, changelog proprio e
   versionamento proprio.
6. Receipts e evidencias conseguem ser exportados, assinados e auditados fora do
   runtime original.
7. O runtime de referencia declara claramente a versao do canon a qual adere.
8. O documento `canon-vs-runtime.md` explica a separacao entre norma,
   implementacao, prova e governanca.

## 20. Resultado Esperado

Ao final deste ciclo, o canon LogLine deixa de depender da autoridade implicita
do runtime de referencia e passa a existir como objeto normativo proprio:
versionado, citavel, validavel, testavel e auditavel.

O runtime Rust permanece essencial como implementacao de referencia, mas deixa de
ser a fonte primaria da verdade. A autoridade passa a residir no canon publicado,
no corpus de conformidade e nos receipts produzidos por implementacoes aderentes.

Com isso, LogLine se torna nao apenas uma ideia elegante bem codificada, mas uma
base normativa distribuivel, capaz de sustentar multiplas implementacoes,
auditoria externa e discussoes serias sobre admissibilidade.

## 21. Estado de Evidencia

Esta LIP e uma proposta editorial e normativa.

Nenhuma conformidade de runtime e afirmada por este documento isoladamente. A
verificacao exige execucao futura do corpus de conformidade contra uma
implementacao especifica e emissao de receipts correspondentes.

Estado atual: `proposed`.
