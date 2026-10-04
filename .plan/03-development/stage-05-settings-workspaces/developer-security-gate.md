# Developer Security Gate — stage-05

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `secure-service.md` · `guardrails/security.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa | `stage-05-settings-workspaces` |
| Pacotes | `goroute-api`, `goroute-frontend` |
| SPECs | 09, 10, 11, 19 (parcial) |
| Dados sensíveis | [x] PII [ ] Pagamento [x] Tokens IA (mascarados) [ ] API keys [x] Sessão |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Authn sessão + mapper | ☑ | ☐ | Export sob SessionAuthGuard |
| 2 | Authz BOLA | ☑ | ☐ | Export usa só `req.authContext.user.id` |
| 3 | Role/workspace | ☑ | ☐ | ProfessionalRoleGuard / CompanyRoleGuard + Vitest |
| 4 | DTO / validação | ☑ | ☐ | GET export sem body |
| 5 | Rate limit | ☐ | ☑ | Residual RS-05 |
| 6 | Sem secrets | ☑ | ☐ | AI export: `hasApiKey` only; sem encryptedApiKey |
| 7 | Migration | ☐ | ☑ | — |
| 8–10 | SSRF / Stripe / IA key leak | ☑ | ☐ | BYOK não vazado no export |
| 11 | Testes negativos / isolamento | ☑ | ☐ | Guard specs + privacy Jest |
| 12 | Frontend session | ☑ | ☐ | Cookie + mapper interceptor |

---

## Veredicto Developer

- [x] **PASS** — pronto para Q.A.

### Gaps

1. Account delete deferred (P1) — documentado na UI.
2. Rate limit export residual.
