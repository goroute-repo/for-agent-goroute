---
name: secure-payments
description: Skill do Developer GoRoute para billing Stripe seguro — webhooks, checkout, entitlements e anti-bypass premium.
---

# Skill: Secure Payments (Stripe × GoRoute)

> Usar com [`secure-service.md`](./secure-service.md) e [SPEC-11](../../.specs/11-billing.spec.md).  
> **Persona:** Developer · **Gate:** Q.A. + checklist pagamentos no security gate.

---

## 1. Princípios (Stripe + indústria)

Fontes: [Stripe webhook signatures](https://docs.stripe.com/webhooks/signature); padrões NestJS 2025–2026 (rawBody, idempotência, state machine).

1. **Webhook é a fonte de verdade** do estado de assinatura — não confiar só no return URL do browser.
2. **Verificar assinatura antes de qualquer lógica** (`stripe.webhooks.constructEvent`).
3. **Raw body obrigatório** — JSON parser muta bytes e quebra a assinatura.
4. **Idempotência** — Stripe reenvia eventos; deduplicar por `event.id`.
5. **Entitlements no servidor** — `PremiumFeatureGuard` / plan limits; cliente não “ativa premium”.
6. **PCI:** não tocar em número de cartão; só Checkout/Elements hospedados pela Stripe.

---

## 2. Controles SPEC-11 (obrigatórios)

| ID | Controle | Implementação |
|----|----------|---------------|
| RS-01 | Webhook Stripe | Signature + raw body; rejeitar 400 se inválido |
| RS-02 | Premium bypass | Sem flags client-side; sync só via Stripe/eventos confiáveis |
| RS-03 | Checkout seguro | Session Stripe server-side; `success/cancel` URLs allowlisted |
| Rate | `/billing/sync` | Max 10/h por user (SPEC-19) |

---

## 3. Bootstrap NestJS (padrão)

```typescript
// Conceito — confirmar padrão já usado em goroute-api antes de duplicar
const app = await NestFactory.create(AppModule, { rawBody: true });
// No controller do webhook: usar req.rawBody + header stripe-signature
```

Ordem mental do handler:

```text
1. Ler rawBody + Stripe-Signature
2. constructEvent(secret whsec_*)
3. Se falhar → 400, log sem payload sensível
4. Dedup event.id (INSERT atômico / tabela processed_events)
5. Enfileirar ou processar transição de estado permitida
6. Responder 200 rápido
```

---

## 4. State machine (assinatura)

- Transições ilegais (ex.: `canceled` → `active` sem evento) → ignorar + log warn (não corromper).
- Tratar `past_due`, `unpaid`, `canceled` separadamente — não só `active`.
- `checkout.session.completed` e `customer.subscription.*` devem convergir no mesmo modelo interno.

---

## 5. Segredos

| Secret | Onde |
|--------|------|
| `STRIPE_SECRET_KEY` | Só servidor BFF |
| `STRIPE_WEBHOOK_SECRET` (`whsec_`) | Só verificação webhook |
| Price IDs | Config servidor; alinhados à página pricing |

- Test keys ≠ live keys; CLI `stripe listen` usa secret diferente do Dashboard.
- Nunca logar secret nem objeto Stripe completo com PII desnecessária.

---

## 6. Testes mínimos (Developer + Q.A.)

- [ ] Assinatura inválida → 400 e **zero** side-effect no DB
- [ ] Mesmo `event.id` duas vezes → um único efeito
- [ ] Usuário free não acessa feature premium (guard)
- [ ] BYOK mentor não depende de `ai_mentor_managed` (SPEC-11 RN-123) — se tocar mentor
- [ ] `npm test` no módulo `billing`

---

## 7. Anti-padrões

- Ativar premium no frontend após redirect
- Confiar em query `?session_id=` sem consultar Stripe/DB
- Parsear JSON antes da verificação
- Desabilitar verificação “só em dev” sem guard de `NODE_ENV`
- Expor `STRIPE_SECRET_KEY` no Angular `environment*.ts`

---

## 8. Relação com etapas `.plan`

- Endurecimento Stripe/webhook: cobrir em **stage-01** e validar de novo no **stage-09**.
- UI settings/billing: **stage-05** — só consome APIs já seguras.
