# Q.A. Gate — stage-08-innovation-differentiators

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Jest API | **152 PASS** |
| Vitest frontend | **41 PASS** |
| Security gate Developer | Presente (PASS) |
| Playwright MCP Twin | OK |
| Playwright MCP jobs narrative | Parcial (ver residual) |

---

## Aceite

| ID | Critério | Resultado |
|----|----------|-----------|
| CA-0801 | Simulate sem persistir | **OK** — body `persisted: false`; UI “Simulation only…” |
| CA-0802 | UI Simular + Aplicar | **OK** — Settings Track; botão Apply presente |
| CA-0803 | Slug inválido → 400 | **OK** (Jest); código `INVALID_CAREER_ROLE` (doc acceptance dizia skill — residual naming) |
| CA-0804 | Sem sync/persist | **OK** — `career-twin.service.spec` |
| CA-0811 | % + ≤2 closing skills | **OK** — `job-skill-levels` / narrative heuristic |
| CA-0812 | Heurística declarada | **OK** — `source: heuristic` (MVP sem LLM) |

---

## Smoke MCP

| Cenário | Resultado |
|---------|-----------|
| Login smoke → `/dashboard/settings?tab=carreira` | OK |
| Target → Senior → **Simulate (Career Twin)** | OK |
| UI comparação Pleno 40w vs Senior 80w, delta +40, new gap Senior | OK |
| `POST /api/roadmap/career-twin/simulate` → **201** `persisted:false` | OK |
| `/dashboard/jobs` browse | OK — empty state (0 vagas publicadas no ambiente) |
| `GET …/match-narrative` (job inexistente) | **404** `JOB_NOT_FOUND` (rota autenticada ativa) |

---

## Residual (não bloqueante)

1. Sem vagas publicadas no smoke local → botão “Why this %?” não exercitado na UI; cobertura unitária + rota 404.
2. `acceptance.md` CA-0803: alinhar texto `INVALID_CAREER_ROLE` vs `INVALID_SKILL_REFERENCE`.
3. Narrativa LLM managed/BYOK — fora do MVP (documentado no Developer).

---

## INDEX

`stage-08` → `done`  
Próximos `ready`: **09**, **14**
