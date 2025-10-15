# Developer Guide - VDX

## Bem-vindo!

Este guia fornece todas as informações necessárias para você começar a desenvolver no projeto VDX. Seja bem-vindo ao time!

## Pré-requisitos

### Obrigatórios

- **Node.js**: 22.x ou superior ([Download](https://nodejs.org/))
- **pnpm**: 8.x ou superior (`npm install -g pnpm`)
- **Git**: 2.x ou superior
- **Docker**: 24.x ou superior (para ambiente local)
- **Docker Compose**: 2.x ou superior

### Recomendados

- **VS Code** com extensões:
  - ESLint
  - Prettier
  - Volar (Vue Language Features)
  - TypeScript Vue Plugin (Volar)
  - Tailwind CSS IntelliSense
  - Vitest
- **Postman** ou **Insomnia** para testar APIs
- **PostgreSQL Client** (DBeaver, pgAdmin, etc.)

## Setup Inicial

### 1. Clone o Repositório

```bash
git clone https://github.com/seu-org/vdx.git
cd vdx
```

### 2. Instale as Dependências

```bash
pnpm install
```

### 3. Configure Variáveis de Ambiente

Copie os arquivos de exemplo e ajuste conforme necessário:

```bash
# Backend
cp apps/backend/.env.example apps/backend/.env

# Frontend
cp apps/frontend/.env.example apps/frontend/.env
```

### 4. Suba o Ambiente com Docker

```bash
docker-compose up -d
```

Isso irá iniciar:

- PostgreSQL (porta 5432)
- Redis (porta 6379)
- MailHog (porta 8025 para interface web)

### 5. Execute as Migrações

```bash
cd apps/backend
pnpm db:migrate
pnpm db:seed
```

### 6. Inicie os Serviços

Em terminais separados:

```bash
# Backend
cd apps/backend
pnpm dev

# Frontend
cd apps/frontend
pnpm dev
```

### 7. Acesse a Aplicação

- **Frontend**: http://localhost:5173
- **Backend API**: http://localhost:3000
- **API Docs**: http://localhost:3000/api-docs
- **MailHog**: http://localhost:8025

## Estrutura do Projeto

```
vdx/
├── apps/
│   ├── backend/           # API Node.js + TypeScript
│   └── frontend/          # SPA Vue 3 + TypeScript
├── packages/              # Pacotes compartilhados (futuramente)
├── docs/                  # Documentação
├── docker-compose.yml     # Ambiente local
└── package.json           # Root workspace config
```

## Fluxo de Desenvolvimento

### 1. Escolha uma Task

- Verifique o board do projeto (GitHub Projects, Jira, etc.)
- Pegue uma task **"Ready for Development"**
- Mova para **"In Progress"**
- Leia a descrição e critérios de aceitação

### 2. Crie uma Branch

```bash
git checkout main
git pull origin main
git checkout -b feature/RF003-onboarding-journey
```

**Convenção de Branches**:

- `feature/`: novas funcionalidades
- `fix/`: correção de bugs
- `refactor/`: refatorações
- `docs/`: mudanças em documentação

### 3. Desenvolva

#### Backend

**Passo a passo para nova funcionalidade**:

1. **Domain Layer**: Crie entidades, value objects, domain services
2. **Application Layer**: Crie use cases, DTOs, interfaces de repositórios
3. **Infrastructure Layer**: Implemente repositórios, serviços externos
4. **Presentation Layer**: Crie controllers e rotas

**Exemplo - Criar Use Case**:

```typescript
// apps/backend/src/application/onboarding/use-cases/start-onboarding.use-case.ts

import { Injectable } from "@nestjs/common";
import { OnboardingRepository } from "../repositories/onboarding.repository.interface";
import { StartOnboardingDto } from "../dtos/start-onboarding.dto";

@Injectable()
export class StartOnboardingUseCase {
  constructor(private readonly onboardingRepository: OnboardingRepository) {}

  async execute(dto: StartOnboardingDto) {
    // Lógica do caso de uso
  }
}
```

#### Frontend

**Passo a passo para nova feature**:

1. **Entities**: Crie models e tipos
2. **Features**: Crie API clients, stores (Pinia), composables
3. **Widgets**: Crie componentes complexos de UI
4. **Pages**: Integre tudo na página/rota

**Exemplo - Criar Feature Store**:

```typescript
// apps/frontend/src/features/onboarding/model/store.ts

import { defineStore } from "pinia";
import { ref } from "vue";
import type { OnboardingJourney } from "@/entities/onboarding";
import { onboardingApi } from "../api";

export const useOnboardingStore = defineStore("onboarding", () => {
  const currentJourney = ref<OnboardingJourney | null>(null);
  const loading = ref(false);

  async function fetchCurrentJourney() {
    loading.value = true;
    try {
      currentJourney.value = await onboardingApi.getCurrent();
    } finally {
      loading.value = false;
    }
  }

  return {
    currentJourney,
    loading,
    fetchCurrentJourney,
  };
});
```

### 4. Testes

#### Backend - Vitest

```bash
# Rodar todos os testes
pnpm test

# Modo watch
pnpm test:watch

# Coverage
pnpm test:coverage
```

**Exemplo de Teste**:

```typescript
import { describe, it, expect, beforeEach } from "vitest";
import { StartOnboardingUseCase } from "./start-onboarding.use-case";

describe("StartOnboardingUseCase", () => {
  let useCase: StartOnboardingUseCase;

  beforeEach(() => {
    // Setup
  });

  it("should start onboarding journey", async () => {
    // Arrange
    const dto = { employeeId: "123", templateId: "456" };

    // Act
    const result = await useCase.execute(dto);

    // Assert
    expect(result).toBeDefined();
    expect(result.status).toBe("IN_PROGRESS");
  });
});
```

#### Frontend - Vitest + Playwright

```bash
# Unit tests (Vitest)
pnpm test

# E2E tests (Playwright)
pnpm test:e2e
```

**Testes com MSW**:

O frontend utiliza [MSW (Mock Service Worker)](https://mswjs.io/) para mockar APIs durante testes, garantindo testes rápidos e confiáveis sem depender de backend real.

```typescript
import { describe, it, expect, beforeAll, afterAll, afterEach } from "vitest";
import { render, screen, waitFor } from "@testing-library/vue";
import { server } from "@/shared/api/mocks/server";
import LoginPage from "./LoginPage.vue";

// Setup MSW
beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe("LoginPage", () => {
  it("should login successfully with valid credentials", async () => {
    const { user } = render(LoginPage);

    await user.type(screen.getByLabelText(/email/i), "admin@vdx.com");
    await user.type(screen.getByLabelText(/senha/i), "Admin123");
    await user.click(screen.getByRole("button", { name: /entrar/i }));

    await waitFor(() => {
      expect(screen.getByText(/bem-vindo/i)).toBeInTheDocument();
    });
  });
});
```

**Vantagens do MSW**:

- ✅ Testes não dependem de backend rodando
- ✅ Testes são determinísticos e rápidos
- ✅ Mesmos mocks para dev e testes
- ✅ Network requests reais (aparecem no DevTools)

### 4.1. MSW - Mock Service Worker

O projeto usa MSW para desenvolvimento e testes do frontend **independente do backend**.

#### Setup do MSW

**Instalação** (já incluída no Sprint 0):

```bash
cd apps/frontend
pnpm add -D msw
```

**Inicialização**:

```bash
# Gerar service worker público
pnpm dlx msw init public/ --save
```

**Estrutura**:

```
apps/frontend/src/shared/api/mocks/
├── browser.ts              # Setup para desenvolvimento
├── server.ts               # Setup para testes (Node)
└── handlers/
    ├── auth.handlers.ts    # Handlers de autenticação
    ├── users.handlers.ts   # Handlers de usuários
    └── index.ts            # Export de todos handlers
```

#### Criando Handlers

```typescript
// apps/frontend/src/shared/api/mocks/handlers/auth.handlers.ts
import { http, HttpResponse } from "msw";

export const authHandlers = [
  http.post("/api/v1/auth/login", async ({ request }) => {
    const { email, password } = await request.json();

    if (email === "admin@vdx.com" && password === "Admin123") {
      return HttpResponse.json({
        success: true,
        data: {
          accessToken: "mock-jwt-token",
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

#### Ativando MSW

**Em desenvolvimento** (`apps/frontend/src/main.ts`):

```typescript
import { createApp } from "vue";
import App from "./app/App.vue";

async function enableMocking() {
  if (import.meta.env.MODE !== "development") {
    return;
  }

  const { worker } = await import("./shared/api/mocks/browser");
  return worker.start({
    onUnhandledRequest: "warn",
  });
}

enableMocking().then(() => {
  createApp(App).mount("#app");
});
```

**Em testes** (`vitest.setup.ts`):

```typescript
import { beforeAll, afterEach, afterAll } from "vitest";
import { server } from "./src/shared/api/mocks/server";

beforeAll(() => server.listen({ onUnhandledRequest: "error" }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

#### Debugging com MSW

```typescript
// Ver todas as requisições interceptadas
worker.start({
  onUnhandledRequest: "warn",
  quiet: false, // Log todas requisições
});

// Override handler para teste específico
import { http, HttpResponse } from "msw";
import { server } from "@/shared/api/mocks/server";

it("should handle server error", async () => {
  server.use(
    http.post("/api/v1/auth/login", () => {
      return HttpResponse.json({ error: "Internal error" }, { status: 500 });
    })
  );

  // Test error handling...
});
```

#### Transição para Backend Real

Quando o backend estiver pronto:

1. **Manter MSW para testes**
2. **Desabilitar em produção**:
   ```typescript
   // Apenas em dev ou test
   if (import.meta.env.DEV || import.meta.env.VITEST) {
     await enableMocking();
   }
   ```
3. **Toggle via env var**:
   ```env
   VITE_USE_MOCKS=true  # desenvolvimento
   VITE_USE_MOCKS=false # produção
   ```

### 4.2. shadcn-vue - Sistema de Componentes

O projeto utiliza **[shadcn-vue](https://www.shadcn-vue.com/)** como sistema de componentes UI, construído sobre **TailwindCSS v4** e **Radix Vue**.

#### O que é shadcn-vue?

shadcn-vue **não é uma biblioteca de componentes tradicional**. É uma **coleção de componentes reutilizáveis** que você pode **copiar e colar** em sua aplicação.

**Vantagens**:

- ✅ **Você é dono do código** - componentes ficam no seu projeto
- ✅ **Totalmente customizável** - modifique como quiser
- ✅ **Acessível** - construído com Radix Vue (WAI-ARIA compliant)
- ✅ **Styled com TailwindCSS** - fácil de tematizar
- ✅ **TypeScript first** - tipos completos
- ✅ **Dark mode** - suporte nativo

#### Adicionando Componentes

Use o CLI para adicionar componentes ao projeto:

```bash
cd apps/frontend

# Adicionar um componente específico
pnpm dlx shadcn-vue@latest add button

# Adicionar múltiplos componentes
pnpm dlx shadcn-vue@latest add button card input label

# Listar todos componentes disponíveis
pnpm dlx shadcn-vue@latest add
```

Isso copia o código do componente para `src/shared/ui/`.

#### Usando Componentes

```vue
<script setup lang="ts">
import { Button } from "@/shared/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/shared/ui/card";
import { Input } from "@/shared/ui/input";
import { Label } from "@/shared/ui/label";
</script>

<template>
  <Card class="w-full max-w-md">
    <CardHeader>
      <CardTitle>Login</CardTitle>
      <CardDescription> Entre com suas credenciais </CardDescription>
    </CardHeader>
    <CardContent class="space-y-4">
      <div class="space-y-2">
        <Label for="email">Email</Label>
        <Input id="email" type="email" placeholder="seu@email.com" />
      </div>
      <Button class="w-full"> Entrar </Button>
    </CardContent>
  </Card>
</template>
```

#### Customizando Componentes

Como os componentes estão no seu código, você pode modificá-los diretamente:

```typescript
// src/shared/ui/button.vue
<script setup lang="ts">
import { computed } from 'vue';
import { cn } from '@/shared/lib/utils';

const props = defineProps<{
  variant?: 'default' | 'destructive' | 'outline' | 'ghost' | 'link';
  size?: 'default' | 'sm' | 'lg' | 'icon';
}>();

// Adicionar nova variante personalizada
const buttonClasses = computed(() =>
  cn(
    'inline-flex items-center justify-center rounded-md font-medium',
    {
      'bg-primary text-primary-foreground': props.variant === 'default',
      'bg-destructive text-destructive-foreground': props.variant === 'destructive',
      // Sua variante customizada
      'bg-gradient-to-r from-blue-500 to-purple-600': props.variant === 'gradient',
    }
  )
);
</script>
```

#### Tematização

O shadcn-vue usa **CSS variables** para temas. Configure em `src/app/styles/index.css`:

```css
@import "tailwindcss";

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    /* ... mais variáveis */
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
    --primary: 210 40% 98%;
    --primary-foreground: 222.2 47.4% 11.2%;
    /* ... mais variáveis */
  }
}
```

#### Componentes Recomendados para VDX

Para este projeto, os componentes mais úteis são:

**Formulários**:

```bash
pnpm dlx shadcn-vue@latest add form input label checkbox select textarea
```

**Navegação**:

```bash
pnpm dlx shadcn-vue@latest add navigation-menu tabs breadcrumb
```

**Feedback**:

```bash
pnpm dlx shadcn-vue@latest add alert toast dialog alert-dialog
```

**Data Display**:

```bash
pnpm dlx shadcn-vue@latest add table card badge avatar
```

**Layout**:

```bash
pnpm dlx shadcn-vue@latest add separator sheet sidebar
```

#### Integração com Formulários

shadcn-vue funciona perfeitamente com bibliotecas de validação:

```vue
<script setup lang="ts">
import { useForm } from "vee-validate";
import { toTypedSchema } from "@vee-validate/zod";
import * as z from "zod";
import { Button } from "@/shared/ui/button";
import {
  FormField,
  FormItem,
  FormLabel,
  FormControl,
  FormMessage,
} from "@/shared/ui/form";
import { Input } from "@/shared/ui/input";

const formSchema = toTypedSchema(
  z.object({
    email: z.string().email(),
    password: z.string().min(8),
  })
);

const { handleSubmit } = useForm({
  validationSchema: formSchema,
});

const onSubmit = handleSubmit((values) => {
  console.log(values);
});
</script>

<template>
  <form @submit="onSubmit">
    <FormField v-slot="{ field }" name="email">
      <FormItem>
        <FormLabel>Email</FormLabel>
        <FormControl>
          <Input v-bind="field" type="email" />
        </FormControl>
        <FormMessage />
      </FormItem>
    </FormField>

    <Button type="submit">Login</Button>
  </form>
</template>
```

#### Recursos

- **Documentação**: https://www.shadcn-vue.com/docs
- **Componentes**: https://www.shadcn-vue.com/docs/components
- **Temas**: https://www.shadcn-vue.com/themes
- **Exemplos**: https://www.shadcn-vue.com/blocks

### 5. Code Quality

#### Linting

```bash
# Backend
cd apps/backend
pnpm lint
pnpm lint:fix

# Frontend
cd apps/frontend
pnpm lint
pnpm lint:fix
```

#### Formatação

```bash
# Todo o projeto
pnpm format
```

### 6. Commit

Seguimos **Conventional Commits**:

```bash
git add .
git commit -m "feat(onboarding): add start journey endpoint"
```

**Tipos**:

- `feat`: nova funcionalidade
- `fix`: correção de bug
- `docs`: documentação
- `style`: formatação (sem mudança de lógica)
- `refactor`: refatoração
- `test`: adição/correção de testes
- `chore`: configuração, build, etc.

**Escopo**: módulo afetado (onboarding, assessment, pdi, etc.)

### 7. Push e Pull Request

```bash
git push origin feature/RF003-onboarding-journey
```

Abra um Pull Request no GitHub:

- **Título**: descrição clara da mudança
- **Descrição**:
  - O que foi feito
  - Por que foi feito
  - Como testar
  - Screenshots (se aplicável)
- **Link para issue/task**
- **Checklist**:
  - [ ] Testes passando
  - [ ] Lint sem erros
  - [ ] Documentação atualizada
  - [ ] Reviewed por pelo menos 1 pessoa

### 8. Code Review

- Responda aos comentários
- Faça ajustes necessários
- Push de novos commits
- Aguarde aprovação

### 9. Merge

Após aprovação:

- **Squash and Merge** (padrão)
- Delete a branch
- Mova task para **"Done"**

## Boas Práticas

### Código Limpo

#### SOLID

- **S**ingle Responsibility: uma classe, uma responsabilidade
- **O**pen/Closed: aberto para extensão, fechado para modificação
- **L**iskov Substitution: subtipos devem ser substituíveis
- **I**nterface Segregation: interfaces coesas e específicas
- **D**ependency Inversion: dependa de abstrações

#### DRY (Don't Repeat Yourself)

- Extraia lógica duplicada para funções/classes
- Use composição e herança adequadamente

#### KISS (Keep It Simple, Stupid)

- Prefira soluções simples
- Não over-engineer

#### YAGNI (You Aren't Gonna Need It)

- Não adicione funcionalidades "por precaução"
- Desenvolva o que é necessário agora

### TypeScript

```typescript
// ✅ Bom - tipos explícitos e específicos
interface CreateUserDto {
  name: string;
  email: string;
  role: "admin" | "hr" | "manager" | "employee";
}

function createUser(dto: CreateUserDto): Promise<User> {
  // ...
}

// ❌ Ruim - any, tipos genéricos demais
function createUser(data: any): Promise<any> {
  // ...
}
```

### Vue 3 Composition API

```typescript
// ✅ Bom - composables reutilizáveis, lógica separada
import { ref, computed } from "vue";

export function useOnboarding() {
  const journey = ref<Journey | null>(null);
  const progress = computed(() => {
    if (!journey.value) return 0;
    return (journey.value.completedTasks / journey.value.totalTasks) * 100;
  });

  async function loadJourney() {
    // ...
  }

  return { journey, progress, loadJourney };
}

// No componente
const { journey, progress, loadJourney } = useOnboarding();
```

### Testes

```typescript
// ✅ Bom - testes focados, arrange-act-assert
it("should mark task as completed", () => {
  // Arrange
  const task = new Task({ id: "1", title: "Setup", status: "pending" });

  // Act
  task.complete();

  // Assert
  expect(task.status).toBe("completed");
  expect(task.completedAt).toBeDefined();
});

// ❌ Ruim - teste genérico, múltiplas responsabilidades
it("should work", () => {
  const task = new Task({ id: "1" });
  task.complete();
  expect(task.status).toBe("completed");
  task.uncomplete();
  expect(task.status).toBe("pending");
  // ...
});
```

## Troubleshooting

### Erro: "Port 3000 already in use"

```bash
# Encontre o processo
lsof -ti:3000

# Mate o processo
kill -9 <PID>
```

### Erro: "Cannot connect to database"

```bash
# Verifique se o container está rodando
docker-compose ps

# Reinicie os serviços
docker-compose restart postgres
```

### Erro: "Module not found"

```bash
# Reinstale as dependências
pnpm install

# Limpe cache e reinstale
rm -rf node_modules
pnpm install
```

### Testes falhando

```bash
# Execute um teste específico
pnpm test path/to/test.spec.ts

# Modo verbose
pnpm test --reporter=verbose

# Limpe cache de testes
pnpm test --clearCache
```

## Recursos Úteis

### Documentação Oficial

- [Vue 3](https://vuejs.org/)
- [TypeScript](https://www.typescriptlang.org/)
- [Vite](https://vitejs.dev/)
- [Vitest](https://vitest.dev/)
- [TailwindCSS v4](https://tailwindcss.com/docs/v4-beta)
- [shadcn-vue](https://www.shadcn-vue.com/)
- [Pinia](https://pinia.vuejs.org/)
- [TanStack Query](https://tanstack.com/query/latest)
- [MSW (Mock Service Worker)](https://mswjs.io/)

### Design Patterns

- [Refactoring Guru](https://refactoring.guru/)
- [Feature-Sliced Design](https://feature-sliced.design/)

### Livros Recomendados

- Clean Code (Robert C. Martin)
- Domain-Driven Design (Eric Evans)
- Clean Architecture (Robert C. Martin)
- Refactoring (Martin Fowler)

## Contato

Dúvidas? Entre em contato:

- **Slack**: #vdx-dev
- **Email**: dev-team@empresa.com
- **Tech Lead**: @tech-lead

## Contribuindo

Leia nosso [Guia de Contribuição](./CONTRIBUTING.md) para mais detalhes sobre:

- Code of Conduct
- Processo de review
- Padrões de código
- Como reportar bugs
- Como sugerir features
