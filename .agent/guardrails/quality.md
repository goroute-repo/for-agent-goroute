---
name: quality-criteria
description: Critérios de qualidade e aceitação GoRoute — usados por Developer (implementação) e Q.A. (gate).
allowed-tools:
  - Read
  - Grep
  - Glob
---

# Quality Criteria — Critérios de Aceitação (GoRoute)

Durante qualquer revisão ou gate **Q.A.**, toda modificação deve ser avaliada contra os critérios abaixo.

Uma implementação só pode ser aprovada quando os critérios **obrigatórios do escopo** forem atendidos.

**Personas:** Developer entrega código testável; Q.A. verifica este documento + [`testing.md`](../testing.md).

**Pacotes:** `goroute-frontend`, `goroute-api`, `goroute-service-auth`, admin, mentor, NLP, mobile, `e2e/` — ver `AGENTS.md`.

---

# 1. Corretude (Correctness)

## Critério de aceitação

O código implementa exatamente os requisitos funcionais e comportamentais esperados.

## Checklist

- [ ] Implementa todos os requisitos da task
- [ ] Trata casos de borda
- [ ] Trata `null` e `undefined`
- [ ] Trata arrays vazios
- [ ] Trata erros esperados
- [ ] Mantém consistência com APIs existentes
- [ ] Não introduz regressões
- [ ] Mantém idempotência quando aplicável

## Casos obrigatórios

- entradas inválidas;
- recursos inexistentes;
- estados inconsistentes;
- concorrência;
- timeout;
- retry;
- falha parcial.

## Código inseguro

```typescript
// ❌ Não verifica existência
async updateUser(
  id: string,
  data: UpdateUserDto
) {
  return this.userRepository.update(id, data);
}
```

## Código correto

```typescript
// ✅ Validação adequada
async updateUser(
  id: string,
  data: UpdateUserDto
) {
  const user =
    await this.userRepository.findOne({
      where: { id }
    });

  if (!user) {
    throw new NotFoundException(
      `Usuário ${id} não encontrado`
    );
  }

  return this.userRepository.update(id, data);
}
```

---

# 2. Testabilidade (Testability)

## Critério de aceitação

O código pode ser testado isoladamente sem dependências concretas acopladas.

## Checklist

- [ ] Dependências injetáveis
- [ ] Dependências mockáveis
- [ ] Separação entre domínio e infraestrutura
- [ ] Funções puras quando possível
- [ ] Side effects isolados
- [ ] Serviços desacoplados
- [ ] Sem dependências globais ocultas

## Código ruim

```typescript
// ❌ Dependência concreta
async getUsers() {
  const db = new DatabaseConnection();

  return db.query(
    "SELECT * FROM users"
  );
}
```

## Código correto

```typescript
// ✅ Dependência injetada
@Injectable()
export class UsersService {
  constructor(
    @InjectRepository(User)
    private readonly userRepository:
      Repository<User>,
  ) {}

  async getUsers() {
    return this.userRepository.find();
  }
}
```

---

# 3. Manutenibilidade (Maintainability)

## Critério de aceitação

O código possui baixo custo cognitivo e pode ser modificado com segurança.

## Checklist

- [ ] Nomes claros
- [ ] Responsabilidade única
- [ ] Sem duplicação
- [ ] Funções pequenas
- [ ] Estrutura previsível
- [ ] Código legível sem comentários excessivos
- [ ] Complexidade controlada

## Métricas recomendadas

| Métrica | Aceitável | Ideal |
|---|---|---|
| Linhas por função | ≤ 30 | ≤ 15 |
| Parâmetros por função | ≤ 5 | ≤ 3 |
| Nível de aninhamento | ≤ 3 | ≤ 2 |
| Complexidade ciclomática | ≤ 10 | ≤ 5 |

## Regras arquiteturais

- evitar god classes;
- evitar funções gigantes;
- evitar abstração prematura;
- evitar acoplamento circular;
- evitar lógica de negócio em controllers;
- evitar lógica de domínio em repositories.

