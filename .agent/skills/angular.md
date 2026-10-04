# Skill: Angular Development Best Practices

> **Persona:** [Developer](../personas/developer.md) · **Gate UI:** [Q.A.](../personas/qa.md) + `.agent/testing.md`  
> **Pacotes GoRoute:** `goroute-frontend` (Angular 21, Vitest), `goroute-platform-admin` (Angular 19, Karma).

## Objetivo

Fornecer diretrizes modernas para desenvolvimento Angular focadas em:

- performance
- escalabilidade
- segurança
- legibilidade
- manutenibilidade

Compatível com Angular 17+ e APIs standalone.

---

# Escopo

- Standalone Components
- Componentes, serviços, diretivas e pipes
- RxJS e Angular Signals
- Gerenciamento de estado
- Estrutura de pastas
- Lazy loading e rendering otimizado
- Segurança
- Testes unitários e e2e
- Integração com APIs
- Clean Code
- SSR/SEO
- Observabilidade
- CI/CD
- Internacionalização

---

# Diretrizes Obrigatórias

## 1. Arquitetura e Estrutura

- Prefira Standalone Components (`standalone: true`) em novos projetos
- Utilize `provideRouter`, `provideHttpClient` e APIs standalone
- Use NgModules apenas quando necessário para compatibilidade
- Organize features por domínio de negócio
- Evite dependências circulares
- Separe responsabilidades corretamente
- Utilize arquitetura orientada a features
- Evite acoplamento entre módulos/features

### Estrutura Recomendada

```txt
/src
  /core
    /guards
    /interceptors
    /services
    /config
    /errors
  /shared
    /components
    /directives
    /pipes
    /models
    /utils
    /types
  /features
    /auth
    /dashboard
    /users
  /layout
  /state
  /assets
  /environments
```

### Core

Use `/core` para:

- interceptors
- guards
- serviços singleton
- autenticação
- providers globais
- handlers globais
- configurações globais

### Shared

Use `/shared` para:

- componentes reutilizáveis
- pipes
- diretivas
- utilitários
- models/interfaces
- helpers
- tipos compartilhados

---

# 2. Componentes

- Componentes devem possuir responsabilidade única
- Prefira componentes pequenos e reutilizáveis
- Limite recomendado: ~300 linhas por componente
- Utilize `ChangeDetectionStrategy.OnPush`
- Evite lógica complexa em componentes
- Mova regras de negócio para services/facades
- Nunca faça chamadas HTTP diretamente no componente
- Utilize `@Input()` e `@Output()` tipados
- Evite múltiplos níveis de `@Input`
- Prefira composição ao invés de componentes gigantes
- Evite side effects em componentes
- Evite manipulação direta de DOM

## Smart vs Presentational Components

### Smart Components

- conectam services/store
- possuem lógica de orchestration
- carregam dados
- coordenam fluxo da página

### Presentational Components

- recebem dados via `@Input`
- emitem eventos via `@Output`
- não conhecem API/store
- devem ser reutilizáveis

## Exemplo

```ts
@Component({
  standalone: true,
  selector: 'app-user-card',
  templateUrl: './user-card.component.html',
  changeDetection: ChangeDetectionStrategy.OnPush
})
export class UserCardComponent {}
```

---

# 3. Estado e Reatividade

## Angular Signals

- Prefira Signals para estado local simples
- Utilize `computed()` para derivados
- Utilize `effect()` apenas quando necessário
- Evite efeitos excessivos
- Evite criar Signals desnecessários

## RxJS

Utilize RxJS para:

- chamadas HTTP
- eventos assíncronos
- websockets
- streams complexas
- polling
- sincronização reativa

### Regras

- Prefira `async` pipe em templates
- Evite subscriptions manuais quando possível
- Centralize transformação de dados em services/facades
- Evite lógica RxJS complexa no componente
- Nunca utilize `any` em observables
- Utilize operadores puros
- Evite nested subscriptions

## Gerenciamento de Estado

- Evite gerenciamento global desnecessário
- Prefira estado local antes de introduzir NgRx
- Utilize NgRx apenas para estados complexos/globais
- Utilize facades para desacoplar store/components
- Evite stores gigantes

## Facade Pattern

- Components não devem conhecer detalhes de store/API
- Toda comunicação externa deve passar por facades
- Facades devem expor:
  - signals
  - observables
  - actions públicas

## Cleanup de subscriptions

- Prefira `takeUntilDestroyed()`
- Evite Subjects apenas para destroy lifecycle

