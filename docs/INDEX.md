# Índice da Documentação VDX

Este é o índice centralizado de toda a documentação do projeto VDX.

## 📁 Estrutura de Documentação

### 🎯 Documentação Estratégica (`/strategic`)

Documentação focada em visão de negócio, objetivos e requisitos.

- **[Product Vision](./strategic/product-vision.md)**

  - Visão geral do produto
  - Problema e solução
  - Objetivos de negócio
  - Personas
  - Roadmap estratégico
  - Métricas de sucesso

- **[Requirements](./strategic/requirements.md)**
  - Requisitos funcionais (RF001-RF009)
  - Requisitos não funcionais (performance, segurança, usabilidade)
  - Regras de negócio
  - Casos de uso principais
  - Priorização (Must/Should/Could Have)

### 🔧 Documentação Técnica (`/technical`)

Documentação para desenvolvedores e arquitetos.

- **[Architecture](./technical/architecture.md)**

  - Visão geral da arquitetura
  - Princípios arquiteturais
  - Arquitetura de alto nível
  - Clean Architecture (camadas)
  - Domain-Driven Design
  - Estrutura de diretórios
  - Segurança
  - Banco de dados
  - APIs

- **[Developer Guide](./technical/dev-guide.md)**

  - Setup inicial
  - Estrutura do projeto
  - Fluxo de desenvolvimento
  - Padrões de código
  - Testes
  - Troubleshooting
  - Recursos úteis

- **[API Documentation](./technical/api-documentation.md)**
  - Autenticação
  - Endpoints detalhados
  - Request/Response examples
  - Status codes
  - Rate limiting
  - Webhooks

### ⚙️ Documentação Operacional (`/operational`)

Documentação para DevOps e usuários finais.

- **[Operations Guide](./operational/operations.md)**

  - Arquitetura de infraestrutura
  - Ambientes (dev, staging, prod)
  - Docker e containerização
  - CI/CD pipeline
  - Database management
  - Monitoramento e logs
  - Disaster recovery
  - Troubleshooting

- **[User Guide](./operational/user-guide.md)**
  - Primeiros passos
  - Funcionalidades por perfil
    - Colaborador
    - Gestor
    - RH
    - Admin
  - Guias de uso
  - FAQ

### 📋 Decisões Arquiteturais (`/adr`)

- **[ADR Index](./adr/README.md)**
  - ADR-001: Monorepo com pnpm
  - ADR-002: Vue 3 com Composition API
  - ADR-003: Clean Architecture no Backend
  - ADR-004: PostgreSQL como Banco Principal
  - ADR-005: JWT para Autenticação
  - ADR-006: Feature-Sliced Design
  - ADR-007: TailwindCSS para Estilização
  - ADR-008: Vitest para Testes

### 📝 Tarefas e Sprints (`/tasks`)

Planejamento detalhado de execução.

- **[Tasks Overview](./tasks/README.md)**

  - Metodologia
  - Estrutura de sprints
  - Como executar

- **Sprints**
  - [Sprint 0 - Setup e Infraestrutura](./tasks/sprint-0-setup.md)
  - [Sprint 1 - Autenticação e Usuários](./tasks/sprint-1-authentication.md)
  - [Sprint 2 - Organização](./tasks/sprint-2-organization.md) _(planejado)_
  - [Sprint 3 - Onboarding](./tasks/sprint-3-onboarding.md) _(planejado)_
  - [Sprint 4 - Avaliações](./tasks/sprint-4-assessment.md) _(planejado)_
  - [Sprint 5 - PDI](./tasks/sprint-5-pdi.md) _(planejado)_
  - [Sprint 6 - Analytics](./tasks/sprint-6-analytics.md) _(planejado)_
  - [Sprint 7 - Polish](./tasks/sprint-7-polish.md) _(planejado)_

### 📖 Recursos Gerais

- **[Glossário](./glossary.md)**
  - Termos de negócio
  - Termos técnicos
  - Perfis de usuário
  - Acrônimos
  - Métricas e indicadores
  - Domínios do sistema
  - Convenções de nomenclatura

## 🗺️ Guias Rápidos por Audiência

### Para Product Managers

1. [Product Vision](./strategic/product-vision.md) - Entenda a visão e objetivos
2. [Requirements](./strategic/requirements.md) - Veja requisitos e priorização
3. [Tasks Overview](./tasks/README.md) - Acompanhe o planejamento

### Para Desenvolvedores

1. [Developer Guide](./technical/dev-guide.md) - Comece aqui!
2. [Architecture](./technical/architecture.md) - Entenda a arquitetura
3. [API Documentation](./technical/api-documentation.md) - Referência da API
4. [Sprint 0](./tasks/sprint-0-setup.md) - Setup inicial
5. [Glossário](./glossary.md) - Termos técnicos

### Para DevOps/SRE

1. [Operations Guide](./operational/operations.md) - Infraestrutura e operações
2. [Architecture](./technical/architecture.md) - Visão da arquitetura
3. [Developer Guide](./technical/dev-guide.md) - Setup local

### Para QA/Testers

1. [Requirements](./strategic/requirements.md) - Critérios de aceitação
2. [User Guide](./operational/user-guide.md) - Fluxos do usuário
3. [Tasks](./tasks/README.md) - User stories e DoD

### Para Usuários Finais

1. [User Guide](./operational/user-guide.md) - Manual completo
2. [Glossário](./glossary.md) - Termos e conceitos

### Para Arquitetos

1. [Architecture](./technical/architecture.md) - Design completo
2. [ADRs](./adr/README.md) - Decisões arquiteturais
3. [Requirements](./strategic/requirements.md) - Requisitos não funcionais

## 📚 Como Navegar

### Por Tipo de Informação

**Precisa entender o PORQUÊ?**
→ [Product Vision](./strategic/product-vision.md), [ADRs](./adr/README.md)

**Precisa saber O QUE construir?**
→ [Requirements](./strategic/requirements.md), [Tasks](./tasks/README.md)

**Precisa saber COMO construir?**
→ [Architecture](./technical/architecture.md), [Developer Guide](./technical/dev-guide.md)

**Precisa saber COMO usar?**
→ [User Guide](./operational/user-guide.md), [API Documentation](./technical/api-documentation.md)

**Precisa saber COMO operar?**
→ [Operations Guide](./operational/operations.md)

## 🔄 Manutenção da Documentação

Esta documentação deve ser mantida atualizada:

- **Documentação viva**: Atualizar conforme o código evolui
- **Pull Requests**: Incluir updates de docs em PRs relevantes
- **Revisão**: Review trimestral de toda a documentação
- **Versionamento**: Acompanha versões do produto

## 📝 Contribuindo para a Documentação

1. Identifique documentação desatualizada ou missing
2. Crie branch: `docs/update-<topic>`
3. Faça as alterações
4. Abra PR com label `documentation`
5. Solicite review

## 🔍 Busca

Use o recurso de busca do GitHub ou:

```bash
# Buscar em toda documentação
grep -r "termo" docs/

# Buscar em arquivos markdown
find docs/ -name "*.md" -exec grep -l "termo" {} \;
```

---

**Última atualização**: Janeiro 2024  
**Versão da documentação**: 1.0
