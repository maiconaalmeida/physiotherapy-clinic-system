# Documentação da API - FisioClinic

## Visão Geral

A API do FisioClinic é uma API RESTful desenvolvida em Go que fornece endpoints para gerenciar clínicas de fisioterapia. A API suporta multitenancy, autenticação JWT e oferece funcionalidades completas para gestão de pacientes, profissionais, agendamentos, prontuários médicos e controle financeiro.

### URL Base
```
http://localhost:8080/api
```

### Autenticação

A API utiliza autenticação JWT (JSON Web Token). Para acessar endpoints protegidos, inclua o token no header Authorization:

```
Authorization: Bearer <seu_token_jwt>
```

## Endpoints de Autenticação

### POST /login
Realiza login no sistema e retorna um token JWT.

**Request Body:**
```json
{
  "email": "admin@fisiovida.com.br",
  "password": "admin123"
}
```

**Response (200):**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "uuid",
    "name": "Administrador",
    "email": "admin@fisiovida.com.br",
    "role": "admin",
    "clinic_id": "uuid"
  }
}
```

### GET /profile
Retorna informações do usuário autenticado.

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "id": "uuid",
  "name": "Administrador",
  "email": "admin@fisiovida.com.br",
  "role": "admin",
  "clinic_id": "uuid",
  "created_at": "2024-01-01T00:00:00Z"
}
```

## Endpoints de Usuários

### POST /users
Cria um novo usuário (apenas administradores).

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "name": "Dr. João Silva",
  "email": "joao@fisiovida.com.br",
  "password": "senha123",
  "role": "fisioterapeuta",
  "phone": "(11) 99999-9999",
  "specialization": "Ortopedia"
}
```

**Response (201):**
```json
{
  "id": "uuid",
  "name": "Dr. João Silva",
  "email": "joao@fisiovida.com.br",
  "role": "fisioterapeuta",
  "phone": "(11) 99999-9999",
  "specialization": "Ortopedia",
  "clinic_id": "uuid",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### GET /users
Lista todos os usuários da clínica (apenas administradores).

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `limit` (opcional): Número máximo de resultados (padrão: 20)
- `offset` (opcional): Número de registros para pular (padrão: 0)

**Response (200):**
```json
{
  "users": [
    {
      "id": "uuid",
      "name": "Dr. João Silva",
      "email": "joao@fisiovida.com.br",
      "role": "fisioterapeuta",
      "phone": "(11) 99999-9999",
      "specialization": "Ortopedia",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ],
  "total": 1,
  "limit": 20,
  "offset": 0
}
```

### GET /users/:id
Busca um usuário específico por ID.

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "id": "uuid",
  "name": "Dr. João Silva",
  "email": "joao@fisiovida.com.br",
  "role": "fisioterapeuta",
  "phone": "(11) 99999-9999",
  "specialization": "Ortopedia",
  "clinic_id": "uuid",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### PUT /users/:id
Atualiza um usuário existente.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "name": "Dr. João Silva Santos",
  "phone": "(11) 88888-8888",
  "specialization": "Ortopedia e Traumatologia"
}
```

### DELETE /users/:id
Remove um usuário (apenas administradores).

**Headers:**
```
Authorization: Bearer <token>
```

**Response (200):**
```json
{
  "message": "Usuário deletado com sucesso"
}
```

## Endpoints de Pacientes

### POST /patients
Cria um novo paciente.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "name": "Maria Silva",
  "email": "maria@email.com",
  "phone": "(11) 99999-9999",
  "cpf": "123.456.789-00",
  "rg": "12.345.678-9",
  "birth_date": "1990-01-01",
  "gender": "feminino",
  "address": "Rua das Flores, 123",
  "city": "São Paulo",
  "state": "SP",
  "zip_code": "01234-567",
  "emergency_contact": "João Silva - (11) 88888-8888",
  "health_insurance": "Unimed",
  "insurance_number": "123456789",
  "medical_history": "Histórico de lesões no joelho"
}
```

