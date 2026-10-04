# GoRoute — Relatório de Análise Técnica e de Negócio

| Campo | Valor |
|-------|-------|
| **Data** | 19 de junho de 2026 |
| **Escopo** | Análise integral do ecossistema GoRoute (código, specs, documentação, infra) |
| **Metodologia** | Duas passagens: (1) inventário faseado por camada/domínio; (2) verificação cruzada contra specs, código-fonte e documentação |
| **Base analisada** | `PROJETO.md`, `.specs/*` (23 specs), `planos/production-readiness-by-spec.md`, código em todos os repositórios da pasta raiz |

---

## Sumário executivo

O **GoRoute** é uma plataforma **skills-first** de carreira e contratação, com proposta clara documentada em `PROJETO.md`: grafo de competências, roadmap personalizado, plano semanal executável, marketplace de vagas com match percentual e workspace separado para profissionais e empresas.

### Maturidade geral (estimativa)

| Área | Maturidade | Comentário |
|------|------------|------------|
| Arquitetura base (SPA + Auth + BFF) | **Alta** | Padrão implementado e coerente |
| Autenticação e sessão | **Alta** | MFA, OAuth, reset, Redis, mapper API |
| Onboarding | **Alta** | Schema, complete, skip, sync Neo4j, sanitize |
| Roadmap de carreira | **Alta** | Grafo por profissões, progresso, mentor, histórico |
| Plano semanal | **Média-alta** | Backend completo; paywall premium; UI funcional |
| Jobs e matching | **Alta** | CRUD, match %, apply 100%, minMatch empresa |
| Ofertas a candidatos (SPEC-08) | **Média** | Backend implementado; UI frontend mínima |
| Billing / Stripe | **Média-alta** | Checkout, webhook, guards; gaps em ATS premium |
| Settings / perfil expandido | **Média-alta** | APIs extensas; UI com 7 abas |
| Admin plataforma | **Média** | SPA separada com usuários, empresas, catálogo, audit |
| B2B API (SPEC-14) | **Baixa** | Endpoint de jobs com API key; bootstrap de keys exposto |
| Mobile (SPEC-15) | **Não iniciado** | Apenas README placeholder |
| Infra / deploy | **Baixa-média** | Docker parcial; sem Dockerfile do BFF/frontend |
| Testes automatizados | **Baixa** | Poucos unitários; e2e auth desabilitado |
| Prontidão produção | **Média-baixa** | Funcional para homolog; dívidas P0 em segurança e ops |

### Conclusão em uma frase

O projeto **supera em muitos pontos** o que `planos/production-readiness-by-spec.md` (maio/2026) ainda classifica como pendente — várias SPECs evoluíram no código —, mas **não está pronto para produção** sem fechar gaps de segurança (B2B keys, premium ATS), cobertura de testes, deploy reproduzível e endurecimento operacional.

---

## 1. Metodologia da análise

### Fase 1 — Inventário estrutural
- Mapeamento de diretórios, repositórios Git independentes, Dockerfiles e compose.
- Leitura de `PROJETO.md`, `projeto/arquitetura.md` e 23 specs em `.specs/`.

### Fase 2 — Análise por camada
- **Frontend** (`goroute-frontend`): rotas, guards, interceptors, páginas, serviços API.
- **Admin** (`goroute-platform-admin`): rotas, features, integração auth/BFF.
- **Auth** (`goroute-service-auth`): controllers, OAuth, MFA, sessão, migrations.
- **BFF** (`goroute-api`): módulos NestJS, endpoints, guards, entidades, integrações.
- **NLP** (`goroute-nlp`): contrato HTTP e papel no pipeline de demanda.

### Fase 3 — Domínios de negócio
- Cruzamento spec ↔ implementação para cada SPEC-00 a SPEC-22.

### Fase 4 — Transversal
- Modelagem de dados (Postgres auth, Postgres BFF, Neo4j, Redis).
- Integrações externas (Stripe, OAuth, open-meteo, RSS jobs).
- Segurança, performance, falhas funcionais.

### Fase 5 — Segunda análise (verificação)
- Releitura direta de arquivos críticos para confirmar achados dos agentes exploratórios.
- Correção de conclusões desatualizadas em `production-readiness-by-spec.md`.
- Validação de gaps de segurança (B2B, premium, OAuth MFA).

