# Developer Security Gate (GoRoute)

Preencher **antes** do handoff Q.A. em qualquer PR que toque auth, billing, PII, webhooks, B2B keys ou IA.

**Skills:** `secure-service.md` · `secure-payments.md` (se billing) · `guardrails/security.md`

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa / task | |
| Pacotes | |
| SPECs | |
| Dados sensíveis | [ ] PII [ ] Pagamento [ ] Tokens IA [ ] API keys [ ] Sessão |

---

## Controles

| # | Controle | OK | N/A | Evidência (path:linha ou teste) |
|---|----------|----|-----|----------------------------------|
| 1 | Authn correta (sessão + mapper se BFF) | ☐ | ☐ | |
| 2 | Authz BOLA (ownership/tenant) | ☐ | ☐ | |
| 3 | Role/workspace (SPEC-09) | ☐ | ☐ | |
| 4 | DTO whitelist / validação | ☐ | ☐ | |
| 5 | Rate limit se endpoint sensível | ☐ | ☐ | |
| 6 | Sem secrets no código/logs | ☐ | ☐ | |
| 7 | Migration (sem synchronize) | ☐ | ☐ | |
| 8 | SSRF / URL allowlist se houver URL user | ☐ | ☐ | |
| 9 | Stripe signature + raw body + idempotência | ☐ | ☐ | |
| 10 | IA/BYOK sem vazamento de chave | ☐ | ☐ | |
| 11 | Testes negativos 401/403 | ☐ | ☐ | |
| 12 | Frontend: sem token em localStorage; guards rota | ☐ | ☐ | |

---

## Veredito Developer

- [ ] **PASS** — pronto para Q.A.
- [ ] **FAIL** — bloquear merge; listar gaps abaixo

### Gaps

1. …

---

## Q.A. (preencher depois)

| Verdict | PASS / FAIL / BLOCKED |
|---------|------------------------|
| Notas | |
