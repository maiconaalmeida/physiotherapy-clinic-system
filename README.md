# FisioClinic - Sistema de Gestão de Clínicas de Fisioterapia

## 📋 Visão Geral

O FisioClinic é um sistema completo de gestão para clínicas de fisioterapia, desenvolvido com tecnologias modernas e arquitetura robusta. O sistema oferece funcionalidades abrangentes para gerenciamento de pacientes, profissionais, agendamentos, prontuários eletrônicos e controle financeiro, com suporte completo a multitenancy.

### 🎯 Características Principais

- **Multitenancy**: Suporte para múltiplas clínicas em uma única instalação
- **Interface Moderna**: Design responsivo inspirado no HITS da App Sistemas
- **Segurança Robusta**: Autenticação JWT e controle de permissões por perfil
- **Gestão Completa**: Agenda, pacientes, profissionais, prontuários e financeiro
- **Upload de Arquivos**: Suporte para PDFs, imagens e documentos médicos
- **APIs RESTful**: Backend bem estruturado com documentação completa
- **Containerização**: Pronto para deploy com Docker e orquestração

## 🏗️ Arquitetura Técnica

### Stack Tecnológico

**Frontend:**
- React 18 com Vite
- Tailwind CSS para estilização
- shadcn/ui para componentes
- React Router para navegação
- Lucide React para ícones

**Backend:**
- Go 1.21+ com Gin Framework
- PostgreSQL 14+ como banco de dados
- JWT para autenticação
- CORS configurado para integração
- Middleware de segurança

**Infraestrutura:**
- Docker e Docker Compose
- Nginx para servir o frontend
- Suporte para AWS e Azure
- Scripts de automação

### Estrutura do Projeto

```
physiotherapy-clinic-system/
├── frontend/                 # Aplicação React
│   ├── src/
│   │   ├── components/      # Componentes reutilizáveis
│   │   ├── pages/          # Páginas da aplicação
│   │   ├── hooks/          # Hooks customizados
│   │   ├── lib/            # Utilitários e API client
│   │   └── assets/         # Recursos estáticos
│   ├── Dockerfile
│   └── nginx.conf
├── backend/                 # API em Go
│   ├── cmd/server/         # Ponto de entrada
│   ├── internal/
│   │   ├── handlers/       # Controllers da API
│   │   ├── models/         # Modelos de dados
│   │   ├── repository/     # Camada de dados
│   │   └── middleware/     # Middlewares
│   ├── pkg/                # Pacotes compartilhados
│   └── Dockerfile
├── database/               # Scripts do banco
│   ├── init.sql           # Schema e dados iniciais
│   └── Dockerfile
├── docker-compose.yml      # Orquestração dos serviços
├── API_DOCUMENTATION.md    # Documentação da API
├── DEPLOYMENT_GUIDE.md     # Guia de deployment
└── README.md              # Este arquivo
```

## 🚀 Início Rápido

### Pré-requisitos

- Docker 20.10+
- Docker Compose 2.0+
- Git

### Instalação com Docker (Recomendado)

1. **Clone o repositório:**
```bash
git clone <repository-url>
cd physiotherapy-clinic-system
```

2. **Configure as variáveis de ambiente:**
```bash
cp .env.example .env
# Edite o arquivo .env conforme necessário
```

3. **Execute o sistema:**
```bash
docker-compose up -d
```

4. **Acesse o sistema:**
- Frontend: http://localhost:3000
- Backend API: http://localhost:8080/api
- Health Check: http://localhost:8080/health

### Instalação Local (Desenvolvimento)

#### 1. Banco de Dados PostgreSQL

```bash
# Instale o PostgreSQL
sudo apt update
sudo apt install postgresql postgresql-contrib

# Inicie o serviço
sudo systemctl start postgresql

# Crie o banco de dados
sudo -u postgres createdb physiotherapy_clinic

# Configure a senha do usuário postgres
sudo -u postgres psql -c "ALTER USER postgres PASSWORD 'postgres123';"

# Execute o script de inicialização
sudo -u postgres psql -d physiotherapy_clinic -f database/init.sql
```

#### 2. Backend (Go)

```bash
# Navegue para o diretório do backend
cd backend

# Instale o Go (se necessário)
wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin

# Configure as variáveis de ambiente
cp .env.example .env
# Edite o arquivo .env com suas configurações

# Instale as dependências
go mod tidy

# Execute o backend
go run cmd/server/main.go
```

