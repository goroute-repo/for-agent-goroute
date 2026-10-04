# Stage 02 — notas Developer (2026-07-17)

## Auditoria vs código existente

Já existiam (não recriados):

- `/login/forgot-password` e `/login/reset-password` + client auth (`reset-request` / `reset`)
- MFA em settings (aba segurança)
- `sessionExpiredInterceptor` em `app.config.ts`

## Entregue nesta rodada

| Item | Path |
|------|------|
| Anti-loop 401 | `session-expired.interceptor.ts` (flag de redirect único) |
| Vitest interceptor | `session-expired.interceptor.spec.ts` |
| Vitest forgot | `login-forgot-password.spec.ts` |
| Vitest reset | `login-reset-password.spec.ts` |

## Validation

- `cd goroute-frontend && npm test` → **21 PASS**
- Playwright MCP: **indisponível** neste ambiente (`user-playwright` sem tools; ver STATUS). Fluxos UI forgot/reset/MFA ficam para Q.A. com MCP ou `e2e/`.

## Handoff

Status INDEX → `qa`.
