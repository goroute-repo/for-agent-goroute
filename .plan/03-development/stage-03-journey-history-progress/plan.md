# Stage 03 — Histórico de jornada + progresso até o alvo

| Campo | Valor |
|-------|-------|
| **ID** | `stage-03-journey-history-progress` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-01` |
| **Stack** | `goroute-api`, `goroute-frontend` |
| **Dor de mercado** | Falta de direcionamento contínuo — “onde estou e o que falta?” |

## Meta

Entregar a capacidade central de **mapeamento efetivo da carreira**: timeline origem → marcos → objetivo e painel “a partir de hoje, o que falta até o alvo”.

## Ler antes

1. `.specs/04-roadmap-history-progress.spec.md` (**fonte de RF/CA**)
2. `PROJETO.md` § Mapeamento de carreira / histórico
3. Código roadmap existente: services, entities `node_progress`, Time Machine
4. Rotas planejadas: `/dashboard/roadmap/history`, `/dashboard/roadmap/progress` (criar se MISSING)

## In scope

- [ ] Modelo de dados para snapshots / `journey_events` **conforme SPEC-04** (migration; sem synchronize)
- [ ] APIs de leitura de histórico e de `pathProgress` (ou nome canônico do SPEC) — **não inventar paths**; seguir SPEC-04 / contratos `.specs/18` se citados
- [ ] UI timeline + UI progresso “o que falta” integrada ao roadmap
- [ ] Reuso de Time Machine existente onde couber (não duplicar ETA)
- [ ] Testes Jest (API) + Vitest (UI) + MCP nas novas telas

## Out of scope

- Career Twin / simulação “e se” (stage-08)
- Replan de objetivo (stage-06) — pode expor read-only do goal atual
- Matching de vagas

## Entregáveis

| Ação | Item |
|------|------|
| A CRIAR | Migration + entities/repos conforme SPEC-04 |
| A CRIAR/MODIFICAR | Module/service journey/progress no BFF |
| A CRIAR/MODIFICAR | Components/rotas frontend |
| A CRIAR | Specs unitários |

## Critérios de aceite

1. Profissional autenticado vê **de onde começou**, marcos relevantes e **objetivo**.
2. Tela/painel mostra progresso no trilho atual e lista do que falta até `goalSlug`, mesmo com skills fora do trilho.
3. CA do SPEC-04 marcados como cobertos nesta etapa (listar IDs no PR/notes).
4. Sem endpoints fantasma na UI.

## Validation (Q.A.)

```bash
cd goroute-api && npm test
cd goroute-frontend && npm test
```

MCP: login profissional → roadmap → history/progress → validar dados coerentes com skills/goal do onboarding de teste.

## Riscos / anti-alucinação

- **Ler SPEC-04 linha a linha** antes de criar tabelas.
- Não apagar `node_progress` existente.
- Não calcular progresso só no frontend sem contrato API.

## Handoff Q.A.

Atualizar INDEX; anotar IDs de CA atendidos em `notes` da etapa (criar `notes.md` se necessário).
