# Q.A. Gate — stage-04-marketplace-loop

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS** (com residual autenticado)

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Jest `goroute-api` | **109 PASS** (incl. `jobs-apply.service.spec` 100% apply) |
| Vitest jobs API client | PASS (`eligibleOnly`, `minMatch`, `apply`, `selectCandidates`) |
| App `:4200` | OK |
| Playwright MCP rotas protegidas | `/dashboard/jobs` e `/dashboard/company/jobs` → redirect `/login` |
| Fluxo autenticado apply/invite | **Residual** — auth (3010) / BFF (3000) offline; sem credenciais E2E |

---

## MCP — evidências

| Cenário | Resultado |
|---------|-----------|
| Guard profissional em `/dashboard/jobs` | Redirect login |
| Guard empresa em `/dashboard/company/jobs` | Redirect login (após wait) |
| Apply / eligibleOnly / minMatch / invite na UI logada | Não executado (stack auth offline) |

Regressão de regra de negócio coberta por unitários: `JOB_APPLY_NOT_ELIGIBLE` em match &lt; 100%.

---

## Residual / follow-up

1. Com stack local (auth+api) e usuário de teste: MCP apply 100% + empresa minMatch + select-candidates.
2. Pode ir em smoke `e2e/` com `E2E_USER_EMAIL` / `E2E_USER_PASSWORD`.

---

## INDEX

`stage-04` → `done` (residual autenticado documentado; não bloqueia merge da entrega unitária + guards)