#### 3. Frontend (React)

```bash
# Em um novo terminal, navegue para o frontend
cd frontend

# Instale as dependências
npm install

# Configure as variáveis de ambiente
cp .env.example .env
# Edite o arquivo .env

# Execute o frontend
npm run dev
```

## 🔐 Credenciais de Teste

O sistema vem com usuários de teste pré-configurados:

**Clínica FisioVida:**
- Email: admin@fisiovida.com.br
- Senha: admin123
- Perfil: Administrador

**Clínica Bem Estar:**
- Email: admin@bemestar.com.br
- Senha: admin123
- Perfil: Administrador

## 📱 Funcionalidades

### 🏥 Gestão de Clínicas (Multitenancy)
- Isolamento completo de dados entre clínicas
- Configurações personalizadas por clínica
- Suporte para múltiplas clínicas na mesma instalação

### 👥 Cadastro de Profissionais
- Perfis de acesso (Administrador, Fisioterapeuta, Recepcionista)
- Controle granular de permissões
- Especialização e informações profissionais
- Gestão de usuários por clínica

### 🧑‍⚕️ Cadastro de Pacientes
- Dados pessoais completos (CPF, RG, contatos)
- Endereço e informações de emergência
- Convênios médicos e números de carteira
- Histórico médico e observações
- Vinculação de arquivos e documentos

### 📅 Sistema de Agenda
- Visualização diária, semanal e mensal
- Marcação e gerenciamento de consultas
- Status de agendamentos (agendado, confirmado, em andamento, concluído, cancelado, faltou)
- Filtros por profissional, paciente e período
- Controle de horários e disponibilidade

### 📋 Prontuário Eletrônico
- Registro detalhado de atendimentos
- Diagnósticos e planos de tratamento
- Evolução clínica e observações
- Anexação de arquivos (exames, imagens, PDFs)
- Histórico completo por paciente
- Vinculação com agendamentos

### 💰 Gestão Financeira
- Controle de receitas e despesas
- Emissão de recibos de atendimento
- Gestão de contas a receber e a pagar
- Status de pagamentos (pendente, pago, vencido, cancelado)
- Relatórios financeiros e resumos
- Múltiplas formas de pagamento

### 📁 Gestão de Arquivos
- Upload de documentos (PDF, imagens, Word)
- Organização por paciente e prontuário
- Visualização e download seguro
- Controle de acesso por permissões
- Armazenamento local ou em nuvem

### 📊 Dashboard e Relatórios
- KPIs financeiros e de atendimentos
- Gráficos e métricas em tempo real
- Resumos por período
- Indicadores de performance
- Ações rápidas para funcionalidades principais

## 🔧 Configuração Avançada

### Variáveis de Ambiente

#### Backend (.env)
```env
DATABASE_URL=postgres://postgres:postgres123@localhost:5432/physiotherapy_clinic?sslmode=disable
JWT_SECRET=your-super-secret-jwt-key-change-in-production
PORT=8080
```

#### Frontend (.env)
```env
VITE_API_URL=http://localhost:8080/api
```

#### Docker Compose (.env)
```env
POSTGRES_DB=physiotherapy_clinic
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123
JWT_SECRET=your-super-secret-jwt-key-change-in-production-2024
VITE_API_URL=http://localhost:8080/api
```

### Configuração de Produção

Para ambientes de produção, considere:

1. **Segurança:**
   - Use senhas fortes e únicas
   - Configure SSL/TLS
   - Implemente rate limiting
   - Use secrets managers (AWS Secrets Manager, Azure Key Vault)

2. **Performance:**
   - Configure connection pooling no banco
   - Implemente cache (Redis)
   - Use CDN para assets estáticos
   - Configure load balancing

3. **Monitoramento:**
   - Logs estruturados
   - Métricas de aplicação
   - Health checks
   - Alertas automatizados

## 📚 Documentação

- **[API Documentation](API_DOCUMENTATION.md)**: Documentação completa das APIs REST
- **[Deployment Guide](DEPLOYMENT_GUIDE.md)**: Guia detalhado para deploy em diferentes ambientes
- **[Database Schema](database/multitenancy-schema.md)**: Documentação do esquema do banco de dados

## 🧪 Testes

### Executar Testes do Backend
```bash
cd backend
go test ./...
```

