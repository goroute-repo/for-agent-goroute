# Stage 08 — Diferenciais inovadores (seleção)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-08-innovation-differentiators` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-03`, `stage-04`, `stage-06`, **`stage-13`** (se opção usar IA) |
| **Stack** | `goroute-api`, `goroute-frontend`, `goroute-mentor-agent` (se narrativo) |

## Meta

Entregar **um fatia vertical inovadora** que o mercado (roadmap.sh / LMS) não resolve bem — sem abrir o backlog inteiro de SPEC-21.

## Ler antes

1. `.plan/00-market/competitive-analysis.md` §5
2. `.specs/21-innovation-backlog.spec.md`
3. `PROJETO.md` § Capacidades inovadoras
4. Mentor agent atual (modos off/managed/BYOK)

## In scope (escolha travada)

| Opção | Status |
|-------|--------|
| **A — Career Twin (MVP)** | **Entregue** — `POST …/career-twin/simulate` + UI settings |
| **B — Match narrativo** | **Entregue (heurística)** — `GET …/match-narrative` + UI jobs |
| **C — Trilha por empresa** | Fora desta fatia |

Ver `notes.md` + `acceptance.md`.

## Out of scope

- Heatmap global, fairness dashboard, LMS webhooks, open API pública de skills (backlog)
- Reescrever motor de match para embeddings (v3) nesta etapa

## Critérios de aceite

1. Feature escolhida tem RF/CA rastreáveis no SPEC-21 ou sub-doc criado nesta pasta.
2. Não quebra stages 03/04/06.
3. Feature flag ou plano premium se billing já exigir (não hardcode bypass inseguro).
4. Testes + MCP no fluxo.

## Validation (Q.A.)

Conforme pacotes tocados + MCP no fluxo inovador.

## Anti-alucinação

- Uma feature bem feita > três meias.
- Não chamar de “IA” heurística sem declarar modo.

## Handoff Q.A.

Atualizar INDEX + notes com opção A/B/C escolhida.
