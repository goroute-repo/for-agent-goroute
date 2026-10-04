# Developer Security Gate — stage-13

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `secure-service.md` § IA/BYOK · `guardrails/security.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa | `stage-13-mentor-ai-security` |
| Pacotes | `goroute-api`, `goroute-mentor-agent`, `goroute-frontend` |
| SPECs | 03 (RF-036), 10, 11 (RN-123), 19 (SEC-07/09) |
| Dados sensíveis | [x] PII [ ] Pagamento [x] Tokens IA [x] API keys [x] Sessão |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | Authn sessão + mapper BFF | ☑ | ☐ | settings/mentor controllers |
| 2 | Authz ownership userId | ☑ | ☐ | `req.authContext!.user.id` |
| 3 | Role/workspace | ☐ | ☑ | settings pessoais |
| 4 | DTO whitelist | ☑ | ☐ | patch AI DTO; mentor-agent `extra: ignore` |
| 5 | Rate limit mentor | ☑ | ☐ | `MentorRateLimitGuard` 20/h |
| 6 | Sem secrets em logs/código | ☑ | ☐ | mask only; env 64 hex |
| 7 | Migration | ☐ | ☑ | coluna já existia |
| 8 | SSRF | ☐ | ☑ | — |
| 9 | Stripe | ☐ | ☑ | — |
| 10 | IA/BYOK sem leak | ☑ | ☐ | `settings-ai.service.spec`; SEC-07; pytest no apiKey |
| 11 | Testes negativos | ☑ | ☐ | BYOK decrypt fail → warning; Forbidden quota |
| 12 | Frontend sem key em storage | ☑ | ☐ | `applyAiDto` zera `apiKey`; clear via DELETE |

---

## Veredicto Developer

- [x] **PASS** — pronto para Q.A.

### Gaps

Nenhum bloqueante. SEC-07/09 cobertos por testes de regressão + schema mentor-agent.

---

## Q.A. (2026-07-17)

| Verdict | **PASS** |
|---------|----------|
| Notas | Jest 149 + pytest 6 + Vitest 39; MCP settings AI + GET `/settings/ai` sem chave; stage-08 liberado |
