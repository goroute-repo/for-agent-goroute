---
name: output-format
description: Define o formato obrigatório de saída para relatórios de revisão de código, segurança e qualidade.
allowed-tools:
  - Read
  - Grep
  - Glob
---

# Output Format — Formato Obrigatório para Relatórios

Todo relatório de revisão **formal** (code-review / validate / security) DEVE seguir este formato Markdown.

A persona **Q.A.** pode usar o **veredito curto** (§11) no dia a dia; reports em `reports/<TASK_ID>/` usam o template completo.

O objetivo é garantir:
- consistência;
- rastreabilidade;
- clareza;
- auditabilidade;
- padronização de findings.

---

# 1. Estrutura Obrigatória do Relatório

Todo relatório deve seguir exatamente esta hierarquia:

```markdown
# 📋 Code Review Report

**Reviewer:** [Nome do agente/revisor]
**Date:** [YYYY-MM-DD]
**Scope:** [arquivos, commit hash ou PR]
**Review Type:** [Security / Quality / Performance / Architecture]
**Status:** [APPROVED / PENDING / CHANGES_REQUESTED / BLOCKED]

---

## 📊 Executive Summary

[Resumo executivo]

---

## 📈 Risk Summary

| Severity | Count |
|---|---|
| 🔴 CRITICAL | 0 |
| 🟠 MAJOR | 0 |
| 🟡 MINOR | 0 |
| 🔵 SUGGESTION | 0 |

---

## 🔴 Critical Issues (0)

[Listagem de issues]

---

## 🟠 Major Issues (0)

[Listagem de issues]

---

## 🟡 Minor Issues (0)

[Listagem de issues]

---

## 🔵 Suggestions (0)

[Listagem de sugestões]

---

## ✅ What Went Well

[Listagem de pontos positivos]

---

## 📈 Recommendations

### Short-term (Before Merge)

[Ações obrigatórias]

### Medium-term

[Melhorias importantes]

### Long-term (Technical Debt)

[Débitos técnicos]

---

## 🧪 Validation Performed

- [ ] Build executado
- [ ] Testes executados
- [ ] Lint executado
- [ ] Security review executado
- [ ] Performance review executado

---

## 🔗 References

[Listagem de referências]

---

## 🏁 Final Decision

[Resumo final da aprovação/reprovação]
```

---

# 2. Template Obrigatório para Cada Issue

Toda issue DEVE seguir exatamente este formato:

```markdown
### [File: `caminho/do/arquivo.ts:linha`]

- **Issue Type:** [Security / Quality / Performance / Architecture / Maintainability]
- **Severity:** [CRITICAL / MAJOR / MINOR / SUGGESTION]
- **Confidence:** [HIGH / MEDIUM / LOW]
- **Exploitability:** [HIGH / MEDIUM / LOW]
- **Rule Violated:** [Nome da regra]
- **Impact:**
  [Impacto técnico ou de negócio]

- **Why It Matters:**
  [Explicação objetiva do problema]

- **Evidence:**
  [Explicação técnica da evidência encontrada]

- **Current Code:**

  ```typescript
  // ❌ Código problemático
  ```

- **Suggested Fix:**

  ```typescript
  // ✅ Correção proposta
  ```

- **Validation Steps:**
  1. [Passo 1]
  2. [Passo 2]

- **References:**
  - [OWASP / documentação oficial / guideline]
```

---

# 3. Regras de Severidade

## 🔴 CRITICAL

Problemas que:
- permitem RCE;
- permitem auth bypass;
- expõem secrets;
- permitem SQL Injection;
- permitem privilege escalation;
- comprometem dados críticos.

### Regra
Bloqueia merge imediatamente.

---

## 🟠 MAJOR

Problemas que:
- afetam segurança;
- afetam integridade;
- introduzem regressões relevantes;
- afetam performance criticamente;
- comprometem confiabilidade.

### Regra
Deve ser corrigido antes do merge.

---

## 🟡 MINOR

Problemas que:
- afetam qualidade;
- aumentam débito técnico;
- reduzem legibilidade;
- reduzem observabilidade;
- causam pequenos riscos.

### Regra
Pode ser corrigido após merge.

---

## 🔵 SUGGESTION

Melhorias opcionais:
- refactors;
- simplificações;
- otimizações futuras;
- melhorias arquiteturais.

### Regra
Não bloqueia merge.

---

# 4. Regras de Confidence

## HIGH

- vulnerabilidade confirmada;
- problema reproduzível;
- evidência concreta;
- exploitabilidade clara.

## MEDIUM

- forte evidência;
- risco plausível;
- depende de contexto adicional.

## LOW

- hipótese;
- possível melhoria;
- evidência insuficiente.

---

# 5. Regras Gerais do Relatório

## Obrigatório

- incluir caminho do arquivo;
- incluir linha;
- incluir evidência;
- incluir código atual;
- incluir solução proposta;
- incluir justificativa técnica.

