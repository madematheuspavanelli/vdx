# Sprint 0 - Setup Frontend e MSW

**Duração**: 2 semanas  
**Objetivo**: Configurar ambiente de desenvolvimento frontend-first com MSW para APIs mockadas

## Estratégia Frontend-First

Este sprint foca **exclusivamente no frontend**, configurando:

- ✅ Projeto Vue 3 + TypeScript + Vite
- ✅ TailwindCSS v4 + shadcn-vue para componentes
- ✅ MSW (Mock Service Worker) para APIs mockadas
- ✅ Feature-Sliced Design
- ✅ Testes com Vitest
- ✅ CI/CD básico

**Backend e infraestrutura serão desenvolvidos em sprints futuros.**

## Metas do Sprint

- ✅ Monorepo configurado (mínimo necessário)
- ✅ Frontend Vue 3 rodando
- ✅ TailwindCSS v4 + shadcn-vue configurados
- ✅ MSW configurado e funcionando
- ✅ Estrutura Feature-Sliced Design
- ✅ Testes unitários com MSW
- ✅ CI/CD para frontend

---

## US-000: Configurar Monorepo

**Como**: Desenvolvedor  
**Quero**: Ter um monorepo configurado com pnpm workspaces  
**Para**: Gerenciar frontend e backend em um único repositório

### Critérios de Aceitação

- [ ] Repositório Git inicializado
- [ ] pnpm workspace configurado
- [ ] Node.js 22 como versão mínima
- [ ] Scripts root funcionando (`pnpm install`, `pnpm build`, `pnpm test`)
- [ ] README.md com instruções básicas

### Tarefas Técnicas

#### T-000.1: Inicializar repositório

```bash
mkdir vdx && cd vdx
git init
pnpm init
```

#### T-000.2: Configurar pnpm workspace

Criar `pnpm-workspace.yaml`:

```yaml
packages:
  - "apps/*"
  - "packages/*"
```

#### T-000.3: Configurar package.json root

```json
{
  "name": "vdx",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "pnpm --parallel --stream dev",
    "build": "pnpm --recursive build",
    "test": "pnpm --recursive test",
    "lint": "pnpm --recursive lint",
    "format": "prettier --write \"**/*.{ts,tsx,js,jsx,json,md}\""
  },
  "devDependencies": {
    "prettier": "^3.1.0",
    "typescript": "^5.3.0"
  },
  "engines": {
    "node": ">=22.0.0",
    "pnpm": ">=8.0.0"
  }
}
```

#### T-000.4: Configurar .gitignore

```gitignore
node_modules/
dist/
build/
.env
.env.local
*.log
.DS_Store
coverage/
.vscode/
.idea/
```

### DoD

- [ ] `pnpm install` executa sem erros
- [ ] Estrutura de workspace reconhecida
- [ ] Git configurado com .gitignore

---

## US-001: Setup do Frontend Vue 3

**Como**: Desenvolvedor Frontend  
**Quero**: Projeto Vue 3 configurado e rodando  
**Para**: Começar a desenvolver a interface do usuário

### Critérios de Aceitação

- [ ] Vite + Vue 3 + TypeScript funcionando
- [ ] Aplicação rodando em localhost:5173
- [ ] Hot reload funcional
- [ ] TailwindCSS v4 + shadcn-vue aplicando estilos
- [ ] ESLint e Prettier configurados
- [ ] Componentes shadcn-vue funcionando

### Tarefas Técnicas

#### T-001.1: Criar projeto na pasta apps/frontend

**Importante**: A pasta `apps/frontend` já existe no repositório. Vamos configurá-la.

```bash
cd apps/frontend

# Se a pasta estiver vazia, inicialize com Vite
# Caso contrário, pule este passo
pnpm create vite . --template vue-ts
```

#### T-001.2: Configurar package.json do backend

