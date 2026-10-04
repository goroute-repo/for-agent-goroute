---
name: testing
description: Instruções obrigatórias para criação, execução e manutenção de testes no monorepo GoRoute. Persona dona — Q.A.
---

# Testing — Criação e Execução de Testes (GoRoute)

**Regra absoluta:** toda **feature nova** e toda **edição relevante** deve ser acompanhada de testes adequados. Entrega sem testes atualizados ou criados é **incompleta**.

| Papel | Responsabilidade |
|-------|------------------|
| **Planner** | Incluir `validation` testável em cada fase do plano |
| **Developer** | Criar/atualizar unitários na mesma entrega |
| **Q.A.** | Gate DoD — executar, evidenciar, PASS/FAIL/BLOCKED |

Leia junto: [`personas/qa.md`](personas/qa.md), [`guardrails/quality.md`](guardrails/quality.md).

---

## 1. Pirâmide de testes por pacote

| Camada | `goroute-frontend` | `goroute-api` / `goroute-service-auth` | Python / mobile |
|--------|--------------------|----------------------------------------|-----------------|
| **Unitário** | Vitest (`*.spec.ts`) | Jest (`*.spec.ts`) | pytest / `flutter test` |
| **UI / integração** | **Playwright MCP** (Cursor) ou suite `e2e/` | Jest e2e auth (`INTEGRATION_TEST=1`) | conforme pacote |
| **Cross-stack** | — | — | `e2e/` (Playwright) |

> **Importante:** Playwright **não** é dependência do `package.json` do frontend. Validação UI com IA usa o **Playwright MCP** (`~/.cursor/mcp.json`). A pasta `e2e/` na raiz do monorepo é o lugar do Playwright Test versionado.

### Frontend — Playwright MCP

O agente deve:

- abrir o browser na app em execução;
- interagir com a UI;
- usar accessibility snapshots;
- validar fluxos e refletir bugs em `*.spec.ts` (Vitest).

**Não usar** no frontend Angular:

- `@playwright/test` como dep do `goroute-frontend`;
- Playwright Test Agents no pacote Angular;
- expandir Cypress.

**App alvo local:** `http://localhost:4200` com `npm start` em `goroute-frontend/` (proxy `proxy.conf.json` → auth + BFF).

---

## 2. Quando testar (obrigatório)

| Tipo de mudança | Testes exigidos |
|-----------------|-----------------|
| Nova feature (UI) | Vitest + **Playwright MCP** (ou `e2e/`) |
| Update de feature (UI) | Vitest atualizado + MCP nos cenários alterados |
| Nova feature (API/auth) | Jest no pacote afetado |
| Bug fix | Regressão unitária + MCP se UI |
| Refactor | Suite unitária verde; MCP se UI crítica |
| Mudança cosmética (só CSS) | MCP opcional; unitário se lógica mudou |

**Edição relevante** = lógica, contrato, fluxo de usuário, integração externa ou regra de negócio.

---

## 3. Frontend — Vitest (`goroute-frontend`)

### Localização

```
src/app/<feature>/<component>.ts
src/app/<feature>/<component>.spec.ts
```

Helper compartilhado: `src/app/core/testing/goroute-test-providers.ts`.

### Padrões

- Arrange – Act – Assert.
- Mock HTTP / Router / auth conforme padrões do repo.
- Nomes descritivos em português ou inglês — seguir o arquivo vizinho.
- Bugs do Playwright MCP → refletir no `*.spec.ts`.

### Comandos

```bash
cd goroute-frontend
npm test
npm start    # http://localhost:4200
```

### Admin (`goroute-platform-admin`)

```bash
cd goroute-platform-admin
npm test -- --watch=false --browsers=ChromeHeadless
# serve: porta 4201
```

---

## 4. Frontend UI — Playwright MCP

### Setup (máquina do desenvolvedor)

