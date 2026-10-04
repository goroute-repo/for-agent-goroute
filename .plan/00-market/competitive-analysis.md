# Pesquisa de mercado — Roadmaps e trilhas de carreira

**Persona:** Planner  
**Data:** 2026-07-17  
**Objetivo da pesquisa:** mapear pontos fortes/fracos dos serviços existentes para posicionar o **GoRoute** como resposta à dor de falta de direcionamento e mapeamento efetivo de carreira (especialmente com proliferação de cargos novos).

---

## 1. Dor de mercado (problema)

| Sintoma | Impacto |
|---------|---------|
| Cargos e stacks novos surgem rápido | Incerteza sobre “o que estudar / em que ordem” |
| Conteúdo abundante, direção escassa | Paralisia de escolha; abandono de trilhas |
| Vagas em texto livre | Difícil saber o gap real vs. perfil |
| Roadmaps genéricos (iguais para todos) | Baixa aderência ao contexto individual e ao mercado local |
| Separação entre aprender e ser contratado | Curso concluído ≠ match com vaga |

**Hipótese de valor GoRoute (a validar em produto):** conectar **skills do indivíduo**, **grafo de pré-requisitos**, **demanda de vagas** e **plano executável semanal** num único mapa vivo — não só uma lista de cursos.

---

## 2. Categorias de concorrentes / análogos

