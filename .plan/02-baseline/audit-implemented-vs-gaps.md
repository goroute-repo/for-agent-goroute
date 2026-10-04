# Baseline — implementado × gaps (anti-alucinação)

**Persona:** Planner  
**Método:** Cruzamento `PROJETO.md` § implementado/estado atual + `planos/` legado + `.specs/` + estrutura do repo.  
**Atenção:** status de UI/API pode ter avançado desde docs de maio/2026 — Developer **deve** confirmar paths em `evidence-anchors.md` antes de codar.

---

## 1. Avaliação do planejamento legado (`projeto/` + `planos/`)

| Artefato legado | Útil? | Problema | Destino no `.plan` |
|-----------------|-------|----------|-------------------|
| `projeto/plano_de_implementação.md` | Sim | Bom mapa MVP vs P1/P2/P3 | Absorvido neste audit + stages |
| `projeto/arquitetura.md` | Sim | Espelha modular monolith real | Apontar `PROJETO.md` / SPEC-16; não duplicar |
| `projeto/plataforma/*`, `webpage/*` | Parcial | Specs de UI/produto espalhados | Prioridades viram stages; detalhe em `.specs/` |
| `planos/refactor-completo` | Parcial | Fases 1–3 marcadas feitas; incompleto vs produto | Não reabrir; novos stages cobrem gaps reais |
| `planos/production-readiness-by-spec.md` | Sim | Melhor mapa P0/P1 por SPEC | Base da stage-01 e prioridades |

**Veredito:** o planejamento base **entendia o objetivo** (roadmap skills-first + marketplace), mas estava **fragmentado** (produto em `projeto/`, readiness em `planos/`, specs em `.specs/`) e parcialmente **desatualizado**. `.plan` unifica ordem executável.

---

## 2. Núcleo já implementado (confirmar no código)

| Capacidade | Pacotes | Notas |
|------------|---------|-------|
| Auth sessão + OAuth + MFA + reset | `goroute-service-auth` | Hexagonal; cookies Redis |
| BFF sessão + `x-mapper-api` | `goroute-api` | Guards de role/onboarding/premium |
| Onboarding profissional/empresa | api + frontend | Schema dinâmico |
| Roadmap / grafo / Time Machine / mentor | api Neo4j + frontend vis-network | Mentor: evoluir BYOK (SPEC-03) |
| Weekly plan | api + frontend | |
| Jobs CRUD + match | api + frontend | Loop ofertas/apply pode estar parcial — validar SPEC-06/07/08 |
| Billing Stripe | api + frontend pricing | |
| Career catalog cargos | api + admin UI | |
| Landing + i18n + tema | frontend | |
| NLP / mentor-agent | python packages | Evolução assistida |

---

## 3. Gaps críticos para a dor de mercado (produto)

Ordenados pelo impacto na dor “falta de direcionamento / mapeamento efetivo”:

| ID | Gap | SPEC / doc | Impacto na dor |
|----|-----|------------|----------------|
| G1 | Histórico de jornada + progresso “o que falta” dedicado | SPEC-04 | Alto — direção contínua |
| G2 | Loop candidatura (100%) + ofertas empresa→profissional | SPEC-07/08 | Alto — mapa vira oportunidade |
| G3 | `minMatch` flexível candidatos empresa | SPEC-06/07 | Alto — hiring skills-first |
| G4 | Replan / mudar `goalSlug` mid-path | SPEC-22 | Alto — cargos novos / pivot |
| G5 | Settings completos (perfil, LGPD, billing UI, storage empresa) | SPEC-10 | Médio — confiança/conta |
| G6 | P0 segurança/produção (MFA OAuth, migrations, sync false) | SPEC-01/19 + readiness | Bloqueia escala |
| G7 | API B2B idempotente | SPEC-14 | Médio — B2B |
| G8 | Diferenciais inovação (Career Twin, match narrativo…) | SPEC-21 | Diferenciação pós-P0 |

---

## 4. Débitos técnicos P0 (produção)

Confirmar no código antes de fechar stage:

- [ ] `synchronize: true` em TypeORM (auth e/ou BFF) → migrations
- [ ] OAuth emitindo sessão sem step-up MFA
- [ ] Verificação de e-mail / SMTP produção
- [ ] Headers/CSP/HSTS efetivos em prod
- [ ] Redis/HA e multi-instância (quando for deploy real)

Fonte: legado `production-readiness-by-spec.md` + `PROJETO.md` débitos.

---

## 5. O que NÃO está faltando (evitar retrabalho)

Não planejar do zero:

- Novo serviço de auth paralelo
- Troca de Neo4j por outra tech de grafo no MVP
- Playwright dentro do `package.json` do frontend
- Microsserviços por módulo do BFF

---

## 6. Mapeamento gap → etapa de desenvolvimento

| Gap | Etapa |
|-----|-------|
| G6 | `stage-01-prod-security-foundation` |
| Auth UX residual / regressão | `stage-02-auth-ux-hardening` |
| G1 | `stage-03-journey-history-progress` |
| G2+G3 | `stage-04-marketplace-loop` |
| G5 | `stage-05-settings-workspaces` |
| G4 | `stage-06-career-replan` |
| G7 | `stage-07-b2b-api` |
| G8 (seleção) | `stage-08-innovation-differentiators` |
| HA/obs release | `stage-09-observability-release-gate` |
