# Guia de Deployment - FisioClinic

## Visão Geral

Este guia fornece instruções detalhadas para fazer o deploy do sistema FisioClinic em diferentes ambientes, incluindo desenvolvimento local, Docker e cloud providers como AWS e Azure.

## Pré-requisitos

### Para Desenvolvimento Local
- Go 1.21 ou superior
- Node.js 18 ou superior
- PostgreSQL 14 ou superior
- Git

### Para Docker
- Docker 20.10 ou superior
- Docker Compose 2.0 ou superior

### Para Cloud (AWS/Azure)
- Conta na AWS ou Azure
- CLI configurado (AWS CLI ou Azure CLI)
- Terraform (opcional, para infraestrutura como código)

## Deployment Local (Desenvolvimento)

### 1. Preparação do Ambiente

```bash
# Clone o repositório
git clone <repository-url>
cd physiotherapy-clinic-system

# Instale o PostgreSQL
sudo apt update
sudo apt install postgresql postgresql-contrib

# Inicie o PostgreSQL
sudo systemctl start postgresql
sudo systemctl enable postgresql
```

### 2. Configuração do Banco de Dados

```bash
# Acesse o PostgreSQL como usuário postgres
sudo -u postgres psql

# Crie o banco de dados
CREATE DATABASE physiotherapy_clinic;

# Crie um usuário (opcional)
CREATE USER fisio_user WITH PASSWORD 'sua_senha_segura';
GRANT ALL PRIVILEGES ON DATABASE physiotherapy_clinic TO fisio_user;

# Saia do PostgreSQL
\q

# Execute o script de inicialização
sudo -u postgres psql -d physiotherapy_clinic -f database/init.sql
```

### 3. Configuração do Backend

```bash
# Navegue para o diretório do backend
cd backend

# Instale o Go (se não estiver instalado)
wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin

# Configure as variáveis de ambiente
cp .env.example .env

# Edite o arquivo .env com suas configurações
nano .env
```

Conteúdo do arquivo `.env`:
```env
DATABASE_URL=postgres://postgres:postgres123@localhost:5432/physiotherapy_clinic?sslmode=disable
JWT_SECRET=your-super-secret-jwt-key-change-in-production
PORT=8080
```

```bash
# Instale as dependências
go mod tidy

# Execute o backend
go run cmd/server/main.go
```

### 4. Configuração do Frontend

```bash
# Em um novo terminal, navegue para o frontend
cd frontend

# Instale as dependências
npm install

# Configure as variáveis de ambiente
cp .env.example .env

# Edite o arquivo .env
nano .env
```

Conteúdo do arquivo `.env`:
```env
VITE_API_URL=http://localhost:8080/api
```

```bash
# Execute o frontend
npm run dev
```

### 5. Acesso ao Sistema

- Frontend: http://localhost:5173
- Backend API: http://localhost:8080/api
- Health Check: http://localhost:8080/health

**Credenciais de teste:**
- Email: admin@fisiovida.com.br
- Senha: admin123

## Deployment com Docker

### 1. Preparação

```bash
# Certifique-se de que o Docker está instalado e funcionando
docker --version
docker-compose --version

# Clone o repositório (se ainda não fez)
git clone <repository-url>
cd physiotherapy-clinic-system
```

### 2. Configuração de Variáveis de Ambiente

Crie um arquivo `.env` na raiz do projeto:

```env
# Banco de dados
POSTGRES_DB=physiotherapy_clinic
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123

# Backend
DATABASE_URL=postgres://postgres:postgres123@db:5432/physiotherapy_clinic?sslmode=disable
JWT_SECRET=your-super-secret-jwt-key-change-in-production-2024
PORT=8080

# Frontend
VITE_API_URL=http://localhost:8080/api
```

### 3. Build e Execução

```bash
# Build dos containers
docker-compose build

# Execução em background
docker-compose up -d

# Verificar logs
docker-compose logs -f

# Verificar status dos containers
docker-compose ps
```

### 4. Acesso ao Sistema