```json
{
  "name": "@vdx/backend",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "tsx watch src/main.ts",
    "build": "tsc",
    "test": "vitest",
    "test:coverage": "vitest --coverage",
    "lint": "eslint src --ext .ts",
    "lint:fix": "eslint src --ext .ts --fix"
  },
  "dependencies": {
    "express": "^4.18.2",
    "dotenv": "^16.3.1"
  },
  "devDependencies": {
    "@types/express": "^4.17.21",
    "@types/node": "^20.10.0",
    "@typescript-eslint/eslint-plugin": "^6.13.0",
    "@typescript-eslint/parser": "^6.13.0",
    "eslint": "^8.55.0",
    "tsx": "^4.7.0",
    "typescript": "^5.3.0",
    "vitest": "^1.0.0"
  }
}
```

#### T-001.3: Configurar TypeScript (tsconfig.json)

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "moduleResolution": "node",
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist", "tests"]
}
```

#### T-001.2: Instalar dependências principais

```bash
cd apps/frontend

pnpm add vue vue-router pinia axios
pnpm add -D @vitejs/plugin-vue vite typescript vue-tsc
pnpm add -D vitest @vue/test-utils happy-dom
pnpm add -D eslint eslint-plugin-vue @vue/eslint-config-typescript
pnpm add -D prettier @types/node
```

#### T-001.3: Instalar TailwindCSS v4 + shadcn-vue

**Passo 1: Instalar TailwindCSS v4**

```bash
pnpm add -D tailwindcss@next @tailwindcss/vite@next
```

**Passo 2: Configurar estilos base**

Criar `src/app/styles/index.css`:

```css
@import "tailwindcss";
```

**Passo 3: Atualizar tsconfig.json**

`tsconfig.json`:

```json
{
  "files": [],
  "references": [
    { "path": "./tsconfig.app.json" },
    { "path": "./tsconfig.node.json" }
  ],
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

**Passo 4: Atualizar tsconfig.app.json**

Adicionar ao `tsconfig.app.json`:

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

**Passo 5: Configurar Vite com TailwindCSS**

`vite.config.ts`:

```typescript
import path from "node:path";
import tailwindcss from "@tailwindcss/vite";
import vue from "@vitejs/plugin-vue";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [vue(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
  server: {
    port: 5173,
    open: true,
  },
});
```

**Passo 6: Inicializar shadcn-vue**

```bash
pnpm dlx shadcn-vue@latest init
```

Responda as perguntas do CLI:

- **Which color would you like to use as base color?** → Neutral (ou sua preferência)
- **Where is your global CSS file?** → src/app/styles/index.css
- **Would you like to use CSS variables for colors?** → Yes
- **Where is your tailwind.config.js located?** → (Enter - usa o padrão)
- **Configure the import alias for components** → @/shared/ui
- **Configure the import alias for utils** → @/shared/lib/utils

Isso criará:

- `components.json` - Configuração do shadcn-vue
- `src/shared/lib/utils.ts` - Utilitários (cn helper)
- `src/shared/ui/` - Diretório para componentes shadcn-vue

**Passo 7: Adicionar componentes iniciais**

```bash
# Adicionar componentes básicos para começar
pnpm dlx shadcn-vue@latest add button
pnpm dlx shadcn-vue@latest add card
pnpm dlx shadcn-vue@latest add input
pnpm dlx shadcn-vue@latest add label
```

#### T-001.4: Configurar Feature-Sliced Design

```bash
cd src
mkdir -p {app,pages,widgets,features,entities,shared}/{ui,api,model,lib,config}
```

Estrutura final:

```
src/
├── app/                    # App initialization
│   ├── providers/
│   ├── router/
│   ├── styles/
│   └── App.vue
├── pages/                  # Route pages
├── widgets/                # Complex UI blocks
├── features/               # Business features
├── entities/               # Business entities
└── shared/                 # Shared code
    ├── ui/                 # shadcn-vue components (auto-generated)
    ├── api/                # API client & mocks
    ├── lib/                # Utils (inclui utils.ts do shadcn)
    └── config/             # Constants
```

    └── utils.ts

````

#### T-001.5: Criar App.vue inicial com shadcn-vue

`src/app/App.vue`:

```vue
<script setup lang="ts">
import { ref } from "vue";
import { Button } from "@/shared/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/shared/ui/card";

const appReady = ref(true);
const count = ref(0);
</script>

<template>
  <div v-if="appReady" class="min-h-screen bg-background">
    <div class="flex items-center justify-center min-h-screen p-4">
      <Card class="w-full max-w-md">
        <CardHeader>
          <CardTitle class="text-3xl">VDX</CardTitle>
          <CardDescription> Plataforma de Gestão de Pessoas </CardDescription>
        </CardHeader>
        <CardContent class="space-y-4">
          <p class="text-sm text-muted-foreground">
            Vue 3 + TypeScript + TailwindCSS v4 + shadcn-vue
          </p>
          <div class="flex items-center gap-4">
            <Button @click="count++"> Clicks: {{ count }} </Button>
            <Button variant="outline"> Outlined Button </Button>
          </div>
        </CardContent>
      </Card>
    </div>
  </div>
</template>
````

`src/main.ts`:

```typescript
import { createApp } from "vue";
import App from "./app/App.vue";
import "./app/styles/index.css";

createApp(App).mount("#app");
```

#### T-001.6: Configurar package.json

```json
{
  "name": "@vdx/frontend",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "lint": "eslint . --ext .vue,.ts,.tsx",
    "lint:fix": "eslint . --ext .vue,.ts,.tsx --fix",
    "format": "prettier --write \"src/**/*.{vue,ts,tsx,js,json,md}\""
  }
}
```

#### T-001.7: Configurar TypeScript

`tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "preserve",

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,

    /* Path mapping */
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src/**/*.ts", "src/**/*.tsx", "src/**/*.vue"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

