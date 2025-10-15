# API Documentation - VDX

## Visão Geral

A API VDX é uma API RESTful que fornece endpoints para gerenciamento completo de recursos humanos, incluindo autenticação, onboarding, avaliações, PDI e analytics.

**Base URL**: `https://api.vdx.com/api/v1`  
**Ambiente de Desenvolvimento**: `http://localhost:3000/api/v1`

## Autenticação

A API usa JWT (JSON Web Tokens) para autenticação.

### Obter Token

```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response 200 OK**:

```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 3600,
    "user": {
      "id": "usr_123",
      "name": "John Doe",
      "email": "user@example.com",
      "role": "employee"
    }
  }
}
```

### Usar Token

Inclua o token no header `Authorization` de todas as requisições:

```http
GET /users/me
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### Refresh Token

```http
POST /auth/refresh
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

## Endpoints

### Authentication

#### Login

```http
POST /auth/login
```

**Request Body**:

```json
{
  "email": "string (required)",
  "password": "string (required)"
}
```

**Response 200**: Autenticação bem-sucedida  
**Response 401**: Credenciais inválidas  
**Response 429**: Too many requests

---

#### Logout

```http
POST /auth/logout
Authorization: Bearer {token}
```

**Response 204**: Logout bem-sucedido

---

#### Esqueci minha senha

```http
POST /auth/forgot-password
```

**Request Body**:

```json
{
  "email": "string (required)"
}
```

**Response 200**: Email de recuperação enviado

---

#### Resetar senha

```http
POST /auth/reset-password
```

**Request Body**:

```json
{
  "token": "string (required)",
  "password": "string (required, min 8 chars)"
}
```

**Response 200**: Senha resetada com sucesso

---

### Users

#### Listar usuários

```http
GET /users?page=1&limit=20&role=employee&search=john
Authorization: Bearer {token}
```

**Query Parameters**:

- `page` (number, default: 1)
- `limit` (number, default: 20, max: 100)
- `role` (string, optional): admin | hr | manager | employee
- `search` (string, optional): busca por nome ou email
- `department` (string, optional): filtrar por departamento

**Response 200**:

```json
{
  "success": true,
  "data": [
    {
      "id": "usr_123",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "employee",
      "position": {
        "id": "pos_456",
        "title": "Software Engineer"
      },
      "department": {
        "id": "dept_789",
        "name": "Engineering"
      },
      "manager": {
        "id": "usr_101",
        "name": "Jane Manager"
      },
      "hireDate": "2024-01-15T00:00:00Z",
      "status": "active"
    }
  ],
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

**Permissions**: admin, hr

---

#### Obter usuário atual

```http
GET /users/me
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": {
    "id": "usr_123",
    "name": "John Doe",
    "email": "john@example.com",
    "role": "employee",
    "position": { ... },
    "department": { ... },
    "manager": { ... },
    "hireDate": "2024-01-15T00:00:00Z"
  }
}
```

---

#### Obter usuário por ID

```http
GET /users/:id
Authorization: Bearer {token}
```

**Response 200**: Usuário encontrado  
**Response 404**: Usuário não encontrado

---

#### Criar usuário

```http
POST /users
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body**:

```json
{
  "name": "string (required)",
  "email": "string (required, valid email)",
  "role": "employee | manager | hr | admin (required)",
  "positionId": "string (required)",
  "departmentId": "string (required)",
  "managerId": "string (optional)",
  "hireDate": "string (ISO date, required)"
}
```

**Response 201**: Usuário criado  
**Response 400**: Validação falhou  
**Response 409**: Email já existe

**Permissions**: admin, hr

---

#### Atualizar usuário

```http
PUT /users/:id
Authorization: Bearer {token}
Content-Type: application/json
```

**Request Body**: (todos os campos opcionais)

```json
{
  "name": "string",
  "positionId": "string",
  "departmentId": "string",
  "managerId": "string"
}
```

**Response 200**: Usuário atualizado  
**Permissions**: admin, hr

---

#### Deletar usuário

```http
DELETE /users/:id
Authorization: Bearer {token}
```

**Response 204**: Usuário deletado (soft delete)  
**Permissions**: admin

---

### Departments

#### Listar departamentos

```http
GET /departments
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": [
    {
      "id": "dept_123",
      "name": "Engineering",
      "parentId": null,
      "headcount": 45,
      "manager": {
        "id": "usr_456",
        "name": "Tech Lead"
      },
      "children": [
        {
          "id": "dept_124",
          "name": "Frontend",
          "headcount": 15
        }
      ]
    }
  ]
}
```

---

#### Obter organograma

```http
GET /departments/:id/org-chart
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": {
    "department": { ... },
    "tree": [
      {
        "employee": { ... },
        "subordinates": [ ... ]
      }
    ]
  }
}
```

---

#### Criar departamento

```http
POST /departments
Authorization: Bearer {token}
```

**Request Body**:

```json
{
  "name": "string (required)",
  "parentId": "string (optional)",
  "managerId": "string (optional)"
}
```

**Permissions**: admin, hr

---

### Onboarding

#### Listar onboardings

```http
GET /onboarding?status=in_progress&employeeId=usr_123
Authorization: Bearer {token}
```

**Query Parameters**:

- `status` (string): not_started | in_progress | completed
- `employeeId` (string): filtrar por colaborador
- `managerId` (string): filtrar por gestor

**Response 200**:

```json
{
  "success": true,
  "data": [
    {
      "id": "onb_123",
      "employee": {
        "id": "usr_456",
        "name": "New Employee"
      },
      "status": "in_progress",
      "progress": 45.5,
      "startedAt": "2024-01-15T08:00:00Z",
      "expectedCompletionDate": "2024-02-15T23:59:59Z",
      "totalSteps": 5,
      "completedSteps": 2,
      "totalTasks": 22,
      "completedTasks": 10
    }
  ]
}
```

---

#### Obter onboarding por ID

```http
GET /onboarding/:id
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": {
    "id": "onb_123",
    "employee": { ... },
    "manager": { ... },
    "status": "in_progress",
    "progress": 45.5,
    "steps": [
      {
        "id": "step_1",
        "title": "Primeiro Dia",
        "description": "Atividades do primeiro dia",
        "order": 1,
        "tasks": [
          {
            "id": "task_1",
            "title": "Assinar documentos",
            "description": "Contrato e políticas",
            "status": "completed",
            "completedAt": "2024-01-15T10:30:00Z"
          }
        ]
      }
    ]
  }
}
```

---

#### Criar onboarding

```http
POST /onboarding
Authorization: Bearer {token}
```

**Request Body**:

```json
{
  "employeeId": "string (required)",
  "templateId": "string (optional)",
  "managerId": "string (required)",
  "startDate": "string (ISO date, optional)"
}
```

**Response 201**: Onboarding criado  
**Permissions**: hr, admin

---

#### Completar tarefa

```http
PUT /onboarding/:id/tasks/:taskId/complete
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": {
    "taskId": "task_123",
    "completedAt": "2024-01-15T14:30:00Z",
    "onboardingProgress": 50.0
  }
}
```

---

### Assessments (Avaliações)

#### Listar ciclos

```http
GET /assessments/cycles?status=active
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": [
    {
      "id": "cycle_123",
      "name": "Avaliação H2 2024",
      "startDate": "2024-07-01T00:00:00Z",
      "endDate": "2024-07-31T23:59:59Z",
      "status": "active",
      "totalAssessments": 450,
      "completedAssessments": 123
    }
  ]
}
```

---

#### Criar ciclo

```http
POST /assessments/cycles
Authorization: Bearer {token}
```

**Request Body**:

```json
{
  "name": "string (required)",
  "startDate": "string (ISO date, required)",
  "endDate": "string (ISO date, required)",
  "competencyIds": ["string"],
  "allowSelfAssessment": "boolean (default: true)",
  "allowPeerReview": "boolean (default: true)",
  "allowUpwardFeedback": "boolean (default: false)"
}
```

**Permissions**: hr, admin

---

#### Listar avaliações pendentes

```http
GET /assessments/pending
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": [
    {
      "id": "assess_123",
      "cycle": {
        "id": "cycle_123",
        "name": "Avaliação H2 2024"
      },
      "evaluated": {
        "id": "usr_456",
        "name": "John Doe"
      },
      "type": "peer_review",
      "dueDate": "2024-07-31T23:59:59Z"
    }
  ]
}
```

---

#### Submeter avaliação

```http
POST /assessments/:id/submit
Authorization: Bearer {token}
```

**Request Body**:

```json
{
  "scores": [
    {
      "competencyId": "comp_1",
      "score": 4,
      "comment": "Excellent communication skills"
    }
  ],
  "generalComment": "string (optional)"
}
```

**Response 201**: Avaliação submetida

---

#### Obter resultados

```http
GET /assessments/:id/results
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": {
    "assessed": { ... },
    "cycle": { ... },
    "averageScore": 4.2,
    "competencies": [
      {
        "id": "comp_1",
        "name": "Communication",
        "selfScore": 4,
        "managerScore": 5,
        "peerScores": [4, 4, 5],
        "averageScore": 4.4
      }
    ]
  }
}
```

**Permissions**: próprio usuário, gestor, hr, admin

---

### PDI (Plano de Desenvolvimento Individual)

#### Listar PDIs

```http
GET /pdis?employeeId=usr_123&status=active
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": [
    {
      "id": "pdi_123",
      "employee": { ... },
      "status": "active",
      "createdAt": "2024-01-01T00:00:00Z",
      "approvedAt": "2024-01-05T00:00:00Z",
      "goalsCount": 3,
      "progress": 33.3
    }
  ]
}
```

---

#### Obter PDI por ID

```http
GET /pdis/:id
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": {
    "id": "pdi_123",
    "employee": { ... },
    "manager": { ... },
    "status": "active",
    "goals": [
      {
        "id": "goal_1",
        "title": "Become Tech Lead",
        "description": "Develop leadership skills",
        "targetDate": "2024-12-31T23:59:59Z",
        "status": "in_progress",
        "progress": 40,
        "actions": [
          {
            "id": "action_1",
            "description": "Complete leadership course",
            "completedAt": "2024-03-15T00:00:00Z"
          }
        ]
      }
    ],
    "reviews": [ ... ]
  }
}
```

---

#### Criar PDI

```http
POST /pdis
Authorization: Bearer {token}
```

**Request Body**:

```json
{
  "employeeId": "string (required)",
  "goals": [
    {
      "title": "string (required)",
      "description": "string (optional)",
      "targetDate": "string (ISO date, required)",
      "actions": [
        {
          "description": "string (required)"
        }
      ]
    }
  ]
}
```

**Response 201**: PDI criado

---

#### Aprovar PDI

```http
PUT /pdis/:id/approve
Authorization: Bearer {token}
```

**Request Body**:

```json
{
  "comment": "string (optional)"
}
```

**Permissions**: gestor do colaborador, hr, admin

---

#### Adicionar goal

```http
POST /pdis/:id/goals
Authorization: Bearer {token}
```

**Request Body**:

```json
{
  "title": "string (required)",
  "description": "string (optional)",
  "targetDate": "string (ISO date, required)"
}
```

---

### KPIs e Dashboards

#### Listar KPIs

```http
GET /kpis?departmentId=dept_123
Authorization: Bearer {token}
```

**Response 200**:

```json
{
  "success": true,
  "data": [
    {
      "id": "kpi_123",
      "name": "Headcount",
      "description": "Total de colaboradores",
      "currentValue": 150,
      "target": 200,
      "unit": "employees",
      "trend": "up"
    }
  ]
}
```

---

#### Obter dashboard

```http
GET /dashboards/:type
Authorization: Bearer {token}
```

**Types**: `executive`, `hr`, `manager`, `employee`

**Response 200**:

```json
{
  "success": true,
  "data": {
    "type": "manager",
    "widgets": [
      {
        "id": "team_headcount",
        "type": "metric",
        "title": "Tamanho da Equipe",
        "value": 12
      },
      {
        "id": "onboarding_progress",
        "type": "chart",
        "title": "Onboardings Ativos",
        "data": [ ... ]
      }
    ]
  }
}
```

---

## Status Codes

| Code | Descrição                                  |
| ---- | ------------------------------------------ |
| 200  | OK - Requisição bem-sucedida               |
| 201  | Created - Recurso criado com sucesso       |
| 204  | No Content - Ação bem-sucedida sem retorno |
| 400  | Bad Request - Dados inválidos              |
| 401  | Unauthorized - Token inválido ou ausente   |
| 403  | Forbidden - Sem permissão                  |
| 404  | Not Found - Recurso não encontrado         |
| 409  | Conflict - Conflito (ex: email duplicado)  |
| 422  | Unprocessable Entity - Validação falhou    |
| 429  | Too Many Requests - Rate limit excedido    |
| 500  | Internal Server Error - Erro no servidor   |

## Rate Limiting

- **Limite**: 100 requisições por minuto por IP
- **Header de resposta**: `X-RateLimit-Remaining`
- **Retry**: Header `Retry-After` quando limite excedido

## Paginação

Endpoints que retornam listas suportam paginação:

**Query Parameters**:

- `page` (number, default: 1)
- `limit` (number, default: 20, max: 100)

**Response Headers**:

- `X-Total-Count`: Total de itens
- `X-Total-Pages`: Total de páginas

## Ordenação

Use o parâmetro `sort`:

```http
GET /users?sort=name:asc
GET /users?sort=createdAt:desc
```

## Filtros

Filtros variam por endpoint. Exemplos:

```http
GET /users?role=employee&department=dept_123
GET /assessments?status=pending&cycleId=cycle_123
```

## Webhooks

Configure webhooks para receber notificações de eventos:

**Eventos disponíveis**:

- `user.created`
- `user.updated`
- `onboarding.started`
- `onboarding.completed`
- `assessment.submitted`
- `pdi.approved`

**Payload exemplo**:

```json
{
  "event": "onboarding.completed",
  "timestamp": "2024-01-15T14:30:00Z",
  "data": {
    "onboardingId": "onb_123",
    "employeeId": "usr_456"
  }
}
```

## Postman Collection

Importe nossa [Postman Collection](./vdx-api.postman_collection.json) para testar todos os endpoints.

## Suporte

Dúvidas sobre a API? Entre em contato:

- **Email**: api-support@vdx.com
- **Slack**: #vdx-api