> **Nota:** Não foi possível trocar o LLM entre passagens (limitação do ambiente). A segunda passagem foi executada manualmente com releitura de código-fonte nos pontos de maior risco.

---

## 2. Proposta inicial vs. realidade implementada

### 2.1 Proposta de valor (PROJETO.md)

| Promessa | Status | Evidência |
|----------|--------|-----------|
| Roadmap com grafo de profissões e pré-requisitos | **Implementado** | `ProfessionRoadmapService`, `dashboard-roadmap` com vis-network |
| Time Machine (ETA, horas, % conclusão) | **Implementado** | Resposta `roadmap/me` com `timeMachine` |
| Plano semanal gamificado | **Implementado** (premium) | `weekly-plan` module + UI; gate `PremiumFeatureGuard` |
| Marketplace com match % | **Implementado** | `JobMatchService`, `jobs-marketplace` |
| Empresa publica vagas com skills | **Implementado** | `JobsController`, `company-jobs` |
| Candidatos 50–100% match (empresa) | **Implementado** | Query `minMatch` em candidatos |
| Apply só com 100% match | **Implementado** | `jobs.service.ts` rejeita score ≠ 100 |
| Ofertas / convites empresa → profissional | **Parcial** | Backend `OffersService`; UI `opportunities` mínima |
| Histórico e progresso da jornada | **Parcial** | `pathProgress`, eventos, export PDF; UI history/progress básica |
| Mentor IA | **Parcial** | Heurística + modo managed (Gemini); BYOK em settings |
| Billing Stripe | **Implementado** | Checkout, webhook, subscription entity |
| Mobile app | **Não iniciado** | `goroute-mobile/README.md` apenas |
| B2B API para ATS externo | **Esboço** | `ApiB2bController` sem hardening |

### 2.2 Arquitetura planejada vs. implementada

```
[Browser SPA] ──proxy dev──► /goroute-auth → goroute-service-auth (:3010)
                          └── /api         → goroute-api (:3000)
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
              PostgreSQL         Redis           Neo4j
              (auth + BFF)      (sessão,        (skills,
                               rate limit,      TARGETS,
                               demand cache)    demand_score)
                                    │
                                    ▼
                              goroute-nlp (RSS → skills → demand)
```

**Confirmado no código:** cookie httpOnly + header `x-mapper-api` validado no BFF via `/internal/*` do auth.

---

## 3. Inventário de repositórios

| Repositório / pasta | Stack | Git próprio | Dockerfile | Papel |
|---------------------|-------|-------------|------------|-------|
| `goroute-frontend` | Angular 21 | Sim | Não | SPA usuário final |
| `goroute-api` | NestJS 11 | Não* | **Não** | BFF de negócio |
| `goroute-service-auth` | NestJS | Sim | Sim (porta 3000 no image) | Identidade e sessão |
| `goroute-platform-admin` | Angular 19 | Não* | Sim (nginx) | Painel operacional |
| `goroute-nlp` | Flask/Python | Sim | Sim | Extração de skills |
| `goroute-mobile` | — | Não | — | Placeholder Flutter |
| `path-goroute` | Docs | Sim | compose espelho | Cópia de specs/docs |
| Raiz `GoRoute` | Compose | — | — | Postgres, Redis, Neo4j, NLP |

\*Podem estar versionados na pasta raiz sem `.git` próprio visível no workspace.

### Dívida estrutural
- **Não é monorepo npm** — deploy e CI precisam orquestrar múltiplos repos/pastas.
- **Auth Dockerfile expõe 3000**; dev usa **3010** — risco de configuração em homolog.
- **BFF e frontend sem Dockerfile** — bloqueio para deploy containerizado padronizado.

---

## 4. Análise por camada

### 4.1 goroute-frontend (Angular 21)

#### Rotas implementadas (22 rotas principais)
- Públicas: home, about, pricing, 5 documentos legais.
- Auth: login, empresa, register, forgot/reset password, MFA, oauth-return.
- Onboarding: fluxo profissional e empresa.
- Dashboard profissional: personal, roadmap, jobs, weekly-plan, settings, opportunities, roadmap/progress, roadmap/history, admin/career-catalog (root).
- Dashboard empresa: company, company/jobs, company/settings.

