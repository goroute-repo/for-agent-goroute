# AGENT.md — Instruções para agentes de IA (GoRoute)

Este arquivo é o **contrato obrigatório** de como **planejar, implementar, testar e revisar** código neste monorepo. Não improvise padrões fora de `.agent/` e das skills do pacote afetado.

---

## 0. Personas (obrigatório)

Toda sessão escolhe **uma persona ativa** conforme a etapa.

| Persona | Arquivo | Responsabilidade |
|---------|---------|------------------|
| **Planner** | [`.agent/personas/planner.md`](.agent/personas/planner.md) | **Principal Solutions Architect** — problema, opções/ADR, riscos, fases |
| **UX/UI** | [`.agent/personas/ux-ui.md`](.agent/personas/ux-ui.md) | **Principal Product Designer** — atritos P0–P2, fluxos, estados |
| **Growth** | [`.agent/personas/growth-marketing.md`](.agent/personas/growth-marketing.md) | **Principal Growth** — ICP, messaging, funil/ASO (ou N/A interno com valor) |
| **Developer** | [`.agent/personas/developer.md`](.agent/personas/developer.md) | Implementar com diff mínimo + unitários |
| **Q.A.** | [`.agent/personas/qa.md`](.agent/personas/qa.md) | Gate DoD: executar testes, Playwright MCP, veredito |

Índice: [`.agent/persona.md`](.agent/persona.md). Pareceres rasos (paleta genérica / 3 títulos ASO / plano só de arquivos) devem ser **rejeitados** — ver gates em `skills/planning.md`, `ux-advisory.md`, `growth-advisory.md`.

```
Pedido
  → Planner (se complexo / multi-fase)
       ↳ se produto/UI/mobile: Council UX + Growth (obrigatório)
  → Developer (implementar)
  → Q.A. (validar) → pronto ou devolver ao Developer
```

| Tipo de tarefa | Fluxo de personas |
|----------------|-------------------|
| Simples (1–2 arquivos, escopo claro) | Developer → Q.A. (checklist mínimo) |
| UI (feature/update) | Developer → Q.A. (**Vitest + Playwright MCP**) |
| Produto / mobile / redesenho | Planner → **UX + Growth** → plano `ready` → Developer → Q.A. |
| Complexo / arquitetural | Planner (+ council se UI) → dual-review → Developer → Q.A. |

Council: [`.agent/workflows/planner-council.md`](.agent/workflows/planner-council.md).

### Leitura obrigatória por persona

| Persona | Ler sempre |
|---------|------------|
| Todas | Guardrails `security.md` + `quality.md` |
| Planner | `personas/planner.md`, `skills/planning.md`, `planner-council.md` se UI, `schemas/plan.schema.json`, `PROJETO.md` |
| UX/UI | `personas/ux-ui.md`, `skills/ux-advisory.md` (+ draft + código das telas) |
| Growth | `personas/growth-marketing.md`, `skills/growth-advisory.md` (+ draft; modo interno se admin) |
| Developer | `personas/developer.md`, skill de stack, `testing.md` (obrigação de unitários) |
| Q.A. | `personas/qa.md`, `testing.md`, regra Playwright MCP se UI |
> **Definition of Done (UI):** implementação + Vitest (`npm test`) + app em `http://localhost:4200` (`npm start`) + **Playwright MCP** ou `e2e/` + guardrails. **Nada de Playwright no `package.json` do frontend.**

---

## 1. Princípio fundamental

**Antes de escrever ou alterar código:**

1. Escolher e assumir a persona correta ([`.agent/persona.md`](.agent/persona.md)).
2. Entender escopo e pacote afetado (mapa §2).
3. Carregar **guardrails** aplicáveis.
4. Carregar a **skill de stack** (Developer) ou **planning** (Planner).
5. Planejar testes conforme [`.agent/testing.md`](.agent/testing.md) — Planner na `validation`; Developer nos specs; Q.A. no gate.
6. Tarefas não triviais → planejamento estruturado + persona Planner.
7. Q.A. valida antes de considerar concluído.

