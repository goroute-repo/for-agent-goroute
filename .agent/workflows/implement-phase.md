# Workflow: Implement Phase

Atalho para **Fase B** de [`dual-review.md`](./dual-review.md) — uma fase do `plano_final`.

**Personas:** **Developer** (implementa) → **Q.A.** (gate) → review opcional.

## Entrada

- `TASK_ID`
- `PHASE_ID` (ex: `phase-1-api`)

## Passos

1. Preferir etapa canônica: `.plan/03-development/stage-XX-*/plan.md` **ou** ler `.plan/tasks/<TASK_ID>/plano_final.md` + `PHASE_ID`.
2. Assumir [`.agent/personas/developer.md`](../personas/developer.md) — implementar só o escopo.
3. Unitários + validações locais.
4. Assumir [`.agent/personas/qa.md`](../personas/qa.md) — executar `validation` (`testing.md`).
   - UI: Playwright MCP em `http://localhost:4200` ou `e2e/`.
   - Se FAIL → voltar ao passo 2 (máx. 2 ciclos de correção).
5. `workflows/code-review.md` → `MODE=code`
6. `workflows/validate.md` → `MODE=code`
7. `workflows/code-review.md` → `MODE=reconcile`
8. Atualizar status no `.plan/03-development/INDEX.md` ou `plano_final.json` + `notes.md`.

## Skills

| Persona | Artefatos |
|---------|-----------|
| Developer | `angular.md` / `nestjs.md` / `python-ai.md` / `flutter.md` + `security-check.md` se sensível |
| Q.A. | `testing.md`, regra Playwright MCP |
| Todas | `security.md`, `quality.md`, `output-format.md` |

## Exemplo

```text
Siga .agent/workflows/implement-phase.md
Persona: Developer → Q.A.
TASK_ID: crud-produto
PHASE_ID: phase-1-api
```
