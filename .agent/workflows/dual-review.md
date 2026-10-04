# Workflow: Dual Review Orchestrator

## Objetivo

Orquestrar **planejamento** e **implementação por fases** com verificação dupla, alinhado às personas **Planner → Developer → Q.A.**:

1. **Codex** — revisão crítica (gaps, riscos, guardrails)
2. **Claude** — validação contra o repositório real + auditoria de alucinações
3. **Codex (reconcile)** — consolida veredito e ações

## Entrada

Objetivo em linguagem natural + opcional `TASK_ID` (slug, ex: `crud-produto`).

Se `TASK_ID` omitido, derive do objetivo (kebab-case, máx. 40 caracteres).

## Papéis × personas

| Papel | Persona GoRoute | Quem executa | Artefato |
|-------|-----------------|--------------|----------|
| Planejadora | **Planner** | IA do chat | `.plan/` ou `.plan/tasks/<TASK_ID>/plano_*.md` |
| Implementadora | **Developer** | IA do chat | código + unitários |
| Gate de testes | **Q.A.** | IA do chat | evidências + veredito PASS/FAIL |
| Codex Review | — (revisor) | `workflows/code-review.md` | `reports/<TASK_ID>/codex_*.md` |
| Claude Validate | — (validador) | `workflows/validate.md` | `reports/<TASK_ID>/claude_*.md` |
| Codex Reconcile | — | `code-review.md` `MODE=reconcile` | `reports/<TASK_ID>/codex_reconcile_*.md` |

## Skills e guardrails

| Etapa | Persona | Skills / workflows | Guardrails |
|-------|---------|-------------------|------------|
| Planejamento | Planner (Solutions Architect) | `skills/planning.md`, `ux-advisory.md`, `growth-advisory.md`, `schemas/plan.schema.json` | `security.md`, `quality.md` |
| Revisão Codex | — | `code-review.md` + stack skills | `output-format.md`, security, quality |
| Validação Claude | — (+ overlap Q.A.) | `validate.md`, `testing.md` | output-format, security, quality |
| Implementação | Developer | `angular.md` / `nestjs.md` / … | security, quality |
| Gate testes | Q.A. | `testing.md`, Playwright MCP se UI | quality, output-format |

Leia `PROJETO.md` e a persona ativa em [`.agent/personas/`](../personas/) (índice: [`persona.md`](../persona.md)) antes de iniciar.

---

## Fase A — Planejamento (persona **Planner**)

### A1 — Gerar plano

1. Assumir [`personas/planner.md`](../personas/planner.md).
2. Ler objetivo + contexto (`PROJETO.md`, pastas citadas).
3. Se produto/UI/mobile/lançamento → **Planner Council** ([`planner-council.md`](./planner-council.md)): parecer UX + Growth em `advisory/` antes do `ready`.
4. Produzir Markdown + JSON opcional (`plan.schema.json`).
5. Fases (máx. 5–8) com aceite, arquivos, `validation` (testes) e `ownerPersona`.
6. Salvar em `.plan/tasks/<TASK_ID>/plano_v1.md` (+ `.json`), ou atualizar etapa em `.plan/03-development/`.

### A2 — Codex Review (`MODE=plan`)

→ `reports/<TASK_ID>/codex_plan_v1.md`

### A3 — Claude Validate

→ `reports/<TASK_ID>/claude_plan_v1.md` + `VALIDATION_STATUS`

### A4 — Codex Reconcile

→ `reports/<TASK_ID>/codex_reconcile_v1.md`  
Decisão: `APPROVED` / `CHANGES_REQUESTED` / `BLOCKED`

### A5 — Iteração (máx. 3 ciclos)

Planner gera `plano_v{N+1}.md` com Changelog; repetir A2–A4.

### A6 — Plano final

Copiar para `.plan/tasks/<TASK_ID>/plano_final.md` / `.json` (`status: approved`), ou marcar etapa `ready` no INDEX.  
Handoff explícito → **Developer**.

---

## Fase B — Implementação (persona **Developer** + gate **Q.A.**)

Para cada fase `pending` em `plano_final`:

### B0 — Pré-condição

`dependsOn` já `implemented` / `qa_passed` / `skipped`.

### B1 — Implementar (Developer)

1. Assumir [`personas/developer.md`](../personas/developer.md).
2. Ler etapa `.plan/03-development/...` ou fase da task + `notes.md`; não expandir escopo.
3. Implementar + unitários.
4. Rodar validações locais listadas na fase.

### B1.5 — Gate Q.A. (obrigatório)

1. Assumir [`personas/qa.md`](../personas/qa.md).
2. Executar `validation` da fase (`testing.md`).
3. UI: Playwright MCP ou `e2e/` ou declarar MCP indisponível.
4. Veredito: **PASS** → seguir; **FAIL** → Developer corrige; **BLOCKED** → humano.

### B2–B4 — Codex / Claude / Reconcile (código)

Após Q.A. PASS (ou em paralelo se o usuário pedir review imediato).

### B5 — Correção

Máx. **2** tentativas Developer+Q.A.; depois `blocked`.

### B6 — Concluir fase

Status → `implemented` + `qa_passed`; registrar em `notes.md`.

Atalho: [`implement-phase.md`](./implement-phase.md).

---

## Fase C — Entrega

`.plan/tasks/<TASK_ID>/IMPLEMENTATION_REPORT.md` (ou notes da stage):

- Objetivo e plano aprovado
- Ciclos de review
- Fases + vereditos Q.A.
- Arquivos alterados
- Comandos de validação executados

---

## Configuração

| Parâmetro | Padrão |
|-----------|--------|
| Máx. ciclos de plano | 3 |
| Máx. correções por fase | 2 |
| Pastas | `.plan/`, `.plan/tasks/<TASK_ID>/`, `reports/<TASK_ID>/` |

## Invocação

```text
Execute .agent/workflows/dual-review.md
TASK_ID: crud-produto
Objetivo: CRUD de produto (NestJS + Angular) em goroute-api e goroute-frontend
Etapa: planejamento (Fase A — persona Planner)
```

## Dependências

| Canônico | Alias |
|----------|-------|
| `code-review.md` | `codex-review.md` |
| `validate.md` | `claude-validate.md` |
| `implement-phase.md` | Fase B |

**Skills:** `planning.md` (Planner), stack (Developer), `testing.md` (Q.A.) — não duplicar review em `skills/`.
