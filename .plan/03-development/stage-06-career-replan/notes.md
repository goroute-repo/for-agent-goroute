# Stage 06 — notas Developer (2026-07-17)

## Entregue

| Item | Detalhe |
|------|---------|
| SPEC path | `PATCH /api/profile/career-goal` (+ `/settings/career-goal`) |
| Body | `{ goalSlug, mode?, confirm? }` |
| RN-170 | `REPLAN_CONFIRM_REQUIRED` (409) se remaining > 30% sem `confirm` |
| Side effects | `goal_changed` com from/to; weekly `recalculateRoute` (best-effort) |
| Career role | `PATCH /settings/career` também exige confirm + weekly |
| Progress | Não toca `roadmap_node_progress` / `user_skills` |
| UI | Confirm dialog; Progress → “Alterar meta” → settings `?tab=carreira` |

## Testes

- Jest `settings-replan.service.spec.ts` — 5 PASS
- API full **121 PASS** · frontend **32 PASS**

## Residual

- Waypoint mode ainda não altera grafo intermediário (aceita flag, comportamento = replace)
- Autocomplete catálogo de skills na meta técnica (campo slug permanece)
- MCP autenticado do fluxo replan completo → Q.A.

## Handoff

INDEX → `qa`
