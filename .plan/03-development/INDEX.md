# Desenvolvimento — índice de etapas

**Persona líder:** Planner (definição) → Developer (execução) → Q.A. (gate)  
**Regra:** executar **uma etapa por vez**; não pular dependências.  
**Segurança:** em etapas sensíveis, Developer usa `.agent/skills/secure-service.md` (+ `secure-payments.md` se billing) e preenche `checklists/developer-security-gate.md`.  
**Alinhamento SPECs:** `.plan/02-baseline/coverage-and-spec-alignment.md`

Cada etapa vive em `stage-XX-*/plan.md` com seções fixas (anti-alucinação).

---

## Legenda de status

| Status | Significado |
|--------|-------------|
| `ready` | Planner liberou; pode iniciar |
| `pending` | Aguardando dependência |
| `in_progress` | Developer trabalhando |
| `qa` | Aguardando Q.A. |
| `done` | Q.A. PASS |
| `blocked` | Impedimento documentado |

---

## Ordem obrigatória

| # | ID | Título | Depende de | Status | SPECs | Pacotes |
|---|-----|--------|------------|--------|-------|---------|
| 01 | [stage-01-prod-security-foundation](./stage-01-prod-security-foundation/plan.md) | Fundação segurança/produção + Stripe RS | — | `done` | 01, 11, 19 | auth, api |
| 02 | [stage-02-auth-ux-hardening](./stage-02-auth-ux-hardening/plan.md) | UX auth + MFA/OAuth | 01 | `done` | 01 | auth, frontend |
| 03 | [stage-03-journey-history-progress](./stage-03-journey-history-progress/plan.md) | Histórico + progresso | 01 | `done` | 04 | api, frontend |
| 04 | [stage-04-marketplace-loop](./stage-04-marketplace-loop/plan.md) | Candidaturas + minMatch + ofertas | 01 | `done` | 06–08 | api, frontend |
| 05 | [stage-05-settings-workspaces](./stage-05-settings-workspaces/plan.md) | Settings + workspaces + LGPD | 02, 04* | `done` | 09, 10, 11 | api, frontend |
| 06 | [stage-06-career-replan](./stage-06-career-replan/plan.md) | Replan / goal | 03 | `done` | 22 | api, frontend |
| 07 | [stage-07-b2b-api](./stage-07-b2b-api/plan.md) | API B2B v1 | 04 | `done` | 14 | api |
| 08 | [stage-08-innovation-differentiators](./stage-08-innovation-differentiators/plan.md) | Diferenciais produto | 03, 04, 06, **13** | `done` | 21 | api, frontend, mentor |
| 09 | [stage-09-observability-release-gate](./stage-09-observability-release-gate/plan.md) | Gate de release | 01–05 + 10 mín. | `done` | 19 | infra, e2e |
| 10 | [stage-10-weekly-catalog-storage](./stage-10-weekly-catalog-storage/plan.md) | Weekly + catálogo + storage | 01, 04 | `done` | 05, 12, 13 | api, frontend |
| 11 | [stage-11-nlp-ingestion-assist](./stage-11-nlp-ingestion-assist/plan.md) | NLP assistido (mínimo) | 10 | `done` | 12, 13 | api, nlp |
| 12 | [stage-12-landing-admin](./stage-12-landing-admin/plan.md) | Landing + admin | 01 | `done` | 20, 11, 12 | frontend, admin |
| 13 | [stage-13-mentor-ai-security](./stage-13-mentor-ai-security/plan.md) | Mentor/BYOK seguro | 01, 05 | `done` | 03, 10, 11, 19 | api, mentor, frontend |
| 14 | [stage-14-mobile-parity](./stage-14-mobile-parity/plan.md) | Mobile paridade mínima | 01–05 | `done` | 15 | mobile |
| 15 | [stage-15-residuals-abcd](./stage-15-residuals-abcd/plan.md) | Residuals A–D (LGPD erase, MFA mobile, match LLM, smoke homolog) | 01–14 | `qa` | 02, 10, 15, 21 | api, auth, frontend, mobile |
| 16 | [stage-16-mobile-product](./stage-16-mobile-product/plan.md) | App mobile produto (UX M3 + paridade P0/P1 web) | 14–15 | `done` | 15, 01, 09 | mobile |
| 17 | [stage-17-product-ux-growth](./stage-17-product-ux-growth/plan.md) | Refatoração UX + Growth (council) | 16 | `done` | 15, 20 | mobile, frontend |
| 18 | [stage-18-platform-admin-refactor](./stage-18-platform-admin-refactor/plan.md) | Refatoração platform-admin (UX ops + RBAC + testes) | 12 | `done` | 12, 11, 19 | admin |
| 19 | [stage-19-admin-content-ops](./stage-19-admin-content-ops/plan.md) | Admin: recursos + Learn by Doing na skill + redesign content ops | 18 | `qa` | 12, 03 | api, admin |

