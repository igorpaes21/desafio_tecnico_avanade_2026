# Landing Page UGC Creator - Gerenciador de Tarefas

![Status](https://img.shields.io/badge/Status-Em%20Desenvolvimento-yellow)
![Versão](https://img.shields.io/badge/Versão-1.0.0-blue)

## 📋 Descrição do Projeto

Aplicação web completa para cadastro e gerenciamento de tarefas, desenvolvida como desafio técnico do Bootcamp Web Front-end (Angular + ASP.NET).

**Objetivos:**
- Avaliar conhecimentos básicos de desenvolvimento web
- Integração front-end e back-end
- Lógica de programação em nível júnior
- Boas práticas de organização de código

---

## 🛠️ Tecnologias Utilizadas

### Front-end
- **Angular** - Framework principal
- **TypeScript** - Linguagem de programação
- **HTML5 / CSS3** - Markup e estilos
- **HttpClient** - Comunicação com API

### Back-end
- **ASP.NET Core** - Framework web
- **C#** - Linguagem de programação
- **Entity Framework Core** - ORM para banco de dados
- **SQL Server** - Banco de dados

### Comunicação
- **API REST** com padrão JSON

---

## 📁 Estrutura do Projeto

```
desafio_técnico_avanade_2026/
├── docs/                          # Documentação do projeto
│   ├── ESPECIFICACOES.md         # Especificações detalhadas
│   ├── ARQUITETURA.md            # Diagrama de arquitetura
│   └── GUIA_DESENVOLVIMENTO.md   # Guia passo a passo
├── backend/                       # API ASP.NET Core
│   ├── TarefasAPI/               # Projeto principal
│   ├── Models/                   # Entidades
│   ├── Controllers/              # Controllers da API
│   ├── Data/                     # DbContext e migrations
│   └── appsettings.json          # Configurações
├── frontend/                      # Aplicação Angular
│   ├── src/
│   │   ├── app/
│   │   │   ├── components/       # Componentes
│   │   │   ├── services/         # Serviços
│   │   │   └── models/           # Modelos de dados
│   │   └── assets/               # Recursos estáticos
│   ├── angular.json              # Configuração Angular
│   └── package.json              # Dependências
├── README.md                      # Este arquivo
└── .gitignore                     # Arquivos ignorados pelo Git
```

---

## 📌 Funcionalidades Obrigatórias

### Back-end (API REST)
- ✅ GET `/api/tarefas` - Listar todas as tarefas
- ✅ GET `/api/tarefas/{id}` - Buscar tarefa por ID
- ✅ POST `/api/tarefas` - Criar nova tarefa
- ✅ PUT `/api/tarefas/{id}` - Atualizar tarefa
- ✅ DELETE `/api/tarefas/{id}` - Excluir tarefa

### Front-end (Angular)
- ✅ Tela para listar tarefas
- ✅ Formulário para criar tarefa
- ✅ Funcionalidade de editar tarefa
- ✅ Funcionalidade de excluir tarefa
- ✅ Consumo da API via HttpClient
- ✅ Organização em componentes e serviços

---

## 🔄 Entidade: Tarefa

| Campo | Tipo | Descrição |
|-------|------|-----------|
| Id | int | Identificador único (auto-generated) |
| Titulo | string | Título da tarefa |
| Descricao | string | Descrição detalhada |
| Status | string | Pendente / Concluída |
| DataCriacao | DateTime | Data e hora de criação |

---

## 🚀 Como Executar o Projeto

### Pré-requisitos

- **.NET SDK 6.0+** ou superior
- **Node.js 16+** com npm
- **Angular CLI**: `npm install -g @angular/cli`
- **SQL Server** instalado e rodando
- **Git** para versionamento

---

### 1️⃣ Configurar o Banco de Dados

#### a) Atualizar Connection String

Editar `backend/TarefasAPI/appsettings.json`:

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=TarefasDB;Trusted_Connection=true;"
  }
}
```

#### b) Criar Banco de Dados

```powershell
cd backend/TarefasAPI