#### Segurança frontend
| Controle | Status |
|----------|--------|
| `withCredentials` em APIs | ✓ |
| `X-Application-From: goroute-web` | ✓ |
| `x-mapper-api` em chamadas BFF | ✓ |
| Re-bootstrap em drift de mapper | ✓ |
| Redirect em 401 | ✓ |
| CSP dev vs produção (`harden-dist.mjs`) | ✓ |
| Cookie consent | ✓ |
| i18n pt-BR/en/es/de | ✓ |

#### Gaps frontend
| Gap | Severidade | Detalhe |
|-----|------------|---------|
| UI mínima para opportunities, progress, history | Média | Funcional mas fora do side-menu |
| Sem página de notificações | Média | API existe no BFF |
| `environment.prod.ts` com URLs relativas | Baixa | Exige reverse proxy same-origin |
| Cobertura de testes mínima | Alta | ~5 specs; sem testes de dashboard |
| Apple OAuth na UI | N/A | Não existe no backend |

#### Qualidade de implementação
- Padrão moderno: standalone components, lazy routes, signals em páginas recentes (`weekly-plan`, `jobs-marketplace`).
- Algumas páginas usam `HttpClient` direto em vez de serviços dedicados (roadmap, settings) — inconsistência de manutenção.

---

### 4.2 goroute-platform-admin (Angular 19)

#### Features
- Login com mesma stack de sessão/mapper do frontend principal.
- Dashboard de métricas (`/api/admin/metrics`).
- Gestão de usuários e empresas (via `/internal/admin` no auth).
- CRUD de catálogo de carreiras e skills.
- Audit log.

#### Gaps
- Porta dev 4201; requer `X-Application-From: goroute-admin` (documentado em PROJETO.md).
- Sem testes além de `app.component.spec.ts`.
- Deploy: Dockerfile nginx pronto; precisa proxy para auth e BFF em homolog.

---

### 4.3 goroute-service-auth (NestJS hexagonal)

#### Endpoints (~40 rotas)
- Users: register, login, MFA, password reset, social unlink.
- Companies: register, login (sem MFA).
- OAuth: Google, GitHub, Microsoft, Facebook, Discord — login + link.
- Internal: session/mapper validate, onboarding PATCH.
- Internal admin: métricas e gestão de usuários/empresas.

#### Segurança implementada
| Controle | Status |
|----------|--------|
| bcrypt (rounds configurável) | ✓ |
| Sessão Redis com TTL inatividade + absoluto | ✓ |
| MFA TOTP + backup codes (AES) | ✓ |
| Rate limit login / reset / MFA | ✓ |
| Helmet + CORS whitelist | ✓ |
| ValidationPipe global | ✓ |
| OAuth PKCE + state Redis | ✓ |
| Internal API key guard | ✓ |
| Onboarding PATCH atômico | ✓ (`markOnboardingCompletedIfPending`) |

#### Gaps auth (verificados na 2ª passagem)
| Gap | Severidade | Detalhe |
|-----|------------|---------|
| Apple OAuth documentado mas **não implementado** | Média | README/docker-compose vs código |
| MFA empresa inexistente | Média | `mfaRequired: false` fixo em company login |
| OAuth MFA step-up | **Resolvido** | `AuthenticatedLoginOutcomeUseCase` verifica `mfaEnabled` no callback social |
| Testes | Alta | 1 unit spec; e2e skipped sem `INTEGRATION_TEST=1` |
| `@nestjs/throttler` instalado mas não usado | Baixa | Rate limit custom em Redis |

#### Migrations auth (2)
1. `InitialAuthSchema` — users, companies, social_accounts, mfa_secrets, hashed_applications.
2. `LegalAcceptanceAuth` — termos, privacidade, DPA.

---

### 4.4 goroute-api (NestJS BFF)

#### Módulos (18 áreas funcionais)
`AuthBff`, `Database`, `Onboarding`, `Dashboard`, `CareerCatalog`, `Roadmap`, `WeeklyPlan`, `Jobs`, `Billing`, `Settings`, `CareerJourney`, `Notifications`, `ApiB2b`, `Admin`, `JobsMarket` (background), `ApiSecret`, `Redis`.

