# 🏫 Classroom Reservation Platform

> Uma aplicação full-stack profissional para gerenciamento de reservas de salas de aula, construída com **FastAPI + MongoDB** no backend e uma moderna **React + TypeScript + Vite** SPA no frontend.

**Desenvolvido com foco em**: arquitetura limpa, segurança, validação robusta, containerização Docker e pronto para produção.

---

## 📋 Tabela de Conteúdos

- [Objetivos do Projeto](#-objetivos-do-projeto)
- [Funcionalidades](#-funcionalidades)
- [Stack Tecnológico](#-stack-tecnológico)
- [Arquitetura](#-arquitetura)
- [Instalação Local](#-instalação-local)
- [Instalação com Docker](#-instalação-com-docker)
- [Uso da API](#-uso-da-api)
- [Estrutura do Projeto](#-estrutura-do-projeto)
- [Créditos](#-créditos)

---

## 🎯 Objetivos do Projeto

Este projeto implementa uma plataforma completa de reservas de salas com os seguintes objetivos:

1. **✅ Gestão de Dados em Base de Dados**
   - CRUD completo de utilizadores, salas e reservas
   - MongoDB como base de dados NoSQL
   - Validação de dados com Pydantic
   - Indexes únicos para evitar conflitos de horários

2. **✅ Autenticação e Autorização (JWT/OAuth2)**
   - Registro e login com JWT tokens
   - Armazenamento seguro de passwords (Argon2)
   - Controlo de acesso baseado em papéis (Admin vs Utilizador)
   - Tokens armazenados em HTTP-only cookies

3. **✅ Modularidade e Containerização (Docker)**
   - Containerização separada de backend e frontend
   - Orchestração com docker-compose
   - Nginx como reverse proxy
   - Ambientes de desenvolvimento e produção

4. **✅ Documentação da API (Swagger/OpenAPI)**
   - Documentação interativa em `/docs`
   - Schema OpenAPI em `/openapi.json`
   - ReDoc em `/redoc`
   - Endpoints totalmente documentados

---

## ✨ Funcionalidades

### 👥 Gestão de Utilizadores
- Registro e login com validação
- Perfis de utilizador (Admin / Utilizador Regular)
- Autenticação via JWT
- Sessões seguras com HTTP-only cookies

### 🏛️ Gestão de Salas
- CRUD completo de salas de aula
- Capacidade configurável (1-150 lugares)
- Metadata e descrição
- Visualização de disponibilidade em tempo real
- Listagem com paginação

### 📅 Gestão de Reservas
- Criar, editar e cancelar reservas
- Validação de conflitos de horário
- Restrições de tempo (início < fim)
- Diferentes permissões para admins e utilizadores
- Listagem por sala com paginação
- Cálculo automático de disponibilidade (`isFree`)

### 🔐 Segurança
- Autenticação JWT com HS512
- Validação de CORS
- Headers de segurança OWASP
- Rate limiting
- Proxy validation em produção

### 🎨 Interface do Utilizador
- Dark mode por omissão
- Design responsivo com Tailwind CSS 4
- Componentes UI com shadcn
- Feedback com toast notifications
- Estados de loading e erro
- Validação de formulários em tempo real

---

## 🛠️ Stack Tecnológico

### Backend

| Tecnologia | Versão | Propósito |
|-----------|---------|----------|
| **FastAPI** | 0.136.1+ | API REST com async/await |
| **Motor** | 3.7.1+ | Driver assíncrono para MongoDB |
| **MongoDB** | Latest | Base de dados NoSQL |
| **Pydantic** | Built-in | Validação de dados |
| **PyJWT** | 2.12.1+ | Geração e validação de tokens JWT |
| **pwdlib** | 0.3.0+ | Hash seguro de passwords (Argon2) |
| **uv** | Latest | Gestor de dependências Python |
| **Python** | 3.14+ | Linguagem de programação |

### Frontend

| Tecnologia | Versão | Propósito |
|-----------|---------|----------|
| **React** | 19.2.4+ | Framework UI |
| **TypeScript** | Latest | Type safety |
| **Vite** | Latest | Build tool e dev server |
| **React Router** | 7.14.2+ | Routing (públicas e privadas) |
| **TanStack Query** | 5.100.6+ | Caching e sincronização de dados |
| **React Hook Form** | 7.74.0+ | Gerenciamento de formulários |
| **Zod** | 4.3.6+ | Validação em cliente |
| **Zustand** | 5.0.12+ | State management global |
| **Tailwind CSS** | 4.2.4+ | Styling com utilitários |
| **shadcn/ui** | Latest | Componentes UI reutilizáveis |
| **React Toastify** | 11.1.0+ | Notificações |

### DevOps & Infraestrutura

| Ferramenta | Propósito |
|-----------|----------|
| **Docker** | Containerização |
| **Docker Compose** | Orchestração de containers |
| **Nginx** | Reverse proxy e servir SPA |
| **OWASP Headers** | Segurança HTTP |

---

## 🏗️ Arquitetura

### Diagrama de Entidades

```
┌─────────────┐
│   Users     │
├─────────────┤
│ _id (PK)    │
│ email       │
│ password    │
│ name        │
│ role        │ (admin | user)
└─────────────┘
      │
      │ (1:N)
      ├──────────────┬──────────────┐
      │              │              │
      ▼              ▼              ▼
┌─────────────┐ ┌─────────────┐
│Reservations │ │    Rooms    │
├─────────────┤ ├─────────────┤
│ _id (PK)    │ │ _id (PK)    │
│ user_id (FK)│ │ capacity    │
│ room_id (FK)│ │ name        │
│ start_time  │ │ description │
│ end_time    │ │ metadata    │
│ created_at  │ └─────────────┘
└─────────────┘
```

### Estrutura da Aplicação

```
WebServices/
├── backend/
│   ├── main.py                 # Entrada da API FastAPI
│   ├── database.py             # Conexão MongoDB
│   ├── pyproject.toml          # Dependências Python
│   ├── Dockerfile              # Containerização backend
│   ├── start.sh                # Script de início
│   │
│   ├── controller/             # Middleware e validações
│   │   ├── authMiddleware.py   # Middleware de autenticação
│   │   └── jwtValidation.py    # Validação de JWT
│   │
│   ├── models/                 # Modelos Pydantic
│   │   ├── userModel.py        # Schema de utilizador
│   │   ├── roomModel.py        # Schema de sala
│   │   └── reservaModel.py     # Schema de reserva
│   │
│   └── routes/                 # Endpoints
│       ├── userRoute.py        # Auth e perfil
│       ├── roomRoute.py        # CRUD de salas
│       └── reservaRoute.py     # CRUD de reservas
│
├── frontend/
│   ├── index.html              # HTML raiz
│   ├── vite.config.ts          # Configuração Vite
│   ├── tsconfig.json           # Configuração TypeScript
│   ├── package.json            # Dependências Node.js
│   ├── Dockerfile              # Containerização frontend
│   ├── start.sh                # Script de início
│   ├── nginx.conf              # Configuração Nginx
│   │
│   └── src/
│       ├── main.tsx            # Entrada React
│       ├── App.tsx             # Componente raiz
│       ├── index.css           # Estilos globais
│       │
│       ├── components/         # Componentes reutilizáveis
│       │   ├── Header.tsx
│       │   ├── DataTable.tsx
│       │   ├── Pagination.tsx
│       │   ├── loading.tsx
│       │   ├── delete-confirmation-dialog.tsx
│       │   │
│       │   ├── columns/        # Definições de colunas
│       │   │   ├── room-columns.tsx
│       │   │   └── reservation-columns.tsx
│       │   │
│       │   ├── forms/          # Formulários com validação
│       │   │   ├── room-form.tsx
│       │   │   └── reservation-form.tsx
│       │   │
│       │   └── ui/             # Componentes shadcn
│       │       ├── button.tsx
│       │       ├── input.tsx
│       │       ├── table.tsx
│       │       ├── card.tsx
│       │       ├── select.tsx
│       │       ├── dropdown-menu.tsx
│       │       ├── alert-dialog.tsx
│       │       ├── skeleton.tsx
│       │       ├── tooltip.tsx
│       │       └── datetime-picker.tsx
│       │
│       ├── hooks/              # Hooks customizados
│       │   ├── use-auth.ts     # Autenticação
│       │   ├── use-rooms.ts    # Operações com salas
│       │   └── use-reservations.ts  # Operações com reservas
│       │
│       ├── pages/              # Páginas/Telas
│       │   ├── login-page.tsx
│       │   ├── register-page.tsx
│       │   │
│       │   └── private/
│       │       ├── Dashboard.tsx
│       │       ├── ReservationCreate.tsx
│       │       ├── ReservasRoom.tsx
│       │       └── RoomEdit.tsx
│       │
│       ├── routes/             # Configuração de rotas
│       │   ├── app-router.tsx
│       │   └── route-guards.tsx
│       │
│       ├── stores/             # Zustand stores
│       │   └── auth-store.ts
│       │
│       ├── lib/                # Utilidades
│       │   ├── api-client.ts   # Cliente HTTP
│       │   ├── utils.ts        # Funções utilitárias
│       │   │
│       │   └── schemas/        # Schemas Zod
│       │       ├── room-schema.ts
│       │       └── reservation-schema.ts
│       │
│       └── types/              # Type definitions
│           ├── auth.ts
│           ├── room.ts
│           └── reservation.ts
│
├── docker-compose.yml          # Orchestração dos containers
└── README.md                   # Este ficheiro
```

### Fluxo de Dados

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (React)                          │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ React Components (TSX)                                   │ │
│ │  ↓                                                        │ │
│ │ React Hook Form + Zod Validation                         │ │
│ │  ↓                                                        │ │
│ │ TanStack Query (API calls + caching)                     │ │
│ │  ↓                                                        │ │
│ │ Zustand Store (Auth state)                               │ │
│ └──────────────────────────────────────────────────────────┘ │
└──────────────────┬──────────────────────────────────────────┘
                   │ HTTP (JWT em cookies)
                   ▼
         ┌─────────────────────┐
         │   Nginx (Proxy)     │
         │ - SPA serving       │
         │ - /api/* routing    │
         └──────────┬──────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────────────────┐
│                  Backend (FastAPI)                          │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ FastAPI App                                              │ │
│ │  ↓                                                        │ │
│ │ Middleware (CORS, JWT validation, rate limit)            │ │
│ │  ↓                                                        │ │
│ │ Route Handlers (/user, /room, /reservation)              │ │
│ │  ↓                                                        │ │
│ │ Pydantic Validation                                      │ │
│ │  ↓                                                        │ │
│ │ Business Logic                                           │ │
│ │  ↓                                                        │ │
│ │ Motor (Async MongoDB Driver)                             │ │
│ └──────────────────────────────────────────────────────────┘ │
└──────────────────┬──────────────────────────────────────────┘
                   │
                   ▼
         ┌─────────────────────┐
         │    MongoDB          │
         │ - Users collection  │
         │ - Rooms collection  │
         │ - Reservations col. │
         └─────────────────────┘
```

---

## 🚀 Instalação Local

### Pré-requisitos

- **Python 3.14+** ([download](https://www.python.org/downloads/))
- **Node.js 20+** ([download](https://nodejs.org/))
- **MongoDB** (instalado localmente ou cloud, ex: MongoDB Atlas)
- **uv** (gestor de dependências Python)

### Backend Setup

1. **Clone o repositório e navegue para o backend:**
   ```bash
   cd backend
   ```

2. **Crie um ficheiro `.env` com as variáveis necessárias:**
   ```bash
   cat > .env << EOF
   MONGO_URL=mongodb://localhost:27017/classroom-reservations
   JWT_SECRET_KEY=your-super-secret-key-change-in-production
   ALGORITHM=HS512
   ACCESS_TOKEN_EXPIRE_MINUTES=60
   APP_ENV=development
   EOF
   ```

3. **Instale o `uv` (se não tiver):**
   ```bash
   pip install uv
   ```

4. **Sincronize as dependências e inicie o servidor:**
   ```bash
   uv sync
   uv run fastapi dev main.py
   ```

   A API estará disponível em: **`http://127.0.0.1:8000`**

5. **Aceda à documentação interativa:**
   - Swagger UI: `http://127.0.0.1:8000/docs`
   - ReDoc: `http://127.0.0.1:8000/redoc`
   - OpenAPI JSON: `http://127.0.0.1:8000/openapi.json`

### Frontend Setup

1. **Navegue para o frontend:**
   ```bash
   cd frontend
   ```

2. **Crie um ficheiro `.env` com a URL do backend:**
   ```bash
   cat > .env << EOF
   VITE_API_URL=http://localhost:8000
   EOF
   ```

3. **Instale as dependências:**
   ```bash
   npm install
   ```

4. **Inicie o servidor de desenvolvimento:**
   ```bash
   npm run dev
   ```

   A aplicação estará disponível em: **`http://localhost:5173`**

5. **Scripts disponíveis:**
   ```bash
   npm run dev       # Servidor de desenvolvimento
   npm run build     # Build para produção
   npm run lint      # Executar ESLint
   npm run preview   # Preview do build
   npm run format    # Formatar código com Prettier
   ```

---

## 🐳 Instalação com Docker

### Pré-requisitos

- **Docker** ([download](https://www.docker.com/products/docker-desktop))
- **Docker Compose** (incluso com Docker Desktop)

### Quick Start

1. **Clone o repositório:**
   ```bash
   cd WebServices
   ```

2. **Crie os ficheiros `.env` necessários:**

   **backend/.env:**
   ```bash
   MONGO_URL=mongodb://mongo:27017/classroom-reservations
   JWT_SECRET_KEY=your-super-secret-key-change-in-production
   ALGORITHM=HS512
   ACCESS_TOKEN_EXPIRE_MINUTES=60
   APP_ENV=production
   ```

   **frontend/.env:**
   ```bash
   VITE_API_URL=http://localhost:8080/api
   ```

3. **Inicie os containers:**
   ```bash
   docker-compose up --build
   ```

   Isto irá:
   - Construir a imagem do backend (FastAPI + Python)
   - Construir a imagem do frontend (React + Node.js)
   - Iniciar MongoDB
   - Servir a aplicação em `http://localhost` (porta 80)

4. **Parar os containers:**
   ```bash
   docker-compose down
   ```

5. **Ver logs em tempo real:**
   ```bash
   docker-compose logs -f
   ```

### Estrutura do docker-compose.yml

```yaml
services:
  backend:
    build: ./backend
    container_name: app-backend
    env_file: ./backend/.env
    restart: always

  frontend:
    build: ./frontend
    container_name: app-frontend
    ports:
      - "127.0.0.1:8080:8080"   # localhost only
      - "0.0.0.0:80:8080"       # all interfaces
    restart: always
    depends_on:
      - backend
```

---

## 📡 Uso da API

### Autenticação

#### 1. Register (Criar Conta)
```bash
POST /auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123",
  "name": "João Silva"
}
```

**Response (201 Created):**
```json
{
  "id": "66a1b2c3d4e5f6g7h8i9j0k",
  "email": "user@example.com",
  "name": "João Silva",
  "role": "user"
}
```

#### 2. Login
```bash
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123"
}
```

**Response (200 OK):**
```json
{
  "access_token": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "user": {
    "id": "66a1b2c3d4e5f6g7h8i9j0k",
    "email": "user@example.com",
    "name": "João Silva",
    "role": "user"
  }
}
```

O token é armazenado num cookie HTTP-only automaticamente.

#### 3. Get Current User
```bash
GET /auth/me
Authorization: Bearer <token>
```

### Salas (Rooms)

#### 1. Listar Salas
```bash
GET /room?page=1&limit=10
```

**Response:**
```json
{
  "data": [
    {
      "id": "66a1b2c3d4e5f6g7h8i9j0k",
      "name": "Sala 101",
      "capacity": 30,
      "description": "Sala com projetor",
      "isFree": true
    }
  ],
  "total": 1,
  "page": 1,
  "limit": 10
}
```

#### 2. Criar Sala (Admin Only)
```bash
POST /room
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "name": "Sala 102",
  "capacity": 40,
  "description": "Sala com quadro branco"
}
```

#### 3. Atualizar Sala (Admin Only)
```bash
PUT /room/{room_id}
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "name": "Sala 102 - Atualizada",
  "capacity": 45
}
```

#### 4. Eliminar Sala (Admin Only)
```bash
DELETE /room/{room_id}
Authorization: Bearer <admin_token>
```

### Reservas (Reservations)

#### 1. Listar Reservas
```bash
GET /reservation?page=1&limit=10
```

#### 2. Listar Reservas por Sala
```bash
GET /reservation/{room_id}?page=1&limit=10
```

#### 3. Criar Reserva
```bash
POST /reservation
Authorization: Bearer <token>
Content-Type: application/json

{
  "room_id": "66a1b2c3d4e5f6g7h8i9j0k",
  "start_time": "2026-05-10T10:00:00Z",
  "end_time": "2026-05-10T12:00:00Z"
}
```

**Response (201 Created):**
```json
{
  "id": "66a1b2c3d4e5f6g7h8i9j0k",
  "user_id": "...",
  "room_id": "...",
  "start_time": "2026-05-10T10:00:00Z",
  "end_time": "2026-05-10T12:00:00Z",
  "created_at": "2026-05-05T15:30:00Z"
}
```

#### 4. Cancelar Reserva
```bash
DELETE /reservation/{reservation_id}
Authorization: Bearer <token>
```

---

## 🔒 Segurança

### Implementações de Segurança

✅ **Autenticação JWT**
- Tokens com HS512
- Expiração configurável
- Armazenamento em HTTP-only cookies

✅ **Validação de Dados**
- Pydantic no backend
- Zod no frontend
- Type-safe validation

✅ **Controlo de Acesso**
- Role-based access control (RBAC)
- Middleware de autenticação
- Proteção de endpoints

✅ **Headers de Segurança**
- CORS configurado
- Content Security Policy
- X-Frame-Options
- X-Content-Type-Options

✅ **Password Security**
- Argon2 hashing
- Salt automático
- Comparação timing-safe

---

## 📝 Variáveis de Ambiente

### Backend (.env)

```
MONGO_URL=mongodb://localhost:27017/classroom-reservations
JWT_SECRET_KEY=your-super-secret-key
ALGORITHM=HS512
ACCESS_TOKEN_EXPIRE_MINUTES=60
APP_ENV=development
```

### Frontend (.env)

```
VITE_API_URL=http://localhost:8000
```

---

## 🧪 Testes e Desenvolvimento

### Backend

```bash
# Executar em modo watch
uv run fastapi dev main.py

# Verificar linting (se configurado)
uv run pylint backend/
```

### Frontend

```bash
# Desenvolvimento com hot reload
npm run dev

# Build para produção
npm run build

# Preview do build
npm run preview

# Linting
npm run lint

# Formatação
npm run format
```

---

## 🎓 Principais Objetivos Alcançados

### ✅ Gestão de Base de Dados
- [x] Modelos Pydantic para Users, Rooms, Reservations
- [x] MongoDB com Motor (driver assíncrono)
- [x] CRUD completo para todas as entidades
- [x] Validação de dados em camada de aplicação
- [x] Indexes únicos para garantir integridade

### ✅ Autenticação & Autorização
- [x] JWT com HS512 signing
- [x] Passwords com Argon2 (pwdlib)
- [x] Role-based access control (Admin/User)
- [x] HTTP-only cookies para segurança
- [x] Middleware de validação JWT

### ✅ Modularidade & Docker
- [x] Backend containerizado (Python/FastAPI)
- [x] Frontend containerizado (Node/React)
- [x] docker-compose para orchestração
- [x] Nginx como reverse proxy
- [x] Multi-stage builds para imagens otimizadas

### ✅ Documentação da API
- [x] Swagger UI automático em `/docs`
- [x] ReDoc em `/redoc`
- [x] Schema OpenAPI em `/openapi.json`
- [x] Endpoints com type hints e docstrings
- [x] README completo com exemplos de uso

### ✅ Interface de Utilizador
- [x] Login e registro com validação
- [x] Dashboard com tabelas de dados
- [x] Formulários com React Hook Form + Zod
- [x] Paginação e filtros
- [x] Dark mode por omissão
- [x] Responsivo e acessível

---

## 📚 Tecnologias Destacadas

### Validação End-to-End
```
Frontend (Zod) → React Hook Form → API HTTP → Backend (Pydantic) → MongoDB
```

### State Management
- **Zustand** para estado global (auth)
- **TanStack Query** para cache de API
- **React hooks** para estado local

### Async/Await
- FastAPI com async routes
- Motor para operações MongoDB assíncronas
- React com suspense e loading states

---

## 🐛 Troubleshooting

### MongoDB não conecta
```bash
# Verificar se MongoDB está rodando
mongosh --eval "db.adminCommand('ping')"

# Ou usar MongoDB Atlas com URL em .env
MONGO_URL=mongodb+srv://user:pass@cluster.mongodb.net/classroom-reservations
```

### JWT token expirado
- Tokens têm expiração de 60 minutos por padrão
- Fazer login novamente para obter novo token
- Configurar `ACCESS_TOKEN_EXPIRE_MINUTES` em backend/.env

### CORS errors no frontend
- Verificar se `VITE_API_URL` está correto em frontend/.env
- Backend deve ter CORS configurado para o domínio do frontend

---

## 📖 Referências Úteis

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [MongoDB Motor Documentation](https://motor.readthedocs.io/)
- [React Router Documentation](https://reactrouter.com/)
- [TanStack Query Documentation](https://tanstack.com/query/latest)
- [Zod Documentation](https://zod.dev/)
- [Tailwind CSS Documentation](https://tailwindcss.com/)
- [Docker Documentation](https://docs.docker.com/)

---

## 👨‍💻 Autor

Desenvolvido por **Miguel** como projeto educacional e portfólio profissional para demonstrar conhecimentos em full-stack development, arquitetura de software e práticas modernas de desenvolvimento.

**Data**: Maio de 2026

---

## 📄 Licença

Este projeto é fornecido como-é para fins educacionais e de portfólio. Todos os direitos reservados.

---

## 🤝 Contribuições

Sugestões e melhorias são bem-vindas! Sinta-se à vontade para abrir issues ou pull requests.
│       ├── main.tsx            # Entrada React
│       ├── App.tsx             # Componente raiz
│       ├── index.css           # Estilos globais
│       │
│       ├── components/         # Componentes reutilizáveis
│       │   ├── Header.tsx
│       │   ├── DataTable.tsx
│       │   ├── Pagination.tsx
│       │   ├── loading.tsx
│       │   ├── delete-confirmation-dialog.tsx
│       │   │
│       │   ├── columns/        # Definições de colunas
│       │   │   ├── room-columns.tsx
│       │   │   └── reservation-columns.tsx
│       │   │
│       │   ├── forms/          # Formulários com validação
│       │   │   ├── room-form.tsx
│       │   │   └── reservation-form.tsx
│       │   │
│       │   └── ui/             # Componentes shadcn
│       │       ├── button.tsx
│       │       ├── input.tsx
│       │       ├── table.tsx
│       │       ├── card.tsx
│       │       ├── select.tsx
│       │       ├── dropdown-menu.tsx
│       │       ├── alert-dialog.tsx
│       │       ├── skeleton.tsx
│       │       ├── tooltip.tsx
│       │       └── datetime-picker.tsx
│       │
│       ├── hooks/              # Hooks customizados
│       │   ├── use-auth.ts     # Autenticação
│       │   ├── use-rooms.ts    # Operações com salas
│       │   └── use-reservations.ts  # Operações com reservas
│       │
│       ├── pages/              # Páginas/Telas
│       │   ├── login-page.tsx
│       │   ├── register-page.tsx
│       │   │
│       │   └── private/
│       │       ├── Dashboard.tsx
│       │       ├── ReservationCreate.tsx
│       │       ├── ReservasRoom.tsx
│       │       └── RoomEdit.tsx
│       │
│       ├── routes/             # Configuração de rotas
│       │   ├── app-router.tsx
│       │   └── route-guards.tsx
│       │
│       ├── stores/             # Zustand stores
│       │   └── auth-store.ts
│       │
│       ├── lib/                # Utilidades
│       │   ├── api-client.ts   # Cliente HTTP
│       │   ├── utils.ts        # Funções utilitárias
│       │   │
│       │   └── schemas/        # Schemas Zod
│       │       ├── room-schema.ts
│       │       └── reservation-schema.ts
│       │
│       └── types/              # Type definitions
│           ├── auth.ts
│           ├── room.ts
│           └── reservation.ts
│
├── docker-compose.yml          # Orchestração dos containers
└── README.md                   # Este ficheiro
```

### Fluxo de Dados

```
┌─────────────────────────────────────────────────────────────┐
│                    Frontend (React)                          │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ React Components (TSX)                                   │ │
│ │  ↓                                                        │ │
│ │ React Hook Form + Zod Validation                         │ │
│ │  ↓                                                        │ │
│ │ TanStack Query (API calls + caching)                     │ │
│ │  ↓                                                        │ │
│ │ Zustand Store (Auth state)                               │ │
│ └──────────────────────────────────────────────────────────┘ │
└──────────────────┬──────────────────────────────────────────┘
                   │ HTTP (JWT em cookies)
                   ▼
         ┌─────────────────────┐
         │   Nginx (Proxy)     │
         │ - SPA serving       │
         │ - /api/* routing    │
         └──────────┬──────────┘
                    │
                    ▼
┌─────────────────────────────────────────────────────────────┐
│                  Backend (FastAPI)                          │
│ ┌──────────────────────────────────────────────────────────┐ │
│ │ FastAPI App                                              │ │
│ │  ↓                                                        │ │
│ │ Middleware (CORS, JWT validation, rate limit)            │ │
│ │  ↓                                                        │ │
│ │ Route Handlers (/user, /room, /reservation)              │ │
│ │  ↓                                                        │ │
│ │ Pydantic Validation                                      │ │
│ │  ↓                                                        │ │
│ │ Business Logic                                           │ │
│ │  ↓                                                        │ │
│ │ Motor (Async MongoDB Driver)                             │ │
│ └──────────────────────────────────────────────────────────┘ │
└──────────────────┬──────────────────────────────────────────┘
                   │
                   ▼
         ┌─────────────────────┐
         │    MongoDB          │
         │ - Users collection  │
         │ - Rooms collection  │
         │ - Reservations col. │
         └─────────────────────┘
```

---

## 🚀 Instalação Local

### Pré-requisitos

- **Python 3.14+** ([download](https://www.python.org/downloads/))
- **Node.js 20+** ([download](https://nodejs.org/))
- **MongoDB** (instalado localmente ou cloud, ex: MongoDB Atlas)
- **uv** (gestor de dependências Python)

### Backend Setup

1. **Clone o repositório e navegue para o backend:**
   ```bash
   cd backend
   ```

2. **Crie um ficheiro `.env` com as variáveis necessárias:**
   ```bash
   cat > .env << EOF
   MONGO_URL=mongodb://localhost:27017/classroom-reservations
   JWT_SECRET_KEY=your-super-secret-key-change-in-production
   ALGORITHM=HS512
   ACCESS_TOKEN_EXPIRE_MINUTES=60
   APP_ENV=development
   EOF
   ```

3. **Instale o `uv` (se não tiver):**
   ```bash
   pip install uv
   ```

4. **Sincronize as dependências e inicie o servidor:**
   ```bash
   uv sync
   uv run fastapi dev main.py
   ```

   A API estará disponível em: **`http://127.0.0.1:8000`**

5. **Aceda à documentação interativa:**
   - Swagger UI: `http://127.0.0.1:8000/docs`
   - ReDoc: `http://127.0.0.1:8000/redoc`
   - OpenAPI JSON: `http://127.0.0.1:8000/openapi.json`

### Frontend Setup

1. **Navegue para o frontend:**
   ```bash
   cd frontend
   ```

2. **Crie um ficheiro `.env` com a URL do backend:**
   ```bash
   cat > .env << EOF
   VITE_API_URL=http://localhost:8000
   EOF
   ```

3. **Instale as dependências:**
   ```bash
   npm install
   ```

4. **Inicie o servidor de desenvolvimento:**
   ```bash
   npm run dev
   ```

   A aplicação estará disponível em: **`http://localhost:5173`**

5. **Scripts disponíveis:**
   ```bash
   npm run dev       # Servidor de desenvolvimento
   npm run build     # Build para produção
   npm run lint      # Executar ESLint
   npm run preview   # Preview do build
   npm run format    # Formatar código com Prettier
   ```

---

## 🐳 Instalação com Docker

### Pré-requisitos

- **Docker** ([download](https://www.docker.com/products/docker-desktop))
- **Docker Compose** (incluso com Docker Desktop)

### Quick Start

1. **Clone o repositório:**
   ```bash
   cd WebServices
   ```

2. **Crie os ficheiros `.env` necessários:**

   **backend/.env:**
   ```bash
   MONGO_URL=mongodb://mongo:27017/classroom-reservations
   JWT_SECRET_KEY=your-super-secret-key-change-in-production
   ALGORITHM=HS512
   ACCESS_TOKEN_EXPIRE_MINUTES=60
   APP_ENV=production
   ```

   **frontend/.env:**
   ```bash
   VITE_API_URL=http://localhost:8080/api
   ```

3. **Inicie os containers:**
   ```bash
   docker-compose up --build
   ```

   Isto irá:
   - Construir a imagem do backend (FastAPI + Python)
   - Construir a imagem do frontend (React + Node.js)
   - Iniciar MongoDB
   - Servir a aplicação em `http://localhost` (porta 80)

4. **Parar os containers:**
   ```bash
   docker-compose down
   ```

5. **Ver logs em tempo real:**
   ```bash
   docker-compose logs -f
   ```

### Estrutura do docker-compose.yml

```yaml
services:
  backend:
    build: ./backend
    container_name: app-backend
    env_file: ./backend/.env
    restart: always

  frontend:
    build: ./frontend
    container_name: app-frontend
    ports:
      - "127.0.0.1:8080:8080"   # localhost only
      - "0.0.0.0:80:8080"       # all interfaces
    restart: always
    depends_on:
      - backend
```

---

## 📡 Uso da API

### Autenticação

#### 1. Register (Criar Conta)
```bash
POST /auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123",
  "name": "João Silva"
}
```

**Response (201 Created):**
```json
{
  "id": "66a1b2c3d4e5f6g7h8i9j0k",
  "email": "user@example.com",
  "name": "João Silva",
  "role": "user"
}
```

#### 2. Login
```bash
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123"
}
```

**Response (200 OK):**
```json
{
  "access_token": "eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer",
  "user": {
    "id": "66a1b2c3d4e5f6g7h8i9j0k",
    "email": "user@example.com",
    "name": "João Silva",
    "role": "user"
  }
}
```

O token é armazenado num cookie HTTP-only automaticamente.

#### 3. Get Current User
```bash
GET /auth/me
Authorization: Bearer <token>
```

### Salas (Rooms)

#### 1. Listar Salas
```bash
GET /room?page=1&limit=10
```

**Response:**
```json
{
  "data": [
    {
      "id": "66a1b2c3d4e5f6g7h8i9j0k",
      "name": "Sala 101",
      "capacity": 30,
      "description": "Sala com projetor",
      "isFree": true
    }
  ],
  "total": 1,
  "page": 1,
  "limit": 10
}
```

#### 2. Criar Sala (Admin Only)
```bash
POST /room
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "name": "Sala 102",
  "capacity": 40,
  "description": "Sala com quadro branco"
}
```

#### 3. Atualizar Sala (Admin Only)
```bash
PUT /room/{room_id}
Authorization: Bearer <admin_token>
Content-Type: application/json

{
  "name": "Sala 102 - Atualizada",
  "capacity": 45
}
```

#### 4. Eliminar Sala (Admin Only)
```bash
DELETE /room/{room_id}
Authorization: Bearer <admin_token>
```

### Reservas (Reservations)

#### 1. Listar Reservas
```bash
GET /reservation?page=1&limit=10
```

#### 2. Listar Reservas por Sala
```bash
GET /reservation/{room_id}?page=1&limit=10
```

#### 3. Criar Reserva
```bash
POST /reservation
Authorization: Bearer <token>
Content-Type: application/json

{
  "room_id": "66a1b2c3d4e5f6g7h8i9j0k",
  "start_time": "2026-05-10T10:00:00Z",
  "end_time": "2026-05-10T12:00:00Z"
}
```

**Response (201 Created):**
```json
{
  "id": "66a1b2c3d4e5f6g7h8i9j0k",
  "user_id": "...",
  "room_id": "...",
  "start_time": "2026-05-10T10:00:00Z",
  "end_time": "2026-05-10T12:00:00Z",
  "created_at": "2026-05-05T15:30:00Z"
}
```

#### 4. Cancelar Reserva
```bash
DELETE /reservation/{reservation_id}
Authorization: Bearer <token>
```

---

## 🔒 Segurança

### Implementações de Segurança

✅ **Autenticação JWT**
- Tokens com HS512
- Expiração configurável
- Armazenamento em HTTP-only cookies

✅ **Validação de Dados**
- Pydantic no backend
- Zod no frontend
- Type-safe validation

✅ **Controlo de Acesso**
- Role-based access control (RBAC)
- Middleware de autenticação
- Proteção de endpoints

✅ **Headers de Segurança**
- CORS configurado
- Content Security Policy
- X-Frame-Options
- X-Content-Type-Options

✅ **Password Security**
- Argon2 hashing
- Salt automático
- Comparação timing-safe

---

## 📝 Variáveis de Ambiente

### Backend (.env)

```
MONGO_URL=mongodb://localhost:27017/classroom-reservations
JWT_SECRET_KEY=your-super-secret-key
ALGORITHM=HS512
ACCESS_TOKEN_EXPIRE_MINUTES=60
APP_ENV=development
```

### Frontend (.env)

```
VITE_API_URL=http://localhost:8000
```

---

## 🧪 Testes e Desenvolvimento

### Backend

```bash
# Executar em modo watch
uv run fastapi dev main.py

# Verificar linting (se configurado)
uv run pylint backend/
```

### Frontend

```bash
# Desenvolvimento com hot reload
npm run dev

# Build para produção
npm run build

# Preview do build
npm run preview

# Linting
npm run lint

# Formatação
npm run format
```

---

## 🎓 Principais Objetivo
cd frontend
# Create .env with VITE_API_URL=http://127.0.0.1:8000
npm install && npm run dev  # http://127.0.0.1:5173
```

## Running with Docker (Recommended)

```bash
docker compose up --build
```

Access the full stack at `http://localhost:8080` (frontend + proxied API).

## API Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/user/register` | POST | Public | Create user (strong password validation) |
| `/user/login` | POST | Public | JWT login (HTTP-only cookie) |
| `/room/` | POST/GET | Admin/User | Room CRUD + paginated list |
| `/reservation/` | POST | User | Create reservation (1-day min lead time) |
| `/reservation/room/{id}` | GET | User | Room reservations (role-based filtering) |

## Security Features
- JWT tokens with 1h expiry in HTTP-only cookies
- Rate limiting (15 req/30s per IP)
- Admin-only room CRUD operations
- Production CORS + proxy validation
- Nginx OWASP headers (XSS, clickjacking protection)

This 2026-ready full-stack project demonstrates production-grade full-stack development with modern tooling, security best practices, and containerized deployment.
