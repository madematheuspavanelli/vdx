# Glossário - VDX

## Termos de Negócio

### Avaliação 360°

Processo de avaliação de desempenho que coleta feedback de múltiplas fontes: autoavaliação, gestor direto, pares (colegas), subordinados e eventualmente clientes. Oferece visão holística do desempenho e comportamento do colaborador.

### Ciclo de Avaliação

Período definido (geralmente semestral ou anual) durante o qual são realizadas avaliações de competência dos colaboradores. Possui data de início, fim e regras específicas.

### Colaborador

Pessoa que trabalha na empresa. Sinônimos: funcionário, membro do time.

### Competência

Conjunto de conhecimentos, habilidades e atitudes (CHA) necessários para desempenhar uma função. Exemplo: liderança, comunicação, pensamento analítico.

### Dashboard

Painel visual que consolida métricas e informações relevantes de forma gráfica, permitindo visão rápida do status de processos e indicadores.

### Departamento

Unidade organizacional que agrupa colaboradores por função, especialidade ou área de atuação. Exemplos: Engenharia, Produto, RH, Vendas.

### Gestor

Colaborador que possui subordinados diretos sob sua liderança e responsabilidade. Sinônimos: líder, manager.

### Headcount

Número total de colaboradores ativos na empresa ou em um departamento específico.

### KPI (Key Performance Indicator)

Indicador-chave de desempenho utilizado para medir o sucesso de uma organização, departamento ou indivíduo em atingir objetivos estratégicos.

### Onboarding

Processo estruturado de integração de novos colaboradores à empresa, incluindo apresentações, treinamentos, documentação e atividades que facilitam a adaptação.

### Organograma

Representação gráfica da estrutura hierárquica da organização, mostrando departamentos, cargos e relações de subordinação.

### PDI (Plano de Desenvolvimento Individual)

Documento que registra objetivos de desenvolvimento profissional de um colaborador, ações planejadas, prazos e acompanhamento de progresso. Co-criado entre colaborador e gestor.

### Peer Review

Avaliação realizada por colegas do mesmo nível hierárquico. Parte do processo de avaliação 360°.

### Subordinado

Colaborador que reporta diretamente a um gestor.

### Turnover

Taxa de rotatividade de colaboradores. Mede quantos colaboradores deixam a empresa em um determinado período.

### Upward Feedback

Feedback dado por subordinados sobre seus gestores. Geralmente anônimo.

## Termos Técnicos

### ADR (Architecture Decision Record)

Documento que registra uma decisão arquitetural importante, incluindo contexto, opções consideradas, decisão tomada e consequências.

### API (Application Programming Interface)

Interface de programação que permite comunicação entre sistemas. No VDX, as APIs RESTful permitem integrações externas.

### Clean Architecture

Padrão arquitetural que separa regras de negócio de detalhes de implementação (frameworks, banco de dados, UI), promovendo testabilidade e manutenibilidade.

### CSRF (Cross-Site Request Forgery)

Tipo de ataque onde comandos não autorizados são transmitidos de um usuário autenticado. O sistema deve implementar proteções contra CSRF.

### DDD (Domain-Driven Design)

Abordagem de desenvolvimento de software que foca em modelar o software baseado no domínio de negócio, com linguagem ubíqua e contextos delimitados.

### Feature-Sliced Design

Arquitetura de frontend que organiza código por features/funcionalidades de negócio, promovendo modularidade e escalabilidade.

### Health Check

Endpoint ou mecanismo que verifica se o sistema está funcionando corretamente. Usado para monitoramento.

### Hexagonal Architecture

Sinônimo de Ports and Adapters. Arquitetura que isola lógica de negócio de dependências externas através de interfaces.

### i18n (Internationalization)

Processo de preparar software para suportar múltiplos idiomas e regiões sem necessidade de alterações de código.

### LGPD (Lei Geral de Proteção de Dados)

Lei brasileira que regula tratamento de dados pessoais. O sistema deve estar em conformidade.

### Monorepo

Repositório único que contém múltiplos projetos relacionados (frontend, backend, shared libs). Gerenciado com ferramentas como pnpm workspaces.

### OAuth2 / OIDC

Protocolos de autenticação e autorização que permitem login via provedores externos (Google, Microsoft, etc.).

### P95 (Percentil 95)

Métrica que indica que 95% das requisições têm tempo de resposta igual ou menor ao valor especificado.

### RBAC (Role-Based Access Control)

Modelo de controle de acesso baseado em papéis/perfis. Usuários recebem permissões através de seus papéis.

### RPO (Recovery Point Objective)

Perda máxima de dados tolerável em caso de desastre. Exemplo: RPO de 24h significa que backups são feitos diariamente.

### RTO (Recovery Time Objective)

Tempo máximo aceitável para restaurar o sistema após um desastre.

