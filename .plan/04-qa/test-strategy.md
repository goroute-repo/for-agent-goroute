# Estratégia de teste (transversal)

**Persona dona:** Q.A.  
**Canônico:** `.agent/testing.md` + `.agent/personas/qa.md`

---

## 1. Por etapa

| Stage | Unitário mínimo | UI / E2E | Security gate |
|-------|-----------------|----------|---------------|
| 01 | Jest auth + api + billing webhook | — | **Obrigatório** |
| 02 | Vitest auth UX | MCP forgot/reset/MFA | Sim |
| 03 | Jest journey/progress + Vitest | MCP history/progress | Sim (PII) |
| 04 | Jest match/apply/offers + Vitest | MCP empresa + profissional | **Obrigatório** (BOLA) |
| 05 | Vitest settings + Jest | MCP workspaces | **Obrigatório** |
| 06 | Jest replan + Vitest | MCP goal | Sim |
| 07 | Jest B2B idempotency | curl | **Obrigatório** |
| 08 | Conforme A/B/C | MCP | Sim |
| 09 | Todos críticos | `e2e/` + MCP smoke | Checklist SPEC-19 |
| 10 | Jest weekly/catalog/storage | MCP smoke | Sim |
| 11 | Jest NLP client + pytest | — | **Obrigatório** |
| 12 | Vitest + Karma admin | MCP landing/pricing | Sim |
| 13 | Jest + pytest mentor | MCP ai-settings | **Obrigatório** |
| 14 | `flutter test` | — | **Obrigatório** |

---

## 2. Dados de teste

- Fixtures fictícias; nunca credenciais reais de prod.
- Preferir usuários seed locais documentados em `e2e/.env.example`.

---

## 3. Definition of Done por etapa

Igual `plan.md` Validation + veredito §11 de `output-format.md`.  
Sem PASS do Q.A., etapa **não** vai para `done` no INDEX.
