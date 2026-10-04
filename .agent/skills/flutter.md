---
name: flutter-mobile-goroute
description: >-
  Diretrizes para desenvolvimento do app mobile GoRoute com Flutter — arquitetura,
  HTTP/auth (SPEC-01/15), segurança, performance e testes. Use quando o usuário
  mencionar Flutter, goroute-mobile, app mobile, iOS/Android ou SPEC-15.
allowed-tools: Read Write Edit Glob Grep Bash
---

# Skill: Flutter — App Mobile GoRoute

## Objetivo

Fornecer diretrizes para o cliente **`goroute-mobile`** (Flutter), alinhado a:

- **SPEC-15** — paridade de APIs com o BFF e auth
- **SPEC-01** — sessão, `X-Application-From: goroute-mobile`, `x-mapper-api`
- **SPEC-09** — workspaces profissional × empresa

Compatível com **Flutter 3.x** / Dart 3.

---

# Escopo

- Estrutura por feature (clean architecture leve)
- Estado (Riverpod recomendado)
- HTTP (Dio) + interceptors
- Auth, cookies, secure storage
- Navegação (go_router)
- i18n, temas, acessibilidade
- Testes (unit, widget, integration)
- Push notifications e deep links (P1)
- CI/CD mobile

---

# Integração GoRoute (obrigatório)

## Headers em toda chamada ao BFF

| Header | Valor |
|--------|--------|
| `X-Application-From` | `goroute-mobile` |
| `x-mapper-api` | Emitido após login (auth) — **nunca** hardcode |
| `Cookie` | Sessão httpOnly repassada se usar cookie jar / WebView bridge |

## APIs MVP (SPEC-15)

| Feature | Endpoint |
|---------|----------|
| Login | auth service (mesmas rotas web) |
| Roadmap | `GET /api/roadmap/me` |
| Vagas | `GET /api/jobs`, `GET /api/jobs/match` |
| Weekly plan | `GET /api/weekly-plan` (+ premium 403) |
| Oportunidades | `GET /api/opportunities` (quando existir) |

## Empresa — mobileAppKey

- Tela read-only para exibir/rotacionar chave (SPEC-01 RS mobile)
- Copiar para clipboard com confirmação
- **Nunca** logar a chave completa

---

# 1. Arquitetura

```text
lib/
  main.dart
  app.dart
  core/
    config/          # env, flavors (dev/stg/prod)
    network/         # Dio, interceptors, ApiException
    auth/            # session, mapper, secure storage
    routing/         # go_router
    theme/
    l10n/
    utils/
  features/
  roadmap/
    data/            # datasources, DTOs, repositories impl
    domain/          # entities, repository contracts
    presentation/    # pages, widgets, providers
  jobs/
  weekly_plan/
  auth/
  settings/
  shared/
    widgets/
    extensions/
```

## Regras

- **Presentation** não importa `dio` diretamente — só repositories
- **Domain** sem dependência de Flutter
- DTOs em `data/`; mapear para entities em `domain/`
- Uma feature = um fluxo de negócio coeso
- Evitar `lib/services/god_service.dart`

---

# 2. Estado — Riverpod

- Prefira **flutter_riverpod** + `riverpod_annotation` para providers gerados
- `AsyncValue` para loading/error/data em telas
- Providers por feature; `keepAlive` só quando necessário
- Não passar `BuildContext` para repositories

## Exemplo

```dart
@riverpod
Future<RoadmapSnapshot> roadmap(Ref ref) async {
  return ref.watch(roadmapRepositoryProvider).fetchMe();
}
```

**Alternativa:** Bloc/Cubit se o time já padronizou — manter uma abordagem por app.

---

# 3. HTTP — Dio

- `BaseOptions`: `connectTimeout`, `receiveTimeout` explícitos
- Interceptor de auth: injeta `x-mapper-api` + cookies
- Interceptor de erro: mapeia `403 PREMIUM_REQUIRED`, `ONBOARDING_REQUIRED` para UX
- Tipar responses com classes imutáveis (`freezed` + `json_serializable`)
- **Nunca** `dynamic` em parsing JSON

```dart
class ApiClient {
  ApiClient(this._dio);
  final Dio _dio;

  Future<RoadmapDto> getRoadmap() async {
    final res = await _dio.get<Map<String, dynamic>>('/api/roadmap/me');
    return RoadmapDto.fromJson(res.data!);
  }
}
```

