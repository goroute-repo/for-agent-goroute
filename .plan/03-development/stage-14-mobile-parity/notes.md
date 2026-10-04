# Stage 14 — notas Developer (2026-07-17)

## Fatia P0 travada

| Item | Status |
|------|--------|
| RF-150 Login profissional | Entregue |
| RF-151 Headers mapper/session | Entregue |
| RS-01 Secure storage | Entregue (`flutter_secure_storage` / MemoryKv tests) |
| Roadmap `GET /roadmap/me` | Entregue (lista de progresso) |
| Push / deep links | **Fora** (documentado) |
| CA-150 mobileAppKey UI empresa | **Fora** — chave ainda no auth/env; sem API de emissão no BFF |
| CA-151 Push background | **Fora** — infra inexistente |

## Auth contrato mobile

`X-Application-From: goroute-mobile` → login body inclui `sessionId` + `mapApiHash` (sem JWT). Web não recebe `sessionId`.

## Testes

- `goroute-mobile` `flutter test` — **4 PASS**
- `goroute-service-auth` mobile-login util — **2 PASS** (suite auth completa re-executada)

## Handoff

INDEX → `qa`
