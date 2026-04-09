# 🏗️ Arquitetura do Sistema

## Visão Geral

```
┌─────────────────────────────────────────────────────────┐
│                   NAVEGADOR (Browser)                   │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │          ANGULAR FRONT-END (Port 4200)          │   │
│  │                                                 │   │
│  │  ┌──────────────┐  ┌──────────────────────┐   │   │
│  │  │   Components │  │    Services          │   │   │
│  │  │              │  │  (HttpClient)        │   │   │
│  │  │  - List      │  │  - TarefaService     │   │   │
│  │  │  - Form      │  │  - HTTP Calls        │   │   │
│  │  │  - Item      │  │                      │   │   │
│  │  └──────────────┘  └──────────────────────┘   │   │
│  │           ↓                                     │   │
│  │     JSON/HTTP (REST)                           │   │
│  └─────────────────────────────────────────────────┘   │
└──────────────────────────↓────────────────────────────────┘
                           │ HTTPS
                           ↓
┌──────────────────────────────────────────────────────────┐
│         ASP.NET CORE WEB API (Port 5001)                │
│                                                         │
│  ┌────────────────────────────────────────────────┐    │
│  │          TarefasController                      │    │
│  │  Routes:                                       │    │
│  │  GET    /api/tarefas                          │    │
│  │  GET    /api/tarefas/{id}                     │    │
│  │  POST   /api/tarefas                          │    │
│  │  PUT    /api/tarefas/{id}                     │    │
│  │  DELETE /api/tarefas/{id}                     │    │
│  └────────────────────────────────────────────────┘    │
│           ↓                                             │
│  ┌────────────────────────────────────────────────┐    │
│  │          Entity Framework Core                  │    │
│  │  (ORM - Mapeamento Objeto-Relacional)          │    │
│  │                                                │    │
│  │  - DbContext (ApplicationDbContext)           │    │
│  │  - DbSet<Tarefa>                              │    │
│  └────────────────────────────────────────────────┘    │
│           ↓                                             │
│  ┌────────────────────────────────────────────────┐    │
│  │          SQL Server Database                    │    │
│  │  (TarefasDB)                                   │    │
│  │                                                │    │
│  │  Tabela: Tarefas                              │    │
│  │  - Id (INT, PK, Identity)                     │    │
│  │  - Titulo (VARCHAR(100))                      │    │
│  │  - Descricao (VARCHAR(500))                   │    │
│  │  - Status (VARCHAR(20))                       │    │
│  │  - DataCriacao (DATETIME2)                    │    │
│  └────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────┘
```

---

## Estrutura de Pastas Detalhada

### Backend (ASP.NET Core)

```
backend/
└── TarefasAPI/
    ├── Controllers/
    │   └── TarefasController.cs          # Endpoints REST
    ├── Models/
    │   └── Tarefa.cs                    # Entidade
    ├── Data/
    │   ├── ApplicationDbContext.cs       # DbContext
    │   └── Migrations/                  # Histórico de banco
    ├── appsettings.json                 # Configurações
    ├── appsettings.Development.json     # Config. desenvolvimento
    ├── Program.cs                       # Configuração da app
    ├── TarefasAPI.csproj                # Projeto C#
    └── Properties/
        └── launchSettings.json          # Configurações launch
```

### Frontend (Angular)

```
frontend/
├── src/
│   ├── app/
│   │   ├── components/
│   │   │   ├── tarefa-list/
│   │   │   │   ├── tarefa-list.component.ts
│   │   │   │   ├── tarefa-list.component.html
│   │   │   │   └── tarefa-list.component.css
│   │   │   ├── tarefa-form/
│   │   │   │   ├── tarefa-form.component.ts
│   │   │   │   ├── tarefa-form.component.html
│   │   │   │   └── tarefa-form.component.css
│   │   │   └── tarefa-item/
│   │   │       ├── tarefa-item.component.ts
│   │   │       ├── tarefa-item.component.html
│   │   │       └── tarefa-item.component.css
│   │   ├── services/
│   │   │   └── tarefa.service.ts       # Serviço API
│   │   ├── models/
│   │   │   └── tarefa.model.ts         # Interface Tarefa
│   │   ├── app.component.ts            # Componente raiz
│   │   ├── app.component.html
│   │   ├── app.module.ts               # Módulo principal
│   │   └── app-routing.module.ts       # Rotas
│   ├── assets/                         # Imagens, ícones
│   ├── styles.css                      # Estilos globais
│   ├── main.ts                         # Entry point
│   └── index.html                      # HTML principal
├── angular.json                        # Configuração Angular
├── package.json                        # Dependências npm
└── tsconfig.json                       # Configuração TypeScript
```

