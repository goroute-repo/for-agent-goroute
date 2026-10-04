# Stage 12 — Landing pública, pricing e platform-admin

| Campo | Valor |
|-------|-------|
| **ID** | `stage-12-landing-admin` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-01` (pode paralelizar após 01 com 03/04) |
| **Stack** | `goroute-frontend`, `goroute-platform-admin` |
| **SPECs** | 20, 11 (pricing UI), 12 (admin catálogo) |

## Meta

Garantir site público coerente (landing/about/pricing) e admin de catálogo seguro — sem inventar CMS.

## Ler antes

1. `.specs/20-landing-public.spec.md`
2. `.specs/11-billing.spec.md` + matriz de planos
3. `.agent/skills/secure-service.md` + `secure-payments.md` (pricing não vaza secrets)
4. Rotas `/`, `/about`, `/pricing`; app `goroute-platform-admin`

## In scope

- [ ] CA SPEC-20 ainda abertos (acessibilidade mínima, grafo público, CTAs)
- [ ] Pricing alinhado a `billing` real (sem preços divergentes)
- [ ] Admin: CRUD catálogo só role root; Karma tests
- [ ] CSP/XSS na landing; sem tokens em bundle
- [ ] MCP smoke landing + pricing

## Out of scope

- Blog/CMS
- Redesign completo brand (salvo bugs SPEC)
- Mobile

## Critérios de aceite

1. Rotas públicas carregam sem auth.
2. Pricing reflete planos da API.
3. Admin não acessível a professional/company comuns.

## Validation (Q.A.)

```bash
cd goroute-frontend && npm test
cd goroute-platform-admin && npm test -- --watch=false --browsers=ChromeHeadless
```

MCP: `/`, `/about`, `/pricing`.

## Handoff Q.A.

Atualizar INDEX.
