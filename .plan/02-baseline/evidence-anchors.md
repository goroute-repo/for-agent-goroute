# Evidence anchors — paths reais (ler antes de planejar/codar)

**Regra:** se o path não existir no disco, marcar `MISSING` e não inventar conteúdo.

---

## Documentação

| Path | Uso |
|------|-----|
| `PROJETO.md` | Visão, fluxos, implementado, próximos passos |
| `.specs/README.md` | Índice de specs |
| `.specs/00-platform-overview.spec.md` | Visão plataforma |
| `.specs/01-auth-identity.spec.md` | Auth |
| `.specs/02-onboarding.spec.md` | Onboarding |
| `.specs/03-career-roadmap.spec.md` | Roadmap |
| `.specs/04-roadmap-history-progress.spec.md` | Histórico/progresso |
| `.specs/05-weekly-plan.spec.md` | Weekly plan |
| `.specs/06-jobs-marketplace.spec.md` | Jobs |
| `.specs/07-matching-candidatures.spec.md` | Match/candidaturas |
| `.specs/08-candidate-offers.spec.md` | Ofertas |
| `.specs/09-workspaces.spec.md` | Workspaces |
| `.specs/10-settings-profile.spec.md` | Settings |
| `.specs/11-billing.spec.md` | Billing |
| `.specs/14-api-b2b.spec.md` | B2B |
| `.specs/19-security.spec.md` | Segurança |
| `.specs/21-innovation-backlog.spec.md` | Inovação |
| `.specs/22-career-replan.spec.md` | Replan |
| `AGENTS.md` | Contrato agentes |
| `.plan/README.md` | Este planejamento |

---

## Pacotes (stack)

| Path | Papel |
|------|-------|
| `goroute-frontend/` | Angular 21 SPA |
| `goroute-api/` | BFF NestJS |
| `goroute-service-auth/` | Auth hexagonal |
| `goroute-mentor-agent/` | Mentor Python |
| `goroute-nlp/` | NLP Flask |
| `goroute-platform-admin/` | Admin |
| `goroute-mobile/` | Flutter scaffold |
| `e2e/` | Playwright cross-stack |
| `docker-compose.yml` | Dados locais |
| `docker-compose.homolog.yml` | Stack homolog |

---

## Âncoras de código (alto valor)

Confirmar com Glob/Grep — nomes podem ter drift:

| Área | Paths candidatos |
|------|------------------|
| Catálogo grafo | `goroute-api/src/roadmap/career-graph.catalog.ts` |
| Match | `goroute-api/src/jobs/job-match.service.ts`, `job-skill-levels.ts` |
| Onboarding schema | `goroute-api/src/onboarding/onboarding-schema.ts` |
| Proxy frontend | `goroute-frontend/proxy.conf.json` |
| Environments | `goroute-frontend/src/environments/environment.ts` |
| Test helpers FE | `goroute-frontend/src/app/core/testing/goroute-test-providers.ts` |
| Auth hexagonal | `goroute-service-auth/src/domain/`, `src/infrastructure/` |

---

## Comandos de validação canônicos

```bash
cd goroute-frontend && npm test
cd goroute-frontend && npm start          # http://localhost:4200
cd goroute-api && npm test
cd goroute-service-auth && npm test
cd e2e && npm test                        # quando stack no ar
```

UI: Playwright MCP (global Cursor) — ver `.agent/testing.md`.
