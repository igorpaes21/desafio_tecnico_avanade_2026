# 📋 Especificações Detalhadas - Desafio Técnico Avanade

## 1. Visão Geral

Sistema de gerenciamento de tarefas com arquitetura cliente-servidor, separando completamente front-end (Angular) e back-end (ASP.NET Core).

---

## 2. Requisitos de Negócio

### 2.1 Objetivo Principal
Criar uma aplicação que permita aos usuários:
- Visualizar lista de tarefas
- Criar novas tarefas
- Editar tarefas existentes
- Excluir tarefas

### 2.2 Usuários Alvo
- Desenvolvedores Júnior durante o Bootcamp
- Avaliadores do desafio técnico

### 2.3 Restrições
- Sem autenticação/login
- Sem padrões avançados de arquitetura
- Design simples
- Foco em CRUD funcional

---

## 3. Especificação de Dados

### 3.1 Entidade Tarefa

```csharp
public class Tarefa
{
    public int Id { get; set; }                          // PK, Auto-increment
    public string Titulo { get; set; }                   // Obrigatório, max 100 caracteres
    public string Descricao { get; set; }                // Opcional, max 500 caracteres
    public string Status { get; set; }                   // "Pendente" ou "Concluída"
    public DateTime DataCriacao { get; set; }            // Timestamp da criação
}
```

### 3.2 Estados de Status
- **Pendente** - Tarefa não concluída
- **Concluída** - Tarefa completada

### 3.3 Banco de Dados
- Banco: `TarefasDB`
- Tabela: `Tarefas`
- Collation: UTF-8 (suporta português)

---

## 4. Especificação da API REST

### 4.1 URL Base
```
https://localhost:5001/api/tarefas
```

### 4.2 Endpoints Obrigatórios

#### 1. Listar todas as tarefas
```http
GET /api/tarefas HTTP/1.1
Host: localhost:5001
```

**Resposta 200 OK:**
```json
[
  {
    "id": 1,
    "titulo": "Implementar API",
    "descricao": "Criar endpoints CRUD",
    "status": "Pendente",
    "dataCriacao": "2026-04-08T10:30:00"
  },
  {
    "id": 2,
    "titulo": "Testar Front-end",
    "descricao": "Validar integração",
    "status": "Concluída",
    "dataCriacao": "2026-04-08T11:45:00"
  }
]
```

---

#### 2. Buscar tarefa por ID
```http
GET /api/tarefas/1 HTTP/1.1
Host: localhost:5001
```

**Resposta 200 OK:**
```json
{
  "id": 1,
  "titulo": "Implementar API",
  "descricao": "Criar endpoints CRUD",
  "status": "Pendente",
  "dataCriacao": "2026-04-08T10:30:00"
}
```

**Resposta 404 Not Found:**
```json
{
  "message": "Tarefa não encontrada"
}
```

---

#### 3. Criar nova tarefa
```http
POST /api/tarefas HTTP/1.1
Host: localhost:5001
Content-Type: application/json

{
  "titulo": "Nova Tarefa",
  "descricao": "Descrição da tarefa",
  "status": "Pendente"
}
```

**Resposta 201 Created:**
```json
{
  "id": 3,
  "titulo": "Nova Tarefa",
  "descricao": "Descrição da tarefa",
  "status": "Pendente",
  "dataCriacao": "2026-04-08T15:20:00"
}
```

**Resposta 400 Bad Request:**
```json
{
  "message": "Título é obrigatório",
  "errors": ["Titulo is required"]
}
```

---

#### 4. Atualizar tarefa
```http
PUT /api/tarefas/1 HTTP/1.1
Host: localhost:5001
Content-Type: application/json

{
  "titulo": "Tarefa Atualizada",
  "descricao": "Nova descrição",
  "status": "Concluída"
}
```

**Resposta 200 OK:**
```json
{
  "id": 1,
  "titulo": "Tarefa Atualizada",
  "descricao": "Nova descrição",
  "status": "Concluída",
  "dataCriacao": "2026-04-08T10:30:00"
}
```

**Resposta 404 Not Found:**
```json
{
  "message": "Tarefa não encontrada"
}
```

---

#### 5. Excluir tarefa
```http
DELETE /api/tarefas/1 HTTP/1.1
Host: localhost:5001
```

**Resposta 204 No Content:**
```
(sem corpo)
```

**Resposta 404 Not Found:**
```json
{
  "message": "Tarefa não encontrada"
}
```

---

### 4.3 Códigos de Status HTTP

| Código | Significado | Quando usar |
|--------|-------------|-------------|
| 200 | OK | GET, PUT bem-sucedido |
| 201 | Created | POST bem-sucedido |
| 204 | No Content | DELETE bem-sucedido |
| 400 | Bad Request | Dados inválidos |
| 404 | Not Found | Recurso não existe |
| 500 | Internal Server Error | Erro do servidor |

---

## 5. Especificação do Front-end Angular

### 5.1 Estrutura de Componentes

```
src/app/
├── components/
│   ├── tarefa-list/          # Lista de tarefas
│   ├── tarefa-form/          # Formulário criar/editar
│   └── tarefa-item/          # Item individual
├── services/
│   └── tarefa.service.ts     # Serviço de chamadas API
└── models/
    └── tarefa.model.ts       # Interface/classe Tarefa
```

