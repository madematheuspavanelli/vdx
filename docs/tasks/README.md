# Tasks - VDX

## Estrutura de Execução

Este diretório contém o planejamento detalhado de execução do projeto VDX, organizado em sprints iterativos que entregam valor incremental.

## Metodologia

- **Sprint**: 2 semanas
- **Planejamento**: Documentation-Driven Development
- **Execução**: Agnóstico (humano ou IA)
- **Validação**: Testes automatizados + Acceptance Criteria
- **Abordagem**: Frontend-first com API mockada (MSW)

## Estratégia de Desenvolvimento

### Frontend-First Approach

O projeto será desenvolvido priorizando o **frontend**, que será construído de forma **independente do backend** utilizando:

- **MSW (Mock Service Worker)** - Para mockar APIs REST durante desenvolvimento e testes
- **Desenvolvimento isolado** - Frontend não depende do backend para funcionar
- **Iteração rápida** - UI/UX pode ser validada sem aguardar backend
- **Testes confiáveis** - Testes de frontend não flaky por dependência de API real

### Por que Frontend-First?

1. **Validação rápida de UX** - Stakeholders podem ver e testar a interface rapidamente
2. **Design System early** - Componentes e padrões definidos desde o início
3. **Contrato de API claro** - Mocks definem o contrato esperado da API
4. **Desenvolvimento paralelo** - Backend pode ser desenvolvido seguindo o contrato
5. **Facilita testes** - Frontend 100% testável sem infraestrutura

## Organização

### Sprints

Cada sprint está documentado em um arquivo separado, organizados em **ordem de execução**:

- [Sprint 0](./sprint-0-setup.md) - Setup inicial do frontend (Vue 3 + MSW)
- [Sprint 1](./sprint-1-authentication.md) - Autenticação e usuários (Frontend + Mocks)
- [Sprint 2](./sprint-2-organization.md) - Estrutura organizacional (Frontend + Mocks)
- [Sprint 3](./sprint-3-onboarding.md) - Sistema de onboarding (Frontend + Mocks)
- [Sprint 4](./sprint-4-assessment.md) - Avaliações de competência (Frontend + Mocks)
- [Sprint 5](./sprint-5-pdi.md) - PDI e desenvolvimento (Frontend + Mocks)
- [Sprint 6](./sprint-6-analytics.md) - KPIs e analytics (Frontend + Mocks)
- [Sprint 7](./sprint-7-backend.md) - Implementação do Backend Real
- [Sprint 8](./sprint-8-integration.md) - Integração Frontend + Backend
- [Sprint 9](./sprint-9-polish.md) - Refinamentos e otimizações

### User Stories

Cada sprint contém user stories no formato:

```markdown
## US-XXX: Título da User Story

**Como**: [persona]
**Quero**: [funcionalidade]
**Para**: [benefício/valor]

### Critérios de Aceitação

- [ ] Critério 1
- [ ] Critério 2

### Tarefas Técnicas

- [ ] Task 1 (Backend)
- [ ] Task 2 (Frontend)
- [ ] Task 3 (Testes)

### DoD (Definition of Done)

- [ ] Testes unitários > 80% coverage
- [ ] Testes E2E passando
- [ ] Code review aprovado
- [ ] Documentação atualizada
```

## Priorização

### Must Have (MVP) - Frontend

- Sprint 0: Setup Frontend + MSW
- Sprint 1: Autenticação (UI + Mocks)
- Sprint 2: Organização (UI + Mocks)
- Sprint 3: Onboarding (UI + Mocks)

### Should Have - Frontend

- Sprint 4: Avaliações (UI + Mocks)
- Sprint 5: PDI (UI + Mocks)
- Sprint 6: Analytics (UI + Mocks)

### Must Have - Backend

- Sprint 7: Backend Real (Domain + Application + Infrastructure)
- Sprint 8: Integração (Substituir mocks por API real)

### Could Have

- Sprint 9: Polish e Otimizações

## Princípios

1. **Frontend-First**: Priorizar UI/UX antes do backend
2. **Incremental**: Cada sprint entrega valor usável visualmente
3. **Testável**: Acceptance criteria claros e testáveis (com MSW)
4. **Documentado**: Especificação detalhada antes da implementação
5. **Independente**: Frontend não depende de backend real (usa mocks)
6. **Granular**: Tasks pequenas e focadas
7. **Contract-Driven**: Mocks definem o contrato da API

