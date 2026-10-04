# Stage 13 — Mentor IA e BYOK (segurança)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-13-mentor-ai-security` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-01`, `stage-05` (ai-settings) |
| **Stack** | `goroute-api`, `goroute-mentor-agent`, `goroute-frontend` |
| **SPECs** | 03 (RF-036), 10, 11 (RN-123), 19 (SEC-07/09) |

## Meta

Modos `off` / `managed` / `BYOK` seguros: credenciais cifradas, sem leak em API/logs/query string; quotas managed corretas.

## Ler antes

1. `.specs/03-career-roadmap.spec.md` RF-036 / RS-08
2. `.specs/10-settings-profile.spec.md` ai-settings
3. `.specs/19-security.spec.md` SEC-07, SEC-09
4. `.agent/skills/secure-service.md`
5. Código mentor no BFF + `goroute-mentor-agent`

## In scope

- [x] Cifrar BYOK em repouso (`AI_CREDENTIALS_ENCRYPTION_KEY` 64 hex)
- [x] API settings nunca devolve chave completa (`apiKeyMasked` + DELETE credentials)
- [x] Remover/proibir key em query string (SEC-07)
- [x] Managed: chave só server-side; quota `ai_mentor_managed` (RN-123)
- [x] Modo `off` = heurística sem LLM; RN-033 retém BYOK
- [x] Testes + security gate
- [x] Rate limit mentor (SPEC-19)

## Out of scope

- Match narrativo produto (stage-08 opção B) — este stage é **segurança/base**
- Treinar SLM próprio

## Critérios de aceite

1. SEC-07 e SEC-09 fechados ou waiver documentado.
2. BYOK funciona sem consumir quota managed (RN-123).
3. Logs sem prompts PII + sem keys.

## Validation (Q.A.)

```bash
cd goroute-api && npm test
cd goroute-mentor-agent && pytest
cd goroute-frontend && npm test
```

## Handoff Q.A.

Atualizar INDEX — **bloqueia** stage-08 se IA narrativa depender de BYOK/managed.
