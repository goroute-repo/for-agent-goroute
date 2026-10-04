# Stage 16 — Mobile product (Flutter) — paridade funcional + UX moderna

| Campo | Valor |
|-------|-------|
| **ID** | `stage-16-mobile-product` |
| **Status** | `done` |
| **Persona** | Planner → Developer → Q.A. |
| **Pacote** | `goroute-mobile` (+ auth/BFF só se contrato faltar) |
| **SPECs** | 15 (mobile), 01 (sessão), 09 (workspace profissional) |
| **Depende de** | stages 01–15 (`done`/`qa`) |

---

## 1. Meta

Transformar o MVP atual (login + MFA + roadmap read-only “feio”) em um **app Flutter de produto**: Material 3, navegação moderna, paridade P0 com o dashboard web profissional, performance e testes — sem inventar JWT nem APIs novas.

**Diagnóstico (evidência):**

| Hoje (`goroute-mobile`) | Web (`goroute-frontend`) |
|-------------------------|--------------------------|
| 2 telas úteis (login/MFA + roadmap texto) | Dashboard completo |
| Sem `go_router`, Riverpod, tema, i18n | Design system + ngx-translate |
| Só `dio` + secure storage | Proxy, guards, toasts, tabs |
| UX mínima / release cleartext só agora | Experiência polida |

---

## 2. Pesquisa — técnicas modernas (2025–2026) a adotar

Fontes de mercado (Flutter community / DEV 2026, offline-first guides):

| Dimensão | Padrão recomendado | Por quê no GoRoute |
|----------|--------------------|--------------------|
| Estado | **Riverpod 3** (+ generator opcional na fase 2+) | Testável, sem `BuildContext` no DI |
| Rotas | **go_router** | Deep links futuros, guards de sessão |
| Models | **freezed** + json_serializable | Contratos BFF estáveis |
| UI | **Material 3** + tokens de cor/tipo alinhados à marca GoRoute | Paridade visual com web sem copiar Angular |
| Shell | **NavigationBar** (bottom) 4–5 destinos | Padrão apps carreira (LinkedIn/Notion mobile) |
| Lista/async | `AsyncValue` + skeletons / pull-to-refresh | Sensação de app nativo |
| Offline | **Fase 3+**: cache Drift ou Hive só para read (roadmap/weekly) — sync leve | Rede flaky no celular; não bloqueia MVP online |
| Observabilidade | Crashlytics / Sentry (P2) | Produção |
| Qualidade | `flutter_lints` strict + golden/widget tests | DoD |

**Funcionalidades mais usadas em apps de carreira / learning (benchmark):**

1. Home “próximo passo” (CTA claro)  
2. Trilha / roadmap com progresso  
3. Plano da semana (tarefas checkáveis)  
4. Vagas + match %  
5. Mentor / chat curto  
6. Notificações in-app  
7. Perfil / settings  
8. Sessão segura + biometria (P1)

---

## 3. Ler antes (Developer)

| Path | Motivo |
|------|--------|
| `.agent/skills/flutter.md` | Contratos auth/headers SPEC-01/15 |
| `.agent/guardrails/security.md` | Sessão, sem secrets no APK |
| `goroute-mobile/lib/**` | Baseline atual |
| `goroute-frontend/src/app/app.routes.ts` | Paridade de rotas |
| `.specs/15-mobile-app.spec.md` / `path-goroute/.specs/15-mobile-app.spec.md` | Aceite SPEC |
| Auth mobile: `sessionId` + `mapApiHash` (já em login/MFA) | Não reinventar |

---

## 4. In scope / Out of scope

### In scope

- Fundação: Riverpod, go_router, theme M3, i18n (pt-BR + en mínimo), flavors/env  
- Shell autenticado com bottom nav  
- Features P0: Home, Roadmap (ações), Weekly, Jobs  
- Features P1: Mentor, Notifications in-app, Settings essenciais  
- UX: empty/error/loading, toasts, pull-to-refresh  
- Testes: unit (repos/providers) + widget (shell/login) + golden opcional  
- Dev: cleartext HTTP só em debug/homolog; documentar LAN IP  

