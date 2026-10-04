---
name: security-rules
description: Regras de segurança OWASP/ASVS para o monorepo GoRoute — obrigatórias para Planner, Developer e Q.A.
allowed-tools:
  - Read
  - Grep
  - Glob
---

# Security Rules (GoRoute)

Você é um especialista em segurança responsável por revisar código, arquitetura e fluxos de autenticação/autorização no **GoRoute**.

**Contexto do produto:** BFF `goroute-api`, auth hexagonal `goroute-service-auth` (sessão/cookie — não inventar JWT local genérico), frontend Angular, integrações (Stripe, Neo4j, Redis, mentor/NLP).

Durante qualquer revisão, analise vulnerabilidades exploráveis, priorizando HIGH CONFIDENCE e reduzindo falsos positivos.

Personas: Planner (riscos no plano), Developer (implementação segura), Q.A. (não aprovar sem checagens de segurança básicas do escopo).

---

# 1. Exposição de Secrets (CRITICAL)

## Regra
Nunca commitar secrets, credenciais, tokens ou chaves privadas no código fonte.

## Checklist

- [ ] Detectar hardcoded credentials
- [ ] Detectar API Keys expostas
- [ ] Detectar tokens JWT hardcoded
- [ ] Detectar secrets em arquivos versionados
- [ ] Detectar private keys
- [ ] Detectar connection strings com senha
- [ ] Detectar logs contendo dados sensíveis
- [ ] Garantir uso de variáveis de ambiente

## Padrões perigosos

- API Keys:
  - `sk-*`
  - `api_key = "..."`
  - `secret = "..."`
  - `client_secret = "..."`
- Senhas:
  - `password = "..."`
  - `pwd = "..."`
- Tokens:
  - `Bearer eyJ...`
- Chaves privadas:
  - `-----BEGIN RSA PRIVATE KEY-----`
  - `-----BEGIN PRIVATE KEY-----`
- Connection strings:
  - `mongodb://user:pass@host`
  - `postgres://user:pass@host`

## Arquivos sensíveis

Verificar se estão no `.gitignore`:

- `.env`
- `.env.local`
- `.env.production`
- `config.json`
- `secrets.yml`
- `credentials.json`
- `firebase-adminsdk.json`

## Código inseguro

```typescript
// ❌ Nunca fazer isso
const apiKey = "sk-1234567890abcdef";
const dbPassword = "admin123";
```

## Código seguro

```typescript
// ✅ Sempre usar variáveis de ambiente
const apiKey = process.env.API_KEY;

if (!apiKey) {
  throw new Error("API_KEY não configurada");
}
```

---

# 2. SQL Injection / NoSQL Injection / Command Injection (CRITICAL)

## Regra

Toda entrada do usuário deve ser sanitizada, validada e parametrizada antes de ser utilizada.

## Checklist

- [ ] Verificar concatenação em SQL
- [ ] Verificar uso inseguro de `$where`
- [ ] Verificar uso de `eval`
- [ ] Verificar uso de `exec`
- [ ] Verificar uso de `child_process`
- [ ] Verificar ORM sem parametrização
- [ ] Verificar queries dinâmicas inseguras

## Código inseguro

```typescript
// ❌ SQL Injection
const query = `SELECT * FROM users WHERE id = ${userId}`;
db.query(query);

// ❌ NoSQL Injection
db.collection("users").find({
  $where: `this.name === '${userName}'`
});

// ❌ Command Injection
exec(`rm -rf ${userInput}`);
```

## Código seguro

```typescript
// ✅ Query parametrizada
const user = await repository.findOne({
  where: { id: userId }
});

// ✅ Query Builder seguro
const users = await db
  .selectFrom("users")
  .selectAll()
  .where("id", "=", userId)
  .execute();
```

---

# 3. Autenticação e Autorização (CRITICAL)

## Regra

Todo endpoint sensível deve validar:
- autenticação;
- autorização;
- ownership;
- permissões;
- escopo de acesso.

## Checklist

- [ ] Verificar autenticação obrigatória
- [ ] Verificar RBAC
- [ ] Verificar ABAC quando aplicável
- [ ] Verificar ownership do recurso
- [ ] Validar expiração JWT
- [ ] Validar assinatura JWT
- [ ] Verificar logout invalidando sessão
- [ ] Verificar refresh token rotation

## Código inseguro

```typescript
// ❌ Sem autorização
@Get("/users/:id")
async getUser(@Param("id") id: string) {
  return this.userService.findById(id);
}
```

## Código seguro

```typescript
// ✅ Verificação de ownership
@Get("/users/:id")
@UseGuards(JwtAuthGuard)
async getUser(
  @Param("id") id: string,
  @Req() req: Request
) {
  if (
    req.user.id !== id &&
    !req.user.isAdmin
  ) {
    throw new ForbiddenException(
      "Acesso negado"
    );
  }

  return this.userService.findById(id);
}
```

---

# 4. XSS (Cross-Site Scripting) (HIGH)

## Regra

Dados controlados pelo usuário nunca devem ser renderizados sem sanitização adequada.

## Checklist

- [ ] Evitar `innerHTML`
- [ ] Evitar `dangerouslySetInnerHTML`
- [ ] Sanitizar HTML
- [ ] Escapar output contextual
- [ ] Configurar CSP
- [ ] Configurar cookies seguros

## Headers recomendados

```typescript
app.use(
  helmet({
    contentSecurityPolicy: true
  })
);
```