#### Inventário de endpoints (resumo)
- **Públicos:** health, weather, billing/plans, stripe/webhook, b2b/health.
- **Autenticados:** me, onboarding, roadmap*, weekly-plan*, jobs*, settings*, billing/subscription, notifications, opportunities.
- **Admin:** metrics, users, companies, audit.
- **B2B:** POST jobs (com API key), POST keys (**sem auth**).

\*Com guards em cadeia: Session → Onboarding → Role → Premium (quando aplicável) → ProfileRequired.

#### Guards — matriz de uso

| Guard | Onde se aplica |
|-------|----------------|
| SessionAuthGuard | Maioria das rotas BFF |
| OnboardingCompletedGuard | Dashboard, roadmap, jobs, weekly-plan, settings |
| ProfessionalRoleGuard | Rotas profissional |
| CompanyRoleGuard | Rotas empresa |
| PremiumFeatureGuard | weekly-plan, webhook ATS |
| ProfileRequiredGuard | roadmap, weekly-plan |
| AdminRoleGuard | career-catalog admin, admin module |
| Rate limits Redis | onboarding, mentor, jobs write, weekly-plan, match |

#### Integrações BFF

| Integração | Implementação | Gap |
|------------|---------------|-----|
| Auth interno | HTTP + `INTERNAL_API_KEY` | ✓ |
| Neo4j | `@Global() Neo4jModule` | Env vars fora do schema |
| Redis | Sessão indireta; rate limit; demand cache; BullMQ | ApiSecret órfão |
| Stripe | Checkout, webhook, idempotency | `STRIPE_PRICE_COMPANY_ATS` fora do schema |
| NLP | `NlpClientService` + fallback local | Só pipeline RSS |
| Gemini (mentor) | `GEMINI_API_KEY` | Não documentado em env.schema |
| ViaCEP | CEP lookup onboarding/settings | ✓ |

---

### 4.5 goroute-nlp (Flask)

- `GET /health`, `POST /extract-skills`.
- Modo rules (default) ou spaCy (`USE_SPACY=1`).
- Catálogo estático `skills.json` — pode divergir do catálogo Postgres/BFF.
- Worker único gunicorn — adequado para homolog; bottleneck em produção com alto volume RSS.

---

## 5. Análise por domínio (SPECs)

Legenda: ✅ Atende | 🟡 Parcial | ❌ Não implementado | 🔧 Dívida técnica

| SPEC | Título | Status | Principais gaps |
|------|--------|--------|-----------------|
| 00 | Visão plataforma | ✅ | Mobile fora do repo |
| 01 | Auth e identidade | 🟡→✅ | Apple OAuth; MFA empresa; testes |
| 02 | Onboarding | ✅ | Rate limit presente; sanitize implementado |
| 03 | Roadmap | ✅ | Mentor BYOK completo depende de settings |
| 04 | Histórico/progresso | 🟡 | pathProgress + eventos + PDF; UI mínima; snapshots limitados |
| 05 | Plano semanal | ✅ | Premium obrigatório; UNIQUE week_key na migration |
| 06 | Jobs marketplace | ✅ | Webhook SSRF: HTTPS obrigatório em `normalizeWebhookUrl` |
| 07 | Matching/candidaturas | ✅ | apply 100%; minMatch 50–100 |
| 08 | Ofertas candidatos | 🟡 | Backend completo; falta UX e notificações visíveis |
| 09 | Workspaces | 🟡 | Guards por role; nem todas rotas segregadas no frontend |
| 10 | Settings/perfil | 🟡 | APIs extensas; falta polish UX; storage quota parcial |
| 11 | Billing | 🟡 | Stripe OK; ATS reports sem premium guard |
| 12 | Catálogo skills | 🟡 | Seed + admin CRUD; demand incrementa em job create |
| 13 | Storage/ingestão | 🟡 | Entity storage + quota default; RSS scheduler 6h |
| 14 | API B2B | 🔧 | POST keys público — **vulnerabilidade** |
| 15 | Mobile | ❌ | Placeholder |
| 16 | Arquitetura | 🟡 | Documentada; deploy incompleto |
| 17 | Modelo de dados | 🟡 | 11 migrations BFF; synchronize ainda default true em dev |
| 18 | Contratos API | 🟡 | Swagger BFF; drift pontual com README auth |
| 19 | Segurança | 🟡 | B2B keys; premium ATS; ver seção 8 |
| 20 | Landing pública | ✅ | Home com grafo, about, i18n |
| 21 | Inovação backlog | — | Não avaliado item a item |
| 22 | Career replan | 🟡 | PATCH career-goal em settings; sem fluxo dedicado |

