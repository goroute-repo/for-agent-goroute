# Developer Security Gate — stage-18-platform-admin-refactor

**Skills:** `secure-service.md` · `guardrails/security.md`

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa / task | `stage-18-platform-admin-refactor` |
| Pacotes | `goroute-platform-admin` |
| SPECs | 12, 11 (métricas), 19 |
| Dados sensíveis | [x] PII [x] Pagamento (métricas) [ ] Tokens IA [ ] API keys [x] Sessão |

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Authn (cookie + mapper + goroute-admin) | ☑ | ☐ | interceptors + AuthApiService |
| 2 | Authz BOLA | ☐ | ☑ | Admin global; BFF `RequireAdminRoles` |
| 3 | Role/tier | ☑ | ☐ | `adminTierGuard` SUPPORT/ADMIN + UI |
| 4 | DTO whitelist | ☐ | ☑ | client only |
| 5 | Rate limit | ☐ | ☑ | BFF |
| 6 | Sem secrets no código | ☑ | ☐ | `config.json` só URLs |
| 7 | Migration | ☐ | ☑ | |
| 8 | SSRF | ☐ | ☑ | |
| 9 | Stripe | ☐ | ☑ | |
| 10 | IA/BYOK | ☐ | ☑ | |
| 11 | Testes negativos 401/403 | ☑ | ☐ | admin-tier.guard.spec |
| 12 | Guards rota; mapper em localStorage (admin) | ☑ | ☐ | session + tier guards |

## Veredito Developer

- [x] **PASS** — pronto para Q.A. após `npm test` verde

### Gaps

1. Mapper admin permanece em `localStorage` (padrão legado do pacote; não introduzido nesta stage).