**Response (201):**
```json
{
  "id": "uuid",
  "name": "Maria Silva",
  "email": "maria@email.com",
  "phone": "(11) 99999-9999",
  "cpf": "123.456.789-00",
  "rg": "12.345.678-9",
  "birth_date": "1990-01-01",
  "gender": "feminino",
  "address": "Rua das Flores, 123",
  "city": "São Paulo",
  "state": "SP",
  "zip_code": "01234-567",
  "emergency_contact": "João Silva - (11) 88888-8888",
  "health_insurance": "Unimed",
  "insurance_number": "123456789",
  "medical_history": "Histórico de lesões no joelho",
  "clinic_id": "uuid",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### GET /patients
Lista todos os pacientes da clínica.

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `search` (opcional): Busca por nome, email ou CPF
- `limit` (opcional): Número máximo de resultados (padrão: 20)
- `offset` (opcional): Número de registros para pular (padrão: 0)

### GET /patients/:id
Busca um paciente específico por ID.

### PUT /patients/:id
Atualiza um paciente existente.

### DELETE /patients/:id
Remove um paciente.

## Endpoints de Agendamentos

### POST /appointments
Cria um novo agendamento.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "patient_id": "uuid",
  "therapist_id": "uuid",
  "appointment_date": "2024-01-15",
  "start_time": "14:00",
  "end_time": "15:00",
  "service_type": "Fisioterapia Ortopédica",
  "notes": "Primeira consulta - avaliação inicial"
}
```

**Response (201):**
```json
{
  "id": "uuid",
  "patient_id": "uuid",
  "patient_name": "Maria Silva",
  "therapist_id": "uuid",
  "therapist_name": "Dr. João Silva",
  "appointment_date": "2024-01-15",
  "start_time": "14:00",
  "end_time": "15:00",
  "service_type": "Fisioterapia Ortopédica",
  "status": "agendado",
  "notes": "Primeira consulta - avaliação inicial",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### GET /appointments
Lista agendamentos da clínica.

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `start_date` (opcional): Data inicial (YYYY-MM-DD)
- `end_date` (opcional): Data final (YYYY-MM-DD)
- `therapist_id` (opcional): Filtrar por fisioterapeuta
- `patient_id` (opcional): Filtrar por paciente
- `status` (opcional): Filtrar por status
- `limit` (opcional): Número máximo de resultados (padrão: 20)
- `offset` (opcional): Número de registros para pular (padrão: 0)

### PATCH /appointments/:id/status
Atualiza o status de um agendamento.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "status": "confirmado"
}
```

**Status válidos:**
- `agendado`
- `confirmado`
- `em_andamento`
- `concluido`
- `cancelado`
- `faltou`

## Endpoints de Prontuários Médicos

### POST /medical-records
Cria um novo prontuário médico.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "patient_id": "uuid",
  "appointment_id": "uuid",
  "diagnosis": "Tendinite no ombro direito",
  "treatment_plan": "Exercícios de fortalecimento e alongamento",
  "observations": "Paciente apresenta dor moderada",
  "next_appointment": "2024-01-22"
}
```

### GET /medical-records/patient/:patient_id
Lista prontuários de um paciente específico.

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `limit` (opcional): Número máximo de resultados (padrão: 20)
- `offset` (opcional): Número de registros para pular (padrão: 0)

## Endpoints Financeiros

### POST /financial/transactions
Cria uma nova transação financeira.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "patient_id": "uuid",
  "appointment_id": "uuid",
  "type": "receita",
  "description": "Consulta de Fisioterapia",
  "amount": 150.00,
  "due_date": "2024-01-15",
  "payment_method": "dinheiro"
}
```

