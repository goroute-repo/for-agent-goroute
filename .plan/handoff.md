# Handoff — Planner → Developer → Q.A.

## Estado após esta rodada de planejamento (2026-07-17)

| Entrega Planner | Path |
|-----------------|------|
| Pesquisa de mercado | `.plan/00-market/competitive-analysis.md` |
| Visão de produto | `.plan/01-product/vision-and-positioning.md` |
| Baseline | `.plan/02-baseline/*` |
| Etapas de desenvolvimento | `.plan/03-development/` (01 `ready`) |
| Estratégia Q.A. | `.plan/04-qa/test-strategy.md` |

**Pastas removidas:** `projeto/`, `planos/` (conteúdo útil absorvido).

**Mantidos como fonte de verdade de detalhe:** `PROJETO.md`, `.specs/`.

---

## Capacitação segurança (2026-07-17)

Developer deve usar antes de codar auth/billing/PII:

- `.agent/skills/secure-service.md`
- `.agent/skills/secure-payments.md`
- checklist `.agent/skills/checklists/developer-security-gate.md`

Cobertura SPECs: `.plan/02-baseline/coverage-and-spec-alignment.md` (stages 01–14).

## Próxima ação recomendada

1. **Developer:** executar `stage-01-prod-security-foundation` (inclui Stripe RS).
2. Após 01: paralelizar 03∥04∥12; 13 após 05; 08 só depois de 13 se for IA.
3. **Q.A.:** Validation da etapa + security gate.

### Prompt Developer

```text
Siga AGENTS.md. Persona: Developer.
Execute somente .plan/03-development/stage-01-prod-security-foundation/plan.md
Leia secure-service.md, secure-payments.md, evidence-anchors e SPECs citados.
Preencha developer-security-gate.md antes do handoff Q.A.
```

### Prompt Q.A.

```text
Siga AGENTS.md. Persona: Q.A.
Valide stage-01 conforme Validation do plan.md e .plan/04-qa/test-strategy.md
```

---

## Atualização de status

Quem concluir uma etapa atualiza a linha correspondente em `.plan/03-development/INDEX.md`.
