# Cobertura do produto × `.plan` × `.specs` (alinhamento)

**Persona:** Planner  
**Data:** 2026-07-17  
**Objetivo:** confirmar se o planejamento cobre site, plataforma, segurança, NLP, API, mentor, mobile etc., e se está alinhado 100% às SPECs — ou documentar gaps explícitos.

---

## 1. Veredito executivo

| Pergunta | Resposta |
|----------|----------|
| O `.plan` cobre o **núcleo** da dor (roadmap + match + jornada + ofertas)? | **Sim** (stages 03–06, 04) |
| O `.plan` cobre **100%** de todas as SPECs 00–22? | **Não** — havia buracos; corrigidos com stages **10–14** + expansão stage-01 (billing) |
| NLP é necessário? | **Sim, mas limitado** — extração de skills em ingestão de vagas (SPEC-12/13); **não** é path crítico do roadmap pessoal |
| Segurança/pagamento estavam só implícitos? | **Parcial** — agora skills `secure-service` + `secure-payments` + gate checklist + stage-01 expandido |

**Alinhamento após esta revisão:** matriz §3 = fonte da verdade; INDEX atualizado.

---

## 2. Superfície do produto (inventário)

| Superfície | Pacote / artefato | SPEC principal | Cobertura no `.plan` (após revisão) |
|------------|-------------------|----------------|-------------------------------------|
| Site / landing pública | `goroute-frontend` `/`, `/about`, `/pricing` | 20, 11 | **stage-12** (+ pricing já MVP) |
| Plataforma web profissional | dashboards, roadmap, semana, vagas | 03–08, 05, 09, 10 | stages **02–06** |
| Plataforma web empresa | jobs, candidatos, ofertas, settings | 06–10, 13 | stages **04–05**, **10** (storage) |
| Auth / identidade | `goroute-service-auth` | 01, 19 | stages **01–02** |
| BFF / API negócio | `goroute-api` | 16, 18 | todas stages API |
| Billing / Stripe | `billing` module | 11, 19 | **stage-01** (RS) + **05** (UI) + skill payments |
| Catálogo skills/cargos | catalog + Neo4j | 12 | **stage-10** |
| Weekly plan | módulo weekly-plan | 05 | **stage-10** (residuals P0) |
| Histórico / progresso | journey | 04 | **stage-03** |
| Replan | goal/TARGETS | 22 | **stage-06** |
| API B2B | api-b2b | 14 | **stage-07** |
| NLP | `goroute-nlp` | 12, 13 | **stage-11** (escopo mínimo) |
| Mentor IA | mentor-agent + roadmap | 03, 10, 11 | **stage-13** (segurança BYOK) + **08** (produto) |
| Mobile | `goroute-mobile` | 15 | **stage-14** |
| Admin | `goroute-platform-admin` | 12 (CRUD root) | **stage-12** |
| Segurança transversal | — | 19 | skills + stage-01 + gate em toda entrega sensível |
| Arquitetura / dados / contratos | — | 16, 17, 18 | âncoras; sem stage de “rewrite” |
| Inovação | — | 21 | **stage-08** (após P0) |
| E2E / release | `e2e/` | — | **stage-09** |
| Observabilidade / HA | compose, SigNoz opcional | 16, PROJETO | **stage-09** |

---

## 3. Matriz SPEC → Stage (alinhamento)

| SPEC | Domínio | Stage(s) | Status alinhamento |
|------|---------|----------|-------------------|
| 00 | Overview | visão `.plan/01-product` | ✅ |
| 01 | Auth | 01, 02 | ✅ |
| 02 | Onboarding | MVP + regressão em 02/05 | ✅ (sem stage dedicado — gap residual só se P0 reabrir) |
| 03 | Roadmap / mentor | 03, 08, **13** | ✅ |
| 04 | History/progress | 03 | ✅ |
| 05 | Weekly plan | **10** | ✅ (era gap → corrigido) |
| 06 | Jobs marketplace | 04, 10 | ✅ |
| 07 | Matching / apply | 04 | ✅ |
| 08 | Offers | 04 | ✅ |
| 09 | Workspaces | 05 | ✅ |
| 10 | Settings / ai-settings | 05, **13** | ✅ |
| 11 | Billing Stripe | **01**, 05 | ✅ (era gap → corrigido) |
| 12 | Catalog sync | **10** | ✅ (era gap → corrigido) |
| 13 | Storage / ingestion | **10**, **11** | ✅ (era gap → corrigido) |
| 14 | B2B API | 07 | ✅ |
| 15 | Mobile | **14** | ✅ (era gap → corrigido) |
| 16 | Architecture | constraints em todas | ✅ (não exige stage) |
| 17 | Data model | migrations nas stages | ✅ |
| 18 | API contracts | cada stage valida contratos do domínio | ✅ |
| 19 | Security | 01 + skills + gate | ✅ |
| 20 | Landing | **12** | ✅ (era gap → corrigido) |
| 21 | Innovation | 08 | ✅ |
| 22 | Replan | 06 | ✅ |

---

## 4. NLP — é necessário?

| Uso | Necessário? | Decisão Planner |
|-----|-------------|-----------------|
| Roadmap pessoal / grafo | Não | Catálogo + Neo4j bastam |
| Extrair skills de texto de vaga / import | **Sim (assistido)** | stage-11: BFF chama NLP; slugs validados no catálogo antes de persistir |
| Mentor conversacional | Não (é mentor-agent) | stage-13/08 |
| Expor NLP na internet | **Não** | Só rede interna |

---

## 5. Segurança e pagamento — capacitação Developer

| Artefato | Papel |
|----------|-------|
| `.agent/skills/secure-service.md` | Como implementar serviço seguro (BOLA, sessão, SSRF, LGPD, IA) |
| `.agent/skills/secure-payments.md` | Stripe raw body, signature, idempotência, anti-bypass |
| `.agent/skills/checklists/developer-security-gate.md` | Gate obrigatório pré-Q.A. |
| `.agent/guardrails/security.md` | Regras OWASP gerais |
| `.agent/skills/security-check.md` | Revisão / threat modeling (review) |

Toda stage que toque dados/pagamento referencia essas skills em **Ler antes**.

---

## 6. Gaps conscientes (não são desalinhamento)

| Item | Tratamento |
|------|------------|
| SigNoz full / K8s | Fora de escopo habitual (`AGENTS.md`); stage-09 só checklist |
| Embeddings ESCO v3 match | SPEC-21 / pós stage-08 |
| Onboarding SPEC-02 P0 remanescentes | Absorvidos se reaparecerem no código durante stage-01/02 — abrir task em `.plan/tasks/` |
| path-goroute espelho | Fora; usar `.specs/` da raiz |

---

## 7. Regra de sincronização contínua

1. Nova RF em `.specs/` → Planner atualiza esta matriz + INDEX.  
2. Developer não implementa SPEC sem stage ou task `.plan/tasks/`.  
3. Q.A. rejeita entrega sensível sem `developer-security-gate.md` PASS.
