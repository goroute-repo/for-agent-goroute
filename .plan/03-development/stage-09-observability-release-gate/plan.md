# Stage 09 — Observabilidade + gate de release

| Campo | Valor |
|-------|-------|
| **ID** | `stage-09-observability-release-gate` |
| **Status** | `done` |
| **ownerPersona** | `qa` (+ developer para gaps) |
| **Depende de** | stages 01–05 no mínimo `done` ou gaps aceitos por escrito |
| **Stack** | infra, `e2e/`, todos os pacotes tocados |

## Meta

Validar que o produto está pronto para release com evidência: testes, smoke, segurança básica, checklist de HA documentado.

## Ler antes

1. `.plan/04-qa/test-strategy.md`
2. `PROJETO.md` critérios produção / débitos
3. `e2e/.env.example`
4. `docker-compose.homolog.yml`
5. Stages 01–05 notes ( Pendências conscientes )

## In scope

- [x] Matriz de regressão executada (unitários pacotes críticos)
- [x] Suite `e2e/` green (18 PASS; fix load `.env`)
- [x] Smoke preflight local OK; homolog full documentado como opcional
- [x] Checklist segurança (secrets, MFA OAuth, migrations) revalidado
- [x] Relatório de release em `RELEASE_REPORT.md`

## Out of scope

- Implementar SigNoz completo se não houver decisão de infra
- Novas features de produto

## Critérios de aceite

1. Nenhuma P0 de segurança aberta sem waiver assinado no RELEASE_REPORT.
2. Fluxos críticos Q.A.: login, onboarding, roadmap, weekly, jobs loop, settings smoke.
3. Documentação de como subir local/homolog atualizada só se estiver errada (diff mínimo).

## Validation (Q.A.)

```bash
cd goroute-frontend && npm test
cd goroute-api && npm test
cd goroute-service-auth && npm test
cd e2e && npm test   # se ambiente ok
```

Playwright MCP: smoke dos fluxos críticos em `:4200`.

## Handoff

Veredito final PASS → produto elegível a release; FAIL → voltar stage responsável.