### Out of scope (explícito)

- Push nativo (FCM) — P2 / SPEC CA-151  
- Pagamentos in-app Stripe nativo — deep-link web (P2)  
- Workspace empresa / ATS / mobileAppKey UI (CA-150 deprecated)  
- Offline-first completo com sync engine (só cache read P1/P2)  
- Paridade pixel-perfect com Angular  
- Admin career catalog  

---

## 5. Arquitetura alvo

```text
lib/
  main.dart
  app.dart
  core/
    config/          # AppConfig, flavors, dart-define
    network/         # Dio, interceptors, ApiException
    auth/            # SessionStore, auth providers
    routing/         # go_router + redirects
    theme/           # ColorScheme M3 GoRoute
    l10n/            # ARB ou slang
    widgets/         # AppScaffold, EmptyState, ErrorView
  features/
    auth/            # login, mfa
    home/            # next action + summary cards
    roadmap/         # list/detail + start/complete
    weekly/          # tasks complete/skip
    jobs/            # list, match, apply, narrative
    mentor/          # chat analyze
    notifications/   # list + mark read
    settings/        # profile, AI mode, logout, privacy export
```

**Contrato HTTP (inalterado):**

| Header | Valor |
|--------|--------|
| `X-Application-From` | `goroute-mobile` |
| `x-session-id` | sessionId secure storage |
| `x-mapper-api` | mapApiHash pós-login/MFA |

---

## 6. Mapa de paridade (prioridade)

| Área | Mobile hoje | Alvo | Pri |
|------|-------------|------|-----|
| Login + MFA | OK | Polir UX + erros de rede | P0 |
| Home | — | Próximo marco + atalhos | P0 |
| Roadmap | read stub | Detalhe nó + start/complete | P0 |
| Weekly | — | Lista + complete/skip | P0 |
| Jobs | — | Match list + apply + “Why %?” | P0 |
| Mentor | — | Chat + analyze | P1 |
| Notifications | — | Lista in-app | P1 |
| Settings | logout só | Perfil, AI, export LGPD | P1 |
| Billing | — | Link status / portal web | P2 |
| Push / deep links | — | — | P2 |

---

## 7. Fases (Developer executa uma por vez)

### Fase 0 — Fundação UX/engenharia

**Goal:** App “parece produto” antes de features.  
**Depends:** —  

**Entregáveis:**
- Dependências: `flutter_riverpod`, `go_router`, `intl` (ou slang), opcional `freezed`  
- Theme M3 (mint/teal GoRoute, tipografia expressiva)  
- `go_router`: `/login`, `/login/mfa`, shell `/app/*` com redirect se sem sessão  
- Widgets: `LoadingView`, `ErrorView`, `EmptyState`  
- i18n pt-BR + en nas strings novas  

**Aceite:**
- Login visual alinhado à marca; erros de rede legíveis (“Sem conexão com o servidor”)  
- Hot restart com sessão restaura shell  

**Validation:**
```bash
cd goroute-mobile && flutter test && flutter analyze
```

---

### Fase 1 — Shell + Home + Roadmap funcional

**Goal:** Bottom nav + valor diário.  
**Depends:** Fase 0  

**Entregáveis:**
- NavigationBar: Home · Trilha · Semana · Vagas · Mais  
- Home: progresso %, próximo marco, CTAs  
- Roadmap: lista nós por estado; detalhe; `POST start/complete`  
- RefreshIndicator em listas  

**Endpoints:** `GET /roadmap/me`, `GET /roadmap/nodes/:slug`, `POST .../start|complete`  

**Aceite:** usuário autentica e conclui um nó (ou vê locked com mensagem clara).  

**Validation:** `flutter test` + dispositivo/emulador smoke manual.

---

### Fase 2 — Weekly + Jobs (SPEC-15 P0)

