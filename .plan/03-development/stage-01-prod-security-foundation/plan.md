# Stage 01 — Fundação segurança / produção (P0)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-01-prod-security-foundation` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | — |
| **Stack** | `goroute-service-auth`, `goroute-api` |
| **SPECs** | 01, 11 (RS Stripe), 19 |

## Meta

Eliminar bloqueadores P0 de segurança e schema (migrations, MFA pós-OAuth, synchronize) e garantir **billing Stripe** com webhook verificável e anti-bypass premium (dados + pagamento).

## Ler antes (obrigatório)

1. `.specs/01-auth-identity.spec.md`
2. `.specs/11-billing.spec.md` (RS-01…03)
3. `.specs/19-security.spec.md`
4. `.agent/skills/secure-service.md`
5. `.agent/skills/secure-payments.md`
6. `.plan/02-baseline/evidence-anchors.md`
7. Código: `synchronize`, OAuth/`issueSession`, módulo `billing` / stripe webhook

## In scope

- [x] Remover ou desativar `synchronize: true` onde existir; migrations TypeORM auth + BFF
- [x] Step-up MFA após OAuth quando MFA ativo (SEC-01) — confirmado + regressão
- [x] Stripe: raw body + `constructEvent` + rejeição 400; idempotência por `event.id` (RS-01)
- [x] Premium só via estado server-side (RS-02); checkout sem secrets no frontend (RS-03)
- [x] Headers helmet/CSP — só gaps comprovados (já OK em `main.ts`)
- [x] Testes unitários/regressão + preencher `developer-security-gate.md`

## Out of scope

- Redis Sentinel / multi-região HA completo (stage-09)
- Verificação de e-mail end-to-end se SMTP indisponível local (documentar gap)
- Features de produto (roadmap, jobs)
- UI pricing/settings billing (stage-05 / 12)
- Mudanças de UI além do necessário para MFA challenge OAuth

## Entregáveis

| Ação | Path (confirmar no repo; marcar A CRIAR se novo) |
|------|--------------------------------------------------|
| MODIFICAR | Config TypeORM auth + api |
| MODIFICAR / A CRIAR | Pasta `migrations/` nos pacotes afetados |
| MODIFICAR | Fluxo OAuth → sessão / MFA challenge |
| MODIFICAR / A CRIAR | `*.spec.ts` de regressão |

## Critérios de aceite

1. App sobe **sem** `synchronize: true` como estratégia de schema em prod-like.
2. OAuth + MFA ativo → sem sessão completa sem challenge (SPEC-01 / SEC-01).
3. Webhook Stripe com assinatura inválida → 400 e zero side-effect; replay idempotente.
4. Feature premium não ativável só pelo cliente.
5. `npm test` verde auth + api (billing) no escopo; security gate PASS.

## Validation (Q.A.)

```bash
cd goroute-service-auth && npm test
cd goroute-api && npm test
```

- Revisar diff: ausência de `synchronize: true` ativo em prod-like config.
- Cenário manual/MCP se houver UI de MFA pós-OAuth: documentar passos.

## Riscos / anti-alucinação

- **Não inventar** provider OAuth que não existe no código.
- **Não** “consertar” MFA apagando a feature.
- Se migration strategy do projeto já existir, **estender** — não criar segunda ferramenta paralela.

## Handoff Q.A.

Veredito PASS/FAIL em formato `.agent/guardrails/output-format.md` §11.  
Atualizar `../INDEX.md` status → `done` ou `blocked`.
