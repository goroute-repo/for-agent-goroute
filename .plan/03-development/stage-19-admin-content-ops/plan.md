# Stage 19 — Admin content ops: trilha técnica (recursos + LBD) + redesign ops

| Campo | Valor |
|-------|-------|
| **ID** | `stage-19-admin-content-ops` |
| **Status** | `qa` |
| **Persona** | UX + Growth → Planner → Developer → Q.A. |
| **Depende de** | stage-18 `done` (admin shell/RBAC); stage-10/12 catálogo |
| **Pacotes** | `goroute-api` (P0), `goroute-platform-admin` (P0); smoke leitura em `goroute-frontend` |
| **SPECs** | 12 (catálogo), 03 (roadmap detail resources/project) |
| **Council** | [`advisory/ux-ui.md`](./advisory/ux-ui.md), [`advisory/growth-marketing.md`](./advisory/growth-marketing.md) |

---

## 1. Meta (problema → resultado)

**Problema:** No detalhe do cargo (ex. Desenvolvedor Web FullStack · Junior) o app mostra **Recursos da trilha técnica** e **Learn by Doing**, mas esses dados vivem no TypeScript estático `career-graph.catalog.ts` (via skill `roadmapSkillSlug`). O `goroute-platform-admin` **não permite** cadastrá-los — só metadata da skill/cargo.

**Resultado:** Operador ADMIN publica/edita resources + projeto LBD na **skill** pelo admin; o produto consome do Postgres (com fallback TS); UI admin redesenhada para content ops (editor + preview + deep-link cargo→trilha), sem redeploy para mudar MDN/To-Do.

---

## 2. Ler antes

1. `advisory/ux-ui.md`, `advisory/growth-marketing.md`, `notes.md` (ADR)
2. `goroute-api/src/roadmap/career-graph.catalog.ts` — `CatalogResource`, `CatalogProject`, nó `javascript`
3. `goroute-api/src/roadmap/profession-roadmap.service.ts` — `technicalTrackForRole`
4. `goroute-api/src/database/entities/skill-reference.entity.ts` + `career-catalog/dto`
5. Admin: `skills-admin.*`, `career-catalog.*`
6. Produto: `dashboard-roadmap.html` blocos resources/project
7. `.agent/skills/planning.md`, `secure-service.md` (URLs)

---

## 3. Estado atual (evidência)

| Camada | Hoje |
|--------|------|
| Produto | `GET /api/roadmap/nodes/:slug` → `resources[]`, `project` |
| Join | `career_role.roadmap_skill_slug` → `CATALOG_BY_SLUG` **ou** skill_reference vazia |
| Postgres skill | Sem resources/project |
| Admin skills | slug, label, category, description |
| Admin cargos | role fields + skillSlugs (1ª = trilha) |

---

## 4. Opções e decisão

Ver ADR completo em [`notes.md`](./notes.md).

**Decisão:** persistir `resources`, `project`, `estimated_hours` em `skill_reference` (jsonb); runtime DB-first + fallback catálogo TS; UI editor na skill + deep-link no cargo.

---

## 5. In scope / Out of scope

### In scope

- Migration + DTOs + service career-catalog (skills)
- `technicalTrackForRole` / seed sync
- Admin: editor skill (recursos, LBD, preview), lista com indicadores, deep-link no cargo
- Redesign ops focado em Dados (shell polish P1)
- Testes API + Karma admin; smoke Playwright admin + verificação leitura roadmap

### Out of scope

- Redesign do roadmap do `goroute-frontend` (só consumo)
- Editor de grafo Neo4j / edges REQUIRES na UI
- Resources no `career_role`
- ASO, ads, landing
- Angular 21 / Vitest no admin
- Analytics de clique em resource

---

## 6. NFRs e riscos

| NFR | Aceite |
|-----|--------|
| Segurança | URL só `http:`/`https:`; sem secrets; ADMIN+ |
| Compat | Skills sem DB content ainda servem via fallback TS |
| Performance | Preview local (client); PATCH único |
| a11y ops | Labels/kind não só cor; confirm delete resource |

Riscos: ver `notes.md`.

---

## 7. Fases

### Fase 0 — Contrato & baseline (gates)

**Goal:** Congelar modelo de dados e inventário anti-alucinação.  
**ownerPersona:** developer  
**DependsOn:** —  

**Aceite:**
- Documentar shape JSON alinhado a `CatalogResource` / `CatalogProject`
- Lista endpoints a alterar (só skill admin + read path roadmap)
- Security note: validação URL

**Files:** `notes.md` (já), opcional comentário no DTO draft  
**Validation:** review diff docs; sem código obrigatório.

---

### Fase 1 — Persistência API (skill_reference)