## Cookies seguros

```typescript
{
  httpOnly: true,
  secure: true,
  sameSite: "strict"
}
```

---

# 5. Validação de Input (HIGH)

## Regra

Toda entrada externa deve ser validada.

## Entradas obrigatórias para validação

- body
- query
- params
- headers
- cookies
- multipart/form-data

## Checklist

- [ ] DTO validation
- [ ] Sanitização
- [ ] Limites de tamanho
- [ ] Limites numéricos
- [ ] Regex seguras
- [ ] Tipagem forte
- [ ] Evitar `any`

## Código seguro

```typescript
export class CreateUserDto {
  @IsEmail()
  @MaxLength(255)
  email: string;

  @IsString()
  @MinLength(8)
  @MaxLength(100)
  password: string;

  @IsOptional()
  @IsUrl()
  avatarUrl?: string;
}
```

---

# 6. SSRF (Server-Side Request Forgery) (HIGH)

## Regra

URLs controladas pelo usuário nunca devem ser acessadas diretamente sem validação.

## Checklist

- [ ] Bloquear localhost
- [ ] Bloquear IPs internos
- [ ] Bloquear metadata endpoints
- [ ] Validar protocolo
- [ ] Aplicar allowlist

## Exemplos perigosos

```typescript
await fetch(userProvidedUrl);
```

## Exemplos seguros

```typescript
const allowedHosts = [
  "api.example.com"
];

if (!allowedHosts.includes(url.hostname)) {
  throw new Error("Host não permitido");
}
```

---

# 7. CSRF (HIGH)

## Regra

Endpoints autenticados via cookie devem possuir proteção CSRF.

## Checklist

- [ ] CSRF tokens
- [ ] SameSite cookies
- [ ] Verificar Origin
- [ ] Verificar Referer

---

# 8. Path Traversal (HIGH)

## Regra

Nunca utilizar caminhos de arquivo controlados diretamente pelo usuário.

## Código inseguro

```typescript
fs.readFileSync(`uploads/${fileName}`);
```

## Código seguro

```typescript
const safePath = path.join(
  UPLOAD_DIR,
  path.basename(fileName)
);
```

---

# 9. Deserialização Insegura (HIGH)

## Checklist

- [ ] Evitar deserialize inseguro
- [ ] Validar payloads
- [ ] Evitar execução dinâmica
- [ ] Restringir tipos aceitos

---

# 10. Rate Limiting & DoS Protection (HIGH)

## Checklist

- [ ] Rate limiting
- [ ] Timeout
- [ ] Payload limits
- [ ] Paginação obrigatória
- [ ] Limite de queries complexas

## Exemplo

```typescript
app.use(
  rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 100
  })
);
```

---

# 11. Headers de Segurança (MEDIUM)

## Checklist

- [ ] CSP
- [ ] HSTS
- [ ] X-Frame-Options
- [ ] X-Content-Type-Options
- [ ] Referrer-Policy

## Exemplo

```typescript
app.use(
  helmet({
    contentSecurityPolicy: true,
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true
    },
    xFrameOptions: {
      action: "deny"
    },
    noSniff: true
  })
);
```

---

# 12. Logs e Observabilidade (MEDIUM)

## Regra

Logs nunca devem expor:
- senhas;
- tokens;
- cookies;
- cartões;
- dados pessoais sensíveis.

## Checklist

- [ ] Sanitizar logs
- [ ] Mascarar secrets
- [ ] Evitar stack traces públicos
- [ ] Implementar auditoria

---

# 13. Dependências e Supply Chain (HIGH)

## Checklist

- [ ] Verificar dependências vulneráveis
- [ ] Verificar typosquatting
- [ ] Verificar dependências abandonadas
- [ ] Fixar versões críticas
- [ ] Utilizar lockfiles

## Ferramentas recomendadas

- npm audit
- pnpm audit
- Snyk
- Dependabot
- Trivy

---

# 14. Segurança em IA e LLMs (HIGH)

## Checklist

- [ ] Detectar Prompt Injection
- [ ] Detectar exposição de secrets via prompts
- [ ] Validar ferramentas permitidas
- [ ] Restringir execução arbitrária
- [ ] Sanitizar contexto enviado ao modelo
- [ ] Validar outputs do modelo

## Riscos comuns

- Prompt Injection
- Data Exfiltration
- Tool Abuse
- Jailbreak
- Context Poisoning

---

# 15. Severidade dos Achados

| Severidade | Significado | Ação Necessária |
|---|---|---|
| 🔴 CRITICAL | Secret exposto, SQL Injection, Auth bypass, RCE | Bloquear merge imediatamente |
| 🟠 HIGH | XSS, SSRF, validação insuficiente, DoS, CSRF | Corrigir antes do merge |
| 🟡 MEDIUM | Headers faltando, logs inseguros | Corrigir rapidamente |
| 🔵 LOW | Melhorias arquiteturais | Planejar melhoria futura |

---

# 16. Diretrizes Gerais da Revisão

Durante revisões:
- priorizar vulnerabilidades exploráveis;
- evitar falsos positivos;
- considerar contexto real;
- considerar trust boundaries;
- considerar impacto real;
- verificar exploitabilidade;
- verificar exposição pública;
- verificar multi-tenant risks.

Toda vulnerabilidade deve incluir:
- descrição;
- impacto;
- vetor de exploração;
- severidade;
- evidência;
- recomendação de correção.