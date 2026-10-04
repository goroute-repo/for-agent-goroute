# Q.A. Gate — stage-07-b2b-api

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Jest API | **128 PASS** |
| Security gate Developer | Presente |
| Migration aplicada | `B2bScopesAndIdempotency1735701000000` OK |
| Smoke HTTP (sem secrets) | OK |

---

## Smoke / evidências

| Cenário | Resultado |
|---------|-----------|
| `GET /api/b2b/v1/health` | `{ ok: true, version: "v1" }` |
| `POST /jobs` key inválida | **401** `INVALID_B2B_API_KEY` |
| `POST /jobs` sem key | **401** `B2B_API_KEY_REQUIRED` |
| `GET /jobs/:id` Bearer inválido | **401** `INVALID_B2B_API_KEY` |
| Idempotência / scopes | Cobertos por Jest (`api-b2b.idempotency`, `b2b-scope.guard`) |
| Prefixo | `/api/b2b/v1` (não `/v1/company`) |
| SessionAuth bypass | Não — guard B2B isolado |

---

## Aceite

1. Constraints via `JobsService.create`: **OK** (unit + shared path)  
2. Idempotency-Key não duplica: **OK** (Jest CA-140)  
3. Key inválida / escopo: **OK** (401 smoke + 403 Jest)  
4. Sem bypass sessão web: **OK**

---

## Nota

Antes do smoke HTTP, migration era necessária — Q.A. executou `npm run migration:run` no ambiente local.

---

## INDEX

`stage-07` → `done`
