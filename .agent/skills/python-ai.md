---
name: python-ai-goroute
description: >-
  Diretrizes Python para serviços de IA/NLP do GoRoute — goroute-nlp, extração de
  skills, integração LLM (mentor BYOK/managed), pipelines assíncronos e segurança.
  Use quando o usuário mencionar Python, IA, LLM, NLP, FastAPI, Flask, spaCy,
  OpenAI, Gemini, embeddings ou goroute-nlp.
allowed-tools: Read Write Edit Glob Grep Bash
---

# Skill: Python — IA e NLP (GoRoute)

## Objetivo

Padronizar desenvolvimento **Python** para:

- **`goroute-nlp`** — extração de skills (`POST /extract-skills`)
- **Pipelines de IA** consumidos pelo BFF (`goroute-api`) — mentor, futura extração de vagas
- Alinhamento a **SPEC-03 RS-08** (off / managed / BYOK) e **SPEC-12** (catálogo de skills)

---

# Escopo no repositório

| Componente | Stack atual | Evolução |
|------------|-------------|----------|
| `goroute-nlp/` | Flask, regras + SpaCy opcional | Manter; evoluir para FastAPI se novo serviço |
| `jobs-market` (BFF) | Chama NLP via HTTP | BullMQ + `NlpClientService` |
| Mentor (BFF) | Gemini em TypeScript | Adapters Python **opcionais** para microserviço LLM |

---

# 1. Arquitetura de serviços Python

## Serviço NLP (existente)

```text
goroute-nlp/
  app.py              # Flask — /health, /extract-skills
  skills.json         # catálogo alinhado a skill_references
  requirements.txt
  requirements-spacy.txt
  Dockerfile
```

**Contrato normativo:**

```http
POST /extract-skills
Content-Type: application/json

{ "text": "string" }

→ 200 { "skills": ["react", "typescript", ...] }
```

- Slugs **somente** do catálogo (`skills.json` / `skill_references`)
- Texto máx. **100_000** chars (truncar com log)
- `USE_SPACY=1` ativa SpaCy; fallback para regras em falha

## Novo serviço LLM (se extraído do BFF)

```text
goroute-llm/          # nome sugerido
  app/
    main.py           # FastAPI
    providers/        # gemini, openai, azure
    schemas/
    routes/
    security/
  tests/
  pyproject.toml
```

- **FastAPI** para APIs novas; Flask aceitável apenas em `goroute-nlp` legado
- Um módulo `providers/` com interface comum — espelhar Apêndice B SPEC-03

---

# 2. Abstração de provedor LLM

```python
from typing import Protocol
from pydantic import BaseModel

class MentorAnswer(BaseModel):
    summary: str
    gaps: list[dict]
    next_challenge: dict | None

class LlmProvider(Protocol):
    async def complete_json(
        self,
        *,
        system: str,
        user: str,
        schema: type[BaseModel],
        timeout_s: float = 15.0,
    ) -> BaseModel: ...
```

## Provedores

| Provider | Env / credencial | Notas |
|----------|------------------|-------|
| `google_gemini` | API key header (não query string em prod) | `response_mime_type: application/json` |
| `openai` | `OPENAI_API_KEY` ou BYOK do request | `response_format` JSON |
| `azure_openai` | endpoint + deployment + key | Base URL do cliente |

## Regras (SPEC-03)

- Modo **off** → não chamar este serviço; BFF usa heurística
- **BYOK** → credencial no body interno (serviço a serviço), nunca logar
- Snapshot roadmap ≤ **24 nós** no prompt
- `summary` ≤ **350** caracteres na validação Pydantic
- Timeout **15s**; falha → BFF faz fallback heurístico

---

# 3. Segurança IA (P0)

| Ameaça | Mitigação |
|--------|-----------|
| Prompt injection | System prompt fixo; `question` sanitizada (len, strip control chars) |
| Exfiltração catálogo | Não enviar lista completa de slugs ao LLM |
| SSRF | Serviço LLM **não** faz fetch de URLs do usuário |
| Secrets | `os.environ` / secret manager; nunca commitar `.env` |
| Logging | Logar `request_id`, provider, latency — **não** prompt/response completos em prod |
| PII | Não persistir conversas BYOK além do necessário |

---

# 4. NLP — extração de skills (sem LLM generativo no P0)

## Ordem de resolução

1. Regras + regex (`extract_with_rules`) — rápido, determinístico
2. SpaCy `PhraseMatcher` se `USE_SPACY=1`
3. **P1 LLM:** sugerir slugs → validar contra catálogo → humano ou auto se confiança > limiar

## Validação pós-extração