**Goal:** DB + API permitem CRUD de resources/project.  
**ownerPersona:** developer  
**DependsOn:** Fase 0  

**Files (indicativo):**
- MODIFICAR: `skill-reference.entity.ts`
- CRIAR: migration TypeORM
- MODIFICAR: `career-catalog.dto.ts`, `career-catalog.service.ts`, controller se preciso
- MODIFICAR: `profession-roadmap.service.ts` (DB-first + fallback)
- CRIAR/MODIFICAR: seed sync from `CATALOG_NODES` → DB (resources/project/hours)
- CRIAR: testes Jest (`technicalTrackForRole` / patch skill)

**Aceite:**
- PATCH skill com resources + project persiste e retorna no GET admin/skills
- `GET /api/roadmap/nodes/:slug` para role com `roadmapSkillSlug=javascript` lê do DB após seed
- URL inválida → 400
- Skill sem conteúdo no DB ainda usa fallback TS se existir no catálogo

**Validation:**
```bash
cd goroute-api && npm test -- --testPathPattern=career-catalog|profession-roadmap|skill-reference
cd goroute-api && npm run migration:run
```

---

### Fase 2 — Admin editor de trilha (skills)

**Goal:** Operador edita resources + LBD com preview.  
**ownerPersona:** developer  
**DependsOn:** Fase 1  

**Files:**
- MODIFICAR: `skills-admin.*` e/ou CRIAR `skills-skill-detail.*`
- MODIFICAR: `career-catalog-api.service.ts`, `app.routes.ts`
- Reusar: `page-header`, `empty-state`, `error-banner`, `confirm-dialog`, toast

**Aceite UX (P0):**
- Seções: Identidade | Recursos | Learn by Doing | Preview
- Add/edit/remove resource (kind, title, url)
- LBD: title, brief, acceptanceCriteria[] (add/remove linhas)
- Empty states explicam impacto no app
- Confirm ao remover resource/LBD

**Validation:**
```bash
cd goroute-platform-admin && npm test -- --watch=false --browsers=ChromeHeadless
```
Smoke Playwright `:4201` — login ADMIN → skill javascript → add resource → save → preview.

---

### Fase 3 — Cargo ↔ trilha (deep-link + clareza)

**Goal:** Eliminar atrito A2 (editar cargo esperando LBD).  
**ownerPersona:** developer  
**DependsOn:** Fase 2  

**Files:** `career-catalog.*` (admin)

**Aceite:**
- Bloco “Trilha técnica” mostra skill da 1ª seleção + link “Editar recursos / Learn by Doing”
- Warning se cargo sem skill ou skill sem conteúdo
- Não duplicar forms de resource no cargo

**Validation:** Karma se houver specs; smoke: cargo → click → editor skill.

---

### Fase 4 — Redesign ops (lista + shell polish)

**Goal:** Lista skills performática para content ops; shell alinhado.  
**ownerPersona:** developer  
**DependsOn:** Fase 2  

**Aceite:**
- Colunas/badges: N recursos, tem LBD, estimated hours
- Filtro “só com conteúdo” (nice) ou sort
- Sticky save / page-header consistente
- Sem regressão RBAC stage-18

**Validation:** Karma + smoke lista skills.

---

### Fase 5 — Q.A. ponta a ponta

**Goal:** Provar publish sem redeploy.  
**ownerPersona:** qa  
**DependsOn:** Fases 1–4  

**Aceite:**
1. Alterar título de um resource no admin
2. `GET /api/roadmap/nodes/<roleSlug>` (ou UI produto) reflete mudança
3. LBD critérios visíveis
4. READ_ONLY/SUPPORT não editam skills (guard)
5. Unitários verdes API + admin

**Validation:** checklist em `QA_GATE.md` (criar na entrega Q.A.)

---

## 8. Validation global

```bash
cd goroute-api && npm test && npm run migration:run
cd goroute-platform-admin && npm test -- --watch=false --browsers=ChromeHeadless
npm start   # admin :4201
```

Produto (smoke): login profissional → roadmap → nó Junior → resources/LBD atualizados.

---

## 9. Anti-alucinação

- Não inventar campo `learnByDoing` na API — usar **`project`** (`title`, `brief`, `acceptanceCriteria`).
- Kinds só: `doc` \| `video` \| `lab` \| `article` \| `course` (como catálogo).
- Não persistir resources no Neo4j nesta stage.
- Não exigir Angular 21.
- Confirmar migration name/timestamp no padrão do `goroute-api`.

---

## 10. Handoff

| De | Para | Artefato |
|----|------|----------|
| Council | Planner | advisories + notes ADR |
| Planner | Developer | este `plan.md` (`ready`) |
| Developer | Q.A. | fases + security gate se URLs |
| Q.A. | INDEX | `qa` → `done` |
