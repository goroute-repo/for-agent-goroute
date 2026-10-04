# Stage 11 — notas Developer (2026-07-17)

## Entregue

| Item | Detalhe |
|------|---------|
| BFF NLP client | Truncate 20k; timeout 8s; `X-Internal-Api-Key`; soft `[]` |
| Catalog gate | `SkillCatalogFilterService` antes de demand |
| Listings processor | Fail soft + drop unknown slugs |
| NLP Flask | Max text; optional internal key; só slugs de `skills.json` |
| Compose | NLP bind `127.0.0.1:5000` (não LAN) |
| README | `/extract-skills` corrigido |

## Testes

- API Jest **138 PASS**
- NLP pytest (após `pip install -r requirements.txt`)

## Fora de escopo (residual)

- CSV import CA-141
- Endpoint público describe→skills
- Alinhar `skills.json` 1:1 com catálogo completo

## Handoff

INDEX → `qa`
