# Stage 18 — Refatoração Platform Admin (`goroute-platform-admin`)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-18-platform-admin-refactor` |
| **Status** | `done` |
| **Persona** | Planner (+ UX; Growth N/A) → Developer → Q.A. |
| **Depende de** | stage-12 `done` (admin baseline); preferível stage-01 auth |
| **Pacotes** | `goroute-platform-admin` (P0); `goroute-api` só se gap de contrato admin |
| **SPECs** | 12 (catálogo), 11 (métricas billing no dashboard), 19 (segurança/ops) |
| **Council** | [`advisory/ux-ui.md`](./advisory/ux-ui.md), [`advisory/growth-marketing.md`](./advisory/growth-marketing.md) |

---

## 1. Meta

Refatorar o **painel administrativo interno** para operação confiável: UX operacional clara, RBAC consistente, estados de erro/vazio, design tokens alinhados à marca GoRoute, e pirâmide de testes real — **sem** transformar o admin em CMS/marketing e **sem** big-bang obrigatório para Angular 21 nesta stage.

---

## 2. Ler antes

1. `goroute-platform-admin/README.md` (papéis, proxy `:4201`, `X-Application-From: goroute-admin`)
2. `goroute-platform-admin/src/app/app.routes.ts` + `core/models/admin-roles.ts` + guards
3. `goroute-platform-admin/src/app/core/services/admin-api.service.ts` + `goroute-api/src/admin/admin.controller.ts`
4. `.agent/skills/angular.md` + `.agent/guardrails/security.md` + `secure-service.md`
5. Stage-12 notes/qa-gate (baseline já entregue)
6. Council: `advisory/ux-ui.md`

---

## 3. In scope / Out of scope

### In scope

- Shell + design tokens + padrões de feedback (loading / empty / error / toast)
- RBAC: `adminTierGuard` nas rotas (espelhar README: SUPPORT/ADMIN/…)
- Refino operacional: Dashboard, Usuários, Empresas, Catálogos, Auditoria
- Confirmações em ações destrutivas (reusar `shared/confirm-dialog`)
- Testes Karma ampliados (guards, session, serviços HTTP mock, 1–2 componentes críticos)
- Docs README: matriz de papéis × rotas atualizada

### Out of scope

- Migração obrigatória Angular 19 → 21 / Karma → Vitest (fase opcional / stage futura)
- CMS, blog, landing pública (já no `goroute-frontend`)
- App mobile admin
- Novos módulos de produto não cobertos pela API admin atual (ex.: editor visual de grafo, CRM)
- Campanhas Growth / ASO
- Reescrita do BFF admin sem gap comprovado

---

## 4. Decisões (council + engenharia)

| Tema | Decisão | Fonte |
|------|--------|--------|
| Nav | Manter shell lateral; grupos Operação / Dados / Compliance | UX |
| Cores | Dark ops + primary `#0F6E56` + signal com ícone | UX |
| Growth | N/A (ferramenta interna) | Growth |
| Angular 21 | **Não** bloquear refator UX; avaliar em fase opcional ou stage-19 | Planner |
| Auth | Cookie + mapper + `goroute-admin` — sem JWT inventado | SPEC-01 / README |

---

## 5. Estado atual (evidência)

| Área | Hoje |
|------|------|
| Stack | Angular **19**, standalone, Karma/Jasmine |
| Features | Dashboard, Users (+detail), Companies, Career catalog, Skills, Audit |
| Auth | `sessionGuard`, tiers em shell; catalog com `adminTierGuard('ADMIN')` |
| Gap RBAC | `/users` e `/companies` **sem** guard SUPPORT+ na rota |
| UI | Dark ad hoc (`styles.scss`), sem design system compartilhado com frontend |
| i18n | Strings PT hardcoded |
| Testes | Poucos specs (roles, tier guard, app.component) |
| API | `/api/admin/*` via `AdminApiService` |

---

## 6. Fases

### Fase 0 — Baseline + segurança (gates)

**Goal:** Congelar contrato e riscos.  
**ownerPersona:** developer (+ security checklist)  
**Aceite:**

- Matriz papel × rota documentada em `notes.md` / README
- Lista de endpoints admin usados vs `admin.controller.ts` (sem inventar)
- Decisão explícita: **carregar** `public/config.json` no bootstrap **ou** remover promessa README/nginx
- Inventário de dead code (`getMetrics`, `menuPinned`) marcado para limpar/completar
- `developer-security-gate.md` preenchido (sessão admin, ações destrutivas; PATCH user = ADMIN)

