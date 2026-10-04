---
name: secure-service
description: Skill obrigatória do Developer GoRoute para implementar serviços seguros (auth, BFF, dados pessoais, pagamentos, IA). Baseado em OWASP API Top 10, ASVS, Stripe e práticas NestJS 2025–2026.
---

# Skill: Secure Service Development (GoRoute)

> **Persona:** [Developer](../personas/developer.md)  
> **Sempre com:** [`guardrails/security.md`](../guardrails/security.md) · [SPEC-19](../../.specs/19-security.spec.md) · checklist [`checklists/developer-security-gate.md`](./checklists/developer-security-gate.md)  
> **Pagamentos:** também ler [`secure-payments.md`](./secure-payments.md)

**Quando usar (obrigatório):** qualquer mudança em auth, sessão, guards, billing/Stripe, webhooks, API keys B2B, PII, LGPD, mentor/BYOK, jobs webhook (SSRF), ou novo endpoint que leia/escreva dados de usuário/empresa.

---

## 1. Fontes da indústria (âncoras — não inventar regras)

| Fonte | Uso no GoRoute |
|-------|----------------|
| [OWASP API Security Top 10 2023](https://github.com/OWASP/API-Security) | BOLA, authn, mass assignment, SSRF, misconfig |
| [OWASP ASVS](https://owasp.org/www-project-application-security-verification-standard/) | Controles verificáveis (auth, session, crypto) |
| [Stripe — Webhook signatures](https://docs.stripe.com/webhooks/signature) | `constructEvent` + raw body |
| NestJS prod patterns (rawBody, config validation) | Bootstrap BFF/auth |
| SPEC-01, 09, 11, 14, 19 | Controles e gaps SEC-* do produto |

---

## 2. Trust boundaries GoRoute (mapa mental)

```text
Browser (Angular)
  │  cookie httpOnly + credentials
  │  header x-mapper-api
  ▼
goroute-api (BFF) ──INTERNAL_API_KEY──► goroute-service-auth
  │                                      │
  │ Postgres negócio / Neo4j / Redis     │ Postgres auth / Redis sessões
  │
  ├── Stripe (webhook signature)
  ├── Company webhooks (SSRF allowlist)
  ├── goroute-mentor-agent / LLM (sem keys no cliente)
  └── goroute-nlp (só rede interna / sem exposição pública)
```

**Regras:**

1. BFF **nunca** confia só no cookie: valida sessão **e** `x-mapper-api` no auth.
2. Auth é a única fronteira de identidade; BFF não reimplementa login.
3. Serviços Python (mentor/NLP) **não** são públicos na internet sem auth de serviço.
4. Cliente **nunca** recebe `INTERNAL_API_KEY`, Stripe secret, ou chave BYOK completa.

---

## 3. Checklist de implementação (ordem)

### 3.1 Antes de codar

- [ ] Ler SPEC do domínio + SPEC-19 SEC-* relacionados
- [ ] Identificar ator (`professional` | `company` | `system` | `anonymous`)
- [ ] Listar dados sensíveis tocados (PII, pagamento, tokens IA)
- [ ] Definir testes negativos (401/403/BOLA) junto com o feliz

### 3.2 Autenticação e sessão

- [ ] Sessão Redis com TTL; cookie `httpOnly`, `Secure` em prod, `SameSite` adequado
- [ ] MFA: sem bypass pós-OAuth (SEC-01); challenge antes de sessão completa
- [ ] Rate limit em login/reset/MFA (Redis) — ver SPEC-01 / SPEC-19 §4
- [ ] Não logar senha, tokens, backup codes, session id completo

### 3.3 Autorização (OWASP API1 BOLA + BFLA)

Em **todo** handler que recebe ID de recurso:

```typescript
// Padrão obrigatório (conceitual)
// 1) identidade da sessão (já no guard)
// 2) carregar recurso
// 3) assert ownership OU role company do mesmo tenant
// 4) só então mutar/retornar
```

- [ ] `ProfessionalRoleGuard` / `CompanyRoleGuard` no endpoint
- [ ] Ownership: `job.companyId === session.companyId`, `profile.userId === session.userId`
- [ ] Nunca listar todos os users para “match” (SEC-03 / SPEC-07 RS-01)
- [ ] Mass assignment: DTO whitelist (`ValidationPipe` forbidNonWhitelisted)
- [ ] Cross-workspace: empresa não chama APIs de roadmap profissional (SPEC-09)

### 3.4 Input e saída

- [ ] `class-validator` em body/query/params; MaxLength; enums fechados
- [ ] Skill slugs só do catálogo (SPEC-12 RS-01)
- [ ] URLs de usuário: allowlist protocolo/host (SSRF) — portfolio, webhooks ATS
- [ ] Respostas sem stack trace; erros tipados; sem vazar existência de email além do necessário

### 3.5 Dados e persistência

- [ ] TypeORM: queries parametrizadas; **sem** `synchronize: true` em prod (SEC-06)
- [ ] Migrations versionadas para qualquer schema change
- [ ] Soft-delete / retenção alinhados a LGPD quando aplicável
- [ ] Neo4j: não apagar `TARGETS`/relações críticas sem SPEC (SEC-04)

### 3.6 Integrações e webhooks

- [ ] Stripe: ver `secure-payments.md` (raw body + signature + idempotência)
- [ ] Webhooks de empresa: bloqueio SSRF (localhost, metadata cloud, IPs privados)
- [ ] HMAC / assinatura quando SPEC-14 exigir
- [ ] Timeouts e tamanho máximo de payload

### 3.7 Segredos e config

- [ ] Secrets só em env / secret manager; validar no bootstrap (falha fast se faltar)
- [ ] `.env` nunca commitado; rotacionar `INTERNAL_API_KEY`, Stripe, MFA encryption keys
- [ ] BYOK: cifrar em repouso; nunca ecoar chave completa (SEC-09)

### 3.8 Frontend (quando tocar UI)

- [ ] CSP / harden build existentes — não enfraquecer
- [ ] Guards de rota Angular por role (SEC-08)
- [ ] `withCredentials` só onde necessário; XSS: sem `innerHTML` com dados de usuário
- [ ] Não guardar tokens de sessão em `localStorage`

### 3.9 Observabilidade segura

- [ ] Logs estruturados sem PII/secrets
- [ ] Correlation id quando existir padrão no repo
- [ ] Auditoria em eventos de segurança (login fail, MFA, reset)

---

## 4. Padrões NestJS (GoRoute)

| Prática | Aplicação |
|---------|-----------|
| Guards em cadeia | Session → Onboarding → Role → Premium → Profile |
| DTOs | class-validator + whitelist |
| Internal routes | `/internal/*` + `InternalApiKeyGuard` |
| Exception filter | Erros padronizados; sem leak |
| Helmet / CSP | Manter opções existentes; não desligar em prod |
| Config validation | Joi/Zod/class-validator no boot |

---

## 5. Python (mentor / NLP) — mínimo seguro

- [ ] Bind em rede interna / compose; sem porta pública sem necessidade
- [ ] Auth de serviço (API key compartilhada ou mTLS futuro) nas chamadas BFF→Python
- [ ] Timeout curto; validar tamanho do texto (prompt injection / DoS)
- [ ] Não persistir prompts com PII sem necessidade; não logar API keys
- [ ] NLP: saída = sugestão de slug; humano/BFF valida contra catálogo antes de persistir

---

## 6. Anti-padrões (nunca)

- Confiar em ID do body sem ownership check
- `synchronize: true` como “atalho”
- Webhook Stripe sem verificação de assinatura
- Colocar Gemini/OpenAI key em query string (SEC-07)
- Endpoint genérico “list all professionals”
- JWT caseiro paralelo ao cookie session
- Retornar `api_secret_key` / BYOK raw no JSON de settings

---

## 7. Definition of Done (segurança)

Uma entrega sensível só está pronta quando:

1. Checklist §3 marcado (ou N/A justificado).
2. Testes negativos (401/403/BOLA) passam.
3. [`developer-security-gate.md`](./checklists/developer-security-gate.md) PASS.
4. Q.A. revisou Validation da etapa + smoke de authz.

---

## 8. Referências rápidas no monorepo

- `.specs/01-auth-identity.spec.md`, `09-workspaces`, `11-billing`, `14-api-b2b`, `19-security`
- `.agent/guardrails/security.md`
- `.plan/03-development/stage-01-prod-security-foundation/plan.md`

*Skill alinhada a pesquisas 2025–2026: OWASP API Security (GitHub), Stripe webhook docs, NestJS rawBody/idempotency patterns.*
