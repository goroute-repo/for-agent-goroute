# Stage 15 — Residuals A→B→C→D — Notes

## Fase A — LGPD erase (2026-07-17)

### Entregue
- Auth: `DELETE /internal/users/:userId/erase` + `EraseProfessionalAccountUseCase` (soft-disable, scrub email/senha, MFA/social, revoke sessions)
- BFF: `DELETE /api/settings/privacy/account` body `{ confirm: "DELETE" }` — auth erase → wipe skills/AI/progress/weekly/notifications/alerts → scrub profile → anonymize journey (CA-042) → soft cancel Stripe se possível → Neo4j `DETACH DELETE` User
- UI: settings privacy — botão exclusão + prompt `DELETE` + logout

### Testes
- Auth Jest: erase use case + login inactive
- BFF Jest: settings-privacy erase orchestration
- Frontend Vitest: privacy delete path

### Fora de A
- Erase empresa; hard-delete UUID; purge Stripe customer

### MCP (A)
- Settings → Security: botão "Delete my account permanently" + prompt DELETE (dismiss sem erase smoke user)

---

## Fase B — Mobile MFA (2026-07-17)

- Auth: `buildMfaVerifyResponseBody` — mobile recebe `sessionId`/`mapApiHash` em `POST /users/mfa/verify`
- Flutter: `LoginMfaPage` + `verifyMfa`; removido bloqueio "finish on web"
- README: `COMPANY_LOGIN_MOBILE_KEY` deprecated/unused; CA-150 doc-only
- Testes: Jest util + `flutter test` auth_repository MFA path

---

## Fase C — Match narrative LLM (2026-07-17)

- Export `MentorService` de RoadmapModule; inject em JobMatchService
- `explainMatchForProfessional`: off → heuristic; managed|byok → mentor.analyze + soft-fallback; ≤2 closingSkills authoritative
- Jest: `job-match.narrative.spec.ts` (off / managed / throw fallback)

---

## Fase D — Smoke homolog (2026-07-17)

### Stack
```text
docker compose -f docker-compose.yml -f docker-compose.homolog.yml up -d --build
INTERNAL_API_KEY=change-me-homolog-internal-key
```
Bloqueador corrigido no build: `AuditEvent` + `ACCOUNT_ERASED` em auth.

### Seed
- `npm run seed:smoke-user` → user `smoke-qa@goroute.local` (id `68ce8281-688a-4542-b8cd-04ec6d52f301`)

### Resultado
| Comando | Resultado |
|---------|-----------|
| `smoke:preflight` | **OK** |
| `smoke:homolog` | **PASS** (subscription active + Stripe checkout URL) |

Evidência também em `.plan/03-development/stage-09-observability-release-gate/RELEASE_REPORT.md` §3.