### SLA (Service Level Agreement)

Acordo de nível de serviço que define disponibilidade esperada do sistema. Exemplo: 99.5% de uptime.

### SOLID

Conjunto de 5 princípios de design orientado a objetos:

- **S**ingle Responsibility Principle
- **O**pen/Closed Principle
- **L**iskov Substitution Principle
- **I**nterface Segregation Principle
- **D**ependency Inversion Principle

### SSR (Server-Side Rendering)

Técnica onde HTML é gerado no servidor. Não aplicável no VDX (usamos SPA).

### TailwindCSS

Framework CSS utility-first usado no frontend do VDX.

### Use Case

Caso de uso que descreve interação entre ator (usuário) e sistema para atingir um objetivo.

### Vue 3

Framework JavaScript progressivo usado para construir a interface do VDX.

### Webhook

Mecanismo que permite que o sistema notifique sistemas externos sobre eventos via HTTP callbacks.

### XSS (Cross-Site Scripting)

Tipo de vulnerabilidade onde scripts maliciosos são injetados em páginas web. O sistema deve implementar proteções contra XSS.

## Perfis de Usuário

### Admin

Perfil com permissões máximas. Pode configurar sistema, gerenciar todos os usuários e acessar todas as funcionalidades. Geralmente limitado a poucos usuários técnicos.

### RH

Perfil para profissionais de recursos humanos. Acesso completo a gestão de pessoas, onboarding, avaliações e relatórios. Não tem acesso a configurações técnicas do sistema.

### Gestor

Perfil para líderes de equipe. Acesso a informações de subordinados diretos, pode conduzir avaliações, aprovar PDIs e acompanhar onboardings de sua equipe.

### Colaborador

Perfil padrão para todos os funcionários. Acesso a informações pessoais, próprio PDI, avaliações, organograma e dashboards limitados.

## Acrônimos

- **API**: Application Programming Interface
- **CSV**: Comma-Separated Values
- **DTO**: Data Transfer Object
- **HR**: Human Resources (Recursos Humanos)
- **HTTP**: HyperText Transfer Protocol
- **HTTPS**: HTTP Secure
- **JSON**: JavaScript Object Notation
- **JWT**: JSON Web Token
- **MVC**: Model-View-Controller
- **OKR**: Objectives and Key Results
- **PDF**: Portable Document Format
- **REST**: Representational State Transfer
- **SMART**: Specific, Measurable, Achievable, Relevant, Time-bound
- **SQL**: Structured Query Language
- **UI**: User Interface
- **URL**: Uniform Resource Locator
- **UX**: User Experience
- **WCAG**: Web Content Accessibility Guidelines

## Métricas e Indicadores

### Taxa de Conclusão de Onboarding

Percentual de onboardings concluídos dentro do prazo esperado (geralmente 30 dias).

### Tempo Médio de Onboarding

Quantidade média de dias entre admissão e conclusão do onboarding.

### Taxa de Participação em Avaliações

Percentual de avaliações completadas em relação ao total esperado em um ciclo.

### Net Promoter Score (NPS)

Métrica que mede satisfação e lealdade dos usuários. Varia de -100 a +100.

### Engajamento

Medido por frequência de uso, usuários ativos, tempo de sessão, etc.

### PDIs Ativos

Percentual de colaboradores com PDI ativo e sendo acompanhado.

### Tempo Médio de Permanência

Tempo médio que colaboradores permanecem na empresa.

## Domínios do Sistema

### Domínio de Identidade

Responsável por autenticação, autorização, usuários e permissões.

### Domínio de Organização

Responsável por estrutura organizacional, departamentos, cargos, hierarquias.

### Domínio de Onboarding

Responsável por processos de integração de novos colaboradores.

### Domínio de Avaliação

Responsável por ciclos de avaliação, competências, feedbacks.

### Domínio de Desenvolvimento

Responsável por PDIs, objetivos, metas, acompanhamento.

### Domínio de Analytics

Responsável por KPIs, métricas, dashboards, relatórios.

## Convenções de Nomenclatura

### Branches

- `main`: branch principal de produção
- `develop`: branch de desenvolvimento
- `feature/`: novas funcionalidades
- `fix/`: correções
- `docs/`: documentação

### Commits

Seguir convenção de Conventional Commits:

- `feat:` nova funcionalidade
- `fix:` correção de bug
- `docs:` mudanças em documentação
- `style:` formatação, sem mudança de código
- `refactor:` refatoração de código
- `test:` adição ou correção de testes
- `chore:` mudanças em build, CI, etc.

### Versionamento

Seguir Semantic Versioning (SemVer):

- `MAJOR.MINOR.PATCH`
- MAJOR: mudanças incompatíveis
- MINOR: novas funcionalidades compatíveis
- PATCH: correções compatíveis