### 5.2 Componentes Obrigatórios

#### TarefaListComponent
- Exibe lista de tarefas
- Botão para criar nova tarefa
- Botões para editar e deletar
- Carrega dados ao inicializar

#### TarefaFormComponent
- Formulário para criar/editar
- Validação básica de campos
- Submit e Cancel

#### TarefaService
- Método GET (listar)
- Método GET by ID (buscar um)
- Método POST (criar)
- Método PUT (editar)
- Método DELETE (deletar)

### 5.3 Fluxo de Dados

```
TarefaListComponent
    ↓
TarefaService (HttpClient)
    ↓
API REST / ASP.NET Core
    ↓
SQL Server
```

### 5.4 Validações Front-end

- Título obrigatório
- Descrição opcional
- Status com dropdown (Pendente/Concluída)
- Mensagens de erro

### 5.5 Layout Mínimo

```
┌─────────────────────────────────────┐
│   GERENCIADOR DE TAREFAS            │
├─────────────────────────────────────┤
│  [+ Nova Tarefa]                    │
├─────────────────────────────────────┤
│ Título       | Status    | Ações     │
├─────────────────────────────────────┤
│ Tarefa 1     | Pendente  | Ed | Del │
│ Tarefa 2     | Concluída | Ed | Del │
│ ...                                 │
└─────────────────────────────────────┘
```

---

## 6. Especificação do Back-end ASP.NET Core

### 6.1 Estrutura de Pastas

```
TarefasAPI/
├── Controllers/
│   └── TarefasController.cs
├── Models/
│   └── Tarefa.cs
├── Data/
│   ├── ApplicationDbContext.cs
│   └── Migrations/
├── appsettings.json
└── Program.cs
```

### 6.2 Configurações Necessárias

#### appsettings.json
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=TarefasDB;Trusted_Connection=true;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### 6.3 Dependências NuGet

```
Microsoft.EntityFrameworkCore
Microsoft.EntityFrameworkCore.SqlServer
Microsoft.AspNetCore.Mvc
```

### 6.4 DbContext

```csharp
public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options) { }

    public DbSet<Tarefa> Tarefas { get; set; }
}
```

### 6.5 Controller

```csharp
[ApiController]
[Route("api/[controller]")]
public class TarefasController : ControllerBase
{
    private readonly ApplicationDbContext _context;

    public TarefasController(ApplicationDbContext context)
    {
        _context = context;
    }

    // GET: api/tarefas
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Tarefa>>> GetTarefas()
    
    // GET: api/tarefas/5
    [HttpGet("{id}")]
    public async Task<ActionResult<Tarefa>> GetTarefa(int id)
    
    // POST: api/tarefas
    [HttpPost]
    public async Task<ActionResult<Tarefa>> CreateTarefa(Tarefa tarefa)
    
    // PUT: api/tarefas/5
    [HttpPut("{id}")]
    public async Task<ActionResult<Tarefa>> UpdateTarefa(int id, Tarefa tarefa)
    
    // DELETE: api/tarefas/5
    [HttpDelete("{id}")]
    public async Task<ActionResult> DeleteTarefa(int id)
}
```

---

## 7. Especificação de Testes

### 7.1 Testes Manuais (Recomendados)

- [ ] Criar tarefa com dados válidos
- [ ] Tentar criar tarefa sem título
- [ ] Listar todas as tarefas
- [ ] Buscar tarefa por ID válido
- [ ] Buscar tarefa por ID inválido
- [ ] Atualizar status para Concluída
- [ ] Atualizar todos campos
- [ ] Deletar tarefa existente
- [ ] Deletar tarefa inexistente

### 7.2 Ferramentas Recomendadas

- **Postman** ou **Insomnia** para testar API
- **Chrome DevTools** para debugar front-end
- **SQL Server Management Studio** para validar banco

---

## 8. Especificação de Deploy

### 8.1 Back-end

```powershell
# Build
dotnet publish -c Release

# Executar
dotnet TarefasAPI.dll
```

### 8.2 Front-end

```bash
# Build
ng build --prod

# Servir arquivos estáticos
```

---

## 9. Critérios de Aceição

### Funcional ✅
- [ ] API retorna lista de tarefas
- [ ] API cria nova tarefa com ID auto-gerado
- [ ] API atualiza tarefa existente
- [ ] API deleta tarefa
- [ ] Front-end lista tarefas
- [ ] Front-end cria nova tarefa
- [ ] Front-end atualiza tarefa
- [ ] Front-end deleta tarefa
- [ ] Front-end comunicação com API funcionando

### Código ✅
- [ ] Sem hard-codes
- [ ] Nomes descritivos
- [ ] Organização em componentes/serviços
- [ ] Comentários em pontos críticos

### Documentação ✅
- [ ] README com instruções
- [ ] Código compilável
- [ ] Código executável
- [ ] Git com commits organizados

---

## 10. Possíveis Melhorias Futuras

- [ ] Paginação
- [ ] Ordenação
- [ ] Busca/Filtro
- [ ] Categorias
- [ ] Prioridades
- [ ] Autenticação
- [ ] Permissões
- [ ] Relatórios

---

**Versão:** 1.0  
**Data:** 08/04/2026  
**Autor:** Igor Paes
