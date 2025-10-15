# Operations Guide - VDX

## Visão Geral

Este guia fornece informações sobre como operar, monitorar e manter o sistema VDX em produção.

## Arquitetura de Infraestrutura

```
┌─────────────────────────────────────────────────┐
│               Load Balancer / CDN               │
└────────────────────┬────────────────────────────┘
                     │
         ┌───────────┴───────────┐
         │                       │
┌────────▼─────────┐    ┌───────▼────────┐
│   Frontend App   │    │  Backend API   │
│   (Static Files) │    │   (Containers) │
└──────────────────┘    └───────┬────────┘
                                │
                    ┌───────────┴──────────┐
                    │                      │
            ┌───────▼──────┐      ┌───────▼──────┐
            │  PostgreSQL  │      │    Redis     │
            │  (Primary +  │      │   (Cache)    │
            │   Replicas)  │      └──────────────┘
            └──────────────┘
```

## Ambientes

### Development (Local)

- **Propósito**: Desenvolvimento local
- **Infra**: Docker Compose
- **URL**: http://localhost:5173
- **Database**: PostgreSQL local

### Staging

- **Propósito**: Testes e homologação
- **URL**: https://staging.vdx.com
- **Deploy**: Automático via GitHub Actions (branch `develop`)
- **Database**: PostgreSQL gerenciado

### Production

- **Propósito**: Ambiente de produção
- **URL**: https://app.vdx.com
- **Deploy**: Automático via GitHub Actions (branch `main`) após aprovação
- **Database**: PostgreSQL gerenciado com replicas

## Docker

### Desenvolvimento Local

**docker-compose.yml**:

```yaml
version: "3.8"

services:
  postgres:
    image: postgres:16
    environment:
      POSTGRES_DB: vdx_dev
      POSTGRES_USER: vdx
      POSTGRES_PASSWORD: vdx_secret
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  mailhog:
    image: mailhog/mailhog
    ports:
      - "8025:8025" # Web UI
      - "1025:1025" # SMTP

volumes:
  postgres_data:
```

**Comandos úteis**:

```bash
# Iniciar todos os serviços
docker-compose up -d

# Ver logs
docker-compose logs -f

# Parar serviços
docker-compose down

# Limpar volumes (CUIDADO: apaga dados)
docker-compose down -v
```

### Produção

**Backend Dockerfile**:

```dockerfile
# Multi-stage build
FROM node:22-alpine AS builder

WORKDIR /app

# Copiar package files
COPY package.json pnpm-lock.yaml ./
COPY apps/backend/package.json ./apps/backend/

# Install dependencies
RUN npm install -g pnpm
RUN pnpm install --frozen-lockfile

# Copy source
COPY apps/backend ./apps/backend

# Build
RUN cd apps/backend && pnpm build

# Production image
FROM node:22-alpine

WORKDIR /app

RUN npm install -g pnpm

# Copy built app
COPY --from=builder /app/apps/backend/dist ./dist
COPY --from=builder /app/apps/backend/package.json ./

# Install production dependencies only
RUN pnpm install --prod --frozen-lockfile

EXPOSE 3000

CMD ["node", "dist/main.js"]
```

**Frontend Dockerfile**:

```dockerfile
FROM node:22-alpine AS builder

WORKDIR /app

COPY package.json pnpm-lock.yaml ./
COPY apps/frontend/package.json ./apps/frontend/

RUN npm install -g pnpm
RUN pnpm install --frozen-lockfile

COPY apps/frontend ./apps/frontend

RUN cd apps/frontend && pnpm build

# Production: serve with nginx
FROM nginx:alpine

COPY --from=builder /app/apps/frontend/dist /usr/share/nginx/html
COPY apps/frontend/nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

## Variáveis de Ambiente

### Backend (.env)

```bash
# Application
NODE_ENV=production
PORT=3000
API_VERSION=v1

# Database
DATABASE_URL=postgresql://user:password@host:5432/dbname
DATABASE_POOL_MIN=2
DATABASE_POOL_MAX=10

# Redis
REDIS_URL=redis://host:6379

