# Developer Security Gate — stage-11

Preenchido em 2026-07-17 (Persona Developer).

**Skills:** `secure-service.md` §5 Python

---

## Identificação

| Campo | Valor |
|-------|-------|
| Etapa | `stage-11-nlp-ingestion-assist` |
| Pacotes | `goroute-api`, `goroute-nlp` |
| SPECs | 12 (RF-132), 13 |
| Dados sensíveis | [x] PII (texto vaga) [ ] Pagamento [ ] Tokens IA [x] API keys |

---

## Controles

| # | Controle | OK | N/A | Evidência |
|---|----------|----|-----|-----------|
| 1 | NLP não público LAN | ☑ | ☐ | compose `127.0.0.1:5000` |
| 2 | Sem proxy frontend NLP | ☑ | ☐ | — |
| 3 | Catalog authority BFF | ☑ | ☐ | SkillCatalogFilterService |
| 4 | Sem log de texto | ☑ | ☐ | só mensagem de erro |
| 5 | Max text / timeout | ☑ | ☐ | 20k / 8s |
| 6 | Service auth opcional | ☑ | ☐ | INTERNAL_API_KEY |
| 7 | Fail soft listings | ☑ | ☐ | soft=[] |
| 8 | Testes | ☑ | ☐ | Jest + pytest |

---

## Veredicto Developer

- [x] **PASS** — pronto para Q.A.
