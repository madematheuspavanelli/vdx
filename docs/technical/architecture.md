# Architecture - VDX

## Visão Geral

O VDX adota uma arquitetura moderna, modular e escalável, combinando os princípios de Domain-Driven Design (DDD), Clean Architecture e Feature-Sliced Design para garantir manutenibilidade, testabilidade e evolução sustentável do sistema.

## Princípios Arquiteturais

### 1. Separation of Concerns

Separação clara entre domínio, aplicação, infraestrutura e apresentação.

### 2. Dependency Inversion

Dependências apontam para abstrações, não para implementações concretas.

### 3. Single Responsibility

Cada módulo/classe tem uma única razão para mudar.

### 4. Domain-First

Modelagem centrada no negócio, tecnologia como detalhe de implementação.

### 5. API-First

APIs bem definidas e documentadas como contrato entre sistemas.

## Arquitetura de Alto Nível

```
┌─────────────────────────────────────────────────────────┐
│                      Frontend (SPA)                      │
│              Vue 3 + TypeScript + Tailwind              │
└───────────────────┬─────────────────────────────────────┘
                    │ HTTPS / REST API
┌───────────────────▼─────────────────────────────────────┐
│                   API Gateway / BFF                      │
│              (Backend For Frontend)                      │
└───────────────────┬─────────────────────────────────────┘
                    │
    ┌───────────────┼───────────────┬──────────────┐
    │               │               │              │
┌───▼────┐    ┌────▼─────┐   ┌────▼────┐   ┌─────▼────┐
│Identity│    │Onboarding│   │Assessment│   │Analytics │
│Service │    │ Service  │   │ Service  │   │ Service  │
└───┬────┘    └────┬─────┘   └────┬────┘   └─────┬────┘
    │              │              │              │
    └──────────────┴──────────────┴──────────────┘
                    │
            ┌───────▼───────┐
            │   Database    │
            │  PostgreSQL   │
            └───────────────┘
```

## Arquitetura do Backend

### Camadas (Clean Architecture)

```
┌──────────────────────────────────────────────────────┐
│                      DOMAIN                          │
│  ┌────────────────────────────────────────────────┐ │
│  │  Entities, Value Objects, Domain Services,     │ │
│  │  Domain Events, Business Rules                 │ │
│  └────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
                        ▲
                        │
┌──────────────────────────────────────────────────────┐
│                   APPLICATION                        │
│  ┌────────────────────────────────────────────────┐ │
│  │  Use Cases, DTOs, Repositories (Interfaces),   │ │
│  │  Application Services                          │ │
│  └────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
                        ▲
                        │
┌──────────────────────────────────────────────────────┐
│                  INFRASTRUCTURE                      │
│  ┌────────────────────────────────────────────────┐ │
│  │  Repository Implementations, Database,         │ │
│  │  External Services, Email, Storage             │ │
│  └────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
                        ▲
                        │
┌──────────────────────────────────────────────────────┐
│                   PRESENTATION                       │
│  ┌────────────────────────────────────────────────┐ │
│  │  Controllers, Routes, Middleware,              │ │
│  │  Request/Response Mapping                      │ │
│  └────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────┘
```

### Estrutura de Diretórios (Backend)

```
apps/backend/
├── src/
│   ├── domain/                      # Camada de Domínio
│   │   ├── identity/
│   │   │   ├── entities/
│   │   │   │   ├── user.entity.ts
│   │   │   │   └── role.entity.ts
│   │   │   ├── value-objects/
│   │   │   │   ├── email.vo.ts
│   │   │   │   └── password.vo.ts
│   │   │   ├── events/
│   │   │   │   └── user-created.event.ts
│   │   │   └── services/
│   │   │       └── password-hasher.service.ts
│   │   ├── onboarding/
│   │   ├── organization/
│   │   ├── assessment/
│   │   └── development/
│   │
│   ├── application/                 # Camada de Aplicação
│   │   ├── identity/
│   │   │   ├── use-cases/
│   │   │   │   ├── authenticate-user.use-case.ts
│   │   │   │   ├── register-user.use-case.ts
│   │   │   │   └── reset-password.use-case.ts
│   │   │   ├── dtos/
│   │   │   │   ├── authenticate.dto.ts
│   │   │   │   └── user-response.dto.ts
│   │   │   └── repositories/
│   │   │       └── user.repository.interface.ts
│   │   ├── onboarding/
│   │   ├── organization/
│   │   ├── assessment/
│   │   └── development/
│   │
│   ├── infrastructure/              # Camada de Infraestrutura
│   │   ├── database/
│   │   │   ├── repositories/
│   │   │   │   └── user.repository.ts
│   │   │   ├── migrations/
│   │   │   └── seeds/
│   │   ├── email/
│   │   │   └── email.service.ts
│   │   ├── storage/
│   │   └── cache/
│   │       └── redis.service.ts
│   │
│   ├── presentation/                # Camada de Apresentação
│   │   ├── http/
│   │   │   ├── controllers/
│   │   │   │   ├── auth.controller.ts
│   │   │   │   ├── users.controller.ts
│   │   │   │   └── onboarding.controller.ts
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.middleware.ts
│   │   │   │   ├── error.middleware.ts
│   │   │   │   └── validation.middleware.ts
│   │   │   └── routes/
│   │   │       └── index.ts
│   │   └── validators/
│   │
│   ├── shared/                      # Código Compartilhado
│   │   ├── errors/
│   │   ├── utils/
│   │   └── types/
│   │
│   └── main.ts                      # Entry Point
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
│
└── package.json
```