### Correções à documentação desatualizada

O arquivo `planos/production-readiness-by-spec.md` (2026-05-23) está **parcialmente obsoleto**. Itens que o código **já implementa**:

| Item marcado pendente no plano | Situação atual no código |
|--------------------------------|--------------------------|
| OAuth sem MFA step-up | **Corrigido** — `AuthenticatedLoginOutcomeUseCase` |
| PATCH onboarding não atômico | **Corrigido** — `markOnboardingCompletedIfPending` |
| UNIQUE week_key | **Presente** — migration `InitialBffSchema` |
| apply + minMatch (SPEC-07) | **Implementado** |
| Ofertas (SPEC-08) "tudo ❌" | **Backend implementado** — `OffersService` |
| Settings "APIs ❌" | **Implementado** — `SettingsController` extenso |
| sanitize onboarding | **Implementado** — `sanitizePlainText` |

---

## 6. Modelagem de dados e schemas

### 6.1 PostgreSQL — Auth (`goroute-service-auth`)

| Tabela | Propósito |
|--------|-----------|
| users | Credenciais, roles, onboarding, MFA flag, legal acceptance |
| companies | Empresa, CNPJ, onboarding |
| social_accounts | OAuth linkage |
| mfa_secrets | TOTP criptografado + backup hashes |
| hashed_applications | Mapper API keys por applicationFrom |

**Sessões:** apenas Redis (não persistidas em Postgres).

### 6.2 PostgreSQL — BFF (`goroute-api`) — 25 entidades

| Grupo | Entidades |
|-------|-----------|
| Perfis | professional_profile, company_profile, user_skill, company_skill |
| Carreira | career_role_category, career_role, skill_reference, roadmap_node_progress |
| Jornada | career_journey_event, career_journey_snapshot |
| Jobs | job_posting, job_skill_requirement, job_application, candidate_offer, job_candidate_selection |
| Plano | weekly_plan, weekly_plan_task |
| Billing | subscription, subscription_payment, stripe_processed_event |
| Plataforma | user_ai_settings, user_notification, company_storage_usage, b2b_api_key, admin_audit_log |

### 6.3 Migrations BFF (11 arquivos)

Evolução incremental desde schema inicial até career role details e billing payments. **Produção:** `TYPEORM_SYNCHRONIZE=false` obrigatório (validado em `env.schema.ts`); `TYPEORM_MIGRATIONS_RUN=true` default em prod.

### 6.4 Neo4j

| Uso | Cypher / nós |
|-----|--------------|
| Skills do usuário | `HAS_SKILL` |
| Meta de carreira | `TARGETS` |
| Pré-requisitos plano semanal | `:REQUIRES` |
| Demanda de mercado | `demand_score` em nós Skill |
| Seed | `SkillGraphSeedService` no boot |

**Risco:** grafo Neo4j e catálogo Postgres de profissões coexistem — roadmap principal usa **Postgres** (`ProfessionRoadmapService`); Neo4j ainda usado para skills/demand/unlock semanal.

### 6.5 Redis

| Chave / uso | Serviço |
|-------------|---------|
| Sessões auth | goroute-service-auth |
| Rate limits | auth + BFF guards |
| OAuth state | auth |
| Demand score cache | BFF |
| BullMQ job-listings | BFF jobs-market |
| api_secret_key rotation | BFF (**sem consumidor identificado**) |
| Mentor quota managed | BFF |

### 6.6 Consistência entre stores

| Fluxo | Mecanismo |
|-------|-----------|
| Onboarding complete | Postgres profile + PATCH auth + Neo4j sync + journey event |
| Skill update settings | Postgres + Neo4j sync |
| Weekly plan complete | Postgres tasks + Neo4j unlock path |
| Billing webhook | Stripe → subscription row |

