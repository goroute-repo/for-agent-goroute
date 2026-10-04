# Stage 12 — notas Developer (2026-07-17)

## Entregue

| Item | Detalhe |
|------|---------|
| CTAs home | `#convite` → `/login/register` e `/login/empresa` |
| Menu público | link Register + `rel="noopener noreferrer"` |
| SEO | `meta description` em `index.html` |
| Pricing | Já via `GET /billing/plans`; Vitest cobre render |
| i18n | Vitest persistência `goroute.lang` |
| Admin catalog | `adminTierGuard('ADMIN')` em data/career-catalog + skills |
| Karma | `admin-roles.spec` + `admin-tier.guard.spec` |

## Testes

- Frontend Vitest **36 PASS**
- Admin Karma **6 PASS**

## Residual (não bloqueia Q.A.)

- CA-162 Lighthouse ≥70 mobile (P1) — sem artefato nesta fase
- CRUD “root-only” do plano: alinhado a **ADMIN+** (BFF `@RequireAdminRoles('ADMIN')` + `root`→SUPER_ADMIN)

## Handoff

INDEX → `qa`
