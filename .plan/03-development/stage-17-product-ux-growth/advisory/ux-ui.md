# Parecer UX/UI — GoRoute (produto completo → foco mobile + paridade web)

**Persona:** UX/UI  
**Date:** 2026-07-18  
**Escopo analisado:** `goroute-mobile` (pós stage-16) + dashboard profissional web (`goroute-frontend`)  
**Draft Planner:** stage-17 product UX refactor

---

## Diagnóstico de Atrito

1. **Mentor isolado e sem contexto** — fica atrás de Mais (2 taps) e não nasce do nó, da semana ou da vaga; o histórico some ao sair. No web o mentor vive no mapa; no mobile o usuário “perde o fio” da carreira.
2. **Dead-ends de produto** — Weekly premium só mostra empty sem caminho para planos; gap de vaga não leva ao nó da trilha; notificação marca lida sem destino. Status do sistema falha (Nielsen: visibilidade / prevenção de erros).
3. **Primeira conexão e confiança** — login em device real falha com mensagem genérica de rede sem orientar IP/Wi‑Fi/flavor; marca “GR” + teal mobile ≠ mint web gera sensação de produto fragmentado.

---

## Estrutura de Navegação

**Manter 5 abas inferiores:** Início · Trilha · Semana · Vagas · Mais.

**Justificativa:** regra dos 3 toques e padrão de apps de carreira; 5 destinos cabem sem overcrowding. **Não** promover Mentor a 6ª aba (custo cognitivo).

**Ajustes de IA:**
| Destino | Mudança |
|---------|---------|
| Início | CTAs: próximo passo, Semana, Vagas, Mentor (1 tap); badge de não lidas |
| Trilha / Semana / Vaga (detalhe) | Ação “Perguntar ao mentor” com contexto pré-preenchido (≤2 taps) |
| Mais | Settings, Notificações (com badge), Sobre/ajuda de conexão LAN |
| Mentor | Rota push mantida, mas **entrada contextual** + histórico da sessão persistido |

Web: manter side-menu; alinhar tokens e garantir que CTAs de “próximo passo” espelhem a home mobile.

---

## Paleta de Cores Sugerida

| Papel | Cor | Uso / psicologia |
|-------|-----|------------------|
| Primary (confiança/foco) | `#0F6E56` | Botões filled, progresso, tab selected — teal escuro WCAG-friendly sobre claro |
| Primary deep | `#0A4F3D` | Títulos / marca |
| Surface | `#F4F7F6` | Fundo low-fatigue |
| Signal / alerta | `#E85D4C` (não só verde/vermelho) | Erros + destaque de gap; ícone+texto sempre |
| Success | `#0F6E56` + ícone check | Nunca só cor |
| CTA anúncio (Growth) | Fora do app — ver parecer Growth; **dentro do app** primary teal manda |

Unificar web: migrar `--color-mint` de `#00bfa5` (fraco em texto) para tom alinhado ao primary mobile em UI crítica, mantendo mint claro só como accent decorativo.

---

## Próximos Passos (MVP UX a prototipar/testar)

1. **Fluxo mentor contextual** — nó/vaga → perguntar → resposta com `scope` visível; histórico persiste na sessão.
2. **Weekly premium** — empty com CTA “Ver planos” (deep link web `/pricing` ou copy clara).
3. **Job → trilha** — tap em closing skill / CTA “Abrir na trilha”.
4. **Home como hub** — 1 tap para mentor, vagas, notificações (badge).
5. **Login rede** — mensagem + dica “mesma Wi‑Fi / APK debug LAN” sem jargão.

Heurísticas críticas a revalidar após MVP: status do sistema, controle do usuário, prevenção de erros, reconhecimento > memória.
