# Q.A. Gate — stage-10-weekly-catalog-storage

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Jest API | **132 PASS** (archive/demand/quota specs) |
| Vitest frontend | **37 PASS** (incl. `POST /jobs/:id/archive` client) |
| Security gate Developer | Presente |
| Migration schema | `archived_at` + `deleted_at` em `job_postings` |
| Playwright MCP | OK (weekly + role guard; archive UI sem conta company) |

---

## MCP — evidências

| Cenário | Resultado |
|---------|-----------|
| Login smoke-qa | → `/dashboard/personal` |
| Weekly plan | Plano da semana com foco Pleno + 3 actions |
| Company jobs como pro | Redirect → `/dashboard/personal` (role guard) |
| Archive UI ATS | Residual: sem user company no smoke; coberto por Vitest + Jest |

---

## Aceite SPEC 05 / 12 / 13

| Item | Evidência |
|------|-----------|
| Weekly P0 (guards, week key, UX) | Weekly MCP + `WEEK_NOT_CURRENT` no service; UNIQUE já existia |
| RN-071 demand update skills | Jest `jobs-archive.service.spec` |
| Archive libera cota / storage | Jest CA-140 + `QUOTA_EXCEEDED` |
| Soft-delete com apps | Jest RS-02 |
| Colunas schema | Query information_schema OK |

---

## Residual aceito

- CSV batch CA-141 → stage-11  
- MCP archive click com conta company (não há seed company no smoke)

---

## INDEX

`stage-10` → `done` · stage-11 pode ir a `ready` (dep 10)
