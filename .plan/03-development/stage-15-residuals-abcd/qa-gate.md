# Q.A. gate — stage-15 residuals A→B→C→D

| Campo | Valor |
|-------|-------|
| Date | 2026-07-17 |
| Developer | PASS (A–D implementados + testes + smoke) |
| Q.A. | pending |

## Evidência Developer

| Fase | Validação |
|------|-----------|
| A | Jest erase/login inactive; Vitest privacy delete; MCP settings delete UI (prompt dismiss) |
| B | Jest MFA mobile body; `flutter test` auth_repository MFA |
| C | Jest off / managed / soft-fallback narrative |
| D | homolog compose UP; `smoke:preflight` OK; `smoke:homolog` PASS |

## Fora de escopo (não bloquear)

- SigNoz install
- Erase empresa / hard FK purge / Stripe customer delete
- UI mobileAppKey (sem API)
