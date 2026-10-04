# Stage 08 — critérios locais (SPEC-21 não tem RF/CA)

## INN-01 Career Twin (primário)

| ID | Critério |
|----|----------|
| CA-0801 | `POST` simulate retorna comparação current vs simulated (ETA/progress/gaps) **sem** persistir goal, Neo4j TARGETS, journey ou weekly |
| CA-0802 | UI “Simular” na carreira/settings mostra delta; “Aplicar” usa PATCH replan existente (409 confirm intacto) |
| CA-0803 | Cargo alvo inválido → 400 `INVALID_CAREER_ROLE` |
| CA-0804 | Testes unitários: simulate não chama sync/recalc/persist |

## INN-03 Match narrativo (secundário, se entregue)

| ID | Critério |
|----|----------|
| CA-0811 | Endpoint explica % match + até 2 nós/skills que fecham gap |
| CA-0812 | Requer mentorMode `managed` ou `byok` (RF-036); senão heurística declarada `source: heuristic` |
