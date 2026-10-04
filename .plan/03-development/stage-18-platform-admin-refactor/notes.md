# Stage 18 — Notes (Planner reconcile + Developer)

## Council

- UX: [`advisory/ux-ui.md`](./advisory/ux-ui.md) — **aceito** (estados, RBAC URL, tokens dark ops)
- Growth: [`advisory/growth-marketing.md`](./advisory/growth-marketing.md) — **N/A** (admin interno)

## Aceito

- Refator UX/ops + RBAC + testes; manter Angular 19/Karma por padrão
- Primary marca `#0F6E56` no admin
- Shell lateral permanece
- `public/config.json` **carregado** no bootstrap (`loadRuntimeConfig`) com whitelist de campos

## Rejeitado / adiado

- Big-bang Angular 21 / Vitest (só Fase 6 opcional)
- CMS / marketing / mobile admin
- Novos módulos sem API admin existente

## ADR — permanece Angular 19 / Karma (Fase 6)

**Decisão:** não migrar o `goroute-platform-admin` para Angular 21 + Vitest nesta stage.

**Contexto:** o frontend produto já está em Angular 21/Vitest; o admin era 19/Karma com suite fina. Migrar toolchain junto com a refator UX aumentava risco sem benefício operacional imediato.

**Consequências:** testes continuam via Karma headless; alinhamento de toolchain fica para stage futura quando houver capacidade.

## Dead code

- `AdminApiService.getMetrics()` — **removido** (dashboard usa `getDashboardMetrics`)
- `menuPinned` — já ausente no código

## Matriz papel × rota (entregue)

| Rota | READ_ONLY | SUPPORT | ADMIN | SUPER_ADMIN |
|------|-----------|---------|-------|-------------|
| `/dashboard` | sim | sim | sim | sim |
| `/audit` | sim | sim | sim | sim |
| `/users`, `/companies` | não | sim | sim | sim |
| `/data/*` | não | não | sim | sim |

Mutação user detail: UI `canMutate()` = ADMIN+.

## Entrega Developer

Fases 0–5 implementadas; Fase 6 ADR acima. Pronto para Q.A. com `npm test` + smoke `:4201`.
