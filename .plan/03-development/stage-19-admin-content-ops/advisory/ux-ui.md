# Parecer UX/UI — Admin content ops + redesign

**Persona:** UX/UI (Principal Product Designer)  
**Data:** 2026-07-18  
**Draft:** stage-19-admin-content-ops  
**Evidência lida:**
- Produto: `goroute-frontend/.../dashboard-roadmap.html` (recursos + Learn by Doing)
- Fonte: `goroute-api/src/roadmap/career-graph.catalog.ts` (`CatalogResource`, `CatalogProject` no nó `javascript`)
- Join: `profession-roadmap.service.ts` → `technicalTrackForRole()` via `career_role.roadmap_skill_slug`
- Admin: `goroute-platform-admin/.../career-catalog/*`, `skills/skills-admin.*`, `admin-shell.*`
- Persistência hoje: `skill_reference` **sem** resources/project; edição só redeploy do TS

---

## 1. Jobs & contexto

**Job do operador de conteúdo:** cadastrar/atualizar o que o profissional vê no detalhe do cargo (DOC/VIDEO/SITE + projeto Learn by Doing) **sem** pedir deploy.

**Job do operador de suporte:** achar usuário/empresa e agir (já coberto stage-18; redesign não pode piorar densidade).

**Restrições:** Angular 19 admin; RBAC ADMIN+ em dados; dark ops; marca `#0F6E56`; produto web já define o contrato visual (chips DOC/VIDEO, card LBD).

---

## 2. Inventário atual (delta)

| Superfície | Hoje | Problema |
|------------|------|----------|
| `/data/skills` | slug, label, category, description | **Não** há resources[] nem project |
| `/data/career-catalog` | cargo + checkboxes de skills; 1ª skill vira `roadmapSkillSlug` | Operador não vê preview do que o app mostra |
| Detalhe cargo (produto) | Recursos + LBD a partir do catálogo TS | Conteúdo “mágico”; admin parece incompleto |
| Shell admin | Nav grupos Operação/Dados/Compliance | Funcional, mas form de skill parece CRUD genérico, não “editor de trilha” |
| Skills com conteúdo só no TS | Ex.: `javascript` MDN + JS.info + To-Do | Skills só no Postgres ficam vazias no roadmap |

---

## 3. Atritos ranqueados

| ID | Sev | Atrito | Evidência | Efeito |
|----|-----|--------|-----------|--------|
| A1 | **P0** | Impossível cadastrar recursos/LBD no admin | skills-admin + DTO sem campos; dados só em `career-graph.catalog.ts` | Operador não entrega conteúdo; gap percebido pelo stakeholder |
| A2 | **P0** | Modelo mental invertido: usuário edita “cargo”, conteúdo vem da **skill** da trilha | `roadmapSkillSlug = skillSlugs[0]` | Edita cargo e não entende por que MDN não muda |
| A3 | **P1** | Form de skill monólito / sem seções (Identidade · Recursos · Projeto · Preview) | `skills-admin.html` layout 2 colunas raso | Erro de cadastro, URLs inválidas, LBD incompleto |
| A4 | **P1** | Sem preview “como no app” | Admin não espelha `rm-resources` / `rm-project` | Deploy surpresa / regressão visual |
| A5 | **P2** | Catálogo de cargos e skills separados sem deep-link contextual | Nav “Dados” lista ambos | Troca de contexto; perde skill ao editar cargo |

---

## 4. Fluxo before → after (crítico)

**Before:**  
1. Operador abre Catálogo de cargos → edita “Desenvolvedor Web FullStack · Junior”.  
2. Marca skills (JS, Git, SQL).  
3. Procura onde colocar MDN / Learn by Doing → **não existe**.  
4. Dev altera `career-graph.catalog.ts` e faz deploy.

**After:**  
1. Em **Catálogo de cargos**, ao marcar skills, UI destaca: “Trilha técnica (1ª skill) = JavaScript → editar conteúdo”.  
2. CTA **Editar trilha técnica** abre `/data/skills/javascript` (rota detail).  
3. Em **Skill → Recursos**, adiciona linhas: kind + title + URL (ícone conforme kind).  
4. Em **Skill → Learn by Doing**, preenche título, brief, critérios (lista dinâmica).  
5. **Preview** lateral espelha chips DOC/VIDEO + card LBD (read-only).  
6. Salvar → toast → produto (`GET /roadmap/nodes/:slug`) reflete sem redeploy.