## MSW (Mock Service Worker)

### O que é MSW?

[MSW](https://mswjs.io/) é uma biblioteca que intercepta requisições HTTP no nível de service worker (browser) ou http/https modules (Node.js), permitindo mockar APIs REST de forma realista.

### Por que usar MSW?

1. **Realismo**: Intercepta requisições reais, não substitui fetch/axios
2. **Reutilização**: Mesmos mocks para desenvolvimento e testes
3. **Tipos seguros**: TypeScript support completo
4. **Network tab**: Requests aparecem no DevTools Network
5. **Debugging**: Fácil debug pois comporta-se como API real

### Benefícios para o Projeto

- ✅ **Desenvolvimento independente**: Frontend não precisa esperar backend
- ✅ **Testes confiáveis**: Sem flakiness de rede ou banco de dados
- ✅ **Iteração rápida**: Mudanças de UI sem deploy de backend
- ✅ **Definição de contrato**: Mocks documentam API esperada
- ✅ **Onboarding facilitado**: Novos devs não precisam configurar backend
- ✅ **Demos**: Apresentações não dependem de infraestrutura

### Estrutura de Mocks

```
apps/frontend/src/
├── shared/
│   └── api/
│       ├── mocks/
│       │   ├── browser.ts        # Setup para browser
│       │   ├── server.ts         # Setup para Node (testes)
│       │   └── handlers/
│       │       ├── auth.handlers.ts
│       │       ├── users.handlers.ts
│       │       ├── onboarding.handlers.ts
│       │       └── index.ts
│       └── client.ts
```

### Exemplo de Handler

```typescript
// apps/frontend/src/shared/api/mocks/handlers/auth.handlers.ts
import { http, HttpResponse } from "msw";

export const authHandlers = [
  // POST /api/v1/auth/login
  http.post("/api/v1/auth/login", async ({ request }) => {
    const { email, password } = await request.json();

    // Simular validação
    if (email === "admin@vdx.com" && password === "Admin123") {
      return HttpResponse.json({
        success: true,
        data: {
          accessToken: "mock-access-token",
          refreshToken: "mock-refresh-token",
          expiresIn: 3600,
          user: {
            id: "usr_1",
            name: "Admin User",
            email: "admin@vdx.com",
            role: "admin",
          },
        },
      });
    }

    return HttpResponse.json(
      {
        success: false,
        error: {
          code: "INVALID_CREDENTIALS",
          message: "Email or password is incorrect",
        },
      },
      { status: 401 }
    );
  }),
];
```

### Transição para Backend Real

Quando o backend estiver pronto:

1. Manter os mocks para testes
2. Adicionar variável de ambiente para toggle:
   ```typescript
   // Usar MSW apenas em dev/test
   if (import.meta.env.MODE === "development" || import.meta.env.VITEST) {
     const { worker } = await import("./mocks/browser");
     await worker.start();
   }
   ```
3. Em produção, requisições vão para API real automaticamente

## Como Executar

### Para Desenvolvedores

1. Leia a documentação do sprint completo
2. Escolha uma user story
3. Leia requisitos e acceptance criteria
4. Implemente seguindo DoD
5. Execute testes
6. Abra PR com checklist
7. Code review
8. Merge

### Para Agentes IA

1. Carregue contexto do sprint
2. Parse user stories e tasks
3. Implemente incrementalmente
4. Execute testes após cada task
5. Valide acceptance criteria
6. Gere relatório de conclusão

## Tracking

Use a ferramenta de sua preferência:

- GitHub Projects
- Jira
- Linear
- Planilha

## Métricas

Acompanhe:

- **Velocity**: Story points por sprint
- **Lead Time**: Tempo de desenvolvimento
- **Cycle Time**: Tempo em cada estágio
- **Quality**: Bugs encontrados pós-release
- **Coverage**: Cobertura de testes

## Retrospectivas

Ao fim de cada sprint:

1. O que funcionou bem?
2. O que pode melhorar?
3. Ações para próximo sprint
4. Atualizar esta documentação

---

**Última atualização**: Janeiro 2024
