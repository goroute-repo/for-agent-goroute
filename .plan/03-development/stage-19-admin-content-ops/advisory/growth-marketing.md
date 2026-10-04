# Parecer Growth — Admin content ops

**Persona:** Growth (Principal)  
**Data:** 2026-07-18  
**Draft:** stage-19-admin-content-ops  
**Modo:** N/A aquisição (interno) + ativação de conteúdo de produto

---

## 1. Valor para o operador / negócio

| Ator | Job |
|------|-----|
| Content / curriculum ops | Publicar recursos e projetos práticos que sustentam a promessa “mapa vivo” |
| Produto GoRoute (B2C) | Diferencial vs curso genérico: **evidência prática (LBD)** + links curados |
| Suporte | Menos escalação a eng para “atualizar o MDN do Junior” |

**Custo do gap atual:** conteúdo da trilha (ex.: JavaScript → MDN + To-Do) só muda com deploy → time-to-content alto → roadmap parece estático → retenção/activation do profissional sofre.

**ICP interno:** operador ADMIN com mandato de catálogo (não SUPPORT).

---

## 2. Messaging interno (1 frase)

> “O admin é a **fonte operacional da trilha técnica**: o que você publica na skill aparece no cargo do profissional.”

Anti-mensagem: “CRUD de labels”. Tom: ops confiável, não marketing.

---

## 3. Ativação interna (primeiro sucesso)

**Momento aha do operador (≤10 min):**  
Editar skill `javascript` → adicionar/alterar um resource → abrir app no cargo Junior → ver o link novo.

**Métrica âncora (hipótese, ops):**
| Etapa | Ação | Métrica |
|-------|------|---------|
| Ativação ops | Skill salva com ≥1 resource **ou** LBD completo | # skills com conteúdo rico / semana |
| Qualidade produto | Cargo com trilha apontando skill rica | % nós path com `resources.length>0` ou `project!=null` |
| Latência | Publish → visible no app | &lt; 1 min (sem redeploy) |

---

## 4. Ligação com aquisição (fora do admin, mas produto)

Não é ASO nesta stage. Porém o conteúdo LBD/recursos **é** prova social implícita na jornada pós-login (“não é só grafo vazio”).  
Backlog Growth produto: screenshots de LBD em store/landing — **outOfScope** aqui.

---

## 5. Riscos se virar B2B self-serve

- Clientes editando URLs maliciosas → validar `https` + allowlist opcional depois.
- Conteúdo vazio em massa → empty states + score “completude da trilha” no admin (fase polish).

---

## 6. In-scope vs backlog

**Entra no plano (via UX/Planner):**
- Persistência editável de resources + project
- UI admin editor + preview
- Copy/microcopy de ativação (“aparece no app”)

**outOfScope / backlog Growth:**
- Campanhas ads, ASO titles, lead magnet
- Portal parceiro self-serve
- Analytics de clique em resources (produto)

---

## 7. Aceites sugeridos

- [ ] README/admin ou empty state explica: conteúdo da trilha vive na **skill**, não no cargo.
- [ ] Após save, operador consegue validar no produto sem ajuda de engenharia.
- [ ] Lista de skills mostra se há conteúdo (ativação mensurável visualmente).

## Implicações para fases
- Incluir: indicadores de completude na lista; copy de deep-link.
- Rejeitar do plano: canais pagos, redesign landing.
