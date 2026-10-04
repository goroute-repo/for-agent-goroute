# Developer Security Gate — stage-04

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `secure-service.md` · `guardrails/security.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa / task | `stage-04-marketplace-loop` |
| Pacotes | `goroute-api`, `goroute-frontend` |
| SPECs | 06, 07, 08 |
| Dados sensíveis | [x] PII [ ] Pagamento [ ] Tokens IA [ ] API keys [x] Sessão |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Authn (sessão + mapper BFF) | ☑ | ☐ | Endpoints sob SessionAuthGuard existentes |
| 2 | Authz BOLA | ☑ | ☐ | `selectCandidates` filtra job por `companyUserId`; apply por userId da sessão |
| 3 | Role/workspace | ☑ | ☐ | CompanyRoleGuard / ProfessionalRoleGuard nos controllers |
| 4 | DTO / validação | ☑ | ☐ | Apply rejeita &lt;100%; minMatch clamp 50–100 no service |
| 5 | Rate limit | ☐ | ☑ | Sem novo endpoint sensível de auth |
| 6 | Sem secrets | ☑ | ☐ | Sem tokens novos |
| 7 | Migration | ☐ | ☑ | Entities já existiam |
| 8 | SSRF | ☐ | ☑ | Sem URL user nova |
| 9 | Stripe | ☐ | ☑ | N/A |
| 10 | IA/BYOK | ☐ | ☑ | N/A |
| 11 | Testes negativos | ☑ | ☐ | `jobs-apply.service.spec.ts` JOB_APPLY_NOT_ELIGIBLE |
| 12 | Frontend session | ☑ | ☐ | Cookie + apiBaseUrl; sem JWT |

---

## Veredito Developer

- [x] **PASS** — pronto para Q.A. (regra 100% apply + minMatch cobertos em unitários)

### Gaps

1. E2E browser (criar vaga → candidatos → oferta → apply) pendente MCP/`e2e/`.
