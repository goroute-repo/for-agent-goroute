# Q.A. Gate — stage-11-nlp-ingestion-assist

**Date:** 2026-07-17  
**Persona:** Q.A.  
**Verdict:** **PASS**

---

## Checklist DoD

| Item | Resultado |
|------|-----------|
| Jest API | **138 PASS** |
| pytest NLP | **5 PASS** |
| Security gate Developer | Presente |
| Smoke HTTP NLP | OK (`docker compose up -d python-nlp`) |

---

## Smoke — evidências

| Cenário | Resultado |
|---------|-----------|
| `GET /health` | `{ ok: true, skills: 28 }` |
| `POST /extract-skills` texto com React + madeup | `{ skills: ["react"] }` — inventado não retorna |
| Soft-fail / catalog filter BFF | Jest processor + nlp-client soft |
| Compose bind localhost | `127.0.0.1:5000` (container recriado) |

---

## Aceite

1. Texto → só slugs canônicos (NLP dict + BFF catalog filter): **OK**  
2. NLP down não corrompe jobs (soft): **OK** (Jest)  
3. Sem PII em logs de texto: **OK** (código + gate)

---

## Residual

- CSV CA-141; endpoint público suggest-skills — fora do escopo mínimo

---

## INDEX

`stage-11` → `done`