**Regra:** ≤ 2 cliques do cargo até o editor da trilha técnica; preview obrigatório antes do primeiro save bem-sucedido da sessão (aceite).

---

## 5. Arquitetura da informação / nav

### Manter
- Shell lateral; grupos Operação / Dados / Compliance.
- RBAC stage-18.

### Mudar
| Item | Proposta |
|------|----------|
| Skills | De “lista CRUD” → **Editor de trilha técnica** (nome na nav pode permanecer “Skills / Trilhas técnicas”; subtítulo explica) |
| Rota | `/data/skills` lista; `/data/skills/:slug` editor com abas/seções |
| Cargo | Bloco “Trilha técnica associada” com preview resumido + link |
| Densidade | Forms longos em **seções colapsáveis**; ações primárias sticky (Salvar) |

### Não fazer
- CMS de marketing / landing no admin.
- Editor visual drag-and-drop do grafo nesta stage.
- Duplicar resources no `career_role` (quebra o modelo do produto).

---

## 6. Sistema visual

- **Manter** dark ops + primary `#0F6E56` + signal `#E85D4C` (LBD no preview pode usar borda signal como no app).
- Hierarquia: page-header + seções com labels uppercase muted (já parcialmente stage-18).
- Lista de recursos: rows com kind badge, title, URL truncada, ações Editar/Remover.
- Preview panel: fundo surface-2, tipografia alinhada ao detalhe do roadmap (sem copiar o app 1:1 — **semelhança funcional**).

---

## 7. Estados & microcopy de sistema

| Estado | Quando | Mensagem/orientação |
|--------|--------|---------------------|
| loading | GET skill | Loading block |
| empty resources | lista vazia | “Nenhum recurso — o app não mostrará a seção Recursos.” + CTA Adicionar |
| empty LBD | project null | “Sem Learn by Doing — card não aparece no app.” |
| error URL | url inválida / http em prod se política exigir https | Inline no campo |
| error API | 4xx/5xx | error-banner + mensagem Nest |
| success | PATCH ok | toast “Trilha técnica salva.” |
| warning trilha | cargo sem skill / skill sem conteúdo | “Cargo sem trilha técnica — detalhe no app fica sem recursos/LBD.” |

---

## 8. Redesign admin (escopo UX desta stage)

Prioridade de redesign **orientada a cadastro de dados da plataforma**:

1. **P0** Editor de skill (recursos + LBD + preview) — coração do pedido.  
2. **P0** Cargo ↔ trilha: deep-link + aviso 1ª skill.  
3. **P1** Shell: busca global de skill/cargo (opcional se tempo); sticky save; densidade tipográfica.  
4. **P1** Lista skills: colunas “# recursos / tem LBD / horas”.  
5. **P2** Polish dashboard/users (já stage-18) — só se não atrasar P0.

---

## 9. MVP UX + aceite para o plano

1. (P0) CRUD resources na skill no admin.  
2. (P0) CRUD project (LBD) na skill no admin.  
3. (P0) Preview + deep-link cargo → skill.  
4. (P1) Lista skills com indicadores de conteúdo.  

**Aceites sugeridos:**
- [ ] Em `/data/skills/:slug`, operador adiciona resource `doc` com URL e vê no preview.
- [ ] Preenche LBD com ≥1 critério; preview mostra chip + lista.
- [ ] Do cargo com `roadmapSkillSlug=javascript`, 1 clique chega ao editor da skill.
- [ ] Empty resources/LBD explicam impacto no app.
- [ ] Save falho não deixa loading eterno; toast/erro legível.

## Implicações para fases
- Fase de **contrato/API** antes da UI (senão alucinação de campos).
- UI do editor skill antes do redesign cosmético do shell.
- Fora de escopo UX: ASO, ads, redesign do roadmap do produto consumidor.