#### T-001.8: Configurar ESLint e Prettier

`.eslintrc.cjs`:

```javascript
module.exports = {
  root: true,
  env: {
    browser: true,
    es2021: true,
    node: true,
  },
  extends: [
    "eslint:recommended",
    "plugin:vue/vue3-recommended",
    "@vue/eslint-config-typescript",
  ],
  parserOptions: {
    ecmaVersion: "latest",
  },
  rules: {
    "vue/multi-word-component-names": "off",
  },
};
```

`.prettierrc`:

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100
}
```

### DoD

- [ ] `pnpm dev` inicia app em localhost:5173
- [ ] TailwindCSS v4 + shadcn-vue aplicando estilos corretamente
- [ ] Componentes shadcn-vue (Button, Card) funcionando
- [ ] Hot reload funciona ao alterar .vue
- [ ] TypeScript sem erros de compilação
- [ ] ESLint e Prettier configurados
- [ ] Feature-Sliced Design estruturado
- [ ] CLI shadcn-vue funcionando (`pnpm dlx shadcn-vue add <component>`)

---

## US-002: Configurar MSW (Mock Service Worker)

**Como**: Desenvolvedor Frontend  
**Quero**: MSW configurado para mockar APIs  
**Para**: Desenvolver frontend independente do backend

### Critérios de Aceitação

- [ ] MSW instalado e configurado
- [ ] Service worker público gerado
- [ ] Handlers básicos criados
- [ ] MSW ativo em desenvolvimento
- [ ] MSW integrado com testes
- [ ] Requisições aparecem no Network tab do DevTools

### Tarefas Técnicas

#### T-002.1: Instalar MSW

```bash
cd apps/frontend
pnpm add -D msw
```

#### T-002.2: Gerar service worker público

```bash
pnpm dlx msw init public/ --save
```

Isso cria `public/mockServiceWorker.js` (não commitar se muito grande, ou adicionar ao .gitignore comentado)

#### T-002.3: Criar estrutura de mocks

```bash
cd src/shared/api
mkdir -p mocks/handlers
touch mocks/browser.ts
touch mocks/server.ts
touch mocks/handlers/index.ts
```

#### T-002.4: Configurar browser worker

`src/shared/api/mocks/browser.ts`:

```typescript
import { setupWorker } from "msw/browser";
import { handlers } from "./handlers";

export const worker = setupWorker(...handlers);
```

#### T-002.5: Configurar server para testes

`src/shared/api/mocks/server.ts`:

```typescript
import { setupServer } from "msw/node";
import { handlers } from "./handlers";

export const server = setupServer(...handlers);
```

#### T-002.6: Criar handlers base

`src/shared/api/mocks/handlers/index.ts`:

```typescript
import { http, HttpResponse } from "msw";

