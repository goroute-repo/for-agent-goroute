# Stage 14 — Mobile (paridade mínima)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-14-mobile-parity` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | stages web P0: **01–05** `done` (recomendado também 04) |
| **Stack** | `goroute-mobile`, auth (`mobileAppKey`) |
| **SPECs** | 15 |

## Meta

Evoluir o scaffold Flutter para paridade mínima com APIs já estáveis (login/sessão/mapper), sem reinventar o BFF.

## Ler antes

1. `.specs/15-mobile-app.spec.md`
2. Auth: `mobileAppKey` empresa / application pairing
3. `.agent/skills/flutter.md` + `secure-service.md` (storage seguro de sessão)
4. `.plan/02-baseline/coverage-and-spec-alignment.md`

## In scope

- [x] Fluxos P0: login profissional + roadmap read (push/deep link fora)
- [x] Sessão via `sessionId` + `x-session-id` / mapper (não JWT)
- [x] Secure storage no device; sem logar secrets
- [x] `flutter test` (4 PASS)
- [x] Security gate

## Out of scope

- Feature parity 100% com web
- Push completo se infra não existir (documentar)
- Pagamentos in-app nativos (usar web/Stripe Checkout)

## Critérios de aceite

1. CA P0 do SPEC-15 desta fatia verdes.
2. App fala com auth/BFF de homolog/local via config — sem keys no binário além do necessário público.
3. Empresa/professional respeitam mesmos guards de API.

## Validation (Q.A.)

```bash
cd goroute-mobile && flutter test
```

## Anti-alucinação

- Se SPEC-15 estiver majoritariamente `planejado`, implementar só a fatia P0 listada no SPEC — não “app completo” nesta stage.

## Handoff Q.A.

Atualizar INDEX.