\* stage-05 pode iniciar após 02; abas de storage/jobs preferem 04.

---

## Paralelismo permitido

Após **stage-01** `done`:

| Paralelo OK | Não paralelizar |
|-------------|-----------------|
| 03 ∥ 04 ∥ 12 | 06 antes de 03 |
| 02 ∥ 03 (se arquivos auth distintos) | 11 antes de 10 |
| 10 após 04 | 08 antes de 13 (se usar IA) |
| 13 após 05 | 14 antes do loop web P0 |

---

## Skills de segurança (Developer)

| Situação | Ler |
|----------|-----|
| Qualquer endpoint/dados/PII | `.agent/skills/secure-service.md` |
| Billing / Stripe / premium | `.agent/skills/secure-payments.md` |
| Antes do Q.A. | `.agent/skills/checklists/developer-security-gate.md` |
| Review / threat model | `.agent/skills/security-check.md` |

---

## Template obrigatório de cada `plan.md`

1. Meta / ownerPersona / SPECs  
2. Ler antes (paths + secure-service se sensível)  
3. In scope / Out of scope  
4. Entregáveis (A CRIAR vs MODIFICAR)  
5. Critérios de aceite  
6. Validation (comandos)  
7. Riscos / anti-alucinação  
8. Handoff Q.A. (+ security gate se sensível)

---

## Changelog do índice

