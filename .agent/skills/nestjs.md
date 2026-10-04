---
name: nestjs-best-practices
description: Comprehensive enterprise-grade guide for building scalable, secure and maintainable NestJS applications.
allowed-tools: Read Write Edit Glob Grep Bash
---

# Skill: NestJS Development Best Practices

> **Persona:** [Developer](../personas/developer.md) · **Gate:** [Q.A.](../personas/qa.md) + Jest  
> **Pacotes GoRoute:** `goroute-api` (BFF), `goroute-service-auth` (hexagonal — priorizar `src/domain/` + `src/infrastructure/`).

## Objetivo

Fornecer diretrizes modernas para desenvolvimento de APIs e microsserviços com NestJS focadas em:

- escalabilidade
- segurança
- performance
- manutenibilidade
- observabilidade
- clean architecture
- integração enterprise

Compatível com NestJS 10+.

---

# Escopo

- Arquitetura modular
- Controllers e Services
- DTOs e validação
- Banco de dados
- Segurança
- Error handling
- Logging
- Cache
- Filas e jobs
- Observabilidade
- Performance
- Testes
- Swagger/OpenAPI
- CI/CD
- Microsserviços
- Integração com APIs externas

---

# Diretrizes Obrigatórias

# 1. Arquitetura

- Organize o projeto por domínio/feature
- Utilize módulos desacoplados
- Evite dependências circulares
- Controllers devem ser finos
- Regras de negócio devem ficar em services/use-cases
- Nunca colocar lógica de negócio em controllers
- Services não devem conhecer HTTP
- Utilize injeção de dependência corretamente
- Prefira composição ao invés de herança
- Utilize interfaces para contratos críticos

---

# 2. Estrutura Recomendada

```txt
/src
  /core
    /config
    /database
    /errors
    /interceptors
    /filters
    /middlewares
    /guards
    /decorators
    /logger

  /modules
    /users
      /controllers
      /services
      /dto
      /entities
      /repositories
      /interfaces
      /mappers
      /tests

    /products
    /orders

  /common
    /utils
    /constants
    /helpers
    /types

  /jobs
  /queues

  main.ts
```

---

# 3. Controllers

- Controllers devem apenas:
  - receber requests
  - validar entrada
  - chamar services
  - retornar responses

- Nunca acessar banco diretamente
- Nunca implementar regras de negócio
- Nunca manipular ORM diretamente
- Utilize DTOs para entrada e saída

## Exemplo

```ts
@Controller('users')
export class UsersController {
  constructor(
    private readonly usersService: UsersService
  ) {}

  @Post()
  async create(
    @Body() dto: CreateUserDto
  ): Promise<UserResponseDto> {
    return this.usersService.create(dto);
  }
}
```

---

# 4. Services

- Services devem conter regras de negócio
- Services não devem conhecer HTTP
- Evite services gigantes
- Prefira services coesos
- Nunca retornar entidades diretamente
- Utilize mappers quando necessário

## Regras

- Controllers → orchestration HTTP
- Services → business logic
- Repositories → acesso a dados

---

# 5. DTOs e Validação

- Toda entrada deve possuir DTO
- Toda saída deve possuir Response DTO
- Nunca expor entidades diretamente
- Utilize `class-validator`
- Utilize `class-transformer`
- Nunca confiar em dados externos

## ValidationPipe Global Obrigatório

```ts
app.useGlobalPipes(
  new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true,
    transformOptions: {
      enableImplicitConversion: true
    }
  })
);
```

## Regras

- Utilize validação declarativa
- Sanitizar inputs
- Nunca aceitar payloads arbitrários
- DTOs devem ser pequenos e específicos

## Exemplo

```ts
export class CreateUserDto {
  @IsEmail()
  email: string;

  @MinLength(8)
  password: string;
}
```

---

# 6. Segurança

- Configure CORS corretamente
- Utilize Helmet
- Nunca exponha stack traces
- Nunca armazenar secrets no código
- Utilize variáveis de ambiente
- Nunca confiar apenas na validação frontend
- Utilize HTTPS
- Sanitizar inputs externos
- Proteja endpoints críticos
- Valide uploads