**Response (201):**
```json
{
  "id": "uuid",
  "patient_id": "uuid",
  "patient_name": "Maria Silva",
  "appointment_id": "uuid",
  "type": "receita",
  "description": "Consulta de Fisioterapia",
  "amount": 150.00,
  "status": "pendente",
  "due_date": "2024-01-15",
  "payment_method": "dinheiro",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### GET /financial/transactions
Lista transações financeiras.

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `type` (opcional): Filtrar por tipo (receita/despesa)
- `status` (opcional): Filtrar por status (pendente/pago/vencido/cancelado)
- `start_date` (opcional): Data inicial (YYYY-MM-DD)
- `end_date` (opcional): Data final (YYYY-MM-DD)
- `limit` (opcional): Número máximo de resultados (padrão: 20)
- `offset` (opcional): Número de registros para pular (padrão: 0)

### GET /financial/summary
Retorna resumo financeiro.

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `start_date` (opcional): Data inicial (YYYY-MM-DD)
- `end_date` (opcional): Data final (YYYY-MM-DD)

**Response (200):**
```json
{
  "total_receitas": 5000.00,
  "total_despesas": 2000.00,
  "receitas_pendentes": 1500.00,
  "despesas_pendentes": 500.00,
  "saldo": 3000.00,
  "periodo": {
    "inicio": "2024-01-01",
    "fim": "2024-01-31"
  }
}
```

### PATCH /financial/transactions/:id/payment
Registra pagamento de uma transação.

**Headers:**
```
Authorization: Bearer <token>
```

**Request Body:**
```json
{
  "payment_date": "2024-01-15T14:30:00Z",
  "payment_method": "cartao_credito"
}
```

## Endpoints de Arquivos

### POST /files/upload
Faz upload de um arquivo.

**Headers:**
```
Authorization: Bearer <token>
Content-Type: multipart/form-data
```

**Form Data:**
- `file`: Arquivo (PDF, JPG, JPEG, PNG, DOC, DOCX)
- `patient_id` (opcional): ID do paciente
- `medical_record_id` (opcional): ID do prontuário
- `description` (opcional): Descrição do arquivo

**Response (201):**
```json
{
  "id": "uuid",
  "file_name": "exame_raio_x.pdf",
  "file_path": "uploads/clinic_id/uuid.pdf",
  "file_type": "pdf",
  "file_size": 1024000,
  "description": "Raio-X do joelho direito",
  "patient_id": "uuid",
  "medical_record_id": "uuid",
  "uploaded_by": "uuid",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### GET /files
Lista arquivos da clínica.

**Headers:**
```
Authorization: Bearer <token>
```

**Query Parameters:**
- `patient_id` (opcional): Filtrar por paciente
- `medical_record_id` (opcional): Filtrar por prontuário

### GET /files/:id/download
Faz download de um arquivo.

**Headers:**
```
Authorization: Bearer <token>
```

**Response:** Arquivo binário com headers apropriados.

### DELETE /files/:id
Remove um arquivo.

**Headers:**
```
Authorization: Bearer <token>
```

## Códigos de Status HTTP

- `200 OK`: Requisição bem-sucedida
- `201 Created`: Recurso criado com sucesso
- `400 Bad Request`: Dados inválidos na requisição
- `401 Unauthorized`: Token inválido ou ausente
- `403 Forbidden`: Sem permissão para acessar o recurso
- `404 Not Found`: Recurso não encontrado
- `500 Internal Server Error`: Erro interno do servidor

## Tratamento de Erros

Todas as respostas de erro seguem o formato:

```json
{
  "error": "Descrição do erro"
}
```

## Multitenancy

O sistema suporta múltiplas clínicas. Cada usuário pertence a uma clínica específica e só pode acessar dados da sua própria clínica. O `clinic_id` é automaticamente extraído do token JWT e aplicado em todas as operações.

## Limitações e Considerações

- Tamanho máximo de arquivo para upload: 10MB
- Tipos de arquivo suportados: PDF, JPG, JPEG, PNG, DOC, DOCX
- Tokens JWT têm validade de 24 horas
- Todas as datas devem estar no formato ISO 8601
- Valores monetários são representados como números decimais (ex: 150.00)

## Exemplos de Uso

### Fluxo Completo de Agendamento

1. **Login:**
```bash
curl -X POST http://localhost:8080/api/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@fisiovida.com.br","password":"admin123"}'
```

2. **Criar Paciente:**
```bash
curl -X POST http://localhost:8080/api/patients \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"name":"Maria Silva","email":"maria@email.com","phone":"(11)99999-9999"}'
```

3. **Criar Agendamento:**
```bash
curl -X POST http://localhost:8080/api/appointments \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"patient_id":"uuid","therapist_id":"uuid","appointment_date":"2024-01-15","start_time":"14:00","end_time":"15:00"}'
```

4. **Criar Transação Financeira:**
```bash
curl -X POST http://localhost:8080/api/financial/transactions \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"patient_id":"uuid","type":"receita","description":"Consulta","amount":150.00}'
```

