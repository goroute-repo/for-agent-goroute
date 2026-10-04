# GoRoute — Agent Workflows (Cursor + Antigravity)

Personas de entrega: **Planner (Solutions Architect)** → **Developer** → **Q.A.**  
Council de produto: **UX/UI (Principal Designer)** + **Growth (Principal)** com skills e **gates de qualidade** (rejeitar parecer raso).  
Verificação dupla: **Codex** (`code-review`) → **Claude** (`validate`) → reconcile.

Orquestração: [`../AGENTS.md`](../AGENTS.md)

## Personas

| Persona | Arquivo | Barra |
|---------|---------|-------|
| Planner | [`personas/planner.md`](./personas/planner.md) | Principal Solutions Architect — opções, ADR, risco |
| UX/UI | [`personas/ux-ui.md`](./personas/ux-ui.md) | Principal Product Designer — atrito P0–P2, fluxos |
| Growth | [`personas/growth-marketing.md`](./personas/growth-marketing.md) | Principal Growth — ICP, funil, ASO com racional |
| Developer | [`personas/developer.md`](./personas/developer.md) | Implementação + testes |
| Q.A. | [`personas/qa.md`](./personas/qa.md) | Gate DoD |

Índice legado: [`persona.md`](./persona.md).

## Skills de planejamento / council

| Skill | Arquivo |
|-------|---------|
| Planning | [`skills/planning.md`](./skills/planning.md) |
| UX advisory | [`skills/ux-advisory.md`](./skills/ux-advisory.md) |
| Growth advisory | [`skills/growth-advisory.md`](./skills/growth-advisory.md) |

## Mapa de arquivos

| Papel | Arquivo canônico | Cursor |
|-------|------------------|--------|
| Orquestrador | `workflows/dual-review.md` | `@.agent/workflows/dual-review.md` |
| Council produto | `workflows/planner-council.md` | Planner → UX + Growth (com gates) |
| Revisão Codex | `workflows/code-review.md` | `@.agent/workflows/code-review.md` |
| Validação Claude | `workflows/validate.md` | `@.agent/workflows/validate.md` |
| Implementar fase | `workflows/implement-phase.md` | Developer → Q.A. |

## Pastas

| Pasta | Uso |
|-------|-----|
| `personas/` | Identidades (barra sênior explícita) |
| `workflows/` | Orquestração |
| `skills/` | planning, ux-advisory, growth-advisory, stacks, security |
| `guardrails/` | security, quality, output-format |
| `schemas/` | `plan.schema.json` |
| `testing.md` | Pirâmide / DoD |

## Fluxo

```text
Planner (problema → opções → evidência)
  → UX advisory (gate U*) + Growth advisory (gate G* ou modo interno)
  → notes.md reconcilia (aceito/rejeitado)
  → dual-review opcional → ready
  → Developer → Q.A.
```

## Exemplo Cursor

```text
Siga AGENTS.md + .agent/workflows/planner-council.md
Persona: Planner (Solutions Architect)
Skills: planning.md, ux-advisory.md, growth-advisory.md
Objetivo: […]
Rejeite parecer genérico; exija evidência do repositório.
```

```text
Siga .agent/workflows/implement-phase.md
Persona: Developer → Q.A.
STAGE: stage-XX
PHASE_ID: …
```