> Regra de ouro: **diff mínimo, escopo focado, convenções existentes, sempre testado**. Não refatore fora do pedido.

---

## 2. Mapa do repositório

| Pacote | Stack | Skill principal | Testes | Documentação |
|--------|-------|-----------------|--------|--------------|
| `goroute-frontend/` | Angular 21, Vitest, vis-network, ngx-translate | `.agent/skills/angular.md` | Vitest + **Playwright MCP** / `e2e/` | `README.md`, `PROJETO.md`, `.specs/` |
| `goroute-api/` | NestJS 11 BFF, TypeORM, Neo4j, Redis, BullMQ, Stripe | `.agent/skills/nestjs.md` | Jest | `README.md`, `PROJETO.md`, `.specs/` |
| `goroute-service-auth/` | NestJS 11 hexagonal, PostgreSQL, Redis, OAuth/MFA | `.agent/skills/nestjs.md` | Jest + e2e integração | `README.md`, `.specs/` |
| `goroute-platform-admin/` | Angular 19, Karma/Jasmine | `.agent/skills/angular.md` | Karma headless | `README.md` |
| `goroute-mentor-agent/` | Python 3.11+, FastAPI, SLM | `.agent/skills/python-ai.md` | pytest | `README.md` |
| `goroute-nlp/` | Python 3.12, Flask | `.agent/skills/python-ai.md` | pytest (quando existir) | `README.md` |
| `goroute-mobile/` | Flutter (scaffold) | `.agent/skills/flutter.md` | `flutter test` | `README.md`, SPEC-15 |
| `e2e/` | Playwright cross-stack | — | `npm test` em `e2e/` | `e2e/.env.example` |
| `.agent/` | Meta-documentação para IA | personas + testing | — | Este arquivo |

**Layout:** múltiplos repositórios Git independentes (não é npm workspaces). Dados: `docker-compose.yml`; stack completa: `docker-compose.homolog.yml`.

**Fora de escopo habitual:** `signoz/`, `path-goroute/` — só se o usuário pedir.

---

## 3. Guardrails — sempre aplicar

| Arquivo | Quando | Personas |
|---------|--------|----------|
| [`.agent/guardrails/security.md`](.agent/guardrails/security.md) | Toda implementação e revisão | Todas |
| [`.agent/guardrails/quality.md`](.agent/guardrails/quality.md) | Aceitação: corretude, testes, TS strict | Developer + Q.A. |
| [`.agent/guardrails/output-format.md`](.agent/guardrails/output-format.md) | Relatórios de review / QA | Q.A. + revisores |

### Checklist pós-implementação (UI) — Q.A.

- [ ] Regra [`.cursor/rules/frontend-playwright-mcp.mdc`](.cursor/rules/frontend-playwright-mcp.mdc)
- [ ] `npm start` em `http://localhost:4200`
- [ ] Playwright MCP (ou `e2e/` / MCP indisponível declarado)
- [ ] Vitest verde (`goroute-test-providers.ts`)
- [ ] Resumo do que foi validado no browser

### Checklist geral

- [ ] Persona correta + `testing.md`
- [ ] Unitários criados/atualizados
- [ ] `npm test` (ou equivalente) no pacote
- [ ] Sem secrets versionados
- [ ] Inputs validados; erros tratados
- [ ] Diff limitado ao pedido

---

## 4. Skills por tipo de trabalho

### Planejamento → persona **Planner**

| Situação | Ler |
|----------|-----|
| Multi-fase, arquitetura, dual-review Fase A | [`.agent/skills/planning.md`](.agent/skills/planning.md) |
| Parecer UX (council) | [`.agent/skills/ux-advisory.md`](.agent/skills/ux-advisory.md) |
| Parecer Growth (council) | [`.agent/skills/growth-advisory.md`](.agent/skills/growth-advisory.md) |
| Plano JSON | [`.agent/schemas/plan.schema.json`](.agent/schemas/plan.schema.json) |