export const handlers = [
  // Health check
  http.get("/api/v1/health", () => {
    return HttpResponse.json({
      status: "healthy",
      timestamp: new Date().toISOString(),
    });
  }),

  // Placeholder - será expandido nos próximos sprints
  http.get("/api/v1/users/me", () => {
    return HttpResponse.json({
      success: true,
      data: {
        id: "usr_1",
        name: "Demo User",
        email: "demo@vdx.com",
        role: "employee",
      },
    });
  }),
];
```

#### T-002.7: Ativar MSW em desenvolvimento

Atualizar `src/main.ts`:

```typescript
import { createApp } from "vue";
import App from "./app/App.vue";
import "./app/styles/index.css";

async function enableMocking() {
  // Apenas em desenvolvimento
  if (import.meta.env.MODE !== "development") {
    return;
  }

  const { worker } = await import("./shared/api/mocks/browser");

  return worker.start({
    onUnhandledRequest: "warn",
    quiet: false, // Mostra logs no console
  });
}

enableMocking().then(() => {
  createApp(App).mount("#app");
});
```

#### T-002.8: Configurar MSW para testes

Criar `vitest.setup.ts`:

```typescript
import { beforeAll, afterEach, afterAll } from "vitest";
import { server } from "./src/shared/api/mocks/server";

