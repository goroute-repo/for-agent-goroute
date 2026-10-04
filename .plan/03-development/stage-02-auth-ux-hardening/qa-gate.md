# Q.A. Gate — stage-02-auth-ux-hardening

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Vitest `goroute-frontend` | **21 PASS** |
| App `http://localhost:4200` | OK (`npm start`) |
| Playwright MCP | OK (Node 22 em `%USERPROFILE%\.cursor\mcp.json`) |
| Security gate Developer | Presente |

---

## MCP — evidências

| Cenário | Rota / ação | Resultado |
|---------|-------------|-----------|
| Happy UI forgot | `/login/forgot-password` — formulário Email + Send link | Render OK |
| Validação formulário | Submit vazio | “This field is required.” |
| Erro API (auth offline) | Submit com e-mail | Toast network / proxy — feedback claro |
| Reset sem token | `/login/reset-password` | Redirect → `/login/forgot-password` + toast “Invalid or expired link…” |
| Anti-loop 401 | Coberto por Vitest interceptor | PASS |

**Não exercitado (ambiente):** SMTP real / e-mail de reset; MFA settings (requer sessão). Aceitável: UI + unitários + redirect token cobertos.

---

## Infra fix desta sessão

- MCP falhava com Node **v14** em `mcp.json`; corrigido para Cursor Node **v22**.

---

## INDEX

`stage-02` → `done`
