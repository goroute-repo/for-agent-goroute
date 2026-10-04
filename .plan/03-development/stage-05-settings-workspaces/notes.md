# Stage 05 — notas Developer (2026-07-17)

## Já existia

- Settings profissional (abas perfil…segurança) + company (org/billing/storage)
- Guards role + APIs `/settings/*`, `/company/settings`
- Consent AI / talent pool

## Entregue nesta rodada

| Item | Path |
|------|------|
| LGPD export profissional | `GET /api/settings/privacy/export` |
| LGPD export empresa | `GET /api/company/settings/privacy/export` |
| UI Privacy (pro Security tab) | settings.html + export download |
| UI Privacy (company storage area) | company-settings |
| Defer exclusão conta | Texto UI + notes (SPEC-19 / RS-04 **P1**) |
| Guards Vitest | professional/company-role.guard.spec |
| Privacy Jest + Vitest | settings-privacy.service.spec / settings-privacy.spec |

## Validation

- frontend `npm test` → **32 PASS**
- api `npm test` → **116 PASS**
- MCP: login smoke → settings Security → Export JSON (arquivo baixado sem BYOK key)
- MCP cross-access: professional → `/dashboard/company/settings` → redirect `/dashboard/personal`

## Defer explícito

- **Exclusão definitiva de conta** — SPEC-19 / RS-04 P1; não implementada nesta stage.
- Experiences CRUD, company hiring/AI/logo tabs — fora do fine-closure (não há aba morta).

## Handoff

INDEX → `qa`
