# Fase 0 — Contrato (Developer)

**Stage:** stage-19-admin-content-ops  
**Data:** 2026-07-18

## Shape JSON (alinhado a `career-graph.catalog.ts`)

```ts
type ResourceKind = 'doc' | 'video' | 'lab' | 'article' | 'course';

type CatalogResource = { kind: ResourceKind; title: string; url: string };

type CatalogProject = {
  title: string;
  brief: string;
  acceptanceCriteria: string[];
};
```

Colunas `skill_reference`:
- `resources` jsonb NOT NULL DEFAULT `[]`
- `project` jsonb NULL
- `estimated_hours` int NULL

Campo API: **`project`** (não `learnByDoing`).

## Endpoints

| Método | Path | Mudança |
|--------|------|---------|
| GET | `/api/career-catalog/skills` | DTO inclui resources, project, estimatedHours |
| GET | `/api/career-catalog/skills/:slug` | Novo (detail) |
| POST/PATCH | `/api/career-catalog/skills` | Aceita resources, project, estimatedHours |
| GET | `/api/roadmap/nodes/:slug` | Lê DB-first via `technicalTrackForRole` |

## Security

- URL: apenas `http:` / `https:` (reject `javascript:`, `data:`, etc.)
- Mutação skills: `RequireAdminRoles('ADMIN')` (já existente)
- Sem secrets no JSON versionado