`C:\Users\<usuario>\.cursor\mcp.json`:

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    }
  }
}
```

```bash
npx playwright install
```

Regra Cursor: [`.cursor/rules/frontend-playwright-mcp.mdc`](../.cursor/rules/frontend-playwright-mcp.mdc).

### Fluxo obrigatório (feature/update UI) — persona Q.A. + Developer

```
1. Developer implementa em goroute-frontend/
2. npm test (Vitest)
3. Se :4200 não responde → npm start (background)
4. Q.A. / Playwright MCP: happy path + erro/validação
5. Corrigir; reforçar *.spec.ts
6. Resumir evidências do browser
```

**Zero** dep Playwright no `package.json` / `angular.json` do frontend.

### Cenários mínimos MCP

- [ ] Happy path
- [ ] Estado vazio
- [ ] Erro de API (feedback)
- [ ] Validação de formulário
- [ ] Auth / rota protegida (se aplicável)

### Boas práticas

- Preferir **local**, não homolog/prod.
- Sem credenciais reais no browser de teste.
- MCP complementa unitários — não substitui.

---

## 5. Backend — Jest

### Pacotes

```bash
cd goroute-api && npm test
cd goroute-service-auth && npm test
# integração auth (Postgres + Redis):
cd goroute-service-auth && INTEGRATION_TEST=1 npm run test:e2e
```

### Foco por camada (auth hexagonal)

| Camada | Foco |
|--------|------|
| `domain/` | Entidades, validações, erros |
| use cases | Regras com ports mockados |
| `infrastructure/` | Adapters com SDK/DB mockados |
| controllers | Status HTTP, guards, delegação |

BFF (`goroute-api`): services, controllers, DTOs; smoke homolog quando stack disponível (`npm run smoke:homolog`).

---

## 6. Python e mobile

```bash
cd goroute-mentor-agent && pytest
cd goroute-nlp && pytest   # quando existir
cd goroute-mobile && flutter test
```

---

## 7. E2E cross-stack (`e2e/`)

```bash
cd e2e && npm test
```

Usar para health, login e páginas públicas quando o fluxo atravessa múltiplos serviços. Ver `e2e/.env.example`.

---

## 8. Fluxo do agente (Developer → Q.A.)

```
1. Ler código e specs relacionados
2. Implementar (Developer)
3. Unitários no pacote
4. Se UI: npm start + Playwright MCP (Q.A.)
5. Corrigir; reforçar specs
6. npm test / pytest novamente
7. Documentar evidências (resumo ou relatório)
```

### Checklist de entrega

- [ ] Unitários criados/atualizados
- [ ] Playwright MCP ou `e2e/` para UI (ou MCP indisponível declarado)
- [ ] Bugs corrigidos ou registrados
- [ ] Comandos de teste executados com sucesso
- [ ] Lint sem erros novos no escopo

---

## 9. Dados e segurança em testes

- Nunca commitar credenciais, tokens reais ou `.env` com secrets.
- Fixtures fictícias.
- MCP em `localhost`; não logar em homolog/prod com dados reais.

---

## 10. Integração com planejamento

No `plan.schema.json`, cada fase com código deve ter `validation`, por exemplo:

```json
"validation": [
  "cd goroute-frontend && npm test",
  "Playwright MCP: validar fluxo [nome] em http://localhost:4200",
  "cd goroute-api && npm test"
]
```

Revisões (`code-review.md`, `validate.md`) confirmam unitários e, para UI, exercício via MCP/`e2e/`.

---

## 11. Referência rápida

| Pacote | Unitário | UI / integração |
|--------|----------|-----------------|
| `goroute-frontend` | `npm test` (Vitest) | Playwright MCP + `npm start` |
| `goroute-platform-admin` | Karma headless | `:4201` |
| `goroute-api` | `npm test` (Jest) | smoke homolog |
| `goroute-service-auth` | Jest | `test:e2e` com `INTEGRATION_TEST=1` |
| `goroute-mentor-agent` | pytest | — |
| `goroute-nlp` | pytest | — |
| `goroute-mobile` | `flutter test` | — |
| `e2e/` | — | Playwright |

---

*Testes não são opcionais — fazem parte da Definition of Done. A persona Q.A. fecha o gate.*
