# Workflow: Planner Council (UX + Growth)

## Objetivo

Antes de marcar um plano de **produto / UI / mobile / lançamento** como `ready`, o **Planner (Solutions Architect)** obriga pareceres de **especialista** — não templates rasos.

1. **UX/UI** — [`personas/ux-ui.md`](../personas/ux-ui.md) + [`skills/ux-advisory.md`](../skills/ux-advisory.md)
2. **Growth** — [`personas/growth-marketing.md`](../personas/growth-marketing.md) + [`skills/growth-advisory.md`](../skills/growth-advisory.md)

Isso evita planos só de engenharia e evita advisories de “pleno inicial” (cores + 3 títulos ASO).

---

## Quando é obrigatório

| Pedido / etapa | Council |
|----------------|---------|
| App mobile, redesign, shell/nav, onboarding | **Obrigatório** UX + Growth |
| Feature UI com impacto em jornada | **Obrigatório** UX; Growth se landing/store/aquisição **ou** modo interno se admin |
| Admin / ferramenta interna | UX obrigatório; Growth em **modo N/A interno** (skill Growth §4) |
| Só API/auth/infra sem UI | **Não** (Planner puro) |
| Bug / 1–2 arquivos | **Não** |

---

## Sequência

```text
Planner: draft com problema, evidência, opções (skills/planning.md G1–G3)
    → UX: advisory/ux-ui.md  —— gate U1–U6 (rejeitar se genérico)
    → Growth: advisory/growth-marketing.md —— gate G1–G6 (ou modo interno)
Planner: reconciliar em notes.md
    - aceitar / rejeitar cada recomendação com motivo
    - incorporar só P0/P1 (e Growth in-scope) nas fases
    → plano ready → Developer
```

---

## Gate do council (Planner rejeita parecer)

O Planner **deve** devolver o advisory se:

| Sinal de parecer júnior | Ação |
|-------------------------|------|
| UX sem paths/rotas reais | Pedir inventário |
| UX = só paleta + “Nielsen” | Pedir atritos P0–P2 + fluxo before/after |
| Growth = TikTok/IG + 3 títulos sem ICP | Pedir formato skill completo |
| Growth N/A em 3 linhas | Pedir modo interno completo |
| Contradiz evidência do repo | Corrigir com path |

---

## Regras de reconciliação

1. **UX vs Growth:** UX manda em fluxo interno e hierarquia; Growth em store/ads/aquisição. Trade-off em `notes.md`.
2. **Cores:** usabilidade no app (UX) ≠ CTA de anúncio (Growth).
3. **Não expandir escopo** porque Growth pediu canal — vai para `outOfScope` + backlog.
4. Cada fase de UI: ≥ 1 aceite de usabilidade **verificável** (vindo do parecer, não inventado depois).
5. Planner documenta ADR se rejeitar opção arquitetural do próprio draft.

---

## Artefatos

```text
.plan/03-development/stage-XX-*/
  plan.md          # inclui opções/decisão (planning.md)
  notes.md         # ADR + aceito/rejeitado do council
  advisory/
    ux-ui.md
    growth-marketing.md
```

---

## Definition of Ready (produto/UI)

- [ ] Plano passa gates G1–G6 de `skills/planning.md`
- [ ] `advisory/ux-ui.md` passa U1–U6
- [ ] `advisory/growth-marketing.md` passa G1–G6 **ou** modo interno completo
- [ ] `notes.md` lista aceito / rejeitado / trade-offs
- [ ] Fases refletem P0 UX (e Growth in-scope)

---

## Exemplo de prompt

```text
Siga AGENTS.md + .agent/workflows/planner-council.md
Persona: Planner (Solutions Architect) → UX + Growth com skills advisory
Objetivo: […]
STAGE: stage-XX-…
Rejeite parecer genérico; exija evidência do repo.
```
