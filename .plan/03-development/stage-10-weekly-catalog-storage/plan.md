# Stage 10 — Weekly plan, catálogo e storage (residuals P0)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-10-weekly-catalog-storage` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-01`, `stage-04` (para demand/jobs) |
| **Stack** | `goroute-api`, `goroute-frontend` |
| **SPECs** | 05, 12, 13, 06 (demand) |

## Meta

Fechar gaps P0 de plano semanal, sincronização de catálogo/demand e ciclo de vida de armazenamento de vagas — alinhados às SPECs (não reimplementar o MVP que já funciona).

## Ler antes

1. `.specs/05-weekly-plan.spec.md`
2. `.specs/12-skills-catalog-sync.spec.md`
3. `.specs/13-jobs-storage-ingestion.spec.md`
4. `.agent/skills/secure-service.md`
5. Código: weekly-plan, GraphSync, demand scores, job archive/storage

## In scope

- [ ] Residuals SPEC-05 P0 (guards, UNIQUE week_key, UX quando roadmap falta) — só o que ainda falhar no código
- [ ] SPEC-12: `incrementFromSlugs` / demand ao CRUD vaga; não engolir erro de GraphSync
- [ ] SPEC-13: `archived` + cotas/uso se ainda planejado; soft-delete coerente
- [ ] Testes Jest + Vitest nos fluxos tocados
- [ ] Security gate checklist

## Out of scope

- Pipeline NLP completo (stage-11)
- B2B (stage-07)
- Redesign gamificação weekly

## Critérios de aceite

1. CA P0 das SPECs 05/12/13 listados como cobertos ou N/A com evidência no código.
2. Publicar/atualizar vaga atualiza demand scores (RN-071).
3. Arquivar vaga respeita cota/regra SPEC-13 se implementado nesta etapa.

## Validation (Q.A.)

```bash
cd goroute-api && npm test
cd goroute-frontend && npm test
```

## Anti-alucinação

- Grep antes de criar entities `archived_at` / storage — podem existir parciais.
- Não inventar campos fora de SPEC-17.

## Handoff Q.A.

Atualizar INDEX.
