# Skills de Segurança (revisão / threat modeling)

> **Implementação segura (Developer):** preferir [`secure-service.md`](./secure-service.md) e [`secure-payments.md`](./secure-payments.md) + [`checklists/developer-security-gate.md`](./checklists/developer-security-gate.md).  
> Este arquivo foca em **review**, OWASP checklist amplo e **threat modeling**.  
> Guardrails: [`security.md`](../guardrails/security.md). Contexto: GoRoute auth + BFF + Stripe + mentor/NLP.

## security-review (Sentry-Style)

Skill focada em identificar vulnerabilidades realmente exploráveis com alta confiança, priorizando precisão e redução de falsos positivos.

Objetivos principais:
- detectar vulnerabilidades exploráveis;
- diferenciar dados trusted e untrusted;
- reduzir falsos positivos;
- validar impacto real da exploração;
- priorizar vulnerabilidades críticas.

A skill utiliza abordagem baseada em fluxo de dados e contexto de execução, analisando:
- entrada controlada por usuários;
- fluxo de autenticação;
- autorização;
- serialização;
- execução dinâmica;
- comunicação entre serviços;
- exposição de segredos;
- manipulação de arquivos;
- consultas a banco de dados;
- renderização HTML.

Foco principal:
- reportar apenas HIGH CONFIDENCE findings;
- ignorar problemas teóricos sem exploração prática;
- evitar alertas irrelevantes em frameworks modernos.

Principais categorias analisadas:
- SQL Injection
- Command Injection
- SSRF
- XSS
- Path Traversal
- Insecure Deserialization
- Broken Authentication
- Broken Authorization
- Secret Exposure
- Unsafe File Upload
- RCE (Remote Code Execution)

Diretrizes:
- considerar contexto real da aplicação;
- verificar sanitização efetiva;
- validar origem dos dados;
- identificar trust boundaries;
- evitar duplicidade de findings;
- priorizar impacto explorável.

Quando utilizar:
- revisão de pull requests;
- auditoria de código;
- análise de APIs;
- revisão de autenticação;
- revisão de middleware;
- análise de pipelines CI/CD;
- hardening de aplicações.

---

## owasp-security (OWASP Complete)

Skill abrangente baseada em práticas modernas da OWASP para desenvolvimento seguro, revisão de código e arquitetura defensiva.

Inclui referências e práticas relacionadas a:
- OWASP Top 10;
- ASVS 5.0;
- Secure Coding Practices;
- Security by Design;
- Defense in Depth;
- Zero Trust;
- AI Security Risks.

Áreas cobertas:
- autenticação;
- autorização;
- criptografia;
- gerenciamento de sessão;
- proteção de APIs;
- validação de entrada;
- segurança de banco de dados;
- segurança em microsserviços;
- segurança em cloud;
- segurança em IA generativa;
- segurança em pipelines DevOps.

A skill cobre múltiplas linguagens e frameworks:
- JavaScript
- TypeScript
- Python
- Java
- Go
- Rust
- PHP
- C#
- Ruby
- Kotlin
- Swift
- Node.js
- React
- Next.js
- NestJS
- Spring Boot
- Django
- Flask
- FastAPI

Checklist principal:
- validação de entrada;
- escaping contextual;
- least privilege;
- proteção CSRF;
- proteção XSS;
- proteção SSRF;
- proteção contra brute force;
- rate limiting;
- MFA;
- secrets management;
- logging seguro;
- auditoria;
- monitoramento;
- criptografia em trânsito;
- criptografia em repouso.

Padrões inseguros identificados:
- hardcoded secrets;
- queries inseguras;
- autenticação fraca;
- autorização implícita;
- exposição de dados sensíveis;
- logs inseguros;
- deserialização insegura;
- uso inseguro de IA;
- configuração insegura de cloud.

Quando utilizar:
- code review;
- implementação de autenticação;
- criação de APIs;
- análise de arquitetura;
- revisão DevSecOps;
- compliance de segurança;
- revisão de aplicações SaaS;
- análise de agentes de IA.

---

## security-threat-modeling (Threat Modeling)

Skill focada em modelagem de ameaças antes da implementação.

Objetivos:
- identificar riscos antecipadamente;
- mapear superfícies de ataque;
- reduzir exposição;
- prever vetores de exploração;
- definir controles preventivos.

Metodologias utilizadas:
- STRIDE
- Attack Trees
- Data Flow Diagrams (DFD)
- Trust Boundary Mapping
- Abuse Cases
- Risk Assessment

Componentes analisados:
- autenticação;
- autorização;
- APIs;
- banco de dados;
- filas;
- microsserviços;
- integrações externas;
- IA generativa;
- storage;
- pipelines;
- infraestrutura cloud.

Categorias STRIDE:
- Spoofing
- Tampering
- Repudiation
- Information Disclosure
- Denial of Service
- Elevation of Privilege

Fluxo de análise:
1. identificar ativos críticos;
2. mapear fluxos de dados;
3. identificar trust boundaries;
4. detectar ameaças;
5. estimar impacto;
6. estimar probabilidade;
7. definir mitigação;
8. priorizar riscos.

A skill também analisa:
- supply chain;
- dependências externas;
- riscos de IA;
- engenharia social;
- ataques internos;
- movimentação lateral;
- privilege escalation.

Entregáveis:
- mapa de ameaças;
- matriz de risco;
- recomendações de mitigação;
- análise de superfície de ataque;
- checklist de segurança;
- prioridades de hardening.

Quando utilizar:
- antes da implementação;
- definição de arquitetura;
- revisão de sistemas críticos;
- aplicações financeiras;
- aplicações SaaS;
- plataformas multi-tenant;
- aplicações com IA;
- ambientes regulados.