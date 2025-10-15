# Requirements - VDX

## Requisitos Funcionais

### RF001 - Autenticação e Autorização

#### RF001.1 - Login

- O sistema DEVE permitir login com email e senha
- O sistema DEVE suportar autenticação OAuth2/OIDC
- O sistema DEVE implementar recuperação de senha via email
- O sistema DEVE bloquear conta após 5 tentativas de login falhadas
- O sistema DEVE manter sessão ativa por 7 dias (com opção "Lembrar-me")

#### RF001.2 - Controle de Acesso

- O sistema DEVE implementar RBAC (Role-Based Access Control)
- O sistema DEVE suportar 4 perfis: Admin, RH, Gestor, Colaborador
- O sistema DEVE permitir que Admin configure permissões granulares
- O sistema DEVE restringir acesso baseado em departamento/hierarquia

### RF002 - Gestão de Usuários

#### RF002.1 - Perfil do Colaborador

- O sistema DEVE permitir visualização de perfil completo
- O sistema DEVE exibir: nome, foto, cargo, departamento, gestor, data de admissão
- O sistema DEVE permitir que colaborador edite informações pessoais
- O sistema DEVE manter histórico de cargos e departamentos

#### RF002.2 - Cadastro de Colaboradores

- O sistema DEVE permitir que RH/Admin cadastre novos colaboradores
- O sistema DEVE enviar email de boas-vindas com credenciais iniciais
- O sistema DEVE permitir importação em lote via CSV
- O sistema DEVE validar dados obrigatórios (nome, email, cargo, departamento)

### RF003 - Onboarding

#### RF003.1 - Jornada de Onboarding

- O sistema DEVE criar jornada de onboarding automática para novos colaboradores
- O sistema DEVE organizar onboarding em etapas configuráveis
- O sistema DEVE exibir progresso visual da jornada
- O sistema DEVE notificar colaborador sobre próximas etapas

#### RF003.2 - Tarefas de Onboarding

- O sistema DEVE permitir checklist de tarefas por etapa
- O sistema DEVE permitir anexar documentos e recursos
- O sistema DEVE permitir marcar tarefas como concluídas
- O sistema DEVE registrar data/hora de conclusão de cada tarefa

#### RF003.3 - Acompanhamento

- O sistema DEVE permitir que gestor visualize progresso do onboarding
- O sistema DEVE permitir que RH monitore todos os onboardings ativos
- O sistema DEVE enviar alertas para tarefas atrasadas
- O sistema DEVE gerar relatório de conclusão do onboarding

### RF004 - Organograma

#### RF004.1 - Visualização

- O sistema DEVE exibir organograma hierárquico da empresa
- O sistema DEVE permitir visualização por departamento
- O sistema DEVE permitir navegação interativa (expandir/colapsar)
- O sistema DEVE exibir foto e cargo de cada colaborador

#### RF004.2 - Gestão de Estrutura

- O sistema DEVE permitir que Admin/RH configure departamentos
- O sistema DEVE permitir definição de hierarquia (subordinação)
- O sistema DEVE permitir criação/edição de cargos
- O sistema DEVE validar loops na hierarquia

### RF005 - Avaliações de Competência

#### RF005.1 - Ciclos de Avaliação

- O sistema DEVE permitir criação de ciclos de avaliação periódicos
- O sistema DEVE permitir configuração de datas de início/fim
- O sistema DEVE permitir definição de competências avaliadas por cargo
- O sistema DEVE suportar escalas de avaliação customizáveis

#### RF005.2 - Avaliação 360°

- O sistema DEVE permitir autoavaliação
- O sistema DEVE permitir avaliação de pares (peer review)
- O sistema DEVE permitir avaliação de subordinados (upward feedback)
- O sistema DEVE permitir avaliação de líderes (downward feedback)
- O sistema DEVE garantir anonimato em avaliações configuradas como anônimas

#### RF005.3 - Formulários de Avaliação

- O sistema DEVE apresentar formulário com competências do cargo
- O sistema DEVE permitir notas e comentários
- O sistema DEVE validar preenchimento obrigatório
- O sistema DEVE permitir salvar rascunho

#### RF005.4 - Resultados

- O sistema DEVE calcular média ponderada das avaliações
- O sistema DEVE gerar relatório individual consolidado
- O sistema DEVE permitir que gestor visualize resultados da equipe
- O sistema DEVE permitir comparação com ciclos anteriores

### RF006 - PDI (Plano de Desenvolvimento Individual)

#### RF006.1 - Criação de PDI

- O sistema DEVE permitir que colaborador crie PDI
- O sistema DEVE permitir que gestor colabore no PDI
- O sistema DEVE permitir definição de objetivos SMART
- O sistema DEVE permitir associar objetivos a competências

#### RF006.2 - Acompanhamento