- Frontend: http://localhost:3000
- Backend API: http://localhost:8080/api
- PostgreSQL: localhost:5432

### 5. Comandos Úteis

```bash
# Parar os serviços
docker-compose down

# Parar e remover volumes (CUIDADO: apaga dados do banco)
docker-compose down -v

# Rebuild de um serviço específico
docker-compose build backend
docker-compose up -d backend

# Acessar logs de um serviço específico
docker-compose logs -f backend

# Executar comandos dentro de um container
docker-compose exec backend sh
docker-compose exec db psql -U postgres -d physiotherapy_clinic
```

## Deployment na AWS

### 1. Arquitetura Recomendada

```
Internet Gateway
    |
Application Load Balancer
    |
    ├── ECS Fargate (Frontend)
    ├── ECS Fargate (Backend)
    └── RDS PostgreSQL
```

### 2. Preparação da Infraestrutura

#### 2.1. RDS PostgreSQL

```bash
# Crie uma instância RDS PostgreSQL
aws rds create-db-instance \
    --db-instance-identifier fisio-clinic-db \
    --db-instance-class db.t3.micro \
    --engine postgres \
    --master-username postgres \
    --master-user-password SuaSenhaSegura123 \
    --allocated-storage 20 \
    --vpc-security-group-ids sg-xxxxxxxxx \
    --db-subnet-group-name default \
    --backup-retention-period 7 \
    --storage-encrypted
```

#### 2.2. ECR (Elastic Container Registry)

```bash
# Crie repositórios para as imagens
aws ecr create-repository --repository-name fisio-clinic-backend
aws ecr create-repository --repository-name fisio-clinic-frontend

# Obtenha o login do ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
```

#### 2.3. Build e Push das Imagens

```bash
# Backend
cd backend
docker build -t fisio-clinic-backend .
docker tag fisio-clinic-backend:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-backend:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-backend:latest

# Frontend
cd ../frontend
docker build -t fisio-clinic-frontend .
docker tag fisio-clinic-frontend:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-frontend:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-frontend:latest
```

### 3. ECS Fargate

#### 3.1. Task Definition (Backend)

```json
{
  "family": "fisio-clinic-backend",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "arn:aws:iam::<account-id>:role/ecsTaskExecutionRole",
  "containerDefinitions": [
    {
      "name": "backend",
      "image": "<account-id>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-backend:latest",
      "portMappings": [
        {
          "containerPort": 8080,
          "protocol": "tcp"
        }
      ],
      "environment": [
        {
          "name": "DATABASE_URL",
          "value": "postgres://postgres:SuaSenhaSegura123@fisio-clinic-db.xxxxxxxxx.us-east-1.rds.amazonaws.com:5432/physiotherapy_clinic?sslmode=require"
        },
        {
          "name": "JWT_SECRET",
          "value": "your-super-secret-jwt-key-production-2024"
        },
        {
          "name": "PORT",
          "value": "8080"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/fisio-clinic-backend",
          "awslogs-region": "us-east-1",
          "awslogs-stream-prefix": "ecs"
        }
      }
    }
  ]
}
```

#### 3.2. Task Definition (Frontend)

```json
{
  "family": "fisio-clinic-frontend",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "arn:aws:iam::<account-id>:role/ecsTaskExecutionRole",
  "containerDefinitions": [
    {
      "name": "frontend",
      "image": "<account-id>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-frontend:latest",
      "portMappings": [
        {
          "containerPort": 3000,
          "protocol": "tcp"
        }
      ],
      "environment": [
        {
          "name": "VITE_API_URL",
          "value": "https://api.seudominio.com/api"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/fisio-clinic-frontend",
          "awslogs-region": "us-east-1",
          "awslogs-stream-prefix": "ecs"
        }
      }
    }
  ]
}
```

### 4. Application Load Balancer

