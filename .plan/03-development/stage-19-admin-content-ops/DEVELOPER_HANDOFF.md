# Developer handoff — stage-19

**Status:** `qa`  
**Date:** 2026-07-18

## Entregue

- Migration `SkillReferenceTrackContent` aplicada
- API skills: resources / project / estimatedHours + GET `:slug`
- Roadmap `technicalTrackForRole`: DB-first + fallback TS
- Seed preenche conteúdo do catálogo só se DB vazio
- Admin: `/data/skills` lista + `/data/skills/:slug` editor (preview LBD/recursos)
- Cargo: deep-link “Editar recursos / Learn by Doing”

## Testes

- Jest `skill-track-content`: **9 PASS**
- Karma admin: **19 PASS**

## Q.A. smoke sugerido

1. Reiniciar `goroute-api` (seed enriquece skills)
2. Admin `:4201` → Skills → `javascript` → editar resource → Salvar
3. Produto roadmap → cargo Junior → ver mudança
4. Cargo admin → link trilha técnica