### Executar Testes do Frontend
```bash
cd frontend
npm test
```

### Testes de Integração
```bash
# Execute o sistema completo
docker-compose up -d

# Execute os testes de integração
npm run test:integration
```

## 🚀 Deploy em Produção

### AWS (ECS + RDS)
```bash
# Consulte o DEPLOYMENT_GUIDE.md para instruções detalhadas
# Exemplo básico:
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account>.dkr.ecr.us-east-1.amazonaws.com
docker build -t fisio-clinic-backend backend/
docker tag fisio-clinic-backend:latest <account>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-backend:latest
docker push <account>.dkr.ecr.us-east-1.amazonaws.com/fisio-clinic-backend:latest
```

### Azure (Container Instances + PostgreSQL)
```bash
# Consulte o DEPLOYMENT_GUIDE.md para instruções detalhadas
az acr build --registry fisioclinicacr --image fisio-clinic-backend:latest backend/
az container create --resource-group fisio-clinic-rg --name backend --image fisioclinicacr.azurecr.io/fisio-clinic-backend:latest
```

## 🛠️ Desenvolvimento

### Estrutura de Desenvolvimento

1. **Backend (Go):**
   - Arquitetura em camadas (handlers, services, repositories)
   - Middleware para autenticação e CORS
   - Validação de dados e tratamento de erros
   - Testes unitários e de integração

2. **Frontend (React):**
   - Componentes funcionais com hooks
   - Context API para gerenciamento de estado
   - Roteamento com React Router
   - Design system com Tailwind CSS

3. **Banco de Dados:**
   - Schema normalizado com relacionamentos
   - Índices para performance
   - Triggers para auditoria
   - Suporte a multitenancy

### Contribuindo

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

### Padrões de Código

- **Go**: Siga as convenções do `gofmt` e `golint`
- **React**: Use ESLint e Prettier
- **SQL**: Use snake_case para nomes de tabelas e colunas
- **Git**: Commits semânticos (feat, fix, docs, style, refactor, test, chore)

## 🔍 Troubleshooting

### Problemas Comuns

1. **Erro de conexão com banco:**
   ```bash
   # Verifique se o PostgreSQL está rodando
   sudo systemctl status postgresql
   
   # Teste a conexão
   psql -h localhost -U postgres -d physiotherapy_clinic
   ```

2. **Erro de CORS:**
   ```bash
   # Verifique as configurações de CORS no backend
   # Certifique-se de que o frontend está na lista de origens permitidas
   ```

3. **Problemas com Docker:**
   ```bash
   # Limpe containers e volumes
   docker-compose down -v
   docker system prune -a
   
   # Rebuild completo
   docker-compose build --no-cache
   docker-compose up -d
   ```

### Logs e Debugging

```bash
# Logs do Docker Compose
docker-compose logs -f

# Logs específicos
docker-compose logs -f backend
docker-compose logs -f frontend
docker-compose logs -f db

# Acesso aos containers
docker-compose exec backend sh
docker-compose exec db psql -U postgres -d physiotherapy_clinic
```

## 📄 Licença

Este projeto está licenciado sob a MIT License - veja o arquivo [LICENSE](LICENSE) para detalhes.

## 👥 Equipe

- **Desenvolvimento**: Manus AI
- **Arquitetura**: Sistema modular e escalável
- **Design**: Inspirado no HITS da App Sistemas
- **Infraestrutura**: Docker e Cloud-ready

## 📞 Suporte

Para suporte técnico ou dúvidas:

1. Consulte a documentação completa
2. Verifique os logs do sistema
3. Consulte o guia de troubleshooting
4. Abra uma issue no repositório

## 🔄 Roadmap

### Versão 2.0 (Planejada)
- [ ] Módulo de relatórios avançados
- [ ] Integração com sistemas de pagamento
- [ ] App mobile (React Native)
- [ ] Notificações push
- [ ] Integração com WhatsApp
- [ ] Telemedicina básica
- [ ] BI e analytics avançados

### Melhorias Contínuas
- [ ] Testes automatizados completos
- [ ] CI/CD pipeline
- [ ] Monitoramento avançado
- [ ] Performance optimization
- [ ] Acessibilidade (WCAG 2.1)
- [ ] Internacionalização (i18n)

---

**FisioClinic** - Sistema completo para gestão de clínicas de fisioterapia 🏥✨

