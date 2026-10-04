# Q.A. Gate — stage-13-mentor-ai-security

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Jest API | **149 PASS** |
| pytest mentor-agent | **6 PASS** |
| Vitest frontend | **39 PASS** |
| Security gate Developer | Presente (PASS) |
| Playwright MCP UI | OK (`:4200` settings AI) |

---

## Aceite

| Critério | Evidência | Resultado |
|----------|-----------|-----------|
| SEC-07 (key não em query) | `google-gemini.provider.spec.ts` | **OK** |
| SEC-09 / sem leak API | GET `/api/settings/ai` → sem `apiKey`/`encryptedApiKey`; mask + `hasApiKey` | **OK** |
| RN-123 BYOK ≠ quota managed | `mentor.service.spec.ts` | **OK** |
| RN-033 retém BYOK | `settings-ai.service.spec.ts` | **OK** |
| Rate limit mentor | `MentorRateLimitGuard` + gate | **OK** |
| Mentor-agent sem apiKey | `test_mentor_request_no_apikey.py` | **OK** |

---

## Smoke MCP (UI)

| Cenário | Resultado |
|---------|-----------|
| Login `smoke-qa@goroute.local` → `/dashboard/settings` | OK |
| Aba **AI mentor** — modos Off / managed / BYOK | OK |
| Managed: texto `Managed quota: 0 / 200 this month` | OK |
| BYOK: campo password vazio + hint encrypt; sem botão clear (sem key) | OK |
| Network `GET /api/settings/ai` **200** body sem chave | OK — `{ mentorMode, hasApiKey, apiKeyMasked, managedQuota }` |

---

## Residual (não bloqueante)

- `managedQuota.resetsAt` no smoke veio `2026-07-01` (início do mês / naming) — validar semântica no stage-08 se UI depender do label “resets”.

---

## INDEX

- `stage-13` → `done`
- `stage-08` → `ready` (dep 13 liberada)