**Gap:** não há saga/transação distribuída — falha parcial pode deixar stores dessincronizados (mitigação: retry manual / jobs futuros).

---

## 7. Integrações externas

### 7.1 Stripe
- Planos: starter (free), weekly_plan, company_starter, company_ats.
- Checkout session, webhook com idempotency, `PremiumFeatureGuard`.
- **Homolog:** usar chaves test + webhook tunnel (Stripe CLI).
- **Gap:** `STRIPE_PRICE_COMPANY_ATS` usado no código mas ausente de `env.schema.ts`.

### 7.2 OAuth social
- 5 provedores ativos; PKCE; link de conta autenticada.
- Redirect errors: `oauth_error=not_configured`.
- **Apple:** não implementado apesar da documentação.

### 7.3 Open-Meteo (weather)
- Proxy BFF `/api/weather` — evita CORS no side-menu.

### 7.4 RSS / NLP (jobs market)
- Scheduler enfileira feeds → NLP extrai skills → incrementa demand Neo4j.
- Fallback keyword local se NLP indisponível — **degradação graciosa**.

---

## 8. Segurança e vulnerabilidades

### 8.1 Controles fortes
- Sessão httpOnly + validação dupla (cookie + mapper).
- bcrypt, MFA, rate limits, helmet, CORS restrito.
- ValidationPipe whitelist no BFF e auth.
- Stripe webhook signature verification.
- Webhook URL empresa: exige HTTPS.
- CSP endurecida no build de produção do frontend.
- INTERNAL_API_KEY obrigatório em produção (BFF).

### 8.2 Vulnerabilidades e riscos (priorizados)

| ID | Risco | Severidade | Local | Recomendação |
|----|-------|------------|-------|--------------|
| **SEC-B2B-01** | `POST /api/b2b/v1/keys` sem autenticação | **Crítica** | `api-b2b.controller.ts` | Proteger com InternalApiKeyGuard ou admin; desabilitar em prod |
| **SEC-PREM-01** | Reports ATS sem `PremiumFeatureGuard` | **Alta** | `jobs.controller.ts` company/reports | Alinhar com SPEC-11 e marketing |
| **SEC-ENV-01** | Secrets default em dev (`dev-internal-key-change-me`) | **Alta** | `env.schema.ts` | Falhar boot se default em staging/prod |
| **SEC-ENV-02** | Neo4j credenciais default hardcoded | **Média** | `neo4j.service.ts` | Exigir env em homolog+ |
| **SEC-DOC-01** | Apple OAuth falso positivo na doc | **Baixa** | README auth | Remover ou implementar |
| **SEC-TEST-01** | Cobertura de testes insuficiente | **Alta** | Global | CI com threshold mínimo |
| **SEC-ORPHAN-01** | ApiSecret rotation sem consumidor | **Média** | `api-secret-rotation.service.ts` | Remover ou integrar |
| **SEC-CORS-01** | Admin origin separado — fácil misconfig | **Média** | auth CORS | Checklist deploy |

### 8.3 LGPD / privacidade
- Termos, privacidade, DPA, banco de talentos — páginas legais no frontend.
- `talent_pool_consent` em profile — implementado.
- Export PDF de histórico — implementado; revisar retenção e direito de exclusão (não automatizado).

---

## 9. Performance e eficiência

### 9.1 Pontos positivos
- Apps stateless — escaláveis horizontalmente (auth + BFF).
- Redis cache para demand scores.
- BullMQ para processamento assíncrono de RSS.
- Lazy routes no Angular — bundle inicial menor.
- vis-network com altura fixa no roadmap — evita loop de layout (corrigido no SCSS).

### 9.2 Riscos de performance

| Área | Risco | Mitigação sugerida |
|------|-------|-------------------|
| Match de candidatos | Scan O(users × jobs) | Índices, paginação, cache (SPEC-07 P0 scan) |
| Neo4j reads no guard | `ProfileRequiredGuard` query por request | Cache goal slug em profile Postgres |
| NLP single worker | Gargalo em picos RSS | Escalar réplicas ou desabilitar spaCy |
| Postgres single instance | Homolog OK; prod precisa RDS tier + connection pool tuning |
| Mentor Gemini | Chamada síncrona | Timeout, circuit breaker, fila |
| Side-menu weather/geo | Request a cada 10 min + geolocation | Aceitável; watch em mobile |

