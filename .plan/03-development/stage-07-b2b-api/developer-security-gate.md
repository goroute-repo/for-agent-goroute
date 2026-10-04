# Developer Security Gate — stage-07

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `secure-service.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa | `stage-07-b2b-api` |
| Pacotes | `goroute-api` |
| SPECs | 14 |
| Dados sensíveis | [ ] PII [ ] Pagamento [ ] Tokens IA [x] API keys [x] Sessão (separada) |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Keys hasheadas | ☑ | ☐ | SHA-256; raw só no create |
| 2 | Sem log de key | ☑ | ☐ | — |
| 3 | Revogada → 401 | ☑ | ☐ | status active only |
| 4 | Scope 403 | ☑ | ☐ | B2bScopeGuard |
| 5 | Rate limit Redis | ☑ | ☐ | 1000/h |
| 6 | Idempotency | ☑ | ☐ | 24h PG |
| 7 | Sem SessionAuth bypass | ☑ | ☐ | guard próprio |
| 8 | SSRF webhook | ☑ | ☐ | via JobsService existente |
| 9 | Migration | ☑ | ☐ | scopes + idempotency |
| 10 | Testes | ☑ | ☐ | guard/scope/idempotency |

---

## Veredicto Developer

- [x] **PASS** — pronto para Q.A.