- O sistema DEVE exibir progresso de cada objetivo
- O sistema DEVE permitir registro de ações e milestones
- O sistema DEVE permitir comentários entre colaborador e gestor
- O sistema DEVE enviar lembretes de revisão periódica

#### RF006.3 - Revisões

- O sistema DEVE permitir agendamento de revisões de PDI
- O sistema DEVE registrar feedback em cada revisão
- O sistema DEVE permitir ajuste de objetivos
- O sistema DEVE manter histórico de todas as revisões

### RF007 - KPIs e Métricas

#### RF007.1 - KPIs Organizacionais

- O sistema DEVE permitir cadastro de KPIs corporativos
- O sistema DEVE permitir KPIs por departamento
- O sistema DEVE permitir visualização de tendências temporais
- O sistema DEVE permitir comparação entre períodos

#### RF007.2 - Dashboards

- O sistema DEVE fornecer dashboard executivo para Admin/CEO
- O sistema DEVE fornecer dashboard de RH com métricas de pessoas
- O sistema DEVE fornecer dashboard de gestor com métricas da equipe
- O sistema DEVE permitir exportação de dados

#### RF007.3 - Métricas Individuais

- O sistema DEVE exibir métricas de performance individual
- O sistema DEVE exibir evolução em avaliações
- O sistema DEVE exibir progresso de PDI
- O sistema DEVE exibir reconhecimentos recebidos

### RF008 - Notificações

#### RF008.1 - Sistema de Notificações

- O sistema DEVE enviar notificações in-app
- O sistema DEVE enviar notificações por email
- O sistema DEVE permitir configuração de preferências de notificação
- O sistema DEVE agrupar notificações similares

#### RF008.2 - Tipos de Notificação

- Novo colaborador com onboarding iniciado
- Tarefas de onboarding pendentes
- Ciclo de avaliação aberto
- Avaliações pendentes
- Revisão de PDI agendada
- Atualização em PDI
- Mudança na estrutura organizacional

### RF009 - Relatórios e Exportações

#### RF009.1 - Relatórios Pré-Configurados

- O sistema DEVE fornecer relatório de headcount
- O sistema DEVE fornecer relatório de turnover
- O sistema DEVE fornecer relatório de avaliações
- O sistema DEVE fornecer relatório de PDIs

#### RF009.2 - Exportação de Dados

- O sistema DEVE permitir exportação em PDF
- O sistema DEVE permitir exportação em Excel
- O sistema DEVE permitir exportação em CSV
- O sistema DEVE aplicar filtros de segurança na exportação

## Requisitos Não Funcionais

### RNF001 - Performance

- O sistema DEVE carregar páginas em menos de 2 segundos (P95)
- O sistema DEVE suportar 500 usuários simultâneos
- O sistema DEVE processar avaliações em lote em menos de 5 minutos
- O sistema DEVE otimizar consultas ao banco de dados (< 100ms)

### RNF002 - Segurança

- O sistema DEVE criptografar dados sensíveis em repouso (AES-256)
- O sistema DEVE usar HTTPS para todas as comunicações
- O sistema DEVE implementar proteção contra CSRF, XSS, SQL Injection
- O sistema DEVE registrar logs de auditoria para ações críticas
- O sistema DEVE estar em conformidade com LGPD

### RNF003 - Disponibilidade

- O sistema DEVE ter disponibilidade de 99.5% (SLA)
- O sistema DEVE implementar backups diários automáticos
- O sistema DEVE ter plano de recuperação de desastres (RPO < 24h, RTO < 4h)
- O sistema DEVE implementar health checks e monitoramento

### RNF004 - Usabilidade

- O sistema DEVE ser responsivo (mobile, tablet, desktop)
- O sistema DEVE seguir padrões de acessibilidade WCAG 2.1 nível AA
- O sistema DEVE suportar internacionalização (i18n) - PT-BR inicial
- O sistema DEVE fornecer feedback visual para todas as ações
- O sistema DEVE ter tempo de aprendizado < 1 hora para usuários básicos

### RNF005 - Manutenibilidade

- O sistema DEVE ter cobertura de testes > 80%
- O sistema DEVE seguir padrões de código (ESLint, Prettier)
- O sistema DEVE ter documentação técnica atualizada
- O sistema DEVE utilizar versionamento semântico
- O sistema DEVE ter arquitetura modular e desacoplada

### RNF006 - Escalabilidade

- O sistema DEVE escalar horizontalmente
- O sistema DEVE suportar até 5000 colaboradores
- O sistema DEVE otimizar para crescimento de 50% ao ano
- O sistema DEVE implementar cache estratégico

### RNF007 - Compatibilidade

