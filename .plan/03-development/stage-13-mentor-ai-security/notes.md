# Stage 13 — notas Developer (2026-07-17)

## Entregue

| Item | Detalhe |
|------|---------|
| AES BYOK | `aes-credential.util` — 64 hex obrigatório; sem pad/base64 fallback |
| RN-033 | Troca `off`/`managed` **retém** BYOK; wipe só `DELETE /settings/ai/credentials` |
| GET AI | `hasApiKey`, `apiKeyMasked`, `managedQuota` — nunca `apiKey` / cipher |
| Aliases | `GET/PATCH /api/profile/ai-settings` |
| Mentor | Decrypt só no BFF; BYOK fail → `warning`; rate limit 20/h |
| RN-123 | BYOK não consome quota managed Redis |
| Frontend | Quota managed + botão limpar chave; i18n pt-BR/en |
| Mentor-agent | Schema `extra: ignore` — sem campo `apiKey` |

## Testes

- `goroute-api` Jest **149 PASS**
- `goroute-mentor-agent` pytest `test_mentor_request_no_apikey` **PASS**
- `goroute-frontend` Vitest **39 PASS** (incl. `settings-ai.spec`)

## Security gate

Ver `developer-security-gate.md` (PASS).

## Handoff

INDEX → `qa` (bloqueia stage-08 até Q.A. PASS).
