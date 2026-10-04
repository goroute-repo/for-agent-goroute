# Developer Security Gate — stage-06

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `secure-service.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa | `stage-06-career-replan` |
| Pacotes | `goroute-api`, `goroute-frontend` |
| SPECs | 22 |
| Dados sensíveis | [x] PII [ ] Pagamento [ ] Tokens IA [ ] API keys [x] Sessão |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Authn | ☑ | ☐ | SessionAuthGuard |
| 2 | Authz BOLA | ☑ | ☐ | userId da sessão apenas |
| 3 | Role | ☑ | ☐ | ProfessionalRoleGuard |
| 4 | Validação slug | ☑ | ☐ | INVALID_SKILL_REFERENCE / career catalog |
| 5 | Confirm gate | ☑ | ☐ | REPLAN_CONFIRM_REQUIRED |
| 6 | Sem wipe progress | ☑ | ☐ | Sem delete em node_progress |
| 7–10 | Migration / Stripe / IA | ☐ | ☑ | — |
| 11 | Testes | ☑ | ☐ | settings-replan.service.spec |
| 12 | Frontend | ☑ | ☐ | confirm dialog; sem JWT |

---

## Veredicto Developer

- [x] **PASS** — pronto para Q.A.