### 9.3 Eficiência de recursos (custo cloud)
- Neo4j Aura + 2 NestJS + Postgres + Redis + NLP ≈ **mínimo 5 serviços** pagos em homolog.
- NLP e jobs-market podem ser desligados se homolog focar só em roadmap/auth.
- Frontend estático em CDN — custo marginal.

---

## 10. Falhas funcionais conhecidas

| Sintoma | Causa provável | Status |
|---------|----------------|--------|
| `GET /api/weekly-plan` 403 | Sem assinatura `weekly_plan` ativa | **Esperado** — UI paywall |
| NG0100 em weekly-plan | Change detection com propriedades síncronas | **Corrigido** — migração para signals |
| Roadmap vazio | Onboarding sem categoria/cargos | Mensagem empty state existe |
| Stripe checkout indisponível | `STRIPE_SECRET_KEY` vazio | Degraded mode |
| OAuth "not_configured" | Provider sem client id/secret | Redirect com erro |
| Mentor managed falha | `GEMINI_API_KEY` ausente | Fallback heurístico |
| Apple login na doc | Provider inexistente | Falha se usuário tentar |

---

## 11. O que ainda falta implementar (priorizado)

### P0 — Bloqueia homolog/produção segura
1. Proteger ou remover `POST /api/b2b/v1/keys`.
2. Dockerfile + CI para `goroute-api` e `goroute-frontend`.
3. Checklist de env vars produção (sem defaults inseguros).
4. `PremiumFeatureGuard` em reports ATS se produto exige ATS pago.
5. Pipeline de migrations automático no deploy.
6. Testes de integração auth ↔ BFF (sessão + mapper).

### P1 — Completude de produto
1. UI de opportunities, notifications, roadmap history/progress no menu.
2. Apple OAuth ou remoção da documentação.
3. MFA para empresas (se requisito de negócio).
4. Mobile app (SPEC-15) — fora do escopo imediato.
5. Career replan dedicado (SPEC-22).
6. B2B API completa com scopes e HMAC webhooks (SPEC-14).
7. Storage quotas enforcement em job archive/delete (SPEC-13).

### P2 — Excelência
1. E2E Playwright/Cypress fluxos críticos.
2. Observabilidade OpenTelemetry (já planejado em `projeto/metricas_e_observabilidade.md`).
3. Saga/retry para sync multi-store.
4. Unificar catálogo NLP `skills.json` com Postgres.

---

## 12. Melhorias recomendadas

### Arquitetura e código
- Monorepo tooling (Nx/Turborepo) ou pipeline único multi-repo.
- Extrair serviços HTTP do frontend para camada `core/services` consistente.
- Remover código morto: `RootRoleGuard`, `ApiSecretRotation` se não usado.
- Atualizar `roadmap.service.spec.ts` para `ProfessionRoadmapService`.
- Adicionar `STRIPE_PRICE_COMPANY_ATS`, `NEO4J_*`, `GEMINI_API_KEY` ao `env.schema.ts`.

### Operações
- Compose de homolog com auth + BFF + front (não só dados).
- Health checks agregados para Railway/K8s.
- Backup automático Postgres + Neo4j Aura.
- Stripe webhook endpoint documentado por ambiente.

### Negócio / UX
- Trial ou flag de premium em homolog para testar weekly-plan sem SQL manual.
- Onboarding guidance quando roadmap empty.
- Notificações in-app para ofertas de emprego.

---

## 13. Possíveis inclusões (além do escopo atual)

| Inclusão | Valor | Esforço |
|----------|-------|---------|
| Modo demo/sandbox sem cadastro | Aquisição | Médio |
| Export roadmap PDF para profissional | Portabilidade | Baixo (já existe para history) |
| Webhooks outbound para profissional (nova vaga match) | Engajamento | Médio |
| Integração LinkedIn import skills | Onboarding | Alto |
| Painel de métricas empresa em tempo real | B2B | Médio |
| Feature flags (LaunchDarkly/unleash) | Homolog segura | Médio |

---

## 14. Segunda análise — verificação cruzada