---

# 4. Cobertura de Testes (Test Coverage)

## Critério de aceitação

Toda alteração relevante deve possuir testes adequados.

## Checklist

- [ ] Testes unitários
- [ ] Testes de integração
- [ ] Testes negativos
- [ ] Testes de edge cases
- [ ] Testes de erro
- [ ] Cobertura dos fluxos críticos

## Cobertura recomendada

| Tipo | Recomendação |
|---|---|
| Unit Tests | ≥ 80% |
| Integration Tests | Fluxos principais |
| E2E Tests | Fluxos críticos |

## Estrutura esperada (GoRoute)

```text
# Frontend (Vitest) — ao lado do fonte
goroute-frontend/src/app/features/.../foo.ts
goroute-frontend/src/app/features/.../foo.spec.ts

# NestJS (Jest) — ao lado do fonte ou pasta test/
goroute-api/src/**/*.spec.ts
goroute-service-auth/src/**/*.spec.ts

# Python
goroute-mentor-agent/tests/
```

Helper frontend: `goroute-frontend/src/app/core/testing/goroute-test-providers.ts`.

UI: Playwright MCP (Cursor) ou suite `e2e/` — **não** adicionar Playwright ao `package.json` do frontend.

## Casos obrigatórios

- sucesso;
- falha;
- timeout;
- exceção;
- autorização negada;
- validação inválida.

---

# 5. Performance

## Critério de aceitação

O código não deve introduzir degradação perceptível de performance.

## Checklist

- [ ] Sem N+1 queries
- [ ] Queries paginadas
- [ ] Uso adequado de índices
- [ ] Estruturas de dados eficientes
- [ ] Operações I/O assíncronas
- [ ] Sem loops desnecessários
- [ ] Sem serialização excessiva

## Código ruim

```typescript
// ❌ N+1 Query
const users = await userRepository.find();

for (const user of users) {
  const orders =
    await orderRepository.find({
      where: {
        userId: user.id
      }
    });

  user.orders = orders;
}
```

## Código correto

```typescript
// ✅ JOIN / eager loading
const users =
  await userRepository.find({
    relations: ["orders"]
  });
```

## Regras adicionais

- preferir `Map` para lookup;
- evitar loops O(n²);
- evitar processamento síncrono pesado;
- evitar payloads excessivos;
- utilizar cache quando apropriado.

---

# 6. TypeScript Strictness

## Critério de aceitação

Projetos TypeScript devem operar em strict mode.

## Checklist

- [ ] `strict: true`
- [ ] `noImplicitAny`
- [ ] `strictNullChecks`
- [ ] `strictFunctionTypes`
- [ ] Sem `any`
- [ ] Nullable tratado explicitamente

## Regras

- preferir `unknown` em vez de `any`;
- evitar type assertions desnecessárias;
- utilizar unions corretamente;
- validar narrowing de tipos.

## Código ruim

```typescript
function process(data: any) {
  return data.user.name;
}
```

## Código correto

```typescript
interface UserPayload {
  user: {
    name: string;
  };
}

function process(
  data: UserPayload
) {
  return data.user.name;
}
```

---

# 7. Documentação

## Critério de aceitação

Componentes públicos e fluxos importantes devem ser documentados.

## Checklist

- [ ] JSDoc/TSDoc
- [ ] Swagger/OpenAPI atualizado
- [ ] README atualizado
- [ ] Exemplos de uso
- [ ] Contratos documentados

## Template recomendado

```typescript
/**
 * Processa o pagamento de um pedido
 *
 * @param orderId - ID do pedido
 * @param paymentMethod - Método de pagamento
 *
 * @returns Resultado da transação
 *
 * @throws {NotFoundException}
 * @throws {PaymentFailedException}
 */
```

---

# 8. Observabilidade (Observability)

## Critério de aceitação

O sistema deve permitir monitoramento e troubleshooting adequados.