## Exemplo

```ts
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

this.userService.getUsers()
  .pipe(takeUntilDestroyed())
  .subscribe();
```

---

# 4. Segurança Angular

- Nunca use `innerHTML` sem sanitização
- Nunca utilize `bypassSecurityTrustHtml` sem necessidade extrema
- Utilize `HttpInterceptor` para autenticação
- Nunca armazene secrets no frontend
- Utilize environments para URLs e configs
- Nunca hardcode URLs de API
- Proteja rotas com:
  - `CanActivate`
  - `CanMatch`

## Segurança Avançada

- Nunca armazenar JWT em `localStorage` quando possível
- Preferir cookies `httpOnly`
- Sanitizar dados externos
- Validar inputs no frontend e backend
- Nunca confiar apenas na validação frontend

## Autenticação

- Tokens JWT devem ser enviados via interceptor
- Utilize refresh token strategy quando aplicável
- Expire sessões corretamente
- Implemente logout seguro

## Headers Recomendados

Configure:

- CSP (Content Security Policy)
- X-Frame-Options
- X-Content-Type-Options
- Referrer-Policy
- XSS Protection

---

# 5. Performance

- Utilize lazy loading em todas as features
- Utilize `@defer` para componentes pesados
- Utilize `trackBy` em loops
- Utilize `OnPush`
- Evite renders desnecessários
- Evite funções diretamente no template
- Utilize `ngOptimizedImage`
- Faça code splitting
- Desabilite `preserveWhitespaces` em produção
- Utilize preloading strategy para rotas críticas
- Utilize virtual scrolling para listas grandes
- Evite re-renderizações desnecessárias
- Utilize memoization quando necessário

## Exemplo trackBy

```ts
trackById(index: number, item: User): number {
  return item.id;
}
```

## Exemplo defer

```html
@defer {
  <heavy-chart />
}
```

---

# 6. Integração com APIs

- Centralize chamadas HTTP em services
- Nunca utilize `HttpClient` diretamente em componentes
- Tipar todas as requests/responses
- Tratar erros globalmente com interceptors
- Utilize retry apenas quando fizer sentido
- Padronize DTOs
- Centralize endpoints
- Utilize timeout para requests críticas

## Contratos de API

- Nunca consumir responses sem tipagem
- Validar contratos quando necessário
- Centralize DTOs
- Evite mapear responses diretamente no componente

## Exemplo

```ts
getUsers(): Observable<User[]> {
  return this.http.get<User[]>('/api/users');
}
```

---

# 7. Tipagem e TypeScript

- Nunca utilize `any`
- Prefira `unknown` quando necessário
- Utilize interfaces e types explícitos
- Ative `strict: true`
- Utilize readonly quando possível
- Utilize enums com moderação
- Prefira union types
- Tipar retorno de funções explicitamente