**Goal:** Fechar MVP SPEC-15.  
**Depends:** Fase 1  

**Entregáveis:**
- Weekly: tasks, complete, skip, recalculate (tratar 403 premium)  
- Jobs: lista match, detalhe, apply, narrative “Why this %?”  
- Empty states (“sem vagas”, “sem plano”)  

**Endpoints:** weekly-plan + jobs/match/apply + match-narrative  

**Aceite:** fluxo login → semana → marcar tarefa; login → vaga → candidatar (ou 403 claro).  

**Validation:** unit repos + widget; smoke LAN/homolog.

---

### Fase 3 — Mentor + Notifications + Settings (P1)

**Goal:** Paridade “uso diário” com web.  
**Depends:** Fase 2  

**Entregáveis:**
- Mentor: input + histórico local da sessão + `POST /roadmap/mentor/analyze`  
- Notifications: `GET /notifications` + mark read  
- Settings: nome/headline, mentorMode, export privacy, logout, link erase (confirm DELETE)  

**Aceite:** pergunta de progressão de carreira responde in-track (já corrigido no BFF).  

**Validation:** widget mentor + unit settings; analyze mock Dio.

---

### Fase 4 — Performance, polish, release

**Goal:** App installável e rápido.  
**Depends:** Fase 3  

**Entregáveis:**
- Cache read (Hive/shared) para roadmap/weekly última resposta  
- Skeleton loaders; debounce search jobs  
- Flavors: `dev` (cleartext+LAN), `prod` (HTTPS only)  
- README: build APK com dart-define; adb path  
- Opcional: biometria para unlock sessão local  

**Aceite:** cold start < 3s em mid-range; login estável em Wi‑Fi LAN.  

**Validation:** `flutter test`, `flutter build apk --release` (prod sem cleartext).

---

## 8. Princípios para o Developer

1. **Diff mínimo por fase** — não reescrever tudo de uma vez; migrar auth/roadmap existentes.  
2. **Reusar BFF** — zero endpoint novo sem gap real confirmado.  
3. **Sessão GoRoute** — secure storage; headers; sem JWT inventado.  
4. **UX mobile-first** — um job por tela; bottom nav; feedback imediato.  
5. **Erros** — mapear Dio → mensagem i18n (timeout, 401→logout, 403 premium).  
6. **Testes obrigatórios** em cada fase (`.agent/testing.md` + skill Flutter).  
7. **Não** instalar Playwright no mobile; validação UI = widget/integration Flutter.

---

## 9. Riscos / anti-alucinação

| Risco | Mitigação |
|-------|-----------|
| Inventar rotas BFF | Confirmar controllers Nest antes de UI |
| Cleartext release | Flavor: cleartext só debug/homolog |
| Rede celular ≠ LAN PC | Documentar Wi‑Fi mesma subnet; health no browser |
| Escopo explosivo | P0 = fases 0–2; P1 = fase 3 |
| Offline completo | Fora; só cache read |

---

## 10. Handoff Q.A. (por fase)

| Fase | Q.A. verifica |
|------|----------------|
| 0 | Theme + login/MFA + analyze limpo |
| 1 | Bottom nav + roadmap start/complete |
| 2 | Weekly + jobs apply |
| 3 | Mentor in-track + settings export |
| 4 | APK release + smoke credentials |

Credenciais smoke (homolog): `smoke-qa@goroute.local` / `SmokeTest123!`  
API LAN exemplo: `AUTH_BASE_URL=http://192.168.x.x:3010` `API_BASE_URL=http://192.168.x.x:3000/api`

---

## 11. Ordem de execução sugerida

```text
Aprovar este plano
  → Developer Fase 0
  → Q.A. Fase 0
  → Developer Fase 1 … (idem)
  → Após Fase 2: SPEC-15 MVP considerado fechado
  → Fases 3–4: produto “bom de usar”
```

**Default de produto:** online-first com UX polida; cache read opcional; push/billing nativo depois.
