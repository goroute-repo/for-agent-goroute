# Stage 07 — notas Developer (2026-07-17)

## Prefixo

**`/api/b2b/v1`** (código existente). SPEC planejava `/api/v1/company` — não duplicado.

## Entregue

| Item | Detalhe |
|------|---------|
| Auth | `X-GoRoute-Api-Key`, `Authorization: Bearer`, `X-Api-Key` |
| Key format | novas keys `gr_company_…` (hash SHA-256); legadas `grb2b_` ainda resolvem |
| Scopes | coluna `scopes` jsonb; `jobs:read` / `jobs:write` + `B2bScopeGuard` |
| POST `/jobs` | `Idempotency-Key` 24h (PG); default status `published` |
| GET `/jobs/:id` | `jobs:read` |
| Rate limit | Redis 1000/h por `b2bKeyId` |
| Skills error | `invalidSlugs` ≤10 + `hint` |
| OpenAPI | scheme `b2bApiKey`; CORS headers B2B |

## Testes manuais (sem secrets)

```bash
# health
curl -s http://localhost:3000/api/b2b/v1/health

# provision (INTERNAL_API_KEY + ENABLE_B2B_KEY_PROVISIONING=true)
curl -s -X POST http://localhost:3000/api/b2b/v1/keys \
  -H "X-Internal-Api-Key: $INTERNAL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"companyUserId":"<uuid>","name":"qa"}'

# create + idempotent replay
curl -s -X POST http://localhost:3000/api/b2b/v1/jobs \
  -H "X-GoRoute-Api-Key: $KEY" \
  -H "Idempotency-Key: demo-1" \
  -H "Content-Type: application/json" \
  -d '{"title":"Dev QA","description":"Vaga via B2B API","skills":[{"slug":"react","minLevel":"intermediate"}]}'
```

## Migration

`1735701000000-B2bScopesAndIdempotency` — `npm run migration:run`

## Residual (fora do fatia stage-07)

- PUT jobs, candidates, select-candidates, GET /skills, webhooks HMAC
- UI admin de keys

## Handoff

INDEX → `qa`