---

## Fluxo de Dados

### 1. Listar Tarefas

```
Usuário clica em "Listar"
         ↓
TarefaListComponent ngOnInit()
         ↓
Chama TarefaService.getTarefas()
         ↓
TarefaService faz GET /api/tarefas
         ↓
ASP.NET retorna JSON com lista
         ↓
Component atualiza template
         ↓
Usuário vê lista na tela
```

### 2. Criar Tarefa

```
Usuário preenche formulário
         ↓
Clica "Salvar"
         ↓
TarefaFormComponent chama onSubmit()
         ↓
Valida dados localmente
         ↓
Chama TarefaService.createTarefa(tarefa)
         ↓
TarefaService faz POST /api/tarefas
         ↓
Controller recebe dados
         ↓
Entity Framework valida
         ↓
Insere no banco de dados
         ↓
Retorna tarefa criada com ID
         ↓
Component atualiza lista
         ↓
Usuário vê confirmação
```

### 3. Editar Tarefa

```
Usuário clica "Editar"
         ↓
TarefaFormComponent carrega dados (getTarefaById)
         ↓
Usuário modifica e clica "Salvar"
         ↓
TarefaService.updateTarefa(id, tarefa)
         ↓
TarefaService faz PUT /api/tarefas/{id}
         ↓
Controller valida ID e dados
         ↓
Entity Framework atualiza no banco
         ↓
Retorna tarefa atualizada
         ↓
Lista é recarregada
         ↓
Usuário vê mudanças
```

### 4. Deletar Tarefa

```
Usuário clica ícone "Deletar"
         ↓
Sistema confirma ação
         ↓
TarefaService.deleteTarefa(id)
         ↓
TarefaService faz DELETE /api/tarefas/{id}
         ↓
Controller encontra tarefa
         ↓
Entity Framework remove do banco
         ↓
Retorna status 204
         ↓
Component remove item da lista
         ↓
Usuário vê item removido
```

---

## Componentes e Responsabilidades

### TarefaListComponent
**Responsabilidade:** Exibir lista de tarefas

- Carrega lista ao inicializar
- Exibe cada tarefa via TarefaItemComponent
- Abre modal/formulário para nova tarefa
- Trata erros de carregamento

```typescript
export class TarefaListComponent implements OnInit {
  tarefas: Tarefa[] = [];
  carregando = true;
  erro: string | null = null;

  constructor(private tarefaService: TarefaService) {}

  ngOnInit() {
    this.carregarTarefas();
  }

  carregarTarefas() {
    this.tarefaService.getTarefas().subscribe(
      (dados) => this.tarefas = dados,
      (erro) => this.erro = erro
    );
  }
}
```

### TarefaFormComponent
**Responsabilidade:** Criar e editar tarefas

- FormGroup com validações
- Submit cria ou atualiza
- Cancel volta à lista

```typescript
export class TarefaFormComponent {
  form: FormGroup;
  tarefaId: number | null = null;

  constructor(
    private fb: FormBuilder,
    private tarefaService: TarefaService
  ) {
    this.form = this.fb.group({
      titulo: ['', Validators.required],
      descricao: [''],
      status: ['Pendente']
    });
  }

  salvar() {
    if (this.form.valid) {
      if (this.tarefaId) {
        this.tarefaService.updateTarefa(this.tarefaId, this.form.value)
          .subscribe(...);
      } else {
        this.tarefaService.createTarefa(this.form.value)
          .subscribe(...);
      }
    }
  }
}
```

### TarefaService
**Responsabilidade:** Comunicação com API

```typescript
export class TarefaService {
  private apiUrl = 'https://localhost:5001/api/tarefas';

  constructor(private http: HttpClient) {}

  getTarefas(): Observable<Tarefa[]> {
    return this.http.get<Tarefa[]>(this.apiUrl);
  }

  getTarefaById(id: number): Observable<Tarefa> {
    return this.http.get<Tarefa>(`${this.apiUrl}/${id}`);
  }

  createTarefa(tarefa: Tarefa): Observable<Tarefa> {
    return this.http.post<Tarefa>(this.apiUrl, tarefa);
  }

  updateTarefa(id: number, tarefa: Tarefa): Observable<Tarefa> {
    return this.http.put<Tarefa>(`${this.apiUrl}/${id}`, tarefa);
  }

  deleteTarefa(id: number): Observable<void> {
    return this.http.delete<void>(`${this.apiUrl}/${id}`);
  }
}
```

