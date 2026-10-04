# `.plan` — Planejamento GoRoute (Persona Planner)

Pasta canônica de **planejamento de produto** e **planejamento de desenvolvimento**.  
Substitui as antigas pastas `projeto/` e `planos/`.

| Persona | Uso desta pasta |
|---------|-----------------|
| **Planner** | Escreve e mantém etapas; pesquisa de mercado; anti-alucinação |
| **Developer** | Executa **uma** etapa de `03-development/` por vez |
| **Q.A.** | Executa `validation` da etapa; veredito PASS/FAIL/BLOCKED |

**Fonte de verdade de produto (código + specs):** `PROJETO.md`, `.specs/`, pacotes `goroute-*`.  
**Esta pasta** orquestra *o que fazer e em que ordem* — não duplica specs inteiras.

---

## Índice

| Ordem | Pasta / arquivo | Conteúdo |
|------:|-----------------|----------|
| 0 | [00-market/competitive-analysis.md](./00-market/competitive-analysis.md) | Pesquisa de mercado: roadmaps e trilhas |
| 1 | [01-product/vision-and-positioning.md](./01-product/vision-and-positioning.md) | Dor, proposta de valor, diferenciação |
| 2 | [02-baseline/audit-implemented-vs-gaps.md](./02-baseline/audit-implemented-vs-gaps.md) | O que já existe × o que falta |
| 2b | [02-baseline/evidence-anchors.md](./02-baseline/evidence-anchors.md) | Paths reais — ler antes de planejar/codar |
| 2c | [02-baseline/coverage-and-spec-alignment.md](./02-baseline/coverage-and-spec-alignment.md) | Cobertura full produto × SPECs 00–22 |
| 3 | [03-development/INDEX.md](./03-development/INDEX.md) | Etapas 01–14 (ordem + paralelismo) |
| 4 | [04-qa/test-strategy.md](./04-qa/test-strategy.md) | Estratégia de teste transversal |
| — | [handoff.md](./handoff.md) | Handoff Planner → Developer → Q.A. |

**Segurança (Developer):** `.agent/skills/secure-service.md`, `secure-payments.md`, `checklists/developer-security-gate.md`

---

## Regras anti-alucinação (obrigatórias)

1. **Nunca inventar** endpoint, tabela, env var ou arquivo sem confirmar em `evidence-anchors.md` ou no código.
2. Cada etapa em `03-development/` tem seção **Ler antes** com paths obrigatórios.
3. Se o path não existir → marcar `A CRIAR` explicitamente na etapa — não assumir.
4. Auth GoRoute = **sessão cookie + `x-mapper-api`** via `goroute-service-auth`. Não planejar JWT local genérico.
5. Escopo de etapa = só o listado em **In scope**. Resto vai para outra etapa ou `out-of-scope`.
6. Toda etapa com código tem **Validation** executável (comandos reais do monorepo).

---

## Como executar uma etapa

```text
1. Planner confirma etapa "ready" no INDEX
2. Developer assume personas/developer.md
3. Ler evidence-anchors + "Ler antes" da etapa
4. Implementar só In scope
5. Q.A. assume personas/qa.md → Validation
6. Atualizar status da etapa no INDEX (pending → done / blocked)
```

Prompts:

```text
Siga AGENTS.md. Persona: Developer.
Execute .plan/03-development/stage-XX-.../plan.md
```

```text
Siga AGENTS.md. Persona: Q.A.
Valide a etapa stage-XX conforme .plan/03-development/stage-XX-.../plan.md
```

---

## Status global (atualizado pelo Planner)

| Área | Status |
|------|--------|
| Pesquisa de mercado | done (2026-07-17) |
| Visão de produto | done |
| Baseline audit | done |
| Desenvolvimento | ver `03-development/INDEX.md` |

*Última atualização Planner: 2026-07-17*
