# Stage 08 — notas Developer (2026-07-17)

## Escolha travada

| Papel | Opção |
|-------|-------|
| **Primário** | **A — Career Twin (MVP)** — INN-01 |
| **Secundário** | **B — Match narrativo (heurística)** — INN-03 |

Aceite local: `acceptance.md`.

## Entregue

| Item | Detalhe |
|------|---------|
| API Twin | `POST /api/roadmap/career-twin/simulate` — dry-run; `persisted: false` |
| Roadmap override | `ProfessionRoadmapService.buildRoadmap(userId, { targetRoleSlug })` |
| UI Twin | Settings → Track: Simular + comparação ETA/gaps + Aplicar (PATCH career 409 intacto) |
| Match narrativo | `GET /api/jobs/:id/match-narrative` — `source: heuristic` + até 2 closing skills |
| UI narrativo | Jobs marketplace: “Por que este %?” |
| Rastreio | `acceptance.md` CA-0801–0804, CA-0811–0812 |

## Testes

- `goroute-api` Jest **152 PASS**
- `goroute-frontend` Vitest **41 PASS** (incl. `settings-career-twin`)

## Fora / residual

- Narrativa LLM managed/BYOK (só heurística nesta fatia; não chama de “IA”)
- Feature flag premium — não exigido pelo billing atual para Twin

## Handoff

INDEX → `qa`