## Arquitetura do Frontend

### Feature-Sliced Design

```
apps/frontend/
├── src/
│   ├── app/                         # Inicialização da aplicação
│   │   ├── providers/
│   │   ├── router/
│   │   └── main.ts
│   │
│   ├── pages/                       # Páginas (rotas)
│   │   ├── login/
│   │   ├── dashboard/
│   │   ├── onboarding/
│   │   └── assessments/
│   │
│   ├── widgets/                     # Blocos complexos de UI
│   │   ├── header/
│   │   ├── sidebar/
│   │   └── onboarding-wizard/
│   │
│   ├── features/                    # Funcionalidades de negócio
│   │   ├── authentication/
│   │   │   ├── api/
│   │   │   ├── model/
│   │   │   └── ui/
│   │   ├── onboarding/
│   │   ├── assessment/
│   │   └── pdi/
│   │
│   ├── entities/                    # Entidades de negócio
│   │   ├── user/
│   │   │   ├── api/
│   │   │   ├── model/
│   │   │   └── ui/
│   │   ├── department/
│   │   └── competency/
│   │
│   ├── shared/                      # Código reutilizável
│   │   ├── ui/                      # Componentes UI genéricos
│   │   │   ├── button/
│   │   │   ├── input/
│   │   │   ├── modal/
│   │   │   └── card/
│   │   ├── api/                     # Cliente HTTP, interceptors
│   │   ├── lib/                     # Utilitários
│   │   └── config/                  # Configurações
│   │
│   └── assets/                      # Assets estáticos
│       ├── images/
│       ├── icons/
│       └── fonts/
│
└── package.json
```

## Domain Model (DDD)

### Contextos Delimitados

#### 1. Identity Context

**Responsabilidade**: Autenticação, autorização, gestão de usuários e permissões.

**Entidades**:

- User (Agregado)
- Role
- Permission

**Value Objects**:

- Email
- Password
- UserId

**Casos de Uso**:

- Authenticate User
- Register User
- Reset Password
- Manage Roles

#### 2. Organization Context

**Responsabilidade**: Estrutura organizacional, departamentos, cargos, hierarquias.

**Entidades**:

- Department (Agregado)
- Position
- Employee (Agregado)
- Hierarchy

**Value Objects**:

- EmployeeId
- DepartmentId
- PositionLevel

**Casos de Uso**:

- Create Department
- Assign Employee to Department
- Define Hierarchy
- View Org Chart

#### 3. Onboarding Context

**Responsabilidade**: Processo de integração de novos colaboradores.

**Entidades**:

- OnboardingJourney (Agregado)
- OnboardingStep
- Task
- Template

**Value Objects**:

- JourneyId
- TaskStatus
- DueDate

**Casos de Uso**:

- Start Onboarding
- Complete Task
- Track Progress
- Create Template

#### 4. Assessment Context

**Responsabilidade**: Avaliações de competências e ciclos de feedback.

**Entidades**:

- AssessmentCycle (Agregado)
- Assessment
- Competency
- Feedback

**Value Objects**:

- Score
- CycleId
- CompetencyId

**Casos de Uso**:

- Create Assessment Cycle
- Submit Assessment
- View Results
- Generate Report

#### 5. Development Context

**Responsabilidade**: PDI, objetivos, metas e acompanhamento de desenvolvimento.

**Entidades**:

- PDI (Agregado)
- Goal
- Action
- Review

**Value Objects**:

- GoalId
- Progress
- TargetDate

**Casos de Uso**:

- Create PDI
- Add Goal
- Track Progress
- Conduct Review

#### 6. Analytics Context

**Responsabilidade**: KPIs, métricas, dashboards e relatórios.

**Entidades**:

- KPI (Agregado)
- Metric
- Dashboard
- Report

**Value Objects**:

- MetricValue
- Period
- Target

**Casos de Uso**:

- Define KPI
- Calculate Metrics
- Generate Dashboard
- Export Report

## Segurança

### Autenticação

- **JWT (JSON Web Tokens)** para sessões stateless
- **Refresh Tokens** para renovação segura
- **OAuth2/OIDC** para login social (Google, Microsoft)
- **MFA (Multi-Factor Authentication)** opcional

### Autorização

- **RBAC (Role-Based Access Control)**
- **Attribute-Based Access Control** para regras complexas
- Verificação de permissões em cada endpoint
- Segregação por departamento/hierarquia

### Proteções

- **HTTPS** obrigatório
- **CSRF Tokens** em formulários
- **XSS Prevention** via sanitização e CSP
- **SQL Injection Prevention** via ORMs e prepared statements
- **Rate Limiting** para prevenir abuso
- **Input Validation** em todas as entradas

