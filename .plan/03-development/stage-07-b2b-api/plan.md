# Stage 07 — API B2B v1 (jobs)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-07-b2b-api` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-04` |
| **Stack** | `goroute-api` |

## Meta

Expor criação/leitura de vagas por API key de empresa, idempotente, alinhada ao modelo interno de jobs.

## Ler antes

1. `.specs/14-api-b2b.spec.md`
2. `PROJETO.md` § API B2B
3. Módulo `api-b2b` existente (Grep) — estender o que houver
4. `.agent/guardrails/security.md` (SSRF, authz, rate limit)

## In scope

- [ ] API keys por empresa + escopos mínimos (`jobs:write`, `jobs:read`)
- [ ] `POST` jobs com `Idempotency-Key`
- [ ] Validação de skill slugs (mesmo catálogo)
- [ ] Rate limit Redis
- [ ] Testes de contrato/idempotência
- [ ] Docs OpenAPI/Swagger atualizadas no BFF

## Out of scope

- OAuth2 client credentials enterprise completo
- Webhooks HMAC avançados além do já existente — só se SPEC-14 exigir nesta fatia
- Frontend admin de keys se não estiver no SPEC desta fase (pode ser mínimo root/settings)

## Critérios de aceite

1. Empresa autenticada por key cria vaga igualável à UI (mesmas constraints).
2. Replay com mesma Idempotency-Key não duplica.
3. Key inválida / escopo errado → 401/403.
4. Nenhum bypass de SessionAuthGuard em rotas web por causa da API key.

## Validation (Q.A.)

```bash
cd goroute-api && npm test
```

Testes manuais com curl/httpie documentados em notes da etapa (sem secrets reais).

## Anti-alucinação

- Prefixo de rota: seguir SPEC-14 / código existente (`/api/v1/company` vs outro).
- Não logar API keys.

## Handoff Q.A.

Atualizar INDEX.