## Erros de API GoRoute

Tratar `code` no body (SPEC-18): `INVALID_SKILL_REFERENCE`, `PROFILE_REQUIRED`, etc.

---

# 4. Segurança (P0)

| Regra | Implementação |
|-------|----------------|
| Tokens/sessão | `flutter_secure_storage` — **nunca** `SharedPreferences` para secrets |
| Certificate pinning | P1 — `dio` + `SecurityContext` em produção |
| Logs | Não logar tokens, mapper, PII |
| Deep links | Validar scheme `goroute://` — não executar rotas arbitrárias |
| WebView | Evitar para auth; preferir fluxo nativo + cookies gerenciados |
| Biometria | Opcional para reabrir app (local_auth) — não substitui MFA servidor |

---

# 5. UI / UX

- **Material 3** com tema claro/escuro (paridade web)
- Roadmap mobile: lista linear + detalhe de nó **ou** WebView do grafo apenas se performance OK (SPEC-03 UX P1)
- Estados vazios com CTA (onboarding, perfil, premium)
- Pull-to-refresh em listas (jobs, opportunities)
- Snackbar para erros recuperáveis; dialog para ações destrutivas
- Suporte a font scale e contraste (a11y)

## Navegação

- `go_router` com redirects:
  - não autenticado → login
  - `onboardingCompleted == false` → onboarding
  - role `company` → shell empresa (sem roadmap)

---

# 6. Performance

- `const` constructors onde possível
- `ListView.builder` / slivers para listas longas
- Cache de imagens (`cached_network_image`)
- Evitar rebuilds: `Consumer`/`select` no Riverpod
- Parse JSON em isolate apenas para payloads grandes (>100KB)
- Lazy load de features com deferred imports se o bundle crescer

---

# 7. i18n

- `flutter_gen` + ARB files (`pt`, `en`, `es`, `de`) — paridade web
- Nunca strings hardcoded em widgets de produção
- Datas/números com `intl`

---

# 8. Testes

| Tipo | Ferramenta | Meta |
|------|------------|------|
| Unit | `flutter test` + mocks (`mocktail`) | repositories, mappers |
| Widget | `flutter_test` | páginas críticas, estados AsyncValue |
| Integration | `integration_test` | login → roadmap smoke |

- Mock `ApiClient` / repositories — não chamar rede em unit
- Golden tests opcionais para componentes estáveis

---

# 9. Flavors e config

```text
--dart-define=API_BASE_URL=https://api.example.com
--dart-define=AUTH_BASE_URL=https://auth.example.com
```

- `dev` / `staging` / `prod` com bundle ids distintos
- Nunca commitar `.env` com secrets

---

# 10. Pacotes recomendados

| Necessidade | Pacote |
|-------------|--------|
| HTTP | `dio` |
| Estado | `flutter_riverpod`, `riverpod_annotation` |
| Rotas | `go_router` |
| Modelos | `freezed`, `json_serializable` |
| Storage seguro | `flutter_secure_storage` |
| i18n | `flutter_localizations`, `intl` |

Evitar adicionar pacotes sem necessidade (bundle size).

---

# 11. Anti-patterns proibidos

- Lógica de negócio em `build()`
- `http` package sem interceptors centralizados
- Secrets no código ou no repo
- Ignorar códigos de erro estruturados do BFF
- `setState` em árvores grandes sem motivo
- Testes que dependem de rede real em CI
- Armazenar JWT em plain storage

---

# 12. Checklist PR (mobile)

- [ ] Headers `X-Application-From` e mapper em chamadas BFF
- [ ] DTOs tipados + tratamento de `code` HTTP
- [ ] Sem secrets/logs sensíveis
- [ ] Testes unitários nos repositories alterados
- [ ] Strings em ARB
- [ ] Rotas protegidas por auth/onboarding/role

---

# Exemplo de prompt para IA

```txt
Ao gerar código Flutter para goroute-mobile:

- Use arquitetura feature-first (data/domain/presentation)
- Riverpod + AsyncValue para telas assíncronas
- Dio com interceptors para x-mapper-api e X-Application-From: goroute-mobile
- flutter_secure_storage para sessão
- go_router com guards de auth/onboarding/role
- freezed/json_serializable para DTOs
- Trate códigos de erro do BFF (SPEC-18)
- Alinhe endpoints a SPEC-15
- Testes unitários em repositories
- Sem any implícito; null-safety estrito
```