beforeAll(() => server.listen({ onUnhandledRequest: "error" }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

Atualizar `vite.config.ts` para incluir setup:

```typescript
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import tailwindcss from "@tailwindcss/vite";
import path from "path";

export default defineConfig({
  plugins: [vue(), tailwindcss()],
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
  server: {
    port: 5173,
    open: true,
  },
  test: {
    globals: true,
    environment: "happy-dom",
    setupFiles: ["./vitest.setup.ts"],
  },
});
```

#### T-002.9: Criar API client

`src/shared/api/client.ts`:

```typescript
import axios from "axios";

export const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_URL || "/api/v1",
  headers: {
    "Content-Type": "application/json",
  },
});

// Interceptor para adicionar token
apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem("accessToken");
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

#### T-002.10: Criar .env para configuração

`.env`:

```env
VITE_API_URL=/api/v1
VITE_USE_MOCKS=true
```

`.env.production`:

```env
VITE_API_URL=https://api.vdx.com/api/v1
VITE_USE_MOCKS=false
```

#### T-002.11: Teste básico com MSW

Criar `src/shared/api/__tests__/client.test.ts`:

```typescript
import { describe, it, expect } from "vitest";
import { apiClient } from "../client";

describe("API Client with MSW", () => {
  it("should fetch health check", async () => {
    const response = await apiClient.get("/health");

    expect(response.status).toBe(200);
    expect(response.data.status).toBe("healthy");
  });

  it("should fetch current user", async () => {
    const response = await apiClient.get("/users/me");

    expect(response.status).toBe(200);
    expect(response.data.success).toBe(true);
    expect(response.data.data.email).toBe("demo@vdx.com");
  });
});
```

### DoD

- [ ] MSW instalado e service worker gerado
- [ ] Handlers básicos funcionando
- [ ] MSW ativo em `pnpm dev`
- [ ] Requisições mockadas aparecem no Network tab
- [ ] Testes rodando com MSW
- [ ] `pnpm test` passa com sucesso
- [ ] Documentação de como adicionar novos handlers

---

## US-003: Setup de Roteamento (Vue Router)

    alias: {
      "@": path.resolve(__dirname, "./src"),
    },

},
server: {
port: 5173,
proxy: {
"/api": {
target: "http://localhost:3000",
changeOrigin: true,
},
},
},
});

````

#### T-002.5: Configurar package.json

```json
{
  "name": "@vdx/frontend",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "vite",
    "build": "vue-tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "lint": "eslint . --ext .vue,.ts,.tsx",
    "lint:fix": "eslint . --ext .vue,.ts,.tsx --fix"
  },
  "dependencies": {
    "vue": "^3.4.0",
    "vue-router": "^4.2.0",
    "pinia": "^2.1.0",
    "@tanstack/vue-query": "^5.0.0",
    "axios": "^1.6.0"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^5.0.0",
    "@vue/eslint-config-typescript": "^12.0.0",
    "eslint": "^8.55.0",
    "eslint-plugin-vue": "^9.19.0",
    "typescript": "^5.3.0",
    "vite": "^5.0.0",
    "vitest": "^1.0.0",
    "vue-tsc": "^1.8.0"
  }
}
````

#### T-002.6: Criar App.vue básico

`src/app/App.vue`:

```vue
<script setup lang="ts">
import { ref } from "vue";

const count = ref(0);
</script>

<template>
  <div class="min-h-screen bg-gray-50 flex items-center justify-center">
    <div class="text-center">
      <h1 class="text-4xl font-bold text-gray-900 mb-4">VDX</h1>
      <p class="text-gray-600 mb-4">Vue 3 + TypeScript + Tailwind</p>
      <button
        @click="count++"
        class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Count: {{ count }}
      </button>
    </div>
  </div>
</template>
```

### DoD

- [ ] `pnpm dev` inicia app em localhost:5173
- [ ] TailwindCSS aplicando estilos
- [ ] Hot reload funciona ao alterar .vue
- [ ] TypeScript sem erros
- [ ] ESLint configurado

---

## US-003: Setup de Roteamento (Vue Router)

**Como**: Desenvolvedor Frontend  
**Quero**: Sistema de rotas configurado  
**Para**: Navegar entre páginas da aplicação

### Critérios de Aceitação

- [ ] Vue Router instalado e configurado
- [ ] Rotas básicas criadas (Home, Login, Dashboard)
- [ ] Navegação funcional
- [ ] Lazy loading de componentes

### Tarefas Técnicas

#### T-003.1: Instalar Vue Router

```bash
cd apps/frontend
pnpm add vue-router
```

#### T-003.2: Configurar router

`src/app/router/index.ts`:

```typescript
import { createRouter, createWebHistory } from "vue-router";

const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: "/",
      name: "home",
      component: () => import("@/pages/home/HomePage.vue"),
    },
    {
      path: "/login",
      name: "login",
      component: () => import("@/pages/login/LoginPage.vue"),
    },
    {
      path: "/dashboard",
      name: "dashboard",
      component: () => import("@/pages/dashboard/DashboardPage.vue"),
      meta: { requiresAuth: true },
    },
  ],
});

export default router;
```

#### T-003.3: Criar páginas básicas com shadcn-vue

`src/pages/home/HomePage.vue`:

```vue
<script setup lang="ts">
import { useRouter } from "vue-router";
import { Button } from "@/shared/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/shared/ui/card";

const router = useRouter();
</script>

<template>
  <div
    class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 flex items-center justify-center p-4"
  >
    <Card class="w-full max-w-lg">
      <CardHeader class="text-center">
        <CardTitle class="text-5xl font-bold">VDX</CardTitle>
        <CardDescription class="text-lg">
          Plataforma de Gestão de Pessoas
        </CardDescription>
      </CardHeader>
      <CardContent class="flex justify-center">
        <Button size="lg" @click="router.push('/login')">
          Acessar Sistema
        </Button>
      </CardContent>
    </Card>
  </div>
</template>
```

`src/pages/login/LoginPage.vue`:

```vue
<script setup lang="ts">
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/shared/ui/card";

// Implementação completa no Sprint 1
</script>

<template>
  <div class="min-h-screen bg-background flex items-center justify-center p-4">
    <Card class="w-full max-w-md">
      <CardHeader>
        <CardTitle class="text-2xl">Login</CardTitle>
        <CardDescription>
          Página de login (implementação no Sprint 1)
        </CardDescription>
      </CardHeader>
      <CardContent>
        <p class="text-sm text-muted-foreground">
          Esta página será implementada com formulário de autenticação no
          próximo sprint.
        </p>
      </CardContent>
    </Card>
  </div>
</template>
```

`src/pages/dashboard/DashboardPage.vue`:

```vue
<script setup lang="ts">
import {
  Card,
  CardContent,
  CardDescription,
  CardHeader,
  CardTitle,
} from "@/shared/ui/card";

// Implementação completa no Sprint 1
</script>

<template>
  <div class="min-h-screen bg-background p-8">
    <Card>
      <CardHeader>
        <CardTitle class="text-3xl">Dashboard</CardTitle>
        <CardDescription>
          Visão geral do sistema (implementação no Sprint 1)
        </CardDescription>
      </CardHeader>
      <CardContent>
        <p class="text-sm text-muted-foreground">
          Este dashboard será implementado com widgets e dados reais nos
          próximos sprints.
        </p>
      </CardContent>
    </Card>
  </div>
</template>
```

#### T-003.4: Integrar router no app

Atualizar `src/main.ts`:

```typescript
import { createApp } from "vue";
import App from "./app/App.vue";
import router from "./app/router";
import "./app/styles/index.css";

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
  const app = createApp(App);
  app.use(router);
  app.mount("#app");
});
```

Atualizar `src/app/App.vue`:

```vue
<template>
  <RouterView />
</template>
```

### DoD

- [ ] Vue Router instalado
- [ ] Rotas funcionando
- [ ] Navegação entre páginas
- [ ] Lazy loading configurado

---

## US-004: Configurar CI/CD

**Como**: DevOps  
**Quero**: Pipeline de CI/CD automatizado para frontend  
**Para**: Garantir qualidade do código

### Critérios de Aceitação

- [ ] GitHub Actions configurado
- [ ] Lint, testes e build executam em cada PR
- [ ] Deploy automático para staging (branch develop)
- [ ] Status badges no README

### Tarefas Técnicas

#### T-004.1: Criar workflow de CI

`.github/workflows/ci.yml`:

```yaml
name: CI

on:
  pull_request:
    branches: [main, develop]
  push:
    branches: [main, develop]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 8
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: "pnpm"

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Lint
        run: pnpm lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 8
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: "pnpm"

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Run tests
        run: pnpm test

  build:
    runs-on: ubuntu-latest
    needs: [lint, test]
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 8
      - uses: actions/setup-node@v4
        with:
          node-version: 22
          cache: "pnpm"

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Build
        run: pnpm build
```

#### T-004.2: Adicionar badges no README

```markdown
[![CI](https://github.com/seu-org/vdx/actions/workflows/ci.yml/badge.svg)](https://github.com/seu-org/vdx/actions/workflows/ci.yml)
```

### DoD

- [ ] Pipeline executa apenas quando frontend muda
- [ ] Todos os jobs (lint, test, build) passam
- [ ] Testes com MSW funcionam no CI
- [ ] Coverage é coletado e enviado
- [ ] Badges aparecem no README

---

## Checklist Final do Sprint 0

### Estrutura

- [ ] Monorepo pnpm configurado
- [ ] Workspace apps/frontend criado
- [ ] Feature-Sliced Design estruturado

### Frontend

- [ ] Vue 3 + TypeScript rodando em localhost:5173
- [ ] TailwindCSS v4 configurado e funcionando
- [ ] shadcn-vue instalado e componentes básicos adicionados (Button, Card, Input, Label)
- [ ] components.json configurado corretamente
- [ ] Vue Router com rotas básicas (/, /login, /dashboard)
- [ ] Pinia store configurada
- [ ] TanStack Query configurada

### Testes

- [ ] Vitest configurado e funcionando
- [ ] MSW instalado e configurado
- [ ] Handlers básicos criados (/api/auth/\*, /api/users/me)
- [ ] Testes de exemplo passando com MSW
- [ ] Playwright E2E configurado

### API Client

- [ ] Axios configurado com baseURL
- [ ] Interceptors básicos (auth, error)
- [ ] Tipos TypeScript para requests/responses

### CI/CD

- [ ] GitHub Actions workflow frontend-ci.yml
- [ ] Lint, test e build executam automaticamente
- [ ] Coverage collection funcionando
- [ ] Badges no README

### Qualidade

- [ ] ESLint configurado
- [ ] Prettier configurado
- [ ] TypeScript strict mode
- [ ] Git hooks (pre-commit) com lint-staged

### Documentação

- [ ] README.md atualizado com quick start
- [ ] Dev guide com seção MSW
- [ ] Sprint 0 documentado

**Entregável**: Frontend independente e funcional com API mockada, pronto para desenvolvimento de features