| Categoria | Exemplos | O que vendem |
|-----------|----------|--------------|
| A. Roadmap comunitário visual | [roadmap.sh](https://roadmap.sh/) | Mapas role/skill-based, checklists, comunidade |
| B. LMS / trilhas com conteúdo | Alura Carreiras, Rocketseat, Coursera, Pluralsight | Cursos + jornadas + certificados |
| C. Career pathing enterprise | TalentGuard, Degreed, Cornerstone, Pluralsight Skills | Paths internos, Skill IQ, mobilidade |
| D. Talent marketplace / hiring | LinkedIn, ATS + skills proxies, marketplaces skills-first | Matching de talentos / vagas |
| E. Taxonomias open | ESCO, O*NET, Nesta OJA skills | Vocabulário comum de skills/ocupações |

---

## 3. Análise por serviço (forças × fraquezas)

### 3.1 roadmap.sh

| | |
|--|--|
| **Forças** | Clareza visual; cobertura enorme de roles/skills; gratuito; atualização comunitária; baixo atrito para iniciar |
| **Fraquezas** | Pouco **personalizado** ao perfil real; fraca ligação a **vagas/match %**; progresso superficial (checklist); sem lado empresa/marketplace nativo; não modela demanda de mercado por região |
| **Ameaça ao GoRoute** | Default mental de “roadmap = site estático famoso” |
| **Oportunidade GoRoute** | Ser o “roadmap.sh **ligado ao meu perfil + mercado + vagas**” |

### 3.2 Alura (Carreiras / Trilhas)

| | |
|--|--|
| **Forças** | Jornada guiada PT-BR; checkpoints; conteúdo próprio; IA (Luri); comunidade; amplitude de temas |
| **Fraquezas** | Centro de gravidade = **consumo de curso**, não grafo de skills ↔ vagas; match empregador limitado; catálogo fechado na plataforma |
| **Oportunidade** | GoRoute como camada de **direção + evidência de mercado**, podendo referenciar recursos externos (não precisar ser LMS completo no MVP) |

### 3.3 Rocketseat

| | |
|--|--|
| **Forças** | Foco empregabilidade; stacks modernas; imersão prática; comunidade forte |
| **Fraquezas** | Trilha estreita (web-centric); pouco career pathing multi-cargo; sem marketplace bidirecional skills-first |
| **Oportunidade** | GoRoute atende quem precisa **mapear carreira além de um bootcamp** |

### 3.4 Pluralsight Skills / Coursera

| | |
|--|--|
| **Forças** | Role paths; assessments (Skill IQ); credenciais reconhecidas (Coursera); profundidade enterprise |
| **Fraquezas** | Caros; UX “catálogo de aulas”; matching de vagas não é o core; pouco grafo vivo com demanda de job postings |
| **Oportunidade** | GoRoute: **explainable match** + weekly plan + grafo Neo4j com demand scores |

### 3.5 LinkedIn Learning / Career tools

| | |
|--|--|
| **Forças** | Dados de mercado e rede; sugestões de skills; integração perfil profissional |
| **Fraquezas** | Ruído; skills autodeclaradas sem grafo de pré-requisitos rigoroso; roadmap pouco “executável semana a semana” |
| **Oportunidade** | GoRoute: progresso por nó + projeto prático + % match auditável |

### 3.6 TalentGuard / Degreed / career pathing B2B

| | |
|--|--|
| **Forças** | Paths por role interno; readiness; governança HR |
| **Fraquezas** | Foco **intra-empresa**; pouco marketplace externo; implementação pesada; não resolve dor do profissional autônomo |
| **Oportunidade** | GoRoute B2C forte + B2B (vagas, API, trilhas por empresa) mais leve e skills-first |

### 3.7 Trybe / bootcamps de transição

| | |
|--|--|
| **Forças** | Acompanhamento humano; foco emprego |
| **Fraquezas** | Escala cara; trilha única; pós-conclusão o mapa some |
| **Oportunidade** | GoRoute como **mapa contínuo** pós-formação |

---

## 4. Matriz sintética: gaps do mercado × resposta GoRoute

| Gap dominante | Resposta GoRoute (produto) | Evidência no repo |
|---------------|---------------------------|-------------------|
| Roadmap genérico | Grafo personalizado + estados de nó + Time Machine | `goroute-api` roadmap + Neo4j; UI `/dashboard/roadmap` |
| Sem ligação vaga↔skill | Match % por skill/nível | `job-match.service.ts` |
| Só curso, sem execução | Weekly plan (aprender → projeto → praticar) | módulo `weekly-plan` |
| Empresa sem critério objetivo | Vagas com skills obrigatórias + candidatos | jobs + company UI |
| Cargos novos / incerteza | Catálogo de cargos + trilha; demand scores | `career-catalog`, `jobs-market` |
| Histórico da jornada fraco no mercado | Histórico origem→alvo + progresso “o que falta” | SPEC-04 — **ainda gap** (prioridade produto) |

---

## 5. Diferenciadores inovadores (priorização Planner)

Ordem sugerida para máximo impacto na dor (não é ordem de engenharia — ver `03-development/`):

| Prioridade produto | Diferencial | Por quê |
|--------------------|-------------|---------|
| P0 produto | Progresso “daqui até o alvo” + histórico de jornada | Fecha a dor de **direcionamento contínuo** |
| P0 produto | Loop completo vaga↔candidatura↔oferta | Transforma mapa em **oportunidade** |
| P1 | Replan mid-path / Career Twin (simular mudança) | Responde à incerteza de cargos novos |
| P1 | Match narrativo (IA explica o %) | Confiança e clareza |
| P2 | Trilhas por empresa + API B2B | Escala B2B |
| P2 | Integração LMS / prova de skill | Ecossistema |

Detalhe de ideias: `PROJETO.md` § Capacidades inovadoras; `.specs/21-innovation-backlog.spec.md`.

---

## 6. Riscos competitivos

| Risco | Mitigação no plano |
|-------|-------------------|
| Ser visto como “mais um checklist tipo roadmap.sh” | Sempre amarrar UI a **perfil + match + weekly plan** |
| Competir com LMS em conteúdo | Não virar Alura; curar links/projetos por nó; opcional LMS depois |
| Enterprise pathing pesado | Modular monolith; B2B API incremental |
| Alucinação de features “já temos X” | Baseline em `02-baseline/` + anchors |

---

## 7. Conclusão da pesquisa

1. O mercado cobre bem **conteúdo** e **mapas estáticos**; cobre mal **mapa vivo + mercado + contratação skills-first** num único produto acessível ao profissional.
2. GoRoute já tem **núcleo técnico** alinhado a essa lacuna (grafo, match, weekly plan, dual persona profissional/empresa).
3. Lacunas de produto que mais doem na proposta de valor: **histórico/progresso de jornada**, **fechamento do loop de ofertas/candidaturas**, **replan**, e **hardening produção** para escalar com confiança.
4. O planejamento de desenvolvimento em `03-development/` prioriza fechar esses gaps sem inventar stack paralela.

**Referências usadas nesta pesquisa:** roadmap.sh; Alura Carreiras; comparativos Alura/Rocketseat 2026; Pluralsight vs Coursera; career pathing software (TalentGuard, Degreed, Pluralsight Skills); `PROJETO.md` § Práticas de mercado.
