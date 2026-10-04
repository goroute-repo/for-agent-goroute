---
name: planning
description: >-
  Planejamento de soluções GoRoute — decisões arquiteturais, opções, riscos e
  fases executáveis (persona Planner / Principal Solutions Architect).
---

# Skill: Planejamento (Solutions Architecture)

> Persona: [`personas/planner.md`](../personas/planner.md) · Fase A: [`dual-review.md`](../workflows/dual-review.md)

**Não implementa código.** Entrega planos com **decisão**, não listas de tarefas.

---

## 1. Barra de qualidade (gate)

Um plano **passa** só se um arquiteto sênior assinar mentalmente:

| Gate | Critério |
|------|----------|
| G1 Evidência | Estado atual citado com paths/endpoints reais |
| G2 Opções | ≥ 2 alternativas com trade-offs; 1 decisão |
| G3 Risco | Riscos top com mitigação na fase certa |
| G4 Fatia | Fases provam valor; não são “organizar pastas” |
| G5 Validação | Comandos reais por fase; UI inclui Playwright MCP quando aplicável |
| G6 Council | UX/Growth profundos ou N/A com valor (ver skills advisory) |

**Falha automática:** plano = inventário de arquivos + “usar tokens” + “adicionar guards” sem problema/opções/risco.

---

## 2. Onde salvar

| Tipo | Caminho |
|------|---------|
| Etapa de desenvolvimento | `.plan/03-development/stage-XX-*/plan.md` |
| Notas / ADR / council | `notes.md` |
| Advisories | `advisory/` |
| Task isolada | `.plan/tasks/<TASK_ID>/plano_vN.md` |
| Reports dual-review | `reports/<TASK_ID>/` |

---

## 3. Estrutura canônica do `plan.md`

```markdown
# Stage XX — <nome>

| Campo | Valor |
| Status | draft → ready |
| Depende de | ... |
| Pacotes | ... |

## 1. Meta (problema → resultado)
## 2. Ler antes (evidência)
## 3. Estado atual (o que o repo faz hoje)
## 4. Opções e decisão (ADR curto)
## 5. In / Out of scope
## 6. NFRs e riscos
## 7. Fases (máx. 5–8)
## 8. Validation global (Q.A.)
## 9. Anti-alucinação
## 10. Handoff
```

### 4. Opções e decisão (mínimo)

```markdown
| Opção | Prós | Contras | Risco |
| A — ... | | | |
| B — ... | | | |
**Decisão:** B porque ...
**Consequências:** ...
```

### Conteúdo mínimo por fase

| Campo | Obrigatório |
|-------|-------------|
| Goal (resultado da fatia) | Sim |
| dependsOn | Se houver |
| files create/modify | Sim |
| acceptanceCriteria (≥ 1 verificável) | Sim |
| UI: aceite de usabilidade | Se fase UI |
| validation (comandos) | Se houver código |
| risks | Recomendado |
| ownerPersona | Sim |

---

## 4. Processo

1. **Clarificar resultado** — o que muda para usuário/operador/negócio.
2. **Ler evidência** — `PROJETO.md`, `.specs/`, código, envs; preencher `repoPaths`.
3. **Mapear fronteiras** — auth, BFF, UI, dados, jobs; o que **não** mexer.
4. **Council** (produto/UI/mobile/lançamento):
   - [`planner-council.md`](../workflows/planner-council.md)
   - Skills: [`ux-advisory.md`](./ux-advisory.md), [`growth-advisory.md`](./growth-advisory.md)
   - Reconciliar em `notes.md` (aceitar/rejeitar com motivo)
5. **Decidir** — opções + ADR; só então fatiar.
6. **Fatiar** — vertical slice; máx. 5–8 fases; testes em cada fase com código.
7. **Auth GoRoute** — cookie + `goroute-service-auth` + BFF; sem JWT inventado.
8. **Handoff** — `ready` → Developer → Q.A. executa validation.

---

## 5. Princípios de decomposição

- Fatia que **desbloqueia risco** primeiro (auth/RBAC/contrato antes de polish).
- Preferir ponta a ponta quando o aceite for de usuário.
- Não criar fase só para “escrever docs” salvo Fase 0 de gate/security pedida.
- Hipóteses em `notes.md`; fatos no plano.

---

## 6. Exemplos de validation

```text
cd goroute-frontend && npm test
Playwright MCP: fluxo [nome] em http://localhost:4200
cd goroute-platform-admin && npm test -- --watch=false --browsers=ChromeHeadless
cd goroute-api && npm test
```

---

## 7. Quando NÃO usar plano formal

Bug trivial / 1–2 arquivos → Developer + Q.A. mínimo. Guardrails e `testing.md` continuam.

---

## 8. Anti-alucinação

- Path existe **ou** marcado “a criar”.
- Endpoint: confirmar em `goroute-api` / auth.
- Env: só `.env.example` / `environment*.ts` / config do pacote.

---

*Planner decide e fatia risco; Developer executa; Q.A. prova.*
