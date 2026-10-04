---
name: ux-advisory
description: >-
  Parecer UX/UI sênior para o Planner — inventário, atritos P0–P2, fluxos
  before/after, estados e aceite verificável (GoRoute).
---

# Skill: UX Advisory (Principal Designer)

> Persona: [`personas/ux-ui.md`](../personas/ux-ui.md) · Council: [`planner-council.md`](../workflows/planner-council.md)

---

## 1. Gate de qualidade (rejeitar se falhar)

| # | Critério |
|---|----------|
| U1 | Citou **telas/rotas/componentes reais** do repo (ou mobile lib) |
| U2 | Atritos com **severidade P0 / P1 / P2** e impacto (desiste / contorna / cosmético) |
| U3 | Pelo menos **1 fluxo before → after** (passos numerados) |
| U4 | Matriz de estados: loading / empty / error / success onde a feature toca |
| U5 | Recomendações **testáveis** (aceite que Q.A./Developer possam marcar) |
| U6 | Paleta só se mudar tokens — com contraste/uso; senão “manter X” |

**Falha automática:** 3 bullets genéricos + tabela de cores + “seguir Nielsen” sem nome de tela.

---

## 2. Processo

1. Ler draft do Planner (meta, in/out).
2. Abrir rotas/templates/widgets do pacote afetado (evidência).
3. Mapear jornada crítica do pedido (happy path + 1 falha).
4. Ranquear atritos P0→P2.
5. Propor IA/nav **delta** (o que muda vs o que permanece).
6. Definir MVP de UX (o que prototipar/testar primeiro).
7. Listar aceite de usabilidade para o Planner colar nas fases.

---

## 3. Formato obrigatório do `advisory/ux-ui.md`

```markdown
# Parecer UX/UI — <escopo>

**Persona:** UX/UI (Principal)
**Data:** YYYY-MM-DD
**Draft:** <stage ou task>
**Evidência:** <paths ou rotas lidas>

## 1. Jobs & contexto
- Job principal do usuário neste escopo
- Restrições (mobile 5 tabs, admin shell, auth, etc.)

## 2. Inventário atual (delta)
| Superfície | Hoje | Problema |
| ... | ... | ... |

## 3. Atritos ranqueados
| ID | Severidade | Atrito | Evidência | Efeito |
| A1 | P0 | ... | path/rota | ... |

## 4. Fluxo before → after (crítico)
**Before:** 1. … 2. …
**After:** 1. … 2. …
**Regra:** taps/passos máximos ou feedback obrigatório

## 5. Arquitetura da informação / nav
- O que manter
- O que mudar (e por quê — custo cognitivo)
- O que NÃO fazer (anti-scope)

## 6. Sistema visual (só se necessário)
- Tokens: manter / ajustar (hex + contraste + uso)
- Hierarquia, densidade (produto vs ops)

## 7. Estados & microcopy de sistema
| Estado | Quando | Mensagem/orientação |
| loading | | |
| empty | | |
| error | | |
| success | | |

## 8. MVP UX + aceite para o plano
1. … (P0)
2. …
**Aceites sugeridos (colar nas fases):**
- [ ] …
```

Para **ferramenta interna (admin):** ênfase em densidade, RBAC visível, erro explícito, confirm destrutivo — **não** ASO/landing.

---

## 4. Heurísticas (uso, não checklist)

Aplicar Nielsen **citando a tela**:
- Visibilidade do status → onde falta loading/erro
- Prevenção de erro → delete sem confirm
- Reconhecimento → mentor sem contexto de nó/vaga

Máx. 3–5 achados profundos > 10 genéricos.

---

## 5. Handoff ao Planner

No fim do parecer, bloco explícito:

```markdown
## Implicações para fases
- Fase X deve incluir: …
- Fora de escopo UX: …
```

Planner pode rejeitar item com motivo em `notes.md`.
