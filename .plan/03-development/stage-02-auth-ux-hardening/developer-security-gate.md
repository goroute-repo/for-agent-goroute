# Developer Security Gate — stage-02

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `secure-service.md` · `guardrails/security.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa / task | `stage-02-auth-ux-hardening` |
| Pacotes | `goroute-frontend` (auth UX) |
| SPECs | 01 |
| Dados sensíveis | [x] PII [ ] Pagamento [ ] Tokens IA [ ] API keys [x] Sessão |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Authn correta (sessão + mapper se BFF) | ☑ | ☐ | Interceptor limpa mapper + session em 401 `/api/` |
| 2 | Authz BOLA | ☐ | ☑ | Sem novos endpoints |
| 3 | Role/workspace | ☐ | ☑ | Sem mudança |
| 4 | DTO whitelist / validação | ☑ | ☐ | Forms email/password client-side; API auth existente |
| 5 | Rate limit | ☐ | ☑ | UI trata 429 (`LOGIN.ERR_RATE_LIMIT`) |
| 6 | Sem secrets no código/logs | ☑ | ☐ | Token reset só via query → POST body |
| 7 | Migration | ☐ | ☑ | Sem DB |
| 8 | SSRF | ☐ | ☑ | N/A |
| 9 | Stripe | ☐ | ☑ | N/A |
| 10 | IA/BYOK | ☐ | ☑ | N/A |
| 11 | Testes negativos 401 | ☑ | ☐ | `session-expired.interceptor.spec.ts` |
| 12 | Frontend: sem token localStorage; guards | ☑ | ☐ | Cookie/session; sem JWT store |

---

## Veredito Developer

- [x] **PASS** — pronto para Q.A. (unitários OK; MCP browser pendente)

### Gaps

1. Validação visual forgot/reset/MFA settings depende de Playwright MCP ou `e2e/` (MCP indisponível nesta sessão).