### 14.1 Metodologia da verificação
- Releitura direta de: `app.module.ts`, `roadmap.module.ts`, `neo4j.module.ts`, `social.controller.ts`, `authenticated-login-outcome.usecase.ts`, `patch-user-onboarding.usecase.ts`, `api-b2b.controller.ts`, `offers.service.ts`, `jobs.service.ts` (apply), migrations `InitialBffSchema`.
- Confirmação de que `Neo4jModule` é `@Global()` — **falso positivo** da 1ª passagem sobre DI do `ProfileRequiredGuard`.
- Confirmação de que OAuth MFA **não é gap** — `authOutcome.forUser` trata `mfaEnabled`.

### 14.2 Itens confirmados como corretos na 1ª análise
- Arquitetura tripla SPA + auth + BFF.
- 403 weekly-plan = premium gate intencional.
- B2B keys endpoint desprotegido.
- Apple OAuth ausente no código.
- 11 migrations BFF; 2 migrations auth.
- Offers backend implementado.
- Demand score incrementado em job create.
- Sanitize em onboarding e settings.

### 14.3 Itens corrigidos na 2ª passagem
| 1ª passagem | Correção |
|-------------|----------|
| RoadmapModule sem Neo4j = erro DI | `Neo4jModule` é global via `DatabaseModule` |
| OAuth sem MFA (SPEC-01 RS-01) | Implementado em `AuthenticatedLoginOutcomeUseCase` |
| Onboarding PATCH não atômico | `markOnboardingCompletedIfPending` |
| UNIQUE week_key pendente | Constraint na migration inicial |
| SPEC-08 totalmente ausente | Backend presente; falta UX |
| SPEC-10 settings ausente | APIs implementadas |

### 14.4 Cobertura da análise — checklist

| Dimensão | Coberto |
|----------|---------|
| Estrutura de repositórios | ✓ |
| Frontend rotas e features | ✓ |
| Admin plataforma | ✓ |
| Auth endpoints e OAuth | ✓ |
| BFF módulos e endpoints | ✓ |
| Stripe/billing | ✓ |
| Postgres schemas | ✓ |
| Neo4j | ✓ |
| Redis | ✓ |
| NLP | ✓ |
| Specs 00–22 (resumo) | ✓ |
| Segurança | ✓ |
| Performance | ✓ |
| Falhas funcionais | ✓ |
| Deploy/infra | ✓ |
| Testes | ✓ |
| Mobile | ✓ |
| Divergência doc vs código | ✓ |

**Não analisado em profundidade linha a linha:** cada item do backlog SPEC-21 (inovação); código Flutter inexistente; conteúdo completo de todos os 23 specs (resumo por spec).

---

## 15. Recomendação para homologação

Ordem sugerida antes de expor homolog publicamente:

1. **Fechar SEC-B2B-01** (keys endpoint).
2. **Criar Dockerfiles** faltantes + variáveis de ambiente documentadas por serviço.
3. **Deploy híbrido:** Vercel (front + admin) + Railway (auth, BFF, NLP) + Aura Neo4j + Postgres/Redis gerenciados.
4. **Stripe test mode** + usuário seed com subscription active para QA do weekly-plan.
5. **Smoke tests:** login → onboarding → roadmap → job match → billing checkout.
6. **Atualizar** `planos/production-readiness-by-spec.md` com status real deste relatório.

---

## 16. Conclusão final

O GoRoute é um projeto **substancial e bem arquitetado** para estágio pré-produção: a proposta de valor do `PROJETO.md` está **majoritariamente refletida no código**, com destaque para auth robusto, roadmap por profissões, matching de vagas e integração Stripe.

As principais lacunas para homologação segura são **operacionais e de segurança pontual** (B2B keys, deploy reproduzível, testes), não ausência do core do produto. A documentação interna de prontidão (`production-readiness-by-spec.md`) deve ser **atualizada** para evitar decisões baseadas em gaps já resolvidos.

**Veredicto:** apto para **homologação controlada** após correção dos itens P0 de segurança e setup de deploy; **não apto para produção pública** sem testes, observabilidade e hardening completos.

---

*Relatório gerado por análise automatizada assistida com verificação cruzada em código-fonte. Para dúvidas sobre um item específico, referencie a seção e o ID de risco (ex.: SEC-B2B-01).*