# Authentication
JWT_SECRET=your-super-secret-key-change-me
JWT_EXPIRES_IN=1h
REFRESH_TOKEN_EXPIRES_IN=7d

# Email
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=noreply@vdx.com
SMTP_PASSWORD=email-password
SMTP_FROM=VDX <noreply@vdx.com>

# Storage
STORAGE_TYPE=s3
AWS_REGION=us-east-1
AWS_S3_BUCKET=vdx-uploads
AWS_ACCESS_KEY_ID=your-key
AWS_SECRET_ACCESS_KEY=your-secret

# Monitoring
SENTRY_DSN=https://xxx@sentry.io/xxx
LOG_LEVEL=info
```

### Frontend (.env)

```bash
VITE_API_URL=https://api.vdx.com/api/v1
VITE_APP_NAME=VDX
VITE_ENVIRONMENT=production
```

## CI/CD Pipeline

### GitHub Actions Workflow

**.github/workflows/deploy.yml**:

```yaml
name: Deploy

on:
  push:
    branches: [main, develop]

jobs:
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
        run: pnpm install

      - name: Lint
        run: pnpm lint

      - name: Test
        run: pnpm test

      - name: Build
        run: pnpm build

  deploy-staging:
    needs: test
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Staging
        run: |
          # Deploy commands here
          echo "Deploying to staging..."

  deploy-production:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    environment: production
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Production
        run: |
          # Deploy commands here
          echo "Deploying to production..."
```

## Database

### Migrations

```bash
# Criar nova migration
pnpm db:migration:create add_user_avatar

# Executar migrations pendentes
pnpm db:migrate

# Reverter última migration
pnpm db:migrate:rollback

# Ver status das migrations
pnpm db:migrate:status
```

### Backups

**Automático**:

- Backups diários às 02:00 UTC
- Retenção: 30 dias
- Armazenamento: S3 criptografado

**Manual**:

```bash
# Backup
pg_dump -h host -U user -d dbname -F c -f backup.dump

# Restore
pg_restore -h host -U user -d dbname backup.dump
```

### Monitoramento de Performance

```sql
-- Queries lentas
SELECT
  query,
  calls,
  total_time,
  mean_time,
  max_time
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;

-- Tamanho das tabelas
SELECT
  schemaname,
  tablename,
  pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename))
FROM pg_tables
ORDER BY pg_total_relation_size(schemaname||'.'||tablename) DESC;

-- Índices não utilizados
SELECT
  schemaname,
  tablename,
  indexname,
  idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY pg_relation_size(indexrelid) DESC;
```

## Monitoramento

### Health Checks

**Endpoint**: `GET /health`

```json
{
  "status": "healthy",
  "timestamp": "2024-01-15T10:30:00Z",
  "uptime": 86400,
  "services": {
    "database": "healthy",
    "redis": "healthy",
    "email": "healthy"
  }
}
```

### Logs

**Structured Logging** (JSON):

```json
{
  "level": "info",
  "timestamp": "2024-01-15T10:30:00Z",
  "message": "User authenticated",
  "userId": "usr_123",
  "ip": "192.168.1.1",
  "userAgent": "Mozilla/5.0..."
}
```

**Níveis de Log**:

- `error`: Erros críticos
- `warn`: Avisos importantes
- `info`: Informações gerais (default em produção)
- `debug`: Debug detalhado (apenas em dev)

**Visualização**:

```bash
# Logs em tempo real (Docker)
docker-compose logs -f backend

# Filtrar por nível
docker-compose logs backend | grep "level\":\"error"
```

### Métricas

**Prometheus Metrics** (endpoint `/metrics`):

- `http_requests_total`: Total de requisições
- `http_request_duration_seconds`: Duração das requisições
- `database_connections_active`: Conexões ativas no pool
- `cache_hit_rate`: Taxa de hit do cache

### Alertas

**Configurar alertas para**:

- CPU > 80% por 5 minutos
- Memória > 85%
- Disk space < 15%
- Taxa de erro > 5%
- Tempo de resposta P95 > 3s
- Database connections > 90% do pool

## Performance

### Caching

**Redis Cache**:

```typescript
// Exemplo de uso
const cacheKey = `user:${userId}`;
const ttl = 3600; // 1 hora

