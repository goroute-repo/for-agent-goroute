# Stage 03 — notas Developer (2026-07-17)

## Já existia

- API: `GET /roadmap/progress`, `GET /roadmap/history` (+ export)
- UI: `/dashboard/roadmap/progress`, `/dashboard/roadmap/history`
- Entities `career_journey_events` / `career_journey_snapshots`

## Fechamento fino desta rodada

| Item | Mudança |
|------|---------|
| CA-045 | `computePathProgress` exclui `owned` (só `path` + `goal`) |
| CA-040 | Onboarding grava `snapshot('onboarding_baseline')` |
| UI progress | Time Machine + cap 8 remaining + empty CTA settings |
| i18n | Paridade pt/en/es/de (`BACK_MAP`, `ALL_DONE`, `EVENT.*`, `EMPTY`) |
| Testes | Vitest progress/history; Jest path-progress + career-journey |

## CA SPEC-04 cobertos (parcial Fase 1–2)

- **CA-043 / CA-045** — progresso path/goal (Jest)
- **CA-040** — snapshot baseline no onboarding (hook)
- UI progresso “o que falta” + empty — Vitest + template
- History lista eventos — Vitest

## Fora deste fine-closure (residual)

- Contrato history completo SPEC (`origin`/`summary`/cursor)
- LGPD anonymize delete (CA-042)
- Rate limit progress/history
- MCP autenticado nas telas (auth offline nesta sessão) — guard → login validado

## Validation

- `goroute-frontend` `npm test` → **25 PASS**
- `goroute-api` path-progress + career-journey → **6 PASS** (suite full revalidada)
