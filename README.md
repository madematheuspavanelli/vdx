# VDX - Plataforma de Gestão de Pessoas

[![CI](https://github.com/seu-org/vdx/actions/workflows/ci.yml/badge.svg)](https://github.com/seu-org/vdx/actions/workflows/ci.yml)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

> Plataforma integrada de gestão de pessoas para empresas de tecnologia, com foco em onboarding, avaliações 360°, PDI e analytics.

## 🎯 Visão Geral

O **VDX** é uma solução completa para gestão de recursos humanos que centraliza e automatiza processos críticos:

- 🚀 **Onboarding estruturado** - Integração guiada de novos colaboradores
- 🏢 **Organograma dinâmico** - Visualização da estrutura organizacional
- 📊 **Avaliações 360°** - Sistema completo de feedback e avaliação de competências
- 🎯 **PDI (Plano de Desenvolvimento Individual)** - Gestão de carreira e objetivos
- 📈 **KPIs e Analytics** - Dashboards e métricas para tomada de decisão

## 🏗️ Arquitetura

- **Frontend**: Vue 3 + TypeScript + TailwindCSS v4 + shadcn-vue + Feature-Sliced Design
- **Backend**: Node.js + TypeScript + Clean Architecture + DDD
- **Database**: PostgreSQL + Redis
- **DevOps**: Docker + GitHub Actions

## 📚 Documentação

### Documentação Estratégica

- [📋 Product Vision](./docs/strategic/product-vision.md) - Visão do produto, objetivos e roadmap
- [📝 Requirements](./docs/strategic/requirements.md) - Requisitos funcionais e não funcionais

### Documentação Técnica

- [🏛️ Architecture](./docs/technical/architecture.md) - Design técnico e decisões arquiteturais
- [👨‍💻 Developer Guide](./docs/technical/dev-guide.md) - Manual do desenvolvedor
- [🔌 API Documentation](./docs/technical/api-documentation.md) - Especificação da API REST
- [📖 ADRs](./docs/adr/README.md) - Architecture Decision Records

### Documentação Operacional

- [⚙️ Operations Guide](./docs/operational/operations.md) - Guia operacional e DevOps
- [👤 User Guide](./docs/operational/user-guide.md) - Manual do usuário final

### Recursos Adicionais

- [📖 Glossário](./docs/glossary.md) - Termos técnicos e de negócio
- [📋 Tasks & Sprints](./docs/tasks/README.md) - Planejamento e execução

## 🚀 Quick Start

### Pré-requisitos

- Node.js 22+
- pnpm 8+
- Docker e Docker Compose

### Instalação

1. **Clone o repositório**

```bash
git clone https://github.com/seu-org/vdx.git
cd vdx
```

2. **Instale as dependências**

```bash
pnpm install
```

3. **Configure variáveis de ambiente**

```bash
cp apps/backend/.env.example apps/backend/.env
cp apps/frontend/.env.example apps/frontend/.env
```

4. **Inicie os serviços com Docker**

```bash
pnpm docker:up
```

5. **Execute as migrações**

```bash
cd apps/backend
pnpm db:migrate
pnpm db:seed
```

6. **Inicie o desenvolvimento**

Em terminais separados:

```bash
# Backend (porta 3000)
cd apps/backend
pnpm dev

# Frontend (porta 5173)
cd apps/frontend
pnpm dev
```

7. **Acesse a aplicação**

- Frontend: http://localhost:5173
- Backend API: http://localhost:3000
- API Docs: http://localhost:3000/api-docs
- MailHog (emails): http://localhost:8025

## 📦 Estrutura do Projeto

```
vdx/
├── apps/
│   ├── backend/              # API Node.js + TypeScript
│   │   ├── src/
│   │   │   ├── domain/       # Entidades e regras de negócio
│   │   │   ├── application/  # Use cases e DTOs
│   │   │   ├── infrastructure/  # Banco, email, serviços externos
│   │   │   └── presentation/ # Controllers, rotas, middlewares
│   │   └── tests/
│   │
│   └── frontend/             # SPA Vue 3 + TypeScript
│       ├── src/
│       │   ├── app/          # Configuração da aplicação
│       │   ├── pages/        # Páginas/rotas
│       │   ├── widgets/      # Componentes complexos
│       │   ├── features/     # Funcionalidades de negócio
│       │   ├── entities/     # Entidades de domínio
│       │   └── shared/       # Código compartilhado
│       └── tests/
│
├── docs/                     # Documentação completa
│   ├── strategic/           # Visão de produto e requisitos
│   ├── technical/           # Arquitetura e guias técnicos
│   ├── operational/         # Ops e manual do usuário
│   ├── adr/                 # Decisões arquiteturais
│   └── tasks/               # Sprints e user stories
│
├── docker-compose.yml        # Ambiente local
└── package.json             # Workspace config
```

## 🧪 Testes

```bash
# Todos os testes
pnpm test

# Modo watch
pnpm test:watch

# Coverage
pnpm test:coverage

# E2E
pnpm test:e2e
```

## 🔧 Scripts Disponíveis

```bash
# Desenvolvimento
pnpm dev                    # Inicia backend e frontend
pnpm build                  # Build de produção
pnpm lint                   # Verificar código
pnpm lint:fix              # Corrigir problemas automaticamente
pnpm format                # Formatar código com Prettier

# Docker
pnpm docker:up             # Iniciar serviços (PostgreSQL, Redis, MailHog)
pnpm docker:down           # Parar serviços
pnpm docker:logs           # Ver logs
pnpm docker:clean          # Limpar volumes (CUIDADO: apaga dados)

# Database
pnpm db:migrate            # Executar migrações
pnpm db:migrate:rollback   # Reverter última migração
pnpm db:seed               # Popular banco com dados de teste
```

## 🤝 Contribuindo

Leia nosso [Developer Guide](./docs/technical/dev-guide.md) para informações sobre:

- Setup do ambiente de desenvolvimento
- Padrões de código e convenções
- Fluxo de trabalho Git
- Process de code review
- Como criar Pull Requests

### Fluxo Básico

1. Crie uma branch: `git checkout -b feature/minha-feature`
2. Faça commits seguindo [Conventional Commits](https://www.conventionalcommits.org/)
3. Execute testes: `pnpm test`
4. Push e abra um Pull Request

## 📋 Roadmap

### Sprint 0 - ✅ Setup (Concluído)

- Monorepo, CI/CD, Docker

### Sprint 1 - 🚧 Em Andamento

- Autenticação e gestão de usuários

### Sprint 2 - 📅 Planejado

- Estrutura organizacional e organograma

### Sprint 3 - 📅 Planejado

- Sistema de onboarding

### Sprint 4 - 📅 Futuro

- Avaliações 360°

Veja o [planejamento completo](./docs/tasks/README.md) para mais detalhes.

## 👥 Perfis de Usuário

- **Admin**: Configuração completa do sistema
- **RH**: Gestão de colaboradores, onboarding, avaliações
- **Gestor**: Acompanhamento de equipe, aprovação de PDIs
- **Colaborador**: Acesso a onboarding, PDI, avaliações

## 🔐 Segurança

- Autenticação JWT com refresh tokens
- RBAC (Role-Based Access Control)
- Proteção contra CSRF, XSS, SQL Injection
- Criptografia de dados sensíveis
- Conformidade com LGPD

## 📊 Métricas de Qualidade

- Cobertura de testes: > 80%
- Performance: P95 < 2s
- Disponibilidade: 99.5% SLA
- Code quality: ESLint + Prettier

## 📄 Licença

Este projeto está sob a licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## 📞 Suporte

- **Documentação**: [/docs](./docs)
- **Issues**: [GitHub Issues](https://github.com/seu-org/vdx/issues)
- **Email**: dev-team@vdx.com

## 🌟 Tecnologias

### Frontend

- Vue 3 (Composition API)
- TypeScript
- Vite
- TailwindCSS v4
- shadcn-vue (Component System)
- Pinia (state management)
- TanStack Query
- Vue Router
- MSW (Mock Service Worker)
- Vitest + Playwright

### Backend

- Node.js 22
- TypeScript
- Express
- PostgreSQL
- Redis
- JWT
- Bcrypt
- Vitest

### DevOps

- Docker & Docker Compose
- GitHub Actions
- ESLint & Prettier

---

**Desenvolvido com ❤️ para empresas de tecnologia**
