# Stage 11 — NLP assistido para ingestão de skills (escopo mínimo)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-11-nlp-ingestion-assist` |
| **Status** | `done` |
| **ownerPersona** | `developer` |
| **Depende de** | `stage-10` |
| **Stack** | `goroute-api`, `goroute-nlp` |
| **SPECs** | 12 (RF-132), 13 |

## Meta

Usar NLP **apenas** como assistente de sugestão de skill slugs a partir de texto de vaga/import — com validação obrigatória no catálogo e rede interna segura.

## Ler antes

1. `.plan/02-baseline/coverage-and-spec-alignment.md` §4 (decisão NLP)
2. `.specs/12-skills-catalog-sync.spec.md` RF-132
3. `.specs/13-jobs-storage-ingestion.spec.md`
4. `.agent/skills/secure-service.md` §5 (Python)
5. Código: `NlpClientService`, `JobListingsProcessor`, `goroute-nlp`

## In scope

- [ ] Contrato BFF → NLP: timeout, tamanho max, auth de serviço se já houver padrão
- [ ] Resposta NLP = sugestões; **BFF rejeita slug fora de `skill_references`**
- [ ] Fluxo assistido (draft) para revisão humana antes de publicar — se SPEC exigir
- [ ] NLP **não** exposto publicamente (compose/internal)
- [ ] Testes: slug inválido nunca persiste; timeout degradado com erro controlado
- [ ] Security gate

## Out of scope

- Treinar modelo próprio
- Substituir matching por embeddings
- UI marketing de “IA” sem modo off/managed/BYOK

## NLP é necessário?

**Sim neste escopo** (ingestão). **Não** para o grafo de carreira do profissional.

## Critérios de aceite

1. Texto → sugestões → só slugs canônicos no banco.
2. Queda do NLP não corrompe jobs (fail soft + mensagem).
3. Sem PII/prompts em logs.

## Validation (Q.A.)

```bash
cd goroute-api && npm test
cd goroute-nlp && pytest   # se houver testes
```

## Handoff Q.A.

Atualizar INDEX.
