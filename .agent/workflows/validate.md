---
name: validate
description: Validação Claude — repo real, anti-alucinação do relatório Codex. Cursor e Antigravity (/claude-validate).
platforms: [cursor, antigravity]
---

# Workflow: Validate (Claude)

## Invocação

| Plataforma | Como chamar |
|------------|-------------|
| **Antigravity** | `/claude-validate` ou `/validate` |
| **Cursor** | `@.agent/workflows/validate.md` ou turno com modelo Claude |

> Alias: `claude-validate.md` aponta para este arquivo (compatibilidade com `/claude-validate`).

## Objetivo

1. Validar plano/código contra o **repositório real**
2. Auditar o relatório do **Codex** (`code-review.md`) — falsos positivos e omissões
3. Emitir `VALIDATION_STATUS` para o orquestrador (`dual-review.md`)

## Parâmetros

| Variável | Obrigatório | Descrição |
|----------|-------------|-----------|
| `INPUT_DOC` | Sim | Plano ou escopo de código |
| `CODEX_REPORT` | Recomendado | Saída do Codex |
| `TASK_ID` | Recomendado | Pasta `reports/<TASK_ID>/` |
| `OUTPUT_FILE` | Não | Ex.: `reports/<TASK_ID>/claude_plan_v1.md` |
| `MODE` | Não | `plan` \| `code` |

## Contexto obrigatório

- `INPUT_DOC` e `CODEX_REPORT` (conteúdo completo)
- `.agent/persona.md` (Planner no plano; overlap **Q.A.** em testes)
- `.agent/guardrails/security.md`, `quality.md`, `output-format.md`
- `.agent/testing.md` — validar se `validation` das fases é executável
- `PROJETO.md` + **verificar no código** (Read/Grep/Glob) — não assumir estrutura

## Anti-alucinação

Para cada achado P0/P1 do Codex:

| Passo | Ação |
|-------|------|
| 1 | Evidência em `path:line` OU `HALLUCINATION_SUSPECTED` |
| 2 | Arquivo inexistente citado pelo Codex → **Alucinações do Codex** |
| 3 | Risco óbvio no repo omitido pelo Codex → **Omissões** |

Para afirmações do plano sobre módulos/endpoints: confirmar em `goroute-api`, `goroute-frontend`, `goroute-service-auth`.

## Checklist

- [ ] Alinhado ao objetivo do usuário (sem scope creep)
- [ ] Fases executáveis em ordem (persona Developer)
- [ ] Aceite testável + `validation` adequada (persona Q.A.)
- [ ] Auth GoRoute respeitada se aplicável (sessão, não JWT genérico inventado)
- [ ] Pacotes citados existem no mapa GoRoute (`AGENTS.md` §2)
- [ ] Falsos positivos do Codex documentados com prova

## Nota (A–F) e status

| Nota | Critério |
|------|----------|
| A–B | Aprovado |
| C | Ajustes antes de implementar |
| D–F | Bloquear ou rejeitar |

Linha obrigatória no final:

```text
VALIDATION_STATUS: approved | needs_changes | rejected
```

## Seções extras no relatório

```markdown
## 🔍 Codex Hallucination Audit
| # | Achado Codex | Veredito | Evidência |

## 📋 Plan vs Repository
| Afirmação no plano | Existe no repo? | Notas |

## 🏁 Validation Status
**Grade:** B
**VALIDATION_STATUS:** needs_changes
```

Base: `.agent/guardrails/output-format.md`.

## Execução

### Antigravity (API Anthropic)

Requer `ANTHROPIC_API_KEY`. Montar prompt com `INPUT_DOC` + `CODEX_REPORT` + instruções deste arquivo; gravar em `OUTPUT_FILE`.

### Cursor

```text
Turno independente — Validador Claude apenas.
Siga .agent/workflows/validate.md
INPUT_DOC: .plan/tasks/<TASK_ID>/plano_v1.md  (ou etapa em .plan/03-development/)
CODEX_REPORT: reports/<TASK_ID>/codex_plan_v1.md
Confirme paths no repo antes de concluir.
```

### Fallback (sem API Claude)

Mesmo agente com aviso `FALLBACK: not native Claude` no relatório.

## Proibido

- Aprovar plano sem checar paths citados
- Inventar correções de código (só recomendar)
- Concordar automaticamente com o Codex

## Exemplos GoRoute

- Plano com JWT local → projeto usa cookie + `goroute-service-auth`
- Ignorar `x-mapper-api` no BFF → registrar como gap
- Codex cita arquivo inexistente → alucinação
