# 🚀 Guia de Início Rápido - FisioClinic

## ⚡ Execução Rápida com Docker

### 1. Pré-requisitos
- Docker 20.10+
- Docker Compose 2.0+

### 2. Execução em 3 Passos

```bash
# 1. Extrair o projeto
unzip physiotherapy-clinic-system.zip
cd physiotherapy-clinic-system

# 2. Executar o sistema
docker-compose up -d

# 3. Acessar o sistema
# Frontend: http://localhost:3000
# Backend: http://localhost:8080/api
```

### 3. Credenciais de Teste

**Clínica FisioVida:**
- Email: `admin@fisiovida.com.br`
- Senha: `admin123`

**Clínica Bem Estar:**
- Email: `admin@bemestar.com.br`
- Senha: `admin123`

## 🛠️ Execução Local (Desenvolvimento)

### 1. Banco de Dados
```bash
# Instalar PostgreSQL
sudo apt update && sudo apt install postgresql postgresql-contrib

# Iniciar serviço
sudo systemctl start postgresql

# Criar banco
sudo -u postgres createdb physiotherapy_clinic
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'postgres123';"

# Executar script
sudo -u postgres psql -d physiotherapy_clinic -f database/init.sql
```

### 2. Backend (Go)
```bash
cd backend

# Instalar Go (se necessário)
wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin

# Configurar ambiente
cp .env.example .env

# Executar
go mod tidy
go run cmd/server/main.go
```

### 3. Frontend (React)
```bash
cd frontend

# Instalar dependências
npm install

# Configurar ambiente
cp .env.example .env

# Executar
npm run dev
```

## 🔧 Comandos Úteis

### Docker
```bash
# Ver logs
docker-compose logs -f

# Parar serviços
docker-compose down

# Rebuild
docker-compose build --no-cache
docker-compose up -d

# Limpar tudo
docker-compose down -v
docker system prune -a
```

### Banco de Dados
```bash
# Acessar PostgreSQL
docker-compose exec db psql -U postgres -d physiotherapy_clinic

# Backup
docker-compose exec db pg_dump -U postgres physiotherapy_clinic > backup.sql

# Restore
docker-compose exec -T db psql -U postgres physiotherapy_clinic < backup.sql
```

## 📱 Funcionalidades Principais

- ✅ **Dashboard**: Visão geral da clínica
- ✅ **Pacientes**: Cadastro completo com histórico médico
- ✅ **Profissionais**: Gestão de usuários e permissões
- ✅ **Agenda**: Marcação e controle de consultas
- ✅ **Prontuário**: Registro de atendimentos e arquivos
- ✅ **Financeiro**: Controle de receitas e despesas
- ✅ **Multitenancy**: Suporte a múltiplas clínicas

## 🆘 Problemas Comuns

### Erro de Conexão com Banco
```bash
# Verificar se PostgreSQL está rodando
sudo systemctl status postgresql

# Testar conexão
psql -h localhost -U postgres -d physiotherapy_clinic
```

### Erro de CORS
- Verifique se o backend está rodando na porta 8080
- Confirme se o frontend está configurado para http://localhost:8080/api

### Problemas com Docker
```bash
# Verificar status dos containers
docker-compose ps

# Ver logs específicos
docker-compose logs backend
docker-compose logs frontend
docker-compose logs db
```

## 📚 Documentação Completa

- **README.md**: Documentação completa do projeto
- **API_DOCUMENTATION.md**: Documentação das APIs REST
- **DEPLOYMENT_GUIDE.md**: Guia de deploy para produção

## 🎯 Próximos Passos

1. **Explorar o Sistema**: Faça login e explore todas as funcionalidades
2. **Personalizar**: Ajuste cores, logos e configurações
3. **Deploy**: Use o guia de deployment para produção
4. **Backup**: Configure backups regulares dos dados
5. **Monitoramento**: Implemente logs e métricas

---

**Desenvolvido com ❤️ pela equipe Manus AI**

