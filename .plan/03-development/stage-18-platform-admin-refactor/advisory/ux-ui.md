# Parecer UX/UI — Refatoração goroute-platform-admin

**Data:** 2026-07-18  
**Persona:** UX/UI  
**Draft:** stage-18-platform-admin-refactor

## Diagnóstico de Atrito

1. **Erros silenciosos** — várias telas (ex.: dashboard) só desligam `loading` no `error`; o operador não sabe se falhou rede, sessão ou permissão.
2. **Densidade operacional fraca** — tabelas e filtros existem, mas faltam empty states claros, feedback de ação e hierarquia visual consistente (fonte system-ui, tokens ad hoc).
3. **RBAC na URL incompleto** — nav esconde Usuários/Empresas para tiers baixos, mas rotas podem não repetir o mesmo `adminTierGuard` → risco de tela quebrada ou dados indevidos.

## Estrutura de Navegação

Manter **shell lateral + main** (já adequado a ferramenta interna). Melhorias:

- Agrupar: **Operação** (Dashboard, Usuários, Empresas) · **Dados** (Catálogos) · **Compliance** (Auditoria).
- Sempre mostrar papel do operador + atalho “Sair”.
- Em 403/401: página amigável com próximo passo (relogin / pedir elevação).

## Paleta de Cores Sugerida

| Token | Uso | Efeito |
|-------|-----|--------|
| Fundo `#0F1419` / surface `#161D26` | Ops dark (já próximo) | Reduz fadiga em turnos longos |
| Primary `#0F6E56` (mint GoRoute) | CTAs, links ativos, foco | Confiança + alinhamento marca |
| Signal `#E85D4C` | Erro / ação destrutiva (com ícone) | Alerta sem depender só de cor |
| Texto `#E8EEF4` / muted `#9AA8B5` | Hierarquia | Contraste WCAG em dark |

Evitar glow/roxo; admin é ferramenta, não landing.

## Próximos Passos (MVP UX)

1. Design tokens + componentes de estado (loading / empty / error / toast).
2. Guards de rota alinhados aos tiers do README.
3. Dashboard com status visível e empty de métricas.
4. Listagens com empty + erro + confirmação em mutações.