## tsconfig recomendado

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitOverride": true,
    "noPropertyAccessFromIndexSignature": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true
  }
}
```

---

# 8. Templates

- Evite lógica complexa no HTML
- Evite múltiplos `async` pipes na mesma expressão
- Utilize pipes puros
- Utilize `ng-container` quando necessário
- Evite manipulação manual de DOM
- Prefira bindings declarativos
- Evite chamadas de função em templates
- Evite condicionais complexas no HTML

## Evitar

```html
<div>{{ calculateTotal() }}</div>
```

## Preferir

```html
<div>{{ total }}</div>
```

---

# 9. Naming Conventions

## Regras Gerais

- Observable deve terminar com `$`
  - Ex: `users$`

- Signals devem utilizar nomes semânticos
  - Ex: `userSignal`

- Subjects privados devem iniciar com `_`
  - Ex: `_destroy$`

- Métodos booleanos devem iniciar com:
  - `is`
  - `has`
  - `can`
  - `should`

## Convenções

- kebab-case para arquivos
- PascalCase para classes
- camelCase para variáveis e métodos

## Sufixos

- `.component.ts`
- `.service.ts`
- `.guard.ts`
- `.interceptor.ts`
- `.pipe.ts`
- `.facade.ts`
- `.model.ts`
- `.dto.ts`

---

# 10. Clean Code

- Métodos devem possuir responsabilidade única
- Classes devem ser coesas
- Evite componentes “god object”
- Evite duplicação de código
- Utilize nomes semânticos
- Código deve ser autoexplicativo
- Evite comentários desnecessários
- Prefira composição ao invés de herança
- Prefira funções puras
- Evite side effects desnecessários

---

# 11. Tratamento de Erros

- Utilize `ErrorHandler` global
- Centralize logging
- Nunca silencie erros
- Exiba mensagens amigáveis ao usuário
- Utilize retry apenas para falhas transitórias
- Logar erros críticos

---

# 12. Observabilidade

- Centralize logs
- Utilize Sentry/NewRelic/OpenTelemetry quando necessário
- Nunca utilize `console.log` em produção
- Utilize monitoramento de performance
- Registre erros críticos

---

# 13. Internacionalização (i18n)

- Não hardcode textos
- Utilize Angular i18n ou ngx-translate
- Centralize traduções
- Utilize arquivos de locale
- Suporte múltiplos idiomas desde o início quando aplicável

---

# 14. SSR e SEO

- Utilize Angular SSR quando necessário
- Configure meta tags dinamicamente
- Utilize hydration
- Evite conteúdo crítico carregado apenas client-side
- Configure title e meta description dinamicamente
- Prefira renderização híbrida quando aplicável

---

# 15. Testes

## Unitários

- Teste componentes isolados
- Teste services com mocks
- Teste guards e interceptors
- Teste pipes e diretivas
- Cobertura mínima: 80%

## Ferramentas

- Prefira Jest ou Vitest para novos projetos
- Utilize Testing Library quando aplicável

## HTTP Testing

```ts
HttpClientTestingModule
```

## E2E

- Utilize Cypress ou Playwright
- Teste fluxos críticos
- Teste autenticação
- Teste navegação e permissões
- Teste cenários de erro

---

# 16. Acessibilidade

- Utilize HTML semântico
- Utilize labels corretamente
- Componentes devem ser navegáveis por teclado
- Utilize aria-label quando necessário
- Garanta contraste adequado
- Teste navegação por teclado
- Utilize roles corretamente

---

# 17. Feature Flags

- Features experimentais devem utilizar flags
- Evite condicionais hardcoded
- Centralize gerenciamento de flags

---

# 18. Dependências

- Evite bibliotecas desnecessárias
- Prefira APIs nativas do Angular
- Avalie manutenção e bundle size antes de adicionar libs
- Evite dependências abandonadas
- Revise vulnerabilidades periodicamente

---

# 19. Linting e Formatação

## ESLint

- Sem variáveis não utilizadas
- Sem `any`
- Sem `console.log`
- Imports ordenados
- Sem código morto

## Formatação

- Utilize Prettier
- Automatize lint e format no CI/CD
- Utilize husky/lint-staged quando aplicável

---

# 20. CI/CD

- Executar lint no pipeline
- Executar testes automaticamente
- Bloquear merge com cobertura insuficiente
- Automatizar build e deploy
- Automatizar análise estática
- Validar qualidade no pipeline

---

# 21. Microfrontends (Opcional)

- Utilize Module Federation apenas quando houver necessidade real
- Evite fragmentação prematura
- Defina ownership claro entre times

---

# 22. Boas Práticas Gerais

- Prefira composição
- Evite acoplamento forte
- Mantenha consistência no projeto
- Evite overengineering
- Mantenha dependências atualizadas
- Automatize validações
- Documente decisões arquiteturais importantes

---

# Anti-patterns Proibidos

- Uso de `any`
- HTTP em componentes
- Subscriptions sem cleanup
- Funções pesadas no template
- Manipulação direta de DOM
- Lógica de negócio no HTML
- Componentes gigantes
- Duplicação de código
- Hardcoded URLs
- Estado global desnecessário
- Nested subscriptions
- Uso excessivo de `setTimeout`
- Side effects em templates

---

# Exemplo de Prompt para IA Planejadora

```txt
Ao gerar código Angular, siga estas diretrizes:

- Utilize Standalone Components
- Utilize ChangeDetectionStrategy.OnPush
- Prefira Signals para estado local
- Utilize RxJS apenas para fluxos assíncronos
- Nunca utilize any
- Utilize strict typing
- Utilize Facade Pattern
- Utilize Smart/Presentational Components
- Separe regras de negócio em services/facades
- Utilize takeUntilDestroyed()
- Utilize lazy loading
- Utilize @defer quando aplicável
- Proteja rotas com CanActivate e CanMatch
- Nunca faça chamadas HTTP diretamente em componentes
- Utilize DTOs tipados
- Evite lógica complexa em templates
- Gere código limpo, escalável, performático e testável
```