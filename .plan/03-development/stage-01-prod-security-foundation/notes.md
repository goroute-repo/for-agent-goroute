# Notes — stage-01-prod-security-foundation

**Status Developer:** implementação concluída · gate PASS · handoff Q.A.  
**Data:** 2026-07-17

## O que foi feito

1. **TypeORM synchronize opt-in**
   - Auth: `typeorm-options.ts` — default `false`; throw em production se `true`.
   - BFF: `env.schema.ts` — default `TYPEORM_SYNCHRONIZE=false`.
   - Auth `.env.example` documenta sync off + migrations.

2. **SEC-01 OAuth + MFA**
   - Login social já usava `AuthenticatedLoginOutcomeUseCase`.
   - Regressão: `oauth-callback.helper.spec.ts`.

3. **Stripe RS-01**
   - Já havia `rawBody` + `constructEvent`.
   - Idempotência atômica (`INSERT` + unique); release do claim se handler falhar.
   - Testes: `stripe-webhook.controller.spec.ts` + `billing.service.spec.ts`.

4. **Premium RS-02** — confirmado via `PremiumFeatureGuard` / testes existentes.

## Validação executada

```text
goroute-service-auth: 6 suites, 14 tests PASS
goroute-api: 34 suites, 106 tests PASS
```

## Gate

Ver `developer-security-gate.md` (PASS).

## Próximo

Persona Q.A.: Validation do `plan.md` → INDEX `done` ou `blocked`.
