# Stage 17 — Product UX + Growth (refatoração pós-council)

| Campo | Valor |
|-------|-------|
| **ID** | `stage-17-product-ux-growth` |
| **Status** | `done` |
| **Persona** | Planner (+ UX + Growth) → Developer → Q.A. |
| **Pacotes** | `goroute-mobile` (P0), `goroute-frontend` (tokens/paridade P1) |
| **Depende de** | stage-16 `done` |
| **Council** | [`advisory/ux-ui.md`](./advisory/ux-ui.md), [`advisory/growth-marketing.md`](./advisory/growth-marketing.md) |

---

## 1. Meta

Refatorar a experiência do produto (prioridade **mobile**) conforme council UX + Growth: eliminar dead-ends, reduzir toques até mentor/ação, alinhar marca usável, e preparar superfície de aquisição (ASO/landing copy) sem misturar CTA de anúncio com UI interna.

---

## 2. In scope / Out of scope

### In scope
- Mobile: home hub, mentor contextual + histórico sessão, premium weekly CTA, job→trilha, badge notificações, login com dica de rede, tokens de cor alinhados
- Web: alinhar tokens CSS críticos (mint → primary usável) onde afeta contraste
- Docs store: título/subtítulo ASO no README mobile / notes (sem conta developer store)

### Out of scope
- Campanhas pagas, CRM, influenciadores
- Grafo vis-network no mobile (P2)
- Push FCM, Stripe in-app nativo
- BYOK key entry completo no mobile (permanece web)

---

## 3. Decisões do council (reconcile)

| Tema | Decisão | Fonte |
|------|---------|--------|
| Nav | Manter 5 tabs; mentor contextual, não 6ª aba | UX |
| Cores in-app | Primary `#0F6E56`; signal `#E85D4C` | UX |
| Cores ads | Coral `#FF6A3D` só anúncio/store | Growth |
| ASO | Opção 1 como default copy | Growth |
| Canais | LinkedIn + Shorts → backlog growth; engenharia não bloqueia | Growth → outOfScope execução mídia |

---

## 4. Fases

### Fase 1 — Mobile: atritos críticos (P0)

**Goal:** Usuário completa jornadas sem dead-end.  
**Aceite UX:** mentor ≤2 taps a partir de nó/vaga; weekly premium tem CTA; job fecha gap na trilha; home tem atalhos; badge unread.  
**Validation:** `flutter analyze` + `flutter test` + smoke device.

**Entregáveis:**
- Home: CTAs Mentor / Vagas / Notificações; badge unread
- Mentor: query `?context=` / extra; persistência local da sessão; entrada nos detalhes
- Weekly: empty premium → CTA planos (url configurável)
- Jobs detail: CTA “Abrir na trilha” para closing skill slug
- Login: dica de conexão quando erro de rede
- Theme: signal color + semantics leves

### Fase 2 — Mobile polish + ASO surface

**Goal:** Clareza e aquisição.  
**Aceite:** copy ASO no README; tela Mais “Sobre / Ajuda conexão”; skeletons/feedback consistentes.  
**Validation:** `flutter test` + widget Mais.

### Fase 3 — Web token parity (P1)

**Goal:** Identidade única usável.  
**Aceite:** variáveis CSS primary alinhadas; sem regressão visual grave no dashboard.  
**Validation:** Vitest afetados + Playwright MCP smoke home/login.

---

## 5. Handoff Q.A.

| Fase | Verificar |
|------|-----------|
| 1 | Fluxos mentor contextual, premium CTA, deep link trilha, badge |
| 2 | Ajuda LAN, README ASO |
| 3 | Contraste mint/primary web |

Smoke: `smoke-qa@goroute.local` + APK **debug LAN**.
