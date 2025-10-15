# Architecture Decision Records (ADRs)

## O que são ADRs?

Architecture Decision Records (ADRs) documentam decisões arquiteturais importantes tomadas ao longo do projeto. Cada ADR captura o contexto, as opções consideradas, a decisão tomada e suas consequências.

## Formato

Cada ADR segue o template:

```markdown
# ADR-XXX: Título da Decisão

**Status**: Proposto | Aceito | Descontinuado | Substituído por ADR-YYY

**Data**: YYYY-MM-DD

**Contexto**: Por que precisamos tomar essa decisão? Qual é o problema?

**Decisão**: O que decidimos fazer?

**Consequências**: Quais são os impactos (positivos e negativos)?

**Alternativas Consideradas**: Quais outras opções avaliamos?
```

## ADRs do Projeto

### [ADR-001: Adoção de Monorepo com pnpm](./001-monorepo-pnpm.md)

Decisão de usar monorepo gerenciado com pnpm workspaces.

### [ADR-002: Vue 3 com Composition API](./002-vue3-composition-api.md)

Escolha de Vue 3 com Composition API para o frontend.

### [ADR-003: Clean Architecture no Backend](./003-clean-architecture-backend.md)

Adoção de Clean Architecture e DDD no backend.

### [ADR-004: PostgreSQL como Banco Principal](./004-postgresql-database.md)

Escolha de PostgreSQL como banco de dados relacional.

### [ADR-005: JWT para Autenticação](./005-jwt-authentication.md)

Uso de JWT para autenticação stateless.

### [ADR-006: Feature-Sliced Design](./006-feature-sliced-design.md)

Organização do frontend usando Feature-Sliced Design.

### [ADR-007: TailwindCSS para Estilização](./007-tailwindcss-styling.md)

Adoção de TailwindCSS v4 como framework CSS.

### [ADR-008: Vitest para Testes](./008-vitest-testing.md)

Escolha de Vitest como framework de testes.

---

## Como Criar um Novo ADR

1. Crie um arquivo numerado sequencialmente: `XXX-titulo-descritivo.md`
2. Use o template padrão
3. Descreva o contexto claramente
4. Liste alternativas consideradas
5. Documente a decisão e consequências
6. Adicione referência no índice acima
7. Commite com mensagem: `docs(adr): add ADR-XXX about [topic]`

## Revisão de ADRs

ADRs podem ser:

- **Proposto**: Em discussão
- **Aceito**: Decisão aprovada e implementada
- **Descontinuado**: Não mais relevante
- **Substituído**: Substituído por ADR mais recente
