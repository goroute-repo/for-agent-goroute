# Parecer UX/UI — Redesign mobile GoRoute (dashboard + aparência)

**Data:** 2026-07-18  
**Persona:** UX/UI  
**Escopo:** `goroute-mobile` pós stage-17

## Diagnóstico de Atrito

1. **Home sem dashboard** — lista de botões empilhados; o usuário não vê status, progresso e próximo passo em um olhar.
2. **Sem idioma/tema** — i18n e `ThemeData` dark existem no código, mas não há controle na UI; sensação de app incompleto.
3. **Hierarquia fraca** — AppBars genéricos e CTAs sem prioridade; mentor/vagas competem com a ação principal.

## Estrutura de Navegação

- Manter **5 abas** (Home / Trilha / Semana / Vagas / Mais).
- **Home = dashboard** (status + 1 ação primária + atalhos 2×2).
- **Aparência** em Configurações (fonte da verdade) + atalho em Mais.
- Idioma e tema com ≤2 toques a partir de Mais.

## Paleta de Cores Sugerida

| Token | Hex | Uso |
|-------|-----|-----|
| Primary | `#0F6E56` | CTA, progresso, confiança |
| Deep | `#0A4F3D` | Títulos em light |
| Surface light | `#F4F7F6` | Fundo |
| Signal | `#E85D4C` | Alerta / gap (com ícone) |
| Dark surface | `#0E1412` | Fundo dark sem “preto puro” |

Contraste WCAG em texto sobre primary; nunca só vermelho/verde para estado.

## Próximos Passos (MVP UX)

1. Dashboard Home com progresso + próximo foco + grid de atalhos.
2. ThemeMode + Locale persistidos (system/light/dark · pt/en).
3. Seção Aparência em Settings/Mais.
4. Validar com `flutter test` + smoke visual no device.