## Proibido

- issues vagas;
- opiniões sem evidência;
- sugestões genéricas;
- ausência de contexto;
- findings sem impacto descrito.

---

# 6. Diretrizes de Escrita

## O relatório deve ser:

- técnico;
- objetivo;
- auditável;
- reproduzível;
- rastreável.

## Evitar:

- linguagem emocional;
- opiniões subjetivas;
- ambiguidades;
- texto excessivamente longo;
- explicações genéricas.

---

# 7. Exemplo Completo de Relatório

````markdown
# 📋 Code Review Report

**Reviewer:** Security Agent v2.0
**Date:** 2026-05-17
**Scope:** `src/auth/jwt.strategy.ts`
**Review Type:** Security
**Status:** CHANGES_REQUESTED

---

## 📊 Executive Summary

Foi encontrada uma vulnerabilidade MAJOR relacionada à validação incompleta de JWT.

O sistema aceita tokens sem validação explícita de `issuer` e `audience`, permitindo potencial aceitação de tokens emitidos por provedores não confiáveis.

---

## 📈 Risk Summary

| Severity | Count |
|---|---|
| 🔴 CRITICAL | 0 |
| 🟠 MAJOR | 1 |
| 🟡 MINOR | 0 |
| 🔵 SUGGESTION | 0 |

---

## 🟠 Major Issues (1)

### [File: `src/auth/jwt.strategy.ts:45`]

- **Issue Type:** Security
- **Severity:** MAJOR
- **Confidence:** HIGH
- **Exploitability:** MEDIUM
- **Rule Violated:** JWT issuer validation missing

- **Impact:**
  Tokens emitidos por terceiros podem ser aceitos.

- **Why It Matters:**
  A ausência de validação de issuer e audience reduz garantias de autenticidade do token.

- **Evidence:**
  O método validate() não valida claims críticas do JWT.

- **Current Code:**

  ```typescript
  async validate(payload: any) {
    return {
      userId: payload.sub
    };
  }
  ```

- **Suggested Fix:**

  ```typescript
  async validate(payload: JwtPayload) {
    if (
      payload.iss !==
      "https://auth.example.com"
    ) {
      throw new UnauthorizedException();
    }

    if (
      payload.aud !==
      "api-service"
    ) {
      throw new UnauthorizedException();
    }

    return {
      userId: payload.sub
    };
  }
  ```

- **Validation Steps:**
  1. Testar token válido
  2. Testar issuer inválido
  3. Testar audience inválido

- **References:**
  - OWASP JWT Cheat Sheet
  - RFC 7519

---

## ✅ What Went Well

- Uso correto de Guards
- Estrutura modular adequada
- Separação de responsabilidades

---

## 📈 Recommendations

### Short-term (Before Merge)

- Validar issuer
- Validar audience

### Long-term (Technical Debt)

- Implementar rotação de chaves
- Adicionar token revocation

---

## 🧪 Validation Performed

- [x] Build executado
- [x] Testes executados
- [x] Security review executado

---

## 🔗 References

- https://owasp.org/
- https://datatracker.ietf.org/doc/html/rfc7519

---

## 🏁 Final Decision

CHANGES_REQUESTED devido à ausência de validação crítica de JWT.
````

---

# 8. Regras para Ausência de Issues

Quando nenhuma issue for encontrada:

```markdown
## 🔴 Critical Issues (0)

*Nenhuma issue crítica encontrada.*

## 🟠 Major Issues (0)

*Nenhuma issue major encontrada.*

## 🟡 Minor Issues (0)

*Nenhuma issue minor encontrada.*

## 🔵 Suggestions (0)

*Nenhuma sugestão encontrada.*
```

---

# 9. Regras para Segurança de Dados

Nunca expor:

- secrets reais
- tokens reais
- credenciais reais
- dados pessoais
- payloads sensíveis

Sempre utilizar placeholders, valores mascarados e exemplos fictícios.

---

# 10. Regra Final

Todo relatório formal deve:

- ser reproduzível
- conter evidências
- possuir severidade clara
- possuir impacto claro
- possuir recomendação objetiva
- possuir rastreabilidade completa

---

# 11. Veredito curto — persona Q.A.

Para gate de fase/feature (sem report completo), use:

```markdown
# Q.A. Gate

**Task/Phase:** [id]
**Scope:** [pacotes/arquivos]
**Verdict:** PASS | FAIL | BLOCKED

## Evidence
- [ ] Unitários: `comando` → ok/fail
- [ ] UI/MCP ou e2e: [rotas/ações] → ok/fail/skip
- [ ] Security smoke: sem secrets no diff

## Failures (se FAIL)
1. [repro + severidade]

## Next
- Developer: [ações] | Planner: [ações] | Usuário: [bloqueio]
```

`PASS` não dispensa dual-review quando o workflow estiver ativo; apenas libera a fase no checklist de testes.