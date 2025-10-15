# Prompt Base

## Propósito

Desenvolver uma documentação abrangente para o projeto VDX.

A documentação deverá cobrir tanto a parte estratégica (visão de negócios) como a parte tática (visão de tecnologia) e em diferentes níveis de abstração.
Seguiremos os princípios de **Design Docs** e a abordagem **Documentation-Driven Development** com o objetivo de guiar ferramentas e agentes de IA a implementarem a aplicação em `/apps` conforme as especificações construídas em `/docs`

## Descrição do Projeto

O projeto VDX servirá para a gestão interna de uma empresa de tecnologia atendendo às demandas de RH e de gestão de pessoas em geral, terá ambiente de Onboarding de novos colaboradores (bem como visualizações e acompanhamentos de seus gestores), visualização de departamentos e cargos (o organograma da empresa), avaliações de competência (no qual periodicamente os colaboradores avaliarão seus pares, seus subordinados e seus líderes), gestão de KPIs da empresa e por área, e por fim gerenciamento de PDIs de cada colaborador (suas metas, objetivos acordados com o líder e evolução ao longo do tempo). O projeto terá basicamente quatro perfis de usuário: admin, RH, gestor, colaborador.

### Layout

Na pasta `/archive` temos algumas imagens de telas que ficam como sugestão de como essas funcionalidades seriam vistas pelo usuário final. Use como referência para fazermos algo ainda melhor.

## Especificações técnicas

Aqui vamos definir algumas diretrizes técnicas. O projeto a princípio será um monorepo gerenciado com pnpm e rodará com node 22 (ou mais recente).

### Frontend

Pilha básica Vite + Vue 3 + TypeScript + TailwindCSS v4 + shadcn + eslint + prettier + tanstack query + pinia + axios + vitest + playwright.
Para o design e arquitetura do frontend usaremos o feature-sliced design, seguindo ao máximo as diretrizes das documentações oficiais para resaltar as funcionalidades no projeto.

### Backend

Abordagem de Domain-Driven Design + Clean Architecture + Clean Code para definir o núcleo da aplicação, suas áreas de negócios, visão de domínio (e subdomínios), definir contextos delimitados (se for o caso), a modelagem tática e distribuição das regras de negócio em objetos de domínio. Isso deverá ser feito aos poucos, de forma iterativa e dividido em tasks (como será visto adiante).

A justificativa é atrasar a decisão de quais tecnologias usaremos, então vamos montar primeiro o núcleo da aplicação, constituído de domain (as regras que existem por si só) e application (onde teremos os uses cases que farão a orquestração do domínio). Respeitando os princípios de SOLID e design patterns. Somente depois teremos as camadas de adaptadores e de infra, nas quais definiremos os frameworks, bibliotecas, banco de dados e demais tecnologias independente das regras de negócio. Com isso manteremos a separação saudável como indicado no modelo de arquitetura hexagonal.

No momento vamos considerar Node com Typescript (e Vitest) como base da aplicação backend.

### Infraestrutura

Podemos deixar claro para o time de operações como rodar a aplicação, uso de docker, variáveis de ambiente.

### UX/UI

A pasta `/archive` pode auxiliar, mas podemos seguir padrões ainda melhores do shadcn e outros princípios de design.
Considerar **micro-interações**, e libs de motion para dar sabor à aplicação.

## Documentos

Teremos vários documentos, de forma a atender diferentes atores do projeto, sejam eles desenvolvedores, gerentes de projeto, QAs, devOps, clientes etc. Algo próximo dessa tabela:

| Documento                             | Descrição               | Público-Alvo           |
| ------------------------------------- | ----------------------- | ---------------------- |
| [Product Vision](./product-vision.md) | Visão e objetivos       | PMs, Stakeholders      |
| [Requirements](./requirements.md)     | Especificação detalhada | Devs, PMs, QA          |
| [Architecture](./architecture.md)     | Design técnico          | Devs, Arquitetos       |
| [Dev Guide](./dev-guide.md)           | Manual do desenvolvedor | Developers             |
| [API Docs](./api-documentation.md)    | Especificação de APIs   | Devs, Integradores     |
| [Operations](./operations.md)         | Guia operacional        | DevOps, SRE            |
| [User Guide](./user-guide.md)         | Manual do usuário       | End Users              |
| [ADRs](./adr/README.md)               | Decisões arquiteturais  | Arquitetos, Tech Leads |
| [Glossary](./glossary.md)             | Definições e termos     | Todos                  |

A tabela é somente uma sugestão, podemos suprimir ou adicionar conforme a necessidade.

Divida a documentação em pastas que façam sentido (não deixe tudo na raiz em `/docs`). Reserve a pasta `/tasks` para detalhar as histórias de usuário em ordem cronológica de execução, tendo em mente que tanto uma equipe de devs irá executar como também um modelo de linguagem como Claude Opus. Pode dividir em sprints que façam sentido e entreguem valor. Detalhe o máximo que puder para deixar a execução granular e fácil de controlar.