**Validation:** revisão diff docs + checklist security.

---

### Fase 1 — Fundação UX (tokens + estados + shell)

**Goal:** Identidade operacional e feedback sistêmico.  
**DependsOn:** Fase 0  
**Aceite UX:** toda tela base tem loading/empty/error reutilizáveis; shell mostra papel e nav agrupada.  
**Files (indicativo):**

- MODIFICAR: `src/styles.scss`, `layout/admin-shell/*`
- CRIAR: `shared/ui/` (ex.: `empty-state`, `error-banner`, `page-header`) ou equivalente no padrão do repo

**Validation:**

```bash
cd goroute-platform-admin && npm test -- --watch=false --browsers=ChromeHeadless
```

Smoke manual `:4201` login + shell.

---

### Fase 2 — RBAC de rotas + sessão

**Goal:** URL não burla o menu.  
**DependsOn:** Fase 1  
**Aceite:**

- `/users`, `/companies` exigem SUPPORT+
- Catálogos ADMIN+ (já existe; regressão)
- Audit: política explícita (mín. READ_ONLY) documentada e guardada
- 401/403 → unauthorized ou relogin com mensagem clara

**Files:** `app.routes.ts`, guards, specs de guard  
**Validation:** Karma matrix papéis (estender `admin-tier.guard.spec.ts`).

---

### Fase 3 — Dashboard operacional

**Goal:** Métricas legíveis com status do sistema.  
**DependsOn:** Fase 1  
**Aceite UX:** erro de API visível; filtro ano com feedback; empty se sem dados do ano.  
**Files:** `features/dashboard/*`  
**Validation:** Karma (serviço mock) + smoke visual dashboard.

---

### Fase 4 — Usuários & Empresas (operação)

**Goal:** Listagens práticas para suporte.  
**DependsOn:** Fase 2  
**Aceite:**

- Empty “nenhum resultado” vs erro de rede distintos
- Detalhe usuário: mutações (roles / reset onboarding) só UI+rota ADMIN+ (SUPPORT read-only)
- Ações com confirm + toast sucesso/erro
- Paginação/filtros sem “loading eterno” em falha

**Files:** `features/users/*`, `features/companies/*`  
**Validation:** Karma componentes críticos + smoke SUPPORT (leitura) / ADMIN (mutação).

---

### Fase 5 — Catálogos (cargos/skills) + Auditoria

**Goal:** CRUD seguro e auditoria legível.  
**DependsOn:** Fase 2  
**Aceite:**

- Mutações só ADMIN+; confirm em delete
- Mensagens de validação API legíveis
- Audit: filtros/empty/erro

**Files:** `features/data-management/*`, `features/audit/*`  
**Validation:** Karma + smoke ADMIN.

---

### Fase 6 (opcional) — Alinhamento toolchain

**Goal:** Avaliar Vitest/Angular 21 **ou** documentar “permanece 19 até stage-XX”.  
**DependsOn:** Fases 1–5 verdes  
**Aceite:** ADR curto em `notes.md` + se migrar, suite verde no novo runner.  
**Out of scope default:** não executar upgrade se risco alto.

---

## 7. Validation global (Q.A.)

```bash
cd goroute-platform-admin
npm test -- --watch=false --browsers=ChromeHeadless
npm start   # http://localhost:4201
```

Smoke (operador admin real):

1. Login com `X-Application-From: goroute-admin` (via app)
2. Dashboard carrega métricas ou erro explícito
3. Usuário SUPPORT vê users/companies; READ_ONLY não acessa por URL
4. ADMIN edita catálogo; ação destrutiva pede confirm
5. Logout limpa sessão

---

## 8. Anti-alucinação

- Não inventar endpoints admin — confirmar em `goroute-api/src/admin/`.
- Não exigir Angular 21 nesta stage sem fase opcional aprovada.
- Não misturar auth do `goroute-frontend` (`goroute-web`) com admin (`goroute-admin`).
- Não expor secrets em `config.json` versionado.

---

## 9. Handoff

| De | Para | Artefato |
|----|------|----------|
| Planner | Developer | este `plan.md` + `notes.md` + advisories |
| Developer | Q.A. | fases com validation; security gate |
| Q.A. | INDEX | `qa` → `done` por fase / stage |
