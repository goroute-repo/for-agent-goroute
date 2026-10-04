# Stage 10 — notas Developer (2026-07-17)

## Entregue

| SPEC | Item |
|------|------|
| 13 | `archived_at` / `deleted_at` + migration |
| 13 | `POST /api/jobs/:id/archive` — libera cota ativa; `archivedJobsCount++` |
| 13 | Soft-delete se há candidaturas; hard delete senão |
| 13 | `QUOTA_EXCEEDED` quando active ≥ `quotaJobs` |
| 12 | Demand bump também em update de skills com status `published` |
| 05 | `completeTask` exige `weekKey === currentWeekKey()` |
| UI | ATS botão Arquivar + i18n pt/en |

## Já cobertos (N/A reimplementar)

- UNIQUE `(user_id, week_key)`, guards weekly, `ROADMAP_GOAL_MISSING`, demand no publish, GraphSync rethrow

## Residual

- CSV batch abort (CA-141) → stage-11 / NLP
- Upsell 80% cota UI
- Lighthouse / CA-066 503 code rename

## Testes

- API Jest **132 PASS**
- Frontend Vitest **37 PASS**

## Handoff

INDEX → `qa`
