# Stage 19 — Notes (Planner reconcile + ADR)

## Council

| Parecer | Decisão |
|---------|--------|
| UX | **Aceito** — P0 editor skill (resources + LBD + preview); deep-link cargo→skill; lista com indicadores |
| Growth | **Aceito** — modo interno; ativação = publish sem deploy; messaging “fonte da trilha técnica” |
| UX redesign “todo o admin” | **Parcial** — redesign **orientado a content ops** (Dados/skills/cargos + shell polish). Dashboard/users já stage-18: só polish se sobrar (P2) |
| Growth ads/ASO | **Rejeitado** — outOfScope |

## ADR — onde persistir resources / project

| Opção | Prós | Contras | Risco |
|-------|------|---------|-------|
| **A — JSONB em `skill_reference`** | Alinha ao join atual (`roadmapSkillSlug` → skill); admin Skills natural; 1 migration | Precisa migrar seed do TS → DB; Neo4j continua sem esses campos | Médio (migração) |
| B — JSONB em `career_role` | Operador edita no cargo | **Quebra** modelo do produto (trilha = skill); duplicação entre cargos | Alto produto |
| C — Manter só TS catalog | Zero migration | Stakeholder continua sem admin | **Inaceitável** (pedido) |
| D — Tabela normalizada `skill_resource` | Query fina | Overkill MVP; mais UI | Alto prazo |

**Decisão: A** — colunas `resources` (jsonb), `project` (jsonb nullable), `estimated_hours` (int nullable) em `skill_reference`.  
**Runtime:** `technicalTrackForRole` lê **DB primeiro**; se resources/project vazios e existir entry em `CATALOG_BY_SLUG`, **fallback TS** (compat); após seed migration, DB prevalece.  
**Consequência:** Admin edita skill; cargo só aponta a trilha (1ª skill) — UI deve deixar isso óbvio (UX A2).

## Evidência (anti-alucinação)

- UI produto: `dashboard-roadmap.html` `@if (selected.resources)` / `selected.project`
- Tipos: `CatalogResource` / `CatalogProject` em `career-graph.catalog.ts`
- Exemplo JS: MDN + javascript.info + project To-Do (~L197–224)
- Admin gap: `UpsertSkillReferenceDto` sem resources/project
- Entity: `skill-reference.entity.ts` só slug/label/category/description/active

## Riscos

| Risco | Mitigação |
|-------|-----------|
| URL javascript: / data: | Validar URL http(s) no DTO |
| Regressão conteúdo após migration | Seed from CATALOG_NODES + testes de `technicalTrackForRole` |
| Operador edita cargo esperando LBD | Deep-link + warning na UI do cargo |
| Escopo “redesign tudo” explode | Fases: contrato → editor → cargo link → shell polish |

## Próximo

Developer executa fases do `plan.md` após status `ready`. Q.A. valida por fase.