## Segurança Avançada

- Preferir cookies `httpOnly`
- Nunca armazenar tokens inseguros
- Validar origem das requests
- Implementar rate limiting
- Proteger contra brute force
- Implementar auditoria quando necessário

## Helmet

```ts
app.use(helmet());
```

---

# 7. Autenticação e Autorização

## Arquitetura Recomendada

A autenticação deve ficar desacoplada da API principal.

### Recomendação

Separar em:

- `auth-service`
- `auth-api`
- `identity-service`

A API principal deve:

- validar tokens
- consumir identity provider
- evitar lógica pesada de autenticação

## Regras

- Nunca implementar autenticação complexa diretamente no monólito principal
- Utilize JWT apenas para transporte de identidade
- Prefira OAuth2/OpenID Connect quando aplicável
- Utilize refresh tokens seguros
- Implemente rotação de tokens
- Utilize guards para autorização
- Utilize RBAC/permissions quando necessário

## Guards

- Utilize `JwtAuthGuard`
- Utilize `RolesGuard`
- Utilize decorators customizados

## Exemplo

```ts
@UseGuards(JwtAuthGuard, RolesGuard)
@Controller('admin')
export class AdminController {}
```

---

# 8. Banco de Dados

- Utilize Prisma ou TypeORM
- Nunca acessar banco diretamente em controllers
- Utilize repositories/services
- Utilize migrations
- Nunca utilizar `synchronize: true` em produção
- Utilize transactions para operações críticas
- Evite queries desnecessárias
- Evite N+1 queries

## Regras

- Tipar entidades corretamente
- Utilizar índices
- Paginar queries grandes
- Utilizar soft delete quando necessário

## Prisma

- Prefira Prisma para novos projetos
- Utilize schema organizado
- Nunca realizar lógica complexa diretamente no Prisma client

---

# 9. Repository Pattern

- Controllers nunca acessam repositories
- Services devem abstrair persistência
- Utilize repositories para acesso a dados
- Repositories não devem conter regras de negócio

---

# 10. Configuração

- Utilize `ConfigModule` global
- Nunca utilizar `process.env` diretamente
- Valide variáveis de ambiente
- Centralize configurações

## Exemplo

```ts
ConfigModule.forRoot({
  isGlobal: true,
  validationSchema: Joi.object({
    DATABASE_URL: Joi.string().required(),
    JWT_SECRET: Joi.string().required()
  })
});
```

---

# 11. Error Handling

- Utilize Exception Filters globais
- Padronize responses de erro
- Nunca exponha stack traces
- Centralize logging de erros
- Utilize códigos HTTP corretos
- Nunca silencie exceptions

## Exemplo

```ts
@Catch()
export class GlobalExceptionFilter
  implements ExceptionFilter {}
```

---

# 12. Interceptors

Utilize interceptors para:

- logging
- métricas
- transformação de response
- cache
- tracing

## Exemplo

```ts
@UseInterceptors(LoggingInterceptor)
```

---

# 13. Logging

- Utilize Logger do NestJS
- Nunca utilize `console.log`
- Estruture logs
- Utilize correlation id quando necessário
- Centralize logs
- Utilize níveis:
  - error
  - warn
  - log
  - debug
  - verbose

## Observabilidade

- Utilize OpenTelemetry/Sentry/NewRelic quando necessário
- Monitore:
  - latency
  - errors
  - throughput
  - memory
  - CPU

---

# 14. Performance

- Utilize compressão gzip/brotli
- Utilize cache
- Paginar listas grandes
- Evite serialização excessiva
- Utilize streaming quando necessário
- Utilize queries otimizadas
- Evite payloads grandes
- Utilize select específico no banco

## Cache

- Utilize Redis para cache distribuído
- Cache apenas operações idempotentes
- Defina TTL explícito

---

# 15. Filas e Jobs

