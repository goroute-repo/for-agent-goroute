# Stage 04 — Loop marketplace (candidaturas, minMatch, ofertas)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-04-marketplace-loop` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-01` |
| **Stack** | `goroute-api`, `goroute-frontend` |
| **Dor de mercado** | Roadmap sem ponte para oportunidade real |

## Meta

Fechar o ciclo skills → vaga → candidatura/oferta, com match explicável e regras de elegibilidade do SPEC.

## Ler antes

1. `.specs/06-jobs-marketplace.spec.md`
2. `.specs/07-matching-candidatures.spec.md`
3. `.specs/08-candidate-offers.spec.md`
4. `goroute-api/src/jobs/**` — o que já existe (match, candidates, webhooks)
5. Frontend: `JobsMarketplace`, `CompanyJobs`, rotas `opportunities`

## In scope

- [ ] Confirmar estado atual de `job_applications` / `job_offers` / `select-candidates` / `minMatch` no código
- [ ] Completar **somente o que faltar** para:
  - Profissional: listar vagas, `eligibleOnly`, `POST apply` só com 100% (regra SPEC)
  - Empresa: candidatos com `minMatch` 50–100; selecionar e enviar oferta
  - Profissional: ver ofertas em `/dashboard/opportunities` (criar se MISSING)
- [ ] Notificações in-app se já houver módulo — estender, não recriar
- [ ] Testes API + UI + MCP fluxos empresa e profissional

## Out of scope

- API B2B pública (stage-07)
- Ingestão IA de descrição de vaga
- Alterar fórmula de match v1 sem RF no SPEC (pesos v2 = inovação futura)

## Entregáveis

| Ação | Item |
|------|------|
| MODIFICAR/A CRIAR | Entities/migrations applications & offers |
| MODIFICAR | Controllers/services jobs |
| MODIFICAR/A CRIAR | Telas opportunities + filtros company |
| A CRIAR | Testes de regra 100% apply e minMatch |

## Critérios de aceite

1. Empresa filtra candidatos por faixa de match configurável (50–100) conforme SPEC.
2. Profissional só candidata-se quando elegível (100%), com erro claro caso contrário.
3. Oferta empresa aparece na área do profissional; estados aceite/recusa se SPEC exigir nesta fase.
4. Match % permanece explicável (skills faltantes/atendidas).

## Validation (Q.A.)

```bash
cd goroute-api && npm test
cd goroute-frontend && npm test
```

MCP: fluxo empresa (criar vaga → candidatos → oferta) e profissional (ver vaga → apply / ver opportunity).

## Riscos / anti-alucinação

- Antes de criar tabelas, **Grep** se entities já existem com outro nome.
- Não relaxar apply 100% “porque a empresa usa 50%”.
- Guards de role: company vs professional obrigatórios.

## Handoff Q.A.

Atualizar INDEX.
