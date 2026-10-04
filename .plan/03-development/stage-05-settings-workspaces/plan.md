# Stage 05 — Settings e workspaces

| Campo | Valor |
|-------|-------|
| **ID** | `stage-05-settings-workspaces` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-02` (auth UX); `stage-04` recomendado para abas jobs/storage |
| **Stack** | `goroute-api`, `goroute-frontend` |
| **SPECs** | 09, 10, 11 (UI), 19 (LGPD) |

## Meta

Completar configurações de conta profissional e empresa (perfil, experiência/cursos, billing UI, armazenamento, isolamento de workspace, direitos LGPD básicos).

## Ler antes

1. `.specs/10-settings-profile.spec.md`
2. `.specs/09-workspaces.spec.md`
3. `.specs/11-billing.spec.md`
4. `.agent/skills/secure-service.md` (+ `secure-payments.md` se tocar billing UI)
5. Código atual de settings/subdock (pode já existir parcial — não recriar)

## In scope

- [ ] Rotas settings profissional e empresa alinhadas ao SPEC (abas reais, não placeholders)
- [ ] Edição de perfil / skills / goal (read-write conforme API existente ou mínima necessária)
- [ ] Exportação/exclusão de conta (SPEC-19 LGPD / SPEC-10) — implementar se RF P0; senão task defer explícita
- [ ] Empresa: armazenamento/cotas se APIs existirem; senão só UI read-only + gap
- [ ] ai-settings UI pode preparar stage-13 (sem persistir key em claro)
- [ ] Vitest + MCP + security gate

## Out of scope

- LinkedIn import se não houver backend
- Redesign completo de billing (usar Stripe já existente)
- Mobile settings

## Critérios de aceite

1. Cada role só acessa seu workspace (guards + UI).
2. Alterações de perfil refletem em `/api/me` ou endpoints canônicos.
3. Nenhuma aba “morta” sem label de “em breve” explícito — preferir esconder a implementar mentira.

## Validation (Q.A.)

```bash
cd goroute-frontend && npm test
cd goroute-api && npm test
```

MCP: navegar settings profissional e empresa; tentar cross-access (deve falhar).

## Anti-alucinação

- Grep rotas settings antes de criar duplicatas.
- Não inventar campos de perfil fora do schema onboarding/entities.

## Handoff Q.A.

Atualizar INDEX.
