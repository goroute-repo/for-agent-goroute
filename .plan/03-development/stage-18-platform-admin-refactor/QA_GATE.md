# Q.A. Gate — stage-18-platform-admin-refactor

**Task/Phase:** stage-18 (Fases 0–6)  
**Scope:** `goroute-platform-admin`  
**Date:** 2026-07-18  
**Reviewer:** Q.A. (agente)  
**Verdict:** PASS

> Não é “100% sem resíduo”: há 2 MINOR (mensagem unauthorized; confirm de onboarding via `window.confirm`). Não bloqueiam aceite do plano.

## Evidence

- [x] Unitários: `cd goroute-platform-admin && npm test -- --watch=false --browsers=ChromeHeadless` → **18 SUCCESS**
- [x] UI/MCP Playwright: `http://localhost:4201` → ok
- [x] Security smoke: `config.json` só URLs; sem secrets no diff admin

### Smoke MCP (operador)

| # | Critério plano §7 | Resultado |
|---|-------------------|-----------|
| 1 | Login `X-Application-From: goroute-admin` | PASS → `/dashboard` |
| 2 | Dashboard métricas ou erro explícito | PASS (métricas reais: profissionais/empresas/assinaturas) |
| 3 | READ_ONLY: menu só Dashboard+Auditoria; URL `/users` e `/data/*` → unauthorized | PASS |
| 4 | ADMIN+/SUPER: catálogo; delete pede confirm (`ConfirmDialog`) | PASS (cancelado sem destruir dados) |
| 5 | Logout limpa sessão → `/login` | PASS |

Papéis usados no smoke: elevação temporária de `smoke-qa@goroute.local` (SUPER_ADMIN → READ_ONLY) e **revertido** para `["professional"]` ao final. Conta `admin@goroute.com` (SUPER_ADMIN) permanece no banco.

### Checklist por fase

| Fase | Aceite | Resultado |
|------|--------|-----------|
| 0 | Matriz README/notes; config.json load; dead code; security gate | PASS |
| 1 | Tokens `#0F6E56`; shell grupos; empty/error/loading | PASS |
| 2 | Guards SUPPORT/ADMIN/READ_ONLY + specs matriz | PASS |
| 3 | Dashboard erro/empty/ano | PASS (smoke + Karma) |
| 4 | Users/companies empty≠erro; mutação ADMIN+ UI | PASS (smoke SUPER + Karma SUPPORT) |
| 5 | Catálogos confirm delete; audit empty/filtro | PASS |
| 6 | ADR Angular 19 em notes | PASS (opcional; não migrado) |

### Contrato API (anti-alucinação)

Cliente usa: `metrics/dashboard`, `users`, `users/:id` PATCH, `companies`, `audit` + career-catalog/skills APIs.  
BFF `admin.controller.ts` espelha papéis: READ_ONLY métricas/audit; SUPPORT users/companies; ADMIN PATCH user.  
`GET metrics` existe no BFF mas cliente não chama (dead client removido) — OK.

## Failures

_(nenhuma bloqueante)_

## Residual (MINOR — não bloqueia `done`)

1. `/unauthorized` copy genérica (“Conta sem papel administrativo”) também para tier insuficiente (READ_ONLY em `/users`) — deveria distinguir “sem permissão para esta área”.
2. Reset onboarding usa `window.confirm` em vez de `shared/confirm-dialog` (catálogos já usam o dialog).

## Decision

**Stage 18 → `done`** — planejado entregue e funcionando em homolog local; residuals MINOR opcionais para polish.

## Next

- Developer (opcional): copy unauthorized + ConfirmDialog no reset onboarding  
- Ops: usar `admin@goroute.com` ou conta admin dedicada (não depender de elevar smoke-qa)