# Criar migração inicial
dotnet ef migrations add InitialCreate

# Aplicar migração ao banco
dotnet ef database update
```

---

### 2️⃣ Executar o Back-end

```powershell
cd backend/TarefasAPI

# Restaurar dependências
dotnet restore

# Executar a aplicação
dotnet run
```

A API estará disponível em: `https://localhost:5001`

---

### 3️⃣ Executar o Front-end

```powershell
cd frontend

# Instalar dependências
npm install

# Executar aplicação Angular
ng serve
```

A aplicação estará disponível em: `http://localhost:4200`

---

## 📝 Exemplo de Requisição

### Criar Nova Tarefa (POST)

```bash
curl -X POST "https://localhost:5001/api/tarefas" \
  -H "Content-Type: application/json" \
  -d '{
    "titulo": "Implementar CRUD",
    "descricao": "Desenvolver endpoints da API",
    "status": "Pendente"
  }'
```

**Resposta (201 Created):**
```json
{
  "id": 1,
  "titulo": "Implementar CRUD",
  "descricao": "Desenvolver endpoints da API",
  "status": "Pendente",
  "dataCriacao": "2026-04-08T10:30:00"
}
```

---

## ✨ Funcionalidades Opcionais

- ✨ Validação de formulários
- ✨ Mensagens de sucesso/erro
- ✨ Filtro por status
- ✨ Layout responsivo
- ✨ Tratamento robusto de erros
- ✨ Commits organizados no Git

---

## 📊 Cronograma Estimado (80 horas)

| Fase | Horas | Status |
|------|-------|--------|
| Planejamento | 10h | ⏳ |
| Desenvolvimento Back-end | 25h | ⏳ |
| Desenvolvimento Front-end | 25h | ⏳ |
| Integração | 15h | ⏳ |
| Ajustes e Testes | 5h | ⏳ |

---

## 🔍 Critérios de Avaliação

### Obrigatórios ✅
- Funcionamento correto do CRUD
- Comunicação entre Angular e API
- Organização básica do código
- Uso correto de HTTP (GET, POST, PUT, DELETE)
- Projeto compilado e executado corretamente

### Diferenciais 🌟
- Código limpo e bem organizado
- Validações básicas
- Tratamento de erros
- README com instruções (✓ aqui)
- Commits organizados no Git

### Não avaliados ❌
- Design avançado
- Autenticação/login
- Arquitetura complexa
- Padrões avançados (DDD, CQRS)

---

## 📚 Documentação Adicional

- [ESPECIFICACOES.md](docs/ESPECIFICACOES.md) - Requisitos detalhados
- [ARQUITETURA.md](docs/ARQUITETURA.md) - Arquitetura do sistema
- [GUIA_DESENVOLVIMENTO.md](docs/GUIA_DESENVOLVIMENTO.md) - Passo a passo desenvolvimento

---

## 🐛 Troubleshooting

### Erro: "Connection string not found"
- ✓ Verifique `appsettings.json` no diretório do projeto
- ✓ Confirme que SQL Server está rodando

### Erro: "Port 5001 already in use"
- ✓ Altere a porta em `launchSettings.json`
- ✓ Ou finalize o processo usando a porta

### Erro: "Angular command not found"
- ✓ Instale Angular CLI: `npm install -g @angular/cli`
- ✓ Verifique instalação do Node.js

---

## 📧 Contato & Suporte

**Desenvolvedor:** Igor Paes
**Email:** irsp2121@hmail.com
**GitHub:** [igorpaes21](https://github.com/igorpaes21)

---

## 📄 Licença

Projeto desenvolvido para fins educacionais - Bootcamp Avanade 2026

---

## 📝 Changelog

### v1.0.0 (08/04/2026)
- Inicialização do projeto
- Estrutura base criada
- Documentação inicial

---

**Última atualização:** 08/04/2026
