# Stage 02 — UX auth + hardening de regressão

| Campo | Valor |
|-------|-------|
| **ID** | `stage-02-auth-ux-hardening` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-01` |
| **Stack** | `goroute-service-auth`, `goroute-frontend` |

## Meta

Garantir jornada completa de recuperação de senha e configuração MFA no produto web, sem regressões de sessão/401.

## Ler antes

1. `.specs/01-auth-identity.spec.md`
2. Rotas frontend: buscar `forgot-password`, `reset-password`, `mfa`, `settings`
3. Interceptors 401 / oauth-return no frontend
4. Stage-01 notas (o que já foi fechado)

## In scope

- [ ] UI `/login/forgot-password` e `/login/reset-password` **se ainda incompletas** (confirmar no código antes)
- [ ] Configuração MFA acessível na área logada (settings/segurança) alinhada aos endpoints auth existentes
- [ ] Tratamento 401/sessão expirada sem loop infinito de redirect
- [ ] Vitest nos componentes/serviços alterados
- [ ] Playwright MCP nos fluxos forgot/reset/MFA settings

## Out of scope

- MFA empresa completo se SPEC marcar como fase posterior — só se endpoints já existirem
- Redesign visual da landing
- Mobile app

## Entregáveis

| Ação | Path |
|------|------|
| MODIFICAR / A CRIAR | Rotas/components auth no `goroute-frontend` |
| MODIFICAR | Serviços auth API client |
| A CRIAR/MODIFICAR | `*.spec.ts` |

## Critérios de aceite

1. Usuário consegue solicitar reset e concluir reset em ambiente local (SMTP mock/dev aceitável se documentado).
2. Usuário logado consegue ver estado MFA e completar setup/disable conforme API real.
3. Vitest + MCP (ou BLOCKED MCP declarado) no fluxo alterado.

## Validation (Q.A.)

```bash
cd goroute-frontend && npm test
cd goroute-frontend && npm start   # :4200
```

Playwright MCP: forgot → reset; settings MFA (happy path + erro de código inválido).

## Riscos / anti-alucinação

- Chamar apenas endpoints que existem no auth service ( Grep controllers ).
- Não criar JWT store no frontend.

## Handoff Q.A.

Atualizar INDEX.