## Checklist

- [ ] Logs estruturados
- [ ] Correlation ID
- [ ] Métricas
- [ ] Tracing
- [ ] Tratamento de erro consistente
- [ ] Logs sem dados sensíveis

## Regras

- evitar logs genéricos;
- evitar swallow de exceptions;
- registrar contexto suficiente;
- padronizar erros.

---

# 9. Resiliência (Resilience)

## Critério de aceitação

O sistema deve lidar adequadamente com falhas parciais.

## Checklist

- [ ] Timeout configurado
- [ ] Retry controlado
- [ ] Circuit breaker quando necessário
- [ ] Fallbacks definidos
- [ ] Idempotência validada

## Casos críticos

- falha de banco;
- falha de API externa;
- timeout;
- concorrência;
- retry duplicado.

---

# 10. Compatibilidade Arquitetural

## Critério de aceitação

O código deve respeitar os padrões arquiteturais existentes.

## Checklist

- [ ] Segue convenções do projeto
- [ ] Segue padrões de pastas
- [ ] Segue naming conventions
- [ ] Segue boundaries de domínio
- [ ] Não viola camadas arquiteturais

## Exemplos de violações

- controller acessando banco diretamente;
- lógica de domínio em DTO;
- service manipulando HTTP response;
- acesso cross-module indevido.

---

# 11. APIs e Contratos

## Critério de aceitação

Mudanças de API devem preservar compatibilidade quando necessário.

## Checklist

- [ ] Contratos compatíveis
- [ ] Versionamento adequado
- [ ] DTOs consistentes
- [ ] Status codes corretos
- [ ] Erros padronizados

## Regras

- evitar breaking changes silenciosos;
- documentar alterações;
- validar backward compatibility.

---

# 12. Segurança Básica

## Critério de aceitação

Nenhuma alteração pode introduzir vulnerabilidades conhecidas.

## Checklist

- [ ] Input validation
- [ ] Sanitização
- [ ] Autorização
- [ ] Sem secrets hardcoded
- [ ] Queries parametrizadas
- [ ] Headers seguros

---

# 13. Qualidade para IA e Agentes

## Critério de aceitação

Fluxos envolvendo IA devem ser previsíveis e seguros.

## Checklist

- [ ] Prompt validation
- [ ] Tool restrictions
- [ ] Output validation
- [ ] Context sanitization
- [ ] Controle de execução

## Riscos analisados

- prompt injection;
- hallucination amplification;
- unsafe tool execution;
- secret leakage;
- untrusted context.

---

# 14. Critérios de Aprovação

## Uma revisão só pode ser aprovada quando:

- [ ] Todos os testes passam
- [ ] Não existem vulnerabilidades críticas
- [ ] Não existem regressões conhecidas
- [ ] Cobertura adequada foi adicionada
- [ ] Documentação necessária foi atualizada
- [ ] Código segue padrões arquiteturais
- [ ] Performance aceitável foi mantida

---

# 15. Critérios de Bloqueio de Merge

## O merge deve ser bloqueado quando houver:

- vulnerabilidade crítica;
- falha de testes;
- regressão funcional;
- breaking change não documentado;
- ausência de autorização;
- ausência de validação;
- uso de `any` crítico;
- acoplamento excessivo;
- código não testável;
- ausência de tratamento de erro crítico.

---

# 16. Resumo — Checklist Rápido

| Categoria | Obrigatório |
|---|---|
| Corretude | ✅ |
| Testabilidade | ✅ |
| Manutenibilidade | ✅ |
| Cobertura de testes | ✅ |
| Performance | ✅ |
| TypeScript strict | ✅ |
| Documentação | ✅ |
| Observabilidade | ✅ |
| Resiliência | ✅ |
| Segurança | ✅ |
| Compatibilidade arquitetural | ✅ |

---

# Regra Final

Uma code review só pode ser aprovada quando TODOS os critérios obrigatórios forem atendidos.