```bash
# Crie um Application Load Balancer
aws elbv2 create-load-balancer \
    --name fisio-clinic-alb \
    --subnets subnet-xxxxxxxxx subnet-yyyyyyyyy \
    --security-groups sg-xxxxxxxxx

# Crie target groups
aws elbv2 create-target-group \
    --name fisio-clinic-backend-tg \
    --protocol HTTP \
    --port 8080 \
    --vpc-id vpc-xxxxxxxxx \
    --target-type ip \
    --health-check-path /health

aws elbv2 create-target-group \
    --name fisio-clinic-frontend-tg \
    --protocol HTTP \
    --port 3000 \
    --vpc-id vpc-xxxxxxxxx \
    --target-type ip
```

## Deployment no Azure

### 1. Arquitetura Recomendada

```
Azure Front Door
    |
Azure Container Instances
    |
    ├── Backend Container
    ├── Frontend Container
    └── Azure Database for PostgreSQL
```

### 2. Preparação da Infraestrutura

#### 2.1. Resource Group

```bash
# Crie um resource group
az group create --name fisio-clinic-rg --location eastus
```

#### 2.2. Azure Database for PostgreSQL

```bash
# Crie o servidor PostgreSQL
az postgres server create \
    --resource-group fisio-clinic-rg \
    --name fisio-clinic-db \
    --location eastus \
    --admin-user postgres \
    --admin-password SuaSenhaSegura123 \
    --sku-name B_Gen5_1 \
    --version 14

# Crie o banco de dados
az postgres db create \
    --resource-group fisio-clinic-rg \
    --server-name fisio-clinic-db \
    --name physiotherapy_clinic
```

#### 2.3. Azure Container Registry

```bash
# Crie o registry
az acr create \
    --resource-group fisio-clinic-rg \
    --name fisioclinicacr \
    --sku Basic

# Faça login no registry
az acr login --name fisioclinicacr
```

#### 2.4. Build e Push das Imagens

```bash
# Backend
cd backend
az acr build --registry fisioclinicacr --image fisio-clinic-backend:latest .

# Frontend
cd ../frontend
az acr build --registry fisioclinicacr --image fisio-clinic-frontend:latest .
```

### 3. Container Instances

#### 3.1. Backend Container

```bash
az container create \
    --resource-group fisio-clinic-rg \
    --name fisio-clinic-backend \
    --image fisioclinicacr.azurecr.io/fisio-clinic-backend:latest \
    --registry-login-server fisioclinicacr.azurecr.io \
    --registry-username <username> \
    --registry-password <password> \
    --dns-name-label fisio-clinic-api \
    --ports 8080 \
    --environment-variables \
        DATABASE_URL='postgres://postgres:SuaSenhaSegura123@fisio-clinic-db.postgres.database.azure.com:5432/physiotherapy_clinic?sslmode=require' \
        JWT_SECRET='your-super-secret-jwt-key-production-2024' \
        PORT='8080'
```

#### 3.2. Frontend Container

```bash
az container create \
    --resource-group fisio-clinic-rg \
    --name fisio-clinic-frontend \
    --image fisioclinicacr.azurecr.io/fisio-clinic-frontend:latest \
    --registry-login-server fisioclinicacr.azurecr.io \
    --registry-username <username> \
    --registry-password <password> \
    --dns-name-label fisio-clinic-app \
    --ports 3000 \
    --environment-variables \
        VITE_API_URL='https://fisio-clinic-api.eastus.azurecontainer.io/api'
```

## Configurações de Produção

### 1. Segurança

#### 1.1. Variáveis de Ambiente Seguras

```bash
# Use serviços de gerenciamento de secrets
# AWS: AWS Secrets Manager ou Parameter Store
# Azure: Azure Key Vault

# Exemplo AWS Secrets Manager
aws secretsmanager create-secret \
    --name fisio-clinic/database \
    --description "Database credentials" \
    --secret-string '{"username":"postgres","password":"SuaSenhaSegura123"}'
```

#### 1.2. SSL/TLS

```bash
# Configure certificados SSL
# AWS: Use ACM (AWS Certificate Manager)
# Azure: Use Azure Key Vault Certificates

# Exemplo ACM
aws acm request-certificate \
    --domain-name api.seudominio.com \
    --validation-method DNS
```

