# Stage 06 — Career replan (mudança de objetivo)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-06-career-replan` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-03` |
| **Stack** | `goroute-api`, `goroute-frontend`, Neo4j sync |
| **Dor de mercado** | Incerteza / pivot para cargos novos |

## Meta

Permitir mudar `goalSlug` / replanejar rota mid-path preservando progresso concluído e recalculando weekly plan + path.

## Ler antes

1. `.specs/22-career-replan.spec.md`
2. `PROJETO.md` § Grafo pessoal / replanejamento
3. `GraphSyncService` / TARGETS no Neo4j
4. Stage-03 (histórico deve registrar evento de replan)

## In scope

- [ ] Endpoint canônico do SPEC (`PATCH` goal ou `POST` replan — **seguir SPEC-22**, não inventar ambos)
- [ ] Recalcular path Neo4j; preservar `completed`
- [ ] Invalidar/recalcular weekly plan
- [ ] UI de confirmação de mudança de objetivo + impacto (ETA)
- [ ] Evento de jornada (histórico)
- [ ] Testes de não-destruição de progresso

## Out of scope

- Career Twin full simulation UI (stage-08) — replan é commit real
- Multi-waypoint complexo além do SPEC-22 desta fase

## Critérios de aceite

1. Após replan, roadmap mostra novo caminho; nós completed permanecem.
2. Weekly plan reflete novo foco.
3. Histórico registra a mudança.
4. Validação de slug no catálogo (400 se inválido).

## Validation (Q.A.)

```bash
cd goroute-api && npm test
cd goroute-frontend && npm test
```

MCP: mudar objetivo → verificar grafo + semana + histórico.

## Anti-alucinação

- Não apagar skills do perfil no replan.
- Confirmar nome exato do endpoint no SPEC antes de documentar no frontend.

## Handoff Q.A.

Atualizar INDEX.
