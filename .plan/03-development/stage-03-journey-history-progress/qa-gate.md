# Q.A. Gate — stage-03-journey-history-progress

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Vitest frontend | **25 PASS** |
| Jest (path-progress, career-journey, roadmap.service) | **12 PASS** |
| App + auth + BFF locais | `:4200` / `:3010` / `:3000` |
| Playwright MCP com login | OK (`smoke-qa@goroute.local`) |

---

## MCP — evidências (com sessão)

| Cenário | Resultado |
|---------|-----------|
| Login profissional | `/login` → `/dashboard/personal` |
| Progress | `/dashboard/roadmap/progress` — **0%**, **0 / 2 nodes**, Time Machine (ETA 80 wk, 0/640h, pace 8h), Missing: Pleno + Senior roles |
| History | `/dashboard/roadmap/history` — empty state “No events recorded yet.” + Export PDF link |
| Endpoints | Sem 404 fantasma nas rotas UI |

## CA cobertos (notes + testes)

- CA-043 / CA-045 — progresso path+goal (Jest + UI %)
- Empty history OK para usuário smoke sem eventos
- Time Machine reutilizado no painel progress

## Residual

- History ainda sem cards `origin`/`summary` do contrato SPEC completo (documentado no Developer notes)
- Usuário smoke sem `journey_started` na timeline (empty validado)

---

## INDEX

`stage-03` → `done`