// Get from cache
let user = await redis.get(cacheKey);

if (!user) {
  // Cache miss: buscar do DB
  user = await database.users.findById(userId);

  // Salvar no cache
  await redis.set(cacheKey, JSON.stringify(user), "EX", ttl);
}
```

**Estratégias**:

- Cache de sessões (Redis)
- Cache de queries frequentes (1h TTL)
- Cache de assets estáticos (CDN)

### Otimizações

**Database**:

- Índices em colunas de busca
- Connection pooling configurado
- Read replicas para queries pesadas
- Particionamento de tabelas grandes

**API**:

- Rate limiting (100 req/min)
- Compressão gzip
- Pagination em listas
- Field selection (query params)

**Frontend**:

- Code splitting
- Lazy loading de rotas
- Image optimization
- CDN para assets

## Segurança

### SSL/TLS

- Certificados gerenciados via Let's Encrypt
- Renovação automática
- TLS 1.3 obrigatório
- HSTS habilitado

### Firewall

- Apenas portas 80 e 443 públicas
- SSH apenas via VPN
- Database em rede privada

### Secrets Management

- Variáveis sensíveis em secrets manager
- Rotação automática de credenciais
- Nunca commitar secrets no código

### Atualizações de Segurança

- Dependências atualizadas semanalmente
- Security scans automáticos (Dependabot)
- CVE monitoring

## Disaster Recovery

### RTO e RPO

- **RTO** (Recovery Time Objective): 4 horas
- **RPO** (Recovery Point Objective): 24 horas

### Plano de Recuperação

1. **Database corruption**:

   - Restore do backup mais recente
   - Replay de WAL logs se disponível
   - Validação de integridade

2. **Application failure**:

   - Rollback para versão anterior
   - Investigar logs
   - Deploy de hotfix se necessário

3. **Infrastructure outage**:
   - Failover para região secundária
   - Update DNS se necessário
   - Comunicação com stakeholders

### Runbooks

Mantenha runbooks atualizados para:

- [ ] Restore de backup
- [ ] Rollback de deploy
- [ ] Escalação de incidentes
- [ ] Comunicação de downtime

## Manutenção

### Rotina Diária

- [ ] Verificar dashboards de monitoramento
- [ ] Revisar logs de erro
- [ ] Verificar alertas

### Rotina Semanal

- [ ] Revisar performance de queries
- [ ] Verificar espaço em disco
- [ ] Atualizar dependências
- [ ] Revisar backups

### Rotina Mensal

- [ ] Revisar e otimizar custos de infraestrutura
- [ ] Teste de restore de backup
- [ ] Revisar e atualizar documentação
- [ ] Security audit

## Troubleshooting

### Application não inicia

```bash
# Verificar logs
docker-compose logs backend

# Verificar variáveis de ambiente
docker-compose exec backend env | grep DATABASE

# Testar conexão com DB
docker-compose exec backend node -e "require('./dist/main.js')"
```

### Performance degradada

1. Verificar métricas de CPU/Memória
2. Analisar slow queries
3. Verificar cache hit rate
4. Revisar logs de erro
5. Escalar recursos se necessário

### Database connection errors

```bash
# Verificar status do PostgreSQL
docker-compose exec postgres pg_isready

# Ver conexões ativas
docker-compose exec postgres psql -U vdx -c "SELECT count(*) FROM pg_stat_activity;"

# Reiniciar pool de conexões
docker-compose restart backend
```

## Contatos de Emergência

- **Tech Lead**: +55 11 99999-0001
- **DevOps**: +55 11 99999-0002
- **On-Call**: ver PagerDuty schedule

## Recursos

- [Monitoring Dashboard](https://monitoring.vdx.com)
- [Log Aggregation](https://logs.vdx.com)
- [Status Page](https://status.vdx.com)
- [Runbooks](./runbooks/)