### 2. Monitoramento

#### 2.1. Logs

```bash
# AWS CloudWatch
aws logs create-log-group --log-group-name /ecs/fisio-clinic

# Azure Monitor
az monitor log-analytics workspace create \
    --resource-group fisio-clinic-rg \
    --workspace-name fisio-clinic-logs
```

#### 2.2. Métricas

```bash
# Configure alertas para:
# - CPU > 80%
# - Memory > 80%
# - Disk > 90%
# - Response time > 2s
# - Error rate > 5%
```

### 3. Backup

#### 3.1. Banco de Dados

```bash
# AWS RDS - Backup automático habilitado por padrão
# Azure Database - Configure backup retention

az postgres server configuration set \
    --resource-group fisio-clinic-rg \
    --server-name fisio-clinic-db \
    --name backup_retention_days \
    --value 30
```

#### 3.2. Arquivos

```bash
# Configure backup dos uploads
# AWS: S3 com versionamento
# Azure: Blob Storage com soft delete
```

## Troubleshooting

### 1. Problemas Comuns

#### 1.1. Conexão com Banco de Dados

```bash
# Verifique a conectividade
telnet <db-host> 5432

# Teste a conexão PostgreSQL
psql -h <db-host> -U postgres -d physiotherapy_clinic

# Verifique as configurações de firewall
# AWS: Security Groups
# Azure: Network Security Groups
```

#### 1.2. Problemas de CORS

```bash
# Verifique as configurações de CORS no backend
# Certifique-se de que o frontend está na lista de origens permitidas
```

#### 1.3. Problemas de SSL

```bash
# Verifique o certificado
openssl s_client -connect api.seudominio.com:443

# Verifique a configuração do Load Balancer
```

### 2. Logs e Debugging

```bash
# Docker
docker-compose logs -f backend
docker-compose logs -f frontend

# AWS ECS
aws logs get-log-events --log-group-name /ecs/fisio-clinic-backend --log-stream-name <stream-name>

# Azure
az container logs --resource-group fisio-clinic-rg --name fisio-clinic-backend
```

## Manutenção

### 1. Atualizações

```bash
# 1. Teste em ambiente de desenvolvimento
# 2. Build nova imagem
# 3. Deploy em staging
# 4. Testes de aceitação
# 5. Deploy em produção
# 6. Monitoramento pós-deploy
```

### 2. Scaling

```bash
# AWS ECS - Auto Scaling
aws application-autoscaling register-scalable-target \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/fisio-clinic-cluster/fisio-clinic-backend \
    --min-capacity 1 \
    --max-capacity 10

# Azure Container Instances - Manual scaling
az container create --cpu 2 --memory 4
```

### 3. Disaster Recovery

```bash
# 1. Backup regular dos dados
# 2. Documentação de procedimentos
# 3. Testes de recuperação
# 4. RTO/RPO definidos
# 5. Plano de comunicação
```

## Checklist de Deploy

### Pré-Deploy
- [ ] Código testado e revisado
- [ ] Variáveis de ambiente configuradas
- [ ] Certificados SSL válidos
- [ ] Backup do banco de dados atual
- [ ] Plano de rollback definido

### Deploy
- [ ] Build das imagens
- [ ] Push para registry
- [ ] Deploy em staging
- [ ] Testes de smoke
- [ ] Deploy em produção
- [ ] Verificação de health checks

### Pós-Deploy
- [ ] Monitoramento de métricas
- [ ] Verificação de logs
- [ ] Testes funcionais
- [ ] Comunicação para stakeholders
- [ ] Documentação atualizada

## Contatos e Suporte

Para suporte técnico ou dúvidas sobre o deployment:

- **Documentação**: Consulte este guia e a documentação da API
- **Logs**: Sempre verifique os logs primeiro
- **Monitoramento**: Use as ferramentas de monitoramento configuradas
- **Backup**: Mantenha backups atualizados antes de qualquer mudança