### Dados Sensíveis

- **Criptografia em repouso** (AES-256) para dados sensíveis
- **Criptografia em trânsito** (TLS 1.3)
- **Hashing de senhas** (bcrypt/argon2)
- **Logs de auditoria** para ações críticas

## Banco de Dados

### Modelo Relacional (PostgreSQL)

**Vantagens**:

- ACID compliance
- Relacionamentos complexos
- Consultas SQL avançadas
- Maturidade e confiabilidade

**Esquema Lógico** (Simplificado):

```sql
-- Identity Context
users (id, email, password_hash, name, created_at, updated_at)
roles (id, name, description)
permissions (id, name, resource, action)
user_roles (user_id, role_id)
role_permissions (role_id, permission_id)

-- Organization Context
departments (id, name, parent_id, created_at)
positions (id, title, level, department_id)
employees (id, user_id, position_id, manager_id, hire_date)

-- Onboarding Context
onboarding_journeys (id, employee_id, template_id, status, started_at)
onboarding_steps (id, journey_id, title, description, order)
onboarding_tasks (id, step_id, title, completed_at, due_date)

-- Assessment Context
assessment_cycles (id, name, start_date, end_date, status)
competencies (id, name, description, position_id)
assessments (id, cycle_id, evaluator_id, evaluated_id, type)
assessment_scores (id, assessment_id, competency_id, score, comment)

-- Development Context
pdis (id, employee_id, status, created_at, approved_at)
goals (id, pdi_id, title, description, target_date, status)
goal_actions (id, goal_id, description, completed_at)
pdi_reviews (id, pdi_id, reviewer_id, date, feedback)

-- Analytics Context
kpis (id, name, description, target, department_id)
metrics (id, kpi_id, value, period, measured_at)
```

### Estratégias de Otimização

- **Índices** em colunas de busca frequente
- **Views materializadas** para agregações complexas
- **Particionamento** para tabelas grandes (histórico)
- **Connection pooling** para melhor performance
- **Query optimization** com EXPLAIN ANALYZE

## APIs

### REST API

**Convenções**:

- Versionamento via URL: `/api/v1/`
- Recursos nomeados em plural: `/users`, `/departments`
- Métodos HTTP semânticos: GET, POST, PUT, PATCH, DELETE
- Status codes apropriados: 200, 201, 400, 401, 403, 404, 500

**Estrutura de Response**:

```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "per_page": 20,
    "total": 150
  }
}
```

**Estrutura de Error**:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": [
      {
        "field": "email",
        "message": "Invalid email format"
      }
    ]
  }
}
```

### Endpoints Principais

```
POST   /api/v1/auth/login
POST   /api/v1/auth/logout
POST   /api/v1/auth/refresh
POST   /api/v1/auth/forgot-password
POST   /api/v1/auth/reset-password

GET    /api/v1/users
GET    /api/v1/users/:id
POST   /api/v1/users
PUT    /api/v1/users/:id
DELETE /api/v1/users/:id

GET    /api/v1/departments
POST   /api/v1/departments
GET    /api/v1/departments/:id/org-chart

GET    /api/v1/onboarding
POST   /api/v1/onboarding
GET    /api/v1/onboarding/:id
PUT    /api/v1/onboarding/:id/tasks/:taskId/complete

GET    /api/v1/assessments/cycles
POST   /api/v1/assessments/cycles
GET    /api/v1/assessments/pending
POST   /api/v1/assessments/:id/submit

GET    /api/v1/pdis
POST   /api/v1/pdis
GET    /api/v1/pdis/:id
POST   /api/v1/pdis/:id/goals
PUT    /api/v1/pdis/:id/approve

GET    /api/v1/kpis
GET    /api/v1/dashboards/:type
```

## Infraestrutura e DevOps

### Containerização

- **Docker** para containerização
- **Docker Compose** para ambiente local
- **Multi-stage builds** para otimização

### CI/CD

- **GitHub Actions** para pipeline
- **Automated tests** em cada PR
- **Linting e formatting** automáticos
- **Deploy automático** para staging/production

### Monitoramento

- **Logs estruturados** (JSON)
- **Health checks** em `/health`
- **Metrics** (Prometheus format)
- **APM** para performance tracking

### Ambientes

- **Development**: local com Docker Compose
- **Staging**: ambiente de homologação
- **Production**: ambiente de produção

## Escalabilidade

### Estratégias

- **Stateless services** para escalonamento horizontal
- **Load balancing** entre instâncias
- **Database read replicas** para consultas
- **Caching** com Redis para dados frequentes
- **CDN** para assets estáticos
- **Async processing** para tarefas pesadas (filas)

### Limites Iniciais

- 5000 colaboradores
- 500 usuários simultâneos
- < 2s tempo de resposta (P95)
- 99.5% disponibilidade

## Decisões Arquiteturais

Consulte os [ADRs](./adr/README.md) para decisões arquiteturais detalhadas e seus contextos.