- O sistema DEVE suportar Chrome, Firefox, Safari, Edge (últimas 2 versões)
- O sistema DEVE funcionar em iOS 14+ e Android 10+
- O sistema DEVE ter APIs RESTful versionadas
- O sistema DEVE fornecer webhooks para integrações

## Regras de Negócio

### RN001 - Hierarquia

- Um colaborador PODE ter apenas um gestor direto
- Um gestor PODE ter múltiplos subordinados
- Admin e RH PODEM visualizar toda a hierarquia
- Colaborador PODE visualizar apenas sua linha hierárquica

### RN002 - Avaliações

- Autoavaliação é OBRIGATÓRIA para participar do ciclo
- Gestor DEVE avaliar todos os subordinados diretos
- Avaliação de pares é OPCIONAL e configurável
- Resultados SOMENTE são liberados após fechamento do ciclo

### RN003 - PDI

- PDI DEVE ser revisado minimamente a cada trimestre
- Objetivos DEVEM ter prazo definido
- Gestor DEVE aprovar PDI antes de ativar
- Colaborador PODE ter no máximo 5 objetivos ativos simultaneamente

### RN004 - Onboarding

- Onboarding DEVE iniciar na data de admissão
- Tarefas PODEM ter dependências (sequenciais)
- Onboarding é considerado completo com 100% de tarefas concluídas
- Gestor é NOTIFICADO se onboarding não for concluído em 30 dias

### RN005 - Dados Sensíveis

- Salários e compensações NÃO fazem parte do escopo inicial
- Dados de avaliação SOMENTE acessíveis por: avaliado, gestor, RH, Admin
- Histórico completo DEVE ser mantido para auditoria
- Exclusão de usuário DEVE anonimizar dados, não deletar

## Casos de Uso Principais

### UC001 - Realizar Login

**Ator**: Colaborador  
**Pré-condição**: Usuário cadastrado no sistema  
**Fluxo Principal**:

1. Usuário acessa página de login
2. Usuário insere email e senha
3. Sistema valida credenciais
4. Sistema cria sessão
5. Sistema redireciona para dashboard

**Fluxo Alternativo 3a**: Credenciais inválidas

- Sistema exibe mensagem de erro
- Sistema registra tentativa
- Retorna ao passo 2

### UC002 - Iniciar Onboarding

**Ator**: RH  
**Pré-condição**: Novo colaborador cadastrado  
**Fluxo Principal**:

1. RH acessa módulo de onboarding
2. RH seleciona template de onboarding para o cargo
3. RH personaliza etapas e tarefas
4. RH define gestor responsável
5. Sistema envia notificação ao colaborador e gestor
6. Sistema inicia contagem de prazos

### UC003 - Realizar Avaliação 360°

**Ator**: Colaborador  
**Pré-condição**: Ciclo de avaliação ativo  
**Fluxo Principal**:

1. Sistema notifica colaborador sobre avaliação pendente
2. Colaborador acessa módulo de avaliações
3. Sistema exibe lista de avaliações pendentes
4. Colaborador seleciona avaliação
5. Sistema apresenta formulário com competências
6. Colaborador preenche notas e comentários
7. Colaborador submete avaliação
8. Sistema registra e confirma

### UC004 - Criar PDI

**Ator**: Colaborador e Gestor  
**Pré-condição**: Colaborador autenticado  
**Fluxo Principal**:

1. Colaborador acessa módulo de PDI
2. Colaborador define objetivos de desenvolvimento
3. Colaborador define ações e prazos
4. Colaborador solicita aprovação do gestor
5. Sistema notifica gestor
6. Gestor revisa e aprova PDI
7. Sistema ativa PDI

### UC005 - Visualizar Dashboard de Equipe

**Ator**: Gestor  
**Pré-condição**: Gestor com subordinados  
**Fluxo Principal**:

1. Gestor acessa dashboard
2. Sistema exibe métricas da equipe
3. Sistema exibe onboardings em andamento
4. Sistema exibe avaliações pendentes
5. Sistema exibe status de PDIs
6. Gestor pode filtrar e detalhar informações

## Priorização

### Must Have (P0)

- RF001: Autenticação e Autorização
- RF002: Gestão de Usuários
- RF003: Onboarding
- RF004: Organograma
- RNF002: Segurança
- RNF004: Usabilidade básica

### Should Have (P1)

- RF005: Avaliações de Competência
- RF006: PDI
- RF008: Notificações
- RNF001: Performance
- RNF003: Disponibilidade

### Could Have (P2)

- RF007: KPIs e Métricas
- RF009: Relatórios avançados
- RNF006: Escalabilidade avançada
- RNF007: Compatibilidade estendida

### Won't Have (Escopo Futuro)

- Gestão de Folha de Pagamento
- Recrutamento e Seleção (ATS)
- Gestão de Benefícios
- Gestão de Ponto e Frequência
