---
name: code-review
description: Revisão crítica de plano ou código (Codex). Usado no Cursor e no Antigravity (/codex-review).
platforms: [cursor, antigravity]
---

# Workflow: Code Review (Codex)

## Invocação

| Plataforma | Como chamar |
|------------|-------------|
| **Antigravity** | `/codex-review` ou `/code-review` |
| **Cursor** | `@.agent/workflows/code-review.md` ou prompt: *"Siga code-review.md, MODE=plan, …"* |

> Alias: `codex-review.md` aponta para este arquivo (compatibilidade com `/codex-review`).

## Objetivo

Revisão **somente leitura** de plano ou código. **Não implementar** — analisar com evidência no repositório ou no documento.

## Parâmetros

| Variável | Obrigatório | Descrição |
|----------|-------------|-----------|
| `MODE` | Sim | `plan` \| `code` \| `reconcile` |
| `INPUT_FILE` | Sim | Plano ou escopo de código |
| `CODEX_REPORT` | Se `reconcile` | Relatório Codex anterior |
| `CLAUDE_REPORT` | Se `reconcile` | Relatório de `validate.md` |
| `OUTPUT_FILE` | Não | Ex.: `reports/<TASK_ID>/codex_plan_v1.md` |

## Modos

| Modo | Entrada | Foco |
|------|---------|------|
| `plan` | Plano markdown/JSON | Completude, fases, dependências, aderência ao repo |
| `code` | Diff ou arquivos | Corretude, segurança, qualidade, testes |
| `reconcile` | Plano + relatórios Codex e Claude | Fechar pendências; verificar alucinações apontadas pelo Claude |

## Contexto obrigatório

- `.agent/persona.md` + persona do artefato (`planner` no plano; `developer`/`qa` no código)
- `.agent/guardrails/security.md`
- `.agent/guardrails/quality.md`
- `.agent/guardrails/output-format.md`
- `PROJETO.md` (raiz)
- `.agent/skills/angular.md` / `.agent/skills/nestjs.md` se o escopo citar frontend ou API
- `.agent/testing.md` — confirmar que o plano/código prevê gate Q.A.

## Checklist — `plan` (artefato da persona Planner)

- [ ] Objetivo mensurável e alinhado ao pedido do usuário
- [ ] Fases pequenas (≤ 8 no plano; ideal ≤ 5 por entrega)
- [ ] Critérios de aceite verificáveis por fase
- [ ] Paths existem ou estão marcados como **a criar**
- [ ] Auth coerente com `goroute-service-auth` + BFF (`x-mapper-api`, sessão cookie) se aplicável
- [ ] Sem APIs/módulos inventados sem marcar hipótese
- [ ] `validation` por fase com testes reais (handoff Q.A.)
- [ ] `ownerPersona` / stack GoRoute coerentes (`plan.schema.json`)

## Checklist — `code` (artefato da persona Developer; gate Q.A.)

- [ ] Cobre critérios de aceite da fase
- [ ] Sem secrets hardcoded
- [ ] Validação de entrada (DTOs Nest; forms Angular)
- [ ] Erros e bordas tratados
- [ ] Padrões do repo (standalone Angular, módulos Nest)
- [ ] Unitários presentes/atualizados; UI com evidência MCP/`e2e` ou gap declarado

## Checklist — `reconcile`

- [ ] Cada achado do Claude como alucinação → confirmar no repo
- [ ] Achados válidos do Claude omitidos pelo Codex → incorporar
- [ ] Status final: `APPROVED` \| `CHANGES_REQUESTED` \| `BLOCKED`
- [ ] Lista numerada e acionável se `CHANGES_REQUESTED`

## Saída

Markdown conforme `.agent/guardrails/output-format.md`.

Modo `reconcile` — seção obrigatória:

```markdown
## 🔄 Reconciliation (Codex)

| ID | Origem | Veredito | Ação para planner/implementer |
|----|--------|----------|--------------------------------|
| 1 | Claude: alucinação | CONFIRMED_FALSE_POSITIVE | Ignorar |
```

## Execução

### Antigravity (Codex CLI)

```bash
mkdir -p reports/<TASK_ID>
codex exec --sandbox read-only --model gpt-4 \
  --prompt "Siga .agent/workflows/code-review.md MODE=${MODE}. Documento: $(cat ${INPUT_FILE})" \
  > "${OUTPUT_FILE:-reports/<TASK_ID>/codex_plan_v1.md}"
```

### Cursor (turno dedicado)

```text
Atue APENAS como revisor Codex (code-review).
MODE: plan
INPUT_FILE: .plan/tasks/<TASK_ID>/plano_v1.md  (ou .plan/03-development/stage-XX/plan.md)
Salve em: reports/<TASK_ID>/codex_plan_v1.md
Não implemente código.
```

## Proibido

- Inventar paths/endpoints sem evidência ou marcação de hipótese
- Aprovar plano com violação CRITICAL em `security.md`
- Relatório fora de `output-format.md`
