# Developer Security Gate — stage-14

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `flutter.md` §4 · `secure-service.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa | `stage-14-mobile-parity` |
| Pacotes | `goroute-mobile`, `goroute-service-auth` |
| SPECs | 15, 01 |
| Dados sensíveis | [x] PII [ ] Pagamento [ ] Tokens IA [x] API keys [x] Sessão |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Authn sessão Redis (não JWT) | ☑ | ☐ | `sessionId` + cookie ainda setado |
| 2 | Authz via BFF guards | ☑ | ☐ | mesmas rotas `/api/roadmap/me` |
| 3 | Role | ☑ | ☐ | role salva; roadmap profissional |
| 4 | DTO | ☑ | ☐ | login body tipado no client |
| 5 | Rate limit | ☐ | ☑ | auth já rate-limita login |
| 6 | Sem secrets em logs | ☑ | ☐ | sem print de session/mapper |
| 7 | Migration | ☐ | ☑ | — |
| 8 | SSRF | ☐ | ☑ | — |
| 9 | Stripe | ☐ | ☑ | — |
| 10 | Secure storage RS-01 | ☑ | ☐ | `SessionStore` + tests |
| 11 | sessionId só mobile | ☑ | ☐ | `buildUserLoginResponseBody` |
| 12 | Headers obrigatórios | ☑ | ☐ | `api_client_headers_test` |

---

## Veredicto Developer

- [x] **PASS** — pronto para Q.A.

---

## Q.A. (2026-07-17)

| Verdict | **PASS** |
|---------|----------|
| Notas | flutter test 4; auth 16; smoke mobile body tem sessionId/mapApiHash; web não expõe sessionId |