### TarefasController
**Responsabilidade:** Endpoints REST

```csharp
[ApiController]
[Route("api/[controller]")]
public class TarefasController : ControllerBase
{
    private readonly ApplicationDbContext _context;

    [HttpGet]
    public async Task<ActionResult<IEnumerable<Tarefa>>> GetTarefas()
    {
        return await _context.Tarefas.ToListAsync();
    }

    [HttpPost]
    public async Task<ActionResult<Tarefa>> CreateTarefa(Tarefa tarefa)
    {
        tarefa.DataCriacao = DateTime.Now;
        _context.Tarefas.Add(tarefa);
        await _context.SaveChangesAsync();
        return CreatedAtAction("GetTarefa", new { id = tarefa.Id }, tarefa);
    }

    // PUT, DELETE, etc...
}
```

---

## Padrões de Design Utilizados

### 1. Service Pattern (Angular)
- TarefaService encapsula lógica de API
- Componentes não conhecem detalhes de HTTP
- Facilita testes e reutilização

### 2. Dependency Injection
- Angular injeta TarefaService nos componentes
- ASP.NET Core injeta DbContext no controller
- Aumenta testabilidade

### 3. RESTful API
- Recursos representa entidades (tarefas)
- Métodos HTTP indicam operações
- URLs previsíveis

### 4. ORM (Entity Framework)
- LINQ para queries
- Migrações para versionamento do banco
- Abstrai detalhes de SQL

---

## Fluxo de Compilação e Execução

### Backend

```
Fonte C# (.cs)
    ↓
Compilação .NET (.dll)
    ↓
Runtime ASP.NET Core
    ↓
Servidor HTTP (Port 5001)
```

### Frontend

```
Fonte TypeScript (.ts)
    ↓
Compilação TypeScript < JavaScript
    ↓
Bundling e Minificação
    ↓
Servidor Dev Angular (Port 4200)
    ↓ (ng build --prod)
    Arquivos estáticos (HTML, CSS, JS)
```

---

## Comunicação HTTP

### Request/Response Cycle

```
1. Browser faz requisição HTTP
   GET /api/tarefas
   
2. Servidor ASP.NET recebe
   HttpGet("/api/tarefas")
   
3. Controller processa
   return await _context.Tarefas.ToListAsync();
   
4. Entity Framework query banco
   SELECT * FROM Tarefas
   
5. Banco retorna dados
   [Tarefa1, Tarefa2, ...]
   
6. Controller serializa JSON
   [{"id": 1, "titulo": "..."}]
   
7. Servidor envia response HTTP 200
   
8. Browser deserializa JSON
   JSON → Objeto Tarefa
   
9. Angular atualiza componente
   this.tarefas = dados
   
10. Template re-renderiza
    <div>{{ tarefa.titulo }}</div>
```

---

## Tratamento de Erros

### Frontend
```typescript
this.tarefaService.getTarefas().subscribe(
  (dados) => {
    this.tarefas = dados;
    this.carregando = false;
  },
  (erro) => {
    console.error('Erro:', erro);
    this.erro = 'Falha ao carregar tarefas';
    this.carregando = false;
  }
);
```

### Backend
```csharp
try
{
    var tarefa = await _context.Tarefas.FindAsync(id);
    if (tarefa == null)
        return NotFound(new { message = "Tarefa não encontrada" });
    
    return Ok(tarefa);
}
catch (Exception ex)
{
    return StatusCode(500, new { message = "Erro interno" });
}
```

---

## Segurança (Fora do escopo, mas importante conhecer)

- ❌ Sem autenticação (projeto júnior)
- ⚠️ CORS habilitado para localhost
- ⚠️ Sem validação server-side completa
- ⚠️ Sem encryption de conexão (dev apenas)

---

## Performance

- **GET /api/tarefas** - Retorna lista completa
  - Otimização: Paginação (futuro)
  
- **Entity Framework** - Lazy loading
  - Otimização: Select() apenas campos necessários
  
- **Angular** - Change detection OnPush
  - Otimização: Virtual scroll para listas grandes

---

**Data:** 08/04/2026  
**Versão:** 1.0