- Utilize BullMQ
- Nunca execute tarefas pesadas em requests HTTP
- Utilize workers dedicados
- Reprocessamento deve ser idempotente

## Casos comuns

- emails
- processamento de arquivos
- webhooks
- integração externa
- geração de relatórios

---

# 16. Rate Limiting

- Utilize `@nestjs/throttler`
- Proteja rotas públicas
- Limite requests por IP/token
- Defina limites adequados

## Exemplo

```ts
ThrottlerModule.forRoot([
  {
    ttl: 60000,
    limit: 100
  }
]);
```

---

# 17. APIs Externas

- Utilize timeout
- Utilize retry apenas para falhas transitórias
- Implemente circuit breaker quando necessário
- Nunca confiar em APIs externas
- Centralize clients HTTP

---

# 18. Swagger/OpenAPI

- Utilize Swagger
- Documente DTOs
- Documente responses
- Versione APIs
- Documente autenticação
- Mantenha documentação atualizada

## Exemplo

```ts
@ApiTags('users')
@ApiBearerAuth()
```

---

# 19. Testes

## Unitários

- Utilize Jest
- Teste services
- Teste controllers
- Teste guards
- Teste interceptors
- Teste pipes
- Mocke dependências externas

## Integração

- Teste banco de dados
- Teste fluxos críticos
- Teste autenticação/autorização

## E2E

- Utilize ambiente isolado
- Teste APIs reais
- Teste responses HTTP

## Cobertura

- Cobertura mínima: 80%

---

# 20. Naming Conventions

## Regras

- Services:
  - `users.service.ts`

- Controllers:
  - `users.controller.ts`

- DTOs:
  - `create-user.dto.ts`

- Entities:
  - `user.entity.ts`

- Repositories:
  - `users.repository.ts`

## Convenções

- PascalCase para classes
- camelCase para métodos
- kebab-case para arquivos
- interfaces prefixadas com `I` apenas quando necessário

---

# 21. Clean Code

- Métodos devem possuir responsabilidade única
- Evite classes gigantes
- Utilize nomes semânticos
- Evite duplicação
- Código deve ser autoexplicativo
- Evite comentários desnecessários
- Prefira funções puras

---

# 22. CI/CD

- Executar lint no pipeline
- Executar testes automaticamente
- Bloquear merge com falhas
- Automatizar build e deploy
- Validar cobertura mínima
- Automatizar análise estática

---

# 23. Linting e Qualidade

## ESLint

- Sem variáveis não utilizadas
- Sem `any`
- Sem `console.log`
- Imports organizados
- Sem código morto

## TypeScript

- Utilize `strict: true`
- Tipar retornos explicitamente
- Evite `unknown` desnecessário

---

# 24. Microsserviços

- Utilize microsserviços apenas quando necessário
- Evite fragmentação prematura
- Defina ownership claro
- Utilize comunicação resiliente
- Utilize idempotência

---

# 25. Anti-patterns Proibidos

- lógica de negócio em controllers
- acesso direto ao banco em controllers
- `console.log`
- uso excessivo de `any`
- DTOs genéricos demais
- queries sem paginação
- `synchronize=true` em produção
- secrets hardcoded
- nested try/catch desnecessário
- serviços gigantes
- dependências circulares

---

# Exemplo de Prompt para IA Planejadora

```txt
Ao gerar código NestJS, siga estas diretrizes:

- Utilize arquitetura modular
- Controllers devem ser finos
- Regras de negócio devem ficar em services
- Utilize DTOs para toda entrada e saída
- Utilize ValidationPipe global com whitelist
- Nunca exponha entidades diretamente
- Utilize Prisma ou repositories
- Utilize Exception Filters globais
- Utilize Interceptors para logging
- Nunca utilize console.log
- Utilize ConfigModule global
- Valide variáveis de ambiente
- Utilize JWT Guards apenas para autorização
- Considere autenticação desacoplada em auth-service separado
- Utilize cache e paginação quando necessário
- Gere código limpo, escalável, seguro e performático
```