# Q.A. Gate — stage-06-career-replan

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Vitest frontend | **32 PASS** |
| Jest API | **121 PASS** (incl. `settings-replan.service.spec` ×5) |
| Security gate Developer | Presente |
| Playwright MCP + sessão | OK (`smoke-qa` autenticado) |

---

## MCP — evidências

| Cenário | Resultado |
|---------|-----------|
| Progress → Change goal | Link → `/dashboard/settings?tab=carreira` (aba Track) |
| Replan target role Senior → Pleno | `PATCH /api/settings/career` **409** → confirm dialog → **200** |
| Progress pós-replan | Path **0/2 → 0/1**; ETA **wk. 80 → 40**; missing = Pleno |
| Histórico | Evento **Career goal updated** (20:45) |
| Slug inválido | Coberto por Jest 400; UI smoke residual (dialog locale) |

---

## Aceite SPEC-22 / plan

1. Novo caminho no progresso sem wipe: **OK**  
2. Confirm gate remaining >30%: **OK** (409 + dialog)  
3. Histórico registra mudança: **OK**  
4. Validação slug: **OK** (unitário)  
5. Weekly recalc: **OK** (Jest `recalculateRoute`; smoke UI não bloqueante)

---

## Residual (não bloqueia)

- Modo waypoint = replace (documentado no notes Developer)
- i18n es/de das chaves REPLAN (opcional)

---

## INDEX

`stage-06` → `done`