Saída canônica: [`.plan/`](.plan/README.md) (produto + etapas). Tasks isoladas: `.plan/tasks/<TASK_ID>/plano_v1.md` (+ JSON).

### Implementação → persona **Developer**

| Stack | Skill |
|-------|-------|
| Angular | [`.agent/skills/angular.md`](.agent/skills/angular.md) |
| NestJS BFF / auth | [`.agent/skills/nestjs.md`](.agent/skills/nestjs.md) |
| Auth hexagonal | domínio em `src/domain/`, adapters em `src/infrastructure/` |
| Python | [`.agent/skills/python-ai.md`](.agent/skills/python-ai.md) |

### Segurança (dados + pagamento — obrigatório no Developer)

| Artefato | Uso |
|----------|-----|
| [`.agent/skills/secure-service.md`](.agent/skills/secure-service.md) | Implementar auth, BFF, BOLA, SSRF, LGPD, IA |
| [`.agent/skills/secure-payments.md`](.agent/skills/secure-payments.md) | Stripe webhook, checkout, anti-bypass premium |
| [`.agent/skills/checklists/developer-security-gate.md`](.agent/skills/checklists/developer-security-gate.md) | Gate pré-Q.A. |
| [`.agent/skills/security-check.md`](.agent/skills/security-check.md) | Review / threat modeling |
| `guardrails/security.md` | Regras OWASP gerais |
| Alinhamento SPECs | [`.plan/02-baseline/coverage-and-spec-alignment.md`](.plan/02-baseline/coverage-and-spec-alignment.md) |

### Testes → persona **Q.A.** (+ Developer nos unitários)

| Situação | Ler |
|----------|-----|
| Qualquer feature/edição relevante | [`.agent/testing.md`](.agent/testing.md) |
| UI | Playwright MCP + [`.cursor/rules/frontend-playwright-mcp.mdc`](.cursor/rules/frontend-playwright-mcp.mdc) |
| E2E cross-stack | `e2e/` |

---

## 5. Workflows (alinhados às personas)

| Objetivo | Workflow | Persona líder |
|----------|----------|---------------|
| Planejar + revisão dupla | [`.agent/workflows/dual-review.md`](.agent/workflows/dual-review.md) | Planner (A) → Developer (B) → Q.A. |
| Implementar fase aprovada | [`.agent/workflows/implement-phase.md`](.agent/workflows/implement-phase.md) | Developer → Q.A. |
| Revisão crítica | [`.agent/workflows/code-review.md`](.agent/workflows/code-review.md) | Revisor (usa guardrails; Q.A. nos testes) |
| Validação anti-alucinação | [`.agent/workflows/validate.md`](.agent/workflows/validate.md) | Validador + overlap Q.A. |
| Aliases | `codex-review.md`, `claude-validate.md` | — |

### Fluxo recomendado

```
Pedido do usuário
 │
 ├─ UI
 │     → Developer (angular + Vitest)
 │     → Q.A. (npm start + Playwright MCP / e2e/)
 │
 ├─ Simples (sem UI)
 │     → Developer → Q.A. (unitários)
 │
 └─ Complexo / produto
       → Planner (.plan/ + planning.md)
       → Developer (uma stage de .plan/03-development/)
       → Q.A. (validation da stage)
       → dual-review opcional (tasks em .plan/tasks/)
```

---

## 6. Planejamento estruturado (Planner)

1. Ler [`.plan/README.md`](.plan/README.md), `PROJETO.md`, `.specs/`, `.plan/02-baseline/evidence-anchors.md`.
2. Preferir etapas em `.plan/03-development/` (template anti-alucinação) em vez de planos soltos.
3. Tasks ad-hoc: `.plan/tasks/<TASK_ID>/` + `plan.schema.json` (`repoPaths`, `stack`, `ownerPersona`).
4. Declarar `outOfScope`; não inventar APIs/endpoints/env.
5. Toda etapa com código tem **Validation** para a persona Q.A.

---

## 7. Regras de implementação (Developer)

