# RELEASE REPORT — GoRoute (stage-09)

**Date:** 2026-07-17  
**Prepared by:** Developer (pré-gate)  
**Scope:** stages 01–08 `done` + matriz stage-09  
**Ambiente:** local (`npm start` :4200, BFF :3000, auth :3010)

---

## Veredito Developer (pré-Q.A.)

| Campo | Valor |
|-------|-------|
| Elegível a Q.A. release gate | **SIM** |
| P0 segurança aberta sem waiver | **Nenhuma** |
| Bloqueadores de código | **Nenhum** nesta execução |

---

## Veredito Q.A. (final)

| Campo | Valor |
|-------|-------|
| Date | 2026-07-17 |
| Verdict | **PASS** |
| Release elegível (local) | **SIM** |
| E2E re-run | **18 PASS** |
| MCP crítico | login → personal → roadmap → weekly → settings → jobs **OK** |
| Waivers §4 | **Aceitos** |

Assinado: Persona Q.A.

---

## 1. Matriz de regressão (unitários)

| Pacote | Comando | Resultado |
|--------|---------|-----------|
| `goroute-api` | `npm test -- --runInBand` | **152 PASS** (50 suites) |
| `goroute-service-auth` | `npm test -- --runInBand` | **14 PASS** (6 suites) |
| `goroute-frontend` | `npm test` | **41 PASS** (20 files) |
| `goroute-mentor-agent` | `python -m pytest -q` | **6 PASS** |

---

## 2. E2E (`e2e/`)

| Item | Resultado |
|------|-----------|
| Suite | **18 PASS** (0 skip) |
| Gap corrigido nesta etapa | `playwright.config.ts` carrega `e2e/.env` (login deixava de rodar sem dotenv) |
| Credenciais | `e2e/.env` a partir de `.env.example` (gitignored) |
| Cobertura | health, public pages, login+roadmap, responsive, visual smoke |

---

## 3. Smoke / health

| Check | Resultado |
|-------|-----------|
| `GET http://localhost:4200` | 200 |
| `GET http://localhost:3000/api/health` | `{ status: ok }` |
| `GET http://localhost:3010/health` | `{ status: ok }` |
| `npm run smoke:preflight` | **OK** (Auth, BFF, SMOKE_*, INTERNAL_API_KEY) |
| `npm run smoke:homolog` | **PASS** (stage-15 residual D — 2026-07-17) — billing status + Stripe checkout URL |
| Docker `docker-compose.homolog.yml` | **UP** — `docker compose -f docker-compose.yml -f docker-compose.homolog.yml up -d --build` (INTERNAL_API_KEY=`change-me-homolog-internal-key`) |

---

## 4. Checklist segurança (revalidação SPEC-19 / gates anteriores)

| Tema | Status | Evidência / waiver |
|------|--------|--------------------|
| Sessão + `x-mapper-api` (sem JWT em localStorage) | OK | stages 01–02; mapper em storage é hash de contexto, não access token |
| MFA / OAuth hardening | OK | stage-02 `done` |
| Stripe webhook idempotência | OK | stage-01 |
| BYOK cifrado / SEC-07 | OK | stage-13 |
| B2B scopes + Idempotency-Key | OK | stage-07 |
| NLP bind localhost | OK | stage-11 |
| Conta delete permanente | OK | stage-15 A — `DELETE /api/settings/privacy/account` + auth erase + UI |
| Secrets no repo | OK | `.env` e2e gitignored nesta etapa |
| Migrations sem synchronize | OK | prática BFF (stages anteriores) |

### Waivers assinados (não-P0)

1. ~~**LGPD delete account**~~ — **fechado** stage-15 A.  
2. ~~**Match narrativo LLM**~~ — **fechado** stage-15 C (managed/BYOK + soft-fallback).  
3. **SigNoz / observabilidade full** — out of scope stage-09 / stage-15 D.  
4. ~~**smoke:homolog completo**~~ — **PASS** stage-15 D.

---

## 5. Fluxos críticos (evidência acumulada + e2e)

| Fluxo | Evidência |
|-------|-----------|
| Login profissional | e2e `login-flow` PASS + stages 02/03/08 MCP |
| Roadmap | e2e roadmap após login PASS |
| Weekly / settings / Twin / AI | stages 05, 06, 08, 10, 13 Q.A. |
| Jobs loop | stage-04 `done`; marketplace sem vagas no smoke (residual) |
| Landing / pricing | e2e public + stage-12 |

---

## 6. Diff desta etapa (Developer)

| Arquivo | Motivo |
|---------|--------|
| `e2e/playwright.config.ts` | Carregar `.env` para não skipar login |
| `e2e/.gitignore` | Ignorar `.env` |
| `RELEASE_REPORT.md` / `notes.md` / security gate | Entrega stage-09 |

Sem novas features de produto.

---

## 7. Como subir (referência — sem alteração de docs errados)

```bash
# dados
docker compose up -d   # postgres/redis/neo4j (+ nlp se preciso)

# serviços
cd goroute-service-auth && npm run start:dev   # :3010
cd goroute-api && npm run start:dev            # :3000
cd goroute-frontend && npm start               # :4200

# e2e
cd e2e && cp .env.example .env && npm test
```

Homolog container: `docker compose -f docker-compose.yml -f docker-compose.homolog.yml up -d --build`.

---

## Handoff Q.A.

Concluído — ver `qa-gate.md` + veredito acima. INDEX → `done`.
