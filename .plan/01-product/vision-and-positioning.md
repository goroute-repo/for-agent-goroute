# Visão e posicionamento do produto GoRoute

**Persona:** Planner  
**Âncoras:** `PROJETO.md`, `.specs/00-platform-overview.spec.md`, pesquisa em `../00-market/`

---

## 1. Objetivo de produto

Construir a plataforma de referência para **direcionamento e mapeamento efetivo de carreira**, reduzindo a incerteza causada por cargos e skills novos, através de:

1. **Mapa vivo** de competências (grafo com pré-requisitos e estado do usuário).
2. **Evidência de mercado** (demanda de skills / vagas).
3. **Plano executável** (ciclo semanal).
4. **Ponte para oportunidade** (match explicável + ofertas/candidaturas).
5. **Espelho empresa** (vagas estruturadas por skills + descoberta de candidatos).

---

## 2. Proposta de valor (one-liner)

> **GoRoute** transforma “o que estudar?” em um roadmap personalizado ligado ao que o mercado pede — e ao que falta para a próxima vaga ou promoção.

---

## 3. Personas

| Persona | Dor | Resultado esperado |
|---------|-----|-------------------|
| Profissional | Não sabe o caminho nem o gap real | Vê origem → progresso → alvo; executa semana; candidata com critério |
| Empresa | Currículos opacos; fit subjetivo | Publica vaga por skills; filtra por match %; convida candidatos |

Roles no sistema: `professional` | `company` (ver auth + guards BFF).

---

## 4. Posicionamento vs. mercado

| Não somos | Somos |
|-----------|--------|
| Só LMS / biblioteca de cursos | Camada de **direção + grafo + match** |
| Só job board | Marketplace **skills-first** com % auditável |
| Roadmap estático comunitário | Roadmap **pessoal** + demand scores + weekly plan |
| Só HR enterprise pathing | B2C forte + B2B incremental (API/webhooks) |

---

## 5. Princípios de produto (anti-scope-creep)

1. Todo nó do mapa deve explicar: skills, recursos, projeto, esforço, ligação a vagas quando houver.
2. Match é **explicável** (skills atendidas/faltantes) antes de ser “mágico”.
3. Conteúdo de curso pode ser link externo; o core é o **grafo e o progresso**.
4. Auth e dados sensíveis ficam no padrão já implementado (cookie + mapper) — ver anchors.
5. Inovação (Career Twin, heatmap, etc.) só após fechar o loop P0 de jornada + marketplace.

---

## 6. Fora de escopo global (até liberação explícita)

- Kubernetes/Istio/Kafka/API Gateway dedicado como requisito de MVP.
- Scraping massivo de vagas externas no MVP.
- Substituir Alura/Coursera como LMS completo.
- App mobile feature-complete (SPEC-15) antes do loop web P0.
- Rewrites de stack (trocar Angular/Nest/Neo4j).

Detalhes técnicos de arquitetura: `PROJETO.md` + `.specs/16-architecture.spec.md` (não duplicar aqui).

---

## 7. Critérios de sucesso (produto)

| Métrica (definição) | Sinal de sucesso |
|---------------------|------------------|
| Clareza de caminho | Usuário identifica próximo nó recomendado em &lt; 1 min no roadmap |
| Progresso mensurável | UI mostra % / o que falta até `goalSlug` |
| Ligação mercado | Vagas ordenadas por match; empresa filtra candidatos por faixa |
| Execução | Weekly plan gerado a partir do foco do roadmap |
| Confiança | Sem features “fantasma” na UI sem backend (anti-alucinação de produto) |

---

## 8. Handoff

- Gaps concretos → `../02-baseline/audit-implemented-vs-gaps.md`
- Ordem de engenharia → `../03-development/INDEX.md`