### Geral

- Reutilizar padrões existentes; comentários só quando necessário.
- Nunca commitar `.env`/tokens; commits/PRs só se o usuário pedir.

### Frontend (`goroute-frontend`)

- Local: `npm start` → `:4200`; proxy auth (`/goroute-auth`) + BFF (`/api`).
- Vitest + helper `goroute-test-providers.ts`.
- UI: Playwright MCP ou `e2e/` — zero Playwright no frontend.

### Admin

- Porta **4201**; Karma headless.

### BFF (`goroute-api`)

- Prefixo `/api`; migrations `npm run migration:run`; smoke `npm run smoke:homolog`.

### Auth

- Porta **3010**; hexagonal; `INTEGRATION_TEST=1 npm run test:e2e`.

### Python

- Mentor **5010** (pytest); NLP **5000**.

---

## 8. Relatórios e revisões

Formato: [`.agent/guardrails/output-format.md`](.agent/guardrails/output-format.md).

- Severidade CRITICAL / MAJOR / MINOR / SUGGESTION
- Evidência com arquivo e linha
- Decisão APPROVED / CHANGES_REQUESTED / BLOCKED

Q.A. pode emitir veredito curto PASS/FAIL/BLOCKED; reviews formais usam o template completo.

Artefatos: `reports/<TASK_ID>/` quando dual-review ativo.

---

## 9. Invocação rápida

```text
Siga AGENTS.md. Persona: Planner.
Objetivo: [descrever]
TASK_ID: [slug]
```

```text
Siga AGENTS.md e .agent/workflows/planner-council.md
Persona: Planner → UX + Growth
Objetivo: [produto/UI/mobile]
STAGE: stage-XX-...
```

```text
Siga AGENTS.md. Persona: Developer.
Implementar: [escopo] — unitários na entrega
```

```text
Siga AGENTS.md. Persona: Q.A.
Validar: [escopo/fase] — testing.md + Playwright MCP se UI
```

```text
Siga AGENTS.md e .agent/workflows/dual-review.md.
TASK_ID: [slug]
Objetivo: [descrever]
```

```text
Siga AGENTS.md e .agent/workflows/implement-phase.md.
TASK_ID: [slug]
PHASE_ID: [phase-1-...]
```

---

## 10. O que não fazer

- Ignorar personas, `testing.md` ou guardrails.
- Marcar plano de produto/UI/mobile como `ready` **sem** council UX + Growth.
- Entregar feature/edição relevante sem testes.
- Entregar UI sem Playwright MCP quando disponível (sem declarar fallback).
- Instalar Playwright no `package.json` do frontend; expandir Cypress.
- Planejar/implementar sem ler o código do pacote.
- Usar `any` sem justificativa; expor secrets.
- Alterar `environment.prod.ts` para trabalho local.
- Misturar identidade com outros produtos (Notification Service, etc.).
- Criar docs não solicitados (README/CHANGELOG extras).

---

## 11. Referência `.agent/` e `.plan/`

```
.plan/                         # planejamento produto + desenvolvimento (Planner)
├── 00-market/ … 04-qa/
└── 03-development/stage-XX-*/plan.md

.agent/
├── persona.md                 # índice das personas
├── personas/ planner | ux-ui | growth-marketing | developer | qa
├── skills/ planning | ux-advisory | growth-advisory | …
├── workflows/ … | planner-council.md
├── testing.md
├── guardrails/ | schemas/ | skills/ | workflows/
```

---

## 12. Prioridade em caso de conflito

1. Pedido explícito do usuário (se não violar segurança).
2. `.agent/guardrails/security.md`
3. `.agent/testing.md` + persona **Q.A.** (gate de testes)
4. Persona ativa (`planner` | `developer` | `qa`)
5. Skill do pacote (ex.: hexagonal no auth)
6. `.agent/guardrails/quality.md`
7. Convenções locais do código existente

---

*Última atualização: personas Planner / Developer / Q.A. alinhadas ao ecossistema GoRoute.*