| Data | Mudança |
|------|---------|
| 2026-07-17 | Criação inicial pós pesquisa de mercado + baseline |
| 2026-07-17 | Stages 10–14; stage-01+Stripe; alinhamento 100% SPECs 00–22; skills secure-* |
| 2026-07-17 | stage-01 → `qa` (Developer: sync opt-in, Stripe idempotência atômica, regressão MFA OAuth) |
| 2026-07-17 | stage-01 → `done` (Q.A. PASS); 02/03/04/12 → `ready` |
| 2026-07-17 | stage-02 + stage-04 → `qa` (Developer: auth Vitest/anti-loop 401; marketplace apply/eligibleOnly/minMatch/invite) |
| 2026-07-17 | stage-02/04 → `done` (Q.A. + MCP Node 22); stage-03 → `qa` (CA-045, snapshot onboarding, Vitest) |
| 2026-07-17 | stage-03 → `done` (Q.A. login smoke + MCP progress/history); stage-05 → `in_progress`; stage-06 → `ready` |
| 2026-07-17 | stage-05 → `qa` (LGPD export + guards Vitest; delete conta deferred P1) |
| 2026-07-17 | stage-05 → `done` (Q.A.); stage-06 → `qa` (SPEC-22 replan + confirm + weekly) |
| 2026-07-17 | stage-06 → `done` (Q.A. MCP replan 409→200 + histórico); stage-12 → `in_progress` |
| 2026-07-17 | stage-12 → `qa` (CTAs home, pricing Vitest, adminTierGuard ADMIN+, Karma) |
| 2026-07-17 | stage-12 → `done` (Q.A.); stage-07 + stage-10 → `ready` (deps 01/04 ok) |
| 2026-07-17 | stage-07 → `qa` (scopes, Idempotency-Key, rate limit Redis, headers SPEC-14) |
| 2026-07-17 | stage-07 → `done` (Q.A. + migration + smoke 401); stage-10 → `in_progress` |
| 2026-07-17 | stage-10 → `qa` (archive/soft-delete, demand update, week guard, QUOTA_EXCEEDED) |
| 2026-07-17 | stage-10 → `done` (Q.A.); stage-11 → `ready` (dep 10) |
| 2026-07-17 | stage-11 → `qa` (NLP soft-fail + catalog filter + localhost bind + tests) |
| 2026-07-17 | stage-11 → `done` (Q.A.); stage-09/13/14 → `ready` (deps ok); 08 ainda espera 13 |
| 2026-07-17 | stage-13 → `qa` (BYOK AES + RN-033/123, mask/DELETE, rate limit, mentor-agent sem apiKey) |
| 2026-07-17 | stage-13 → `done` (Q.A. PASS + MCP settings AI); stage-08 → `ready`; 09/14 ainda `ready` |
| 2026-07-17 | stage-08 → `qa` (Career Twin simulate + match narrativo heurístico; Jest 152 / Vitest 41) |
| 2026-07-17 | stage-08 → `done` (Q.A. MCP Twin + jobs rota narrative; 09/14 `ready`) |
| 2026-07-17 | stage-09 → `qa` (RELEASE_REPORT; e2e 18 PASS + load .env; matriz unitária; smoke preflight) |
| 2026-07-17 | stage-09 → `done` (Q.A. PASS + MCP crítico; release local elegível; 14 ainda `ready`) |
| 2026-07-17 | stage-14 → `qa` (Flutter login+roadmap+secure storage; auth mobile sessionId; flutter test 4) |
| 2026-07-17 | stage-14 → `done` (Q.A. PASS + contrato auth live); **INDEX 01–14 completo** |
| 2026-07-17 | stage-15 residuals A–D → `qa` (LGPD erase, MFA mobile, match LLM, smoke:homolog PASS) |
| 2026-07-18 | stage-16 mobile product → `ready` (Planner: pesquisa UX Flutter 2026 + plano fases 0–4) |
| 2026-07-18 | stage-16 Fase 0 → `qa` (Riverpod/go_router/M3/i18n; flutter analyze + test 9 PASS) |
| 2026-07-18 | stage-16 Fase 0 Q.A. **PASS** → Developer Fase 1 |
| 2026-07-18 | stage-16 Fase 1 → `qa` (shell+home+roadmap start/complete; flutter test 14 PASS) |
| 2026-07-18 | stage-16 Fase 1 Q.A. **PASS** → Developer Fase 2 |
| 2026-07-18 | stage-16 Fase 2 → `qa` (weekly+jobs match/apply/narrative; flutter test 22 PASS) |
| 2026-07-18 | stage-16 Fase 2 Q.A. **PASS** → Developer Fase 3 |
| 2026-07-18 | stage-16 Fase 3 → `qa` (mentor+notifications+settings; flutter test 30 PASS) |
| 2026-07-18 | stage-16 Fase 3 Q.A. **PASS** → Developer Fase 4 |
| 2026-07-18 | stage-16 Fase 4 → `qa` (cache+flavors+release APK; flutter test 34 PASS) |
| 2026-07-18 | stage-16 Fase 4 Q.A. **PASS** → stage-16 `done` (fases 0–4) |
| 2026-07-18 | Personas **UX/UI** + **Growth** + workflow `planner-council` (obrigatório em planos produto/UI) |
| 2026-07-18 | stage-17 product UX+Growth → `in_progress` (council advisory + Fase 1 mobile) |
| 2026-07-18 | stage-17 Fase 1 → `qa` (mentor contextual, premium CTA, badges; flutter test 34 PASS) |