```python
def filter_valid_slugs(slugs: list[str], catalog: set[str]) -> list[str]:
    return list(dict.fromkeys(s for s in slugs if s in catalog))
```

Slugs inválidos → descartar (nunca inventar slug no NLP).

---

# 5. Stack e qualidade de código

## Versões

- Python **3.11+**
- `pyproject.toml` com `[project]` e dependências pinadas

## Ferramentas

| Ferramenta | Uso |
|------------|-----|
| **ruff** | lint + format |
| **mypy** | `strict = true` em código novo |
| **pytest** | unit + integration |
| **httpx** | test client FastAPI / chamadas async |
| **pydantic v2** | schemas request/response |

## Estrutura de testes

```text
tests/
  unit/
    test_extract_rules.py
    test_slug_validation.py
  integration/
    test_extract_skills_api.py
```

- Fixtures com textos de vagas reais anonimizados
- Mock de APIs LLM em testes — sem chamadas pagas em CI

---

# 6. FastAPI (serviços novos)

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, Field

app = FastAPI(title="goroute-nlp", version="1.0.0")

class ExtractRequest(BaseModel):
    text: str = Field(max_length=100_000)

class ExtractResponse(BaseModel):
    skills: list[str]

@app.post("/extract-skills", response_model=ExtractResponse)
async def extract_skills(body: ExtractRequest) -> ExtractResponse:
    slugs = await run_extraction(body.text)
    return ExtractResponse(skills=slugs)
```

- `lifespan` para warm-up SpaCy
- `/health` com versão e `skills_count`
- Middleware de `request_id`
- Rate limit por IP (slowapi ou proxy nginx)

---

# 7. Integração com BFF (NestJS)

| Fluxo | Direção |
|-------|---------|
| Job listings worker | BFF → `NLP_SERVICE_URL/extract-skills` |
| Mentor | Hoje no BFF; opcional BFF → `goroute-llm/mentor/analyze` |
| Demand score | Neo4j no BFF — Python **não** duplica grafo |

**Contrato:** JSON UTF-8; erros `{ "detail": "..." }` ou `{ "code": "...", "message": "..." }` alinhado SPEC-18.

---

# 8. Embeddings e matching avançado (P2 — SPEC-21 INN-12)

- Armazenar embeddings em pgvector ou serviço dedicado — **não** no hot path de login
- Batch offline; versionar modelo (`text-embedding-3-small`, etc.)
- Documentar drift quando o catálogo de skills mudar

---

# 9. Docker e deploy

```dockerfile
# Multi-stage; usuário non-root
# HEALTHCHECK GET /health
# PORT via env
```

- Imagem slim (`python:3.11-slim`)
- SpaCy em imagem separada ou optional build arg
- Secrets via orchestrator — não `ENV` com keys em Dockerfile

---

# 10. Variáveis de ambiente

| Variável | Serviço | Descrição |
|----------|---------|-----------|
| `PORT` | nlp | default 5000 |
| `USE_SPACY` | nlp | `0` \| `1` |
| `GEMINI_API_KEY` | llm managed | só servidor |
| `LOG_LEVEL` | todos | `INFO` prod |

---

# 11. Anti-patterns proibidos

- LLM para decidir match % ou elegibilidade de vaga (deve ser determinístico no BFF)
- Retornar slugs fora do catálogo
- `eval()` / `exec()` em inputs
- Pickle de objetos não confiáveis
- Chamar LLM sem timeout
- Logar API keys ou prompts com PII
- `requirements.txt` sem pins em produção
- Bloquear event loop com código CPU pesado — usar `run_in_executor`

---

# 12. Checklist PR (Python IA/NLP)

- [ ] Slugs validados contra catálogo
- [ ] Testes pytest para casos felizes e edge (texto vazio, slug inválido)
- [ ] Sem secrets no diff
- [ ] Timeout em chamadas LLM externas
- [ ] Contrato HTTP documentado (OpenAPI se FastAPI)
- [ ] Ruff + mypy passando

---

# Exemplo de prompt para IA

```txt
Ao gerar código Python para GoRoute (IA/NLP):

- Pydantic v2 para todos os payloads
- FastAPI para APIs novas; manter Flask só em goroute-nlp se incremental
- Extração de skills: regras/SpaCy primeiro; LLM só com validação no catálogo
- Provedores LLM via Protocol/ABC; JSON schema estrito; timeout 15s
- Nunca logar secrets nem prompts com PII
- pytest + httpx para rotas
- ruff + mypy strict em código novo
- Alinhar slugs a skill_references (SPEC-12)
- Mentor: snapshot pequeno, fallback heurístico no BFF (SPEC-03 RS-08)
```
