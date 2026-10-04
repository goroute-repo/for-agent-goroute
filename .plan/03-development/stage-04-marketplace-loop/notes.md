# Stage 04 — notas Developer (2026-07-17)

## Auditoria

API já tinha: `POST /jobs/:id/apply` (100%), `GET /jobs/match?eligibleOnly`, `GET /jobs/:id/match?minMatch`, `POST /jobs/:jobId/select-candidates`, oportunidades accept/decline.

Gaps de produto (fechados nesta rodada):

| Gap | Solução |
|-----|---------|
| Profissional sem Apply na UI | Botão + `applyToJob` em Jobs Marketplace |
| Sem filtro elegível | Checkbox `eligibleOnly` |
| Empresa sem `minMatch` na UI | Input 50–100 + reload candidatos |
| Empresa sem convite | Botão Convidar → `selectCandidates` |
| i18n | pt-BR / en / es / de |

## Entregáveis

- `goroute-jobs-api.service.ts` — `matchForMe({eligibleOnly})`, `candidates(minMatch)`, `applyToJob`, `selectCandidates`
- `jobs-marketplace.ts` — apply + eligibleOnly
- `company-jobs` — minMatch + invite
- Testes: `goroute-jobs-api.service.spec.ts`, `jobs-apply.service.spec.ts`

## Validation

- `goroute-frontend` `npm test` → **21 PASS**
- `goroute-api` `npm test` → **109 PASS**
- Playwright MCP: **indisponível** — fluxos empresa/profissional para Q.A.

## Handoff

Status INDEX → `qa`.
