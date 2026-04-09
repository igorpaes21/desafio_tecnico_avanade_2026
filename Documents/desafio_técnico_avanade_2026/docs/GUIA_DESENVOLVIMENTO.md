# 📖 Guia Passo a Passo - Desenvolvimento

## Índice

1. [Setup Inicial](#1-setup-inicial)
2. [Desenvolvimento Back-end](#2-desenvolvimento-back-end)
3. [Desenvolvimento Front-end](#3-desenvolvimento-front-end)
4. [Integração](#4-integração)
5. [Testes e Validação](#5-testes-e-validação)

---

## 1. Setup Inicial

### 1.1 Pré-requisitos

Verificar instalação:

```powershell
# .NET SDK
dotnet --version  # Deve ser 6.0 ou superior

# Node.js
node --version
npm --version

# Angular CLI
ng version

# Git
git --version

# SQL Server
sqlcmd -?
```

### 1.2 Estrutura de Pastas

```powershell
cd c:\Users\Latitude 4\Documents\desafio_técnico_avanade_2026

# Pastas já devem estar criadas:
# ├── backend/
# ├── frontend/
# ├── docs/
# └── README.md
```

---

## 2. Desenvolvimento Back-end

### 2.1 Criar Projeto ASP.NET Core

```powershell
cd backend

# Criar Web API
dotnet new webapi -n TarefasAPI
cd TarefasAPI
```

### 2.2 Instalar Dependências NuGet

```powershell
# Entity Framework Core
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools

# Restaurar
dotnet restore
```

### 2.3 Criar Modelo Tarefa

Arquivo: `backend/TarefasAPI/Models/Tarefa.cs`

```csharp
namespace TarefasAPI.Models
{
    public class Tarefa
    {
        public int Id { get; set; }
        public string Titulo { get; set; }
        public string Descricao { get; set; }
        public string Status { get; set; }
        public DateTime DataCriacao { get; set; }
    }
}
```

✅ **Verificação:**
- Arquivo criado em `Models/`
- Propriedades corretas
- Sem erros de compilação

### 2.4 Criar DbContext

Arquivo: `backend/TarefasAPI/Data/ApplicationDbContext.cs`

```csharp
using Microsoft.EntityFrameworkCore;
using TarefasAPI.Models;

namespace TarefasAPI.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        public DbSet<Tarefa> Tarefas { get; set; }
    }
}
```

✅ **Verificação:**
- DbContext nos herda de DbContext
- DbSet<Tarefa> declarado
- Constructor recebe options

### 2.5 Configurar Connection String

Arquivo: `backend/TarefasAPI/appsettings.json`

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=TarefasDB;Trusted_Connection=true;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

⚙️ **Notas:**
- Ajuste `Server` conforme sua máquina
- `Trusted_Connection=true` para autenticação Windows
- `Database=TarefasDB` nome do banco

### 2.6 Configurar Program.cs

Arquivo: `backend/TarefasAPI/Program.cs`

```csharp
using Microsoft.EntityFrameworkCore;
using TarefasAPI.Data;

var builder = WebApplicationBuilder.CreateBuilder(args);

// Add services
builder.Services.AddControllers();
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection")));

// Adicionar CORS para Angular
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAngular",
        builder => builder
            .WithOrigins("http://localhost:4200")
            .AllowAnyMethod()
            .AllowAnyHeader());
});

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure middleware
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseCors("AllowAngular");
app.UseAuthorization();
app.MapControllers();

app.Run();
```

✅ **Verificação:**
- DbContext adicionado
- CORS configurado
- Porta HTTPS habilitada

### 2.7 Criar Migrações

```powershell
cd backend/TarefasAPI

# Criar migração inicial
dotnet ef migrations add InitialCreate

# Aplicar ao banco
dotnet ef database update
```

✅ **Verificação:**
- Pasta `Migrations/` criada
- Banco `TarefasDB` criado no SQL Server
- Tabela `Tarefas` criada

### 2.8 Criar Controller

Arquivo: `backend/TarefasAPI/Controllers/TarefasController.cs`

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore;
using TarefasAPI.Data;
using TarefasAPI.Models;

namespace TarefasAPI.Controllers
{
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
        {
            return await _context.Tarefas.ToListAsync();
        }

        // GET: api/tarefas/5
        [HttpGet("{id}")]
        public async Task<ActionResult<Tarefa>> GetTarefa(int id)
        {
            var tarefa = await _context.Tarefas.FindAsync(id);
            if (tarefa == null)
                return NotFound();
            return tarefa;
        }

        // POST: api/tarefas
        [HttpPost]
        public async Task<ActionResult<Tarefa>> CreateTarefa(Tarefa tarefa)
        {
            tarefa.DataCriacao = DateTime.Now;
            _context.Tarefas.Add(tarefa);
            await _context.SaveChangesAsync();
            return CreatedAtAction("GetTarefa", new { id = tarefa.Id }, tarefa);
        }

        // PUT: api/tarefas/5
        [HttpPut("{id}")]
        public async Task<IActionResult> UpdateTarefa(int id, Tarefa tarefa)
        {
            if (id != tarefa.Id)
                return BadRequest();

            _context.Entry(tarefa).State = EntityState.Modified;

            try
            {
                await _context.SaveChangesAsync();
                return NoContent();
            }
            catch (DbUpdateConcurrencyException)
            {
                if (!TarefaExists(id))
                    return NotFound();
                throw;
            }
        }

        // DELETE: api/tarefas/5
        [HttpDelete("{id}")]
        public async Task<IActionResult> DeleteTarefa(int id)
        {
            var tarefa = await _context.Tarefas.FindAsync(id);
            if (tarefa == null)
                return NotFound();

            _context.Tarefas.Remove(tarefa);
            await _context.SaveChangesAsync();
            return NoContent();
        }

        private bool TarefaExists(int id)
        {
            return _context.Tarefas.Any(e => e.Id == id);
        }
    }
}
```

✅ **Verificação:**
- 5 endpoints implementados
- Erros de validação tratados
- Responses corretos

### 2.9 Testar Back-end

```powershell
cd backend/TarefasAPI

# Executar
dotnet run

# Abrir em outro terminal
# https://localhost:5001/swagger/index.html
```

Testes no Swagger:
- [ ] GET /api/tarefas (deve retornar lista vazia)
- [ ] POST /api/tarefas com dados válidos
- [ ] GET /api/tarefas/{id}
- [ ] PUT /api/tarefas/{id}
- [ ] DELETE /api/tarefas/{id}

---

## 3. Desenvolvimento Front-end

### 3.1 Criar Projeto Angular

```powershell
cd frontend

# Criar novo projeto
ng new . --skip-git

# Instalar dependências
npm install
```

**Responder prompts:**
- Routing? **Y**
- Stylesheet format? **CSS**

### 3.2 Gerar Componentes

```powershell
cd frontend

# Componente lista
ng generate component components/tarefa-list

# Componente formulário
ng generate component components/tarefa-form

# Componente item
ng generate component components/tarefa-item

# Serviço
ng generate service services/tarefa
```

### 3.3 Criar Modelo Tarefa

Arquivo: `frontend/src/app/models/tarefa.model.ts`

```typescript
export interface Tarefa {
  id?: number;
  titulo: string;
  descricao?: string;
  status: string;
  dataCriacao?: Date;
}
```

### 3.4 Criar Serviço

Arquivo: `frontend/src/app/services/tarefa.service.ts`

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Tarefa } from '../models/tarefa.model';

@Injectable({
  providedIn: 'root'
})
export class TarefaService {
  private apiUrl = 'https://localhost:5001/api/tarefas';

  constructor(private http: HttpClient) { }

  getTarefas(): Observable<Tarefa[]> {
    return this.http.get<Tarefa[]>(this.apiUrl);
  }

  getTarefaById(id: number): Observable<Tarefa> {
    return this.http.get<Tarefa>(`${this.apiUrl}/${id}`);
  }

  createTarefa(tarefa: Tarefa): Observable<Tarefa> {
    return this.http.post<Tarefa>(this.apiUrl, tarefa);
  }

  updateTarefa(id: number, tarefa: Tarefa): Observable<any> {
    return this.http.put(`${this.apiUrl}/${id}`, tarefa);
  }

  deleteTarefa(id: number): Observable<any> {
    return this.http.delete(`${this.apiUrl}/${id}`);
  }
}
```

### 3.5 Configurar HttpClientModule

Arquivo: `frontend/src/app/app.module.ts`

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { HttpClientModule } from '@angular/common/http';
import { FormsModule, ReactiveFormsModule } from '@angular/forms';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { TarefaListComponent } from './components/tarefa-list/tarefa-list.component';
import { TarefaFormComponent } from './components/tarefa-form/tarefa-form.component';
import { TarefaItemComponent } from './components/tarefa-item/tarefa-item.component';

@NgModule({
  declarations: [
    AppComponent,
    TarefaListComponent,
    TarefaFormComponent,
    TarefaItemComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    FormsModule,
    ReactiveFormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### 3.6 Implementar Componentes

#### TarefaListComponent

`frontend/src/app/components/tarefa-list/tarefa-list.component.ts`

```typescript
import { Component, OnInit } from '@angular/core';
import { Tarefa } from '../../models/tarefa.model';
import { TarefaService } from '../../services/tarefa.service';

@Component({
  selector: 'app-tarefa-list',
  templateUrl: './tarefa-list.component.html',
  styleUrls: ['./tarefa-list.component.css']
})
export class TarefaListComponent implements OnInit {
  tarefas: Tarefa[] = [];
  carregando = false;
  erro: string | null = null;
  showForm = false;
  tarefaEmEdicao: Tarefa | null = null;

  constructor(private tarefaService: TarefaService) { }

  ngOnInit(): void {
    this.carregarTarefas();
  }

  carregarTarefas(): void {
    this.carregando = true;
    this.erro = null;
    this.tarefaService.getTarefas().subscribe(
      (dados) => {
        this.tarefas = dados;
        this.carregando = false;
      },
      (erro) => {
        this.erro = 'Erro ao carregar tarefas';
        this.carregando = false;
        console.error(erro);
      }
    );
  }

  novasTarefa(): void {
    this.tarefaEmEdicao = null;
    this.showForm = true;
  }

  editarTarefa(tarefa: Tarefa): void {
    this.tarefaEmEdicao = tarefa;
    this.showForm = true;
  }

  deletarTarefa(id: number): void {
    if (confirm('Deseja realmente deletar?')) {
      this.tarefaService.deleteTarefa(id).subscribe(
        () => this.carregarTarefas(),
        (erro) => alert('Erro ao deletar')
      );
    }
  }

  onFormSubmit(): void {
    this.showForm = false;
    this.carregarTarefas();
  }

  onFormCancel(): void {
    this.showForm = false;
  }
}
```

`frontend/src/app/components/tarefa-list/tarefa-list.component.html`

```html
<div class="container">
  <h1>Gerenciador de Tarefas</h1>

  <button (click)="novasTarefa()" class="btn-novo">+ Nova Tarefa</button>

  <div *ngIf="carregando" class="loading">Carregando...</div>
  <div *ngIf="erro" class="erro">{{ erro }}</div>

  <app-tarefa-form
    *ngIf="showForm"
    [tarefa]="tarefaEmEdicao"
    (onSubmit)="onFormSubmit()"
    (onCancel)="onFormCancel()">
  </app-tarefa-form>

  <table class="tabela-tarefas" *ngIf="!showForm">
    <thead>
      <tr>
        <th>ID</th>
        <th>Título</th>
        <th>Descrição</th>
        <th>Status</th>
        <th>Ações</th>
      </tr>
    </thead>
    <tbody>
      <tr *ngFor="let tarefa of tarefas">
        <td>{{ tarefa.id }}</td>
        <td>{{ tarefa.titulo }}</td>
        <td>{{ tarefa.descricao }}</td>
        <td>{{ tarefa.status }}</td>
        <td>
          <button (click)="editarTarefa(tarefa)" class="btn-editar">Editar</button>
          <button (click)="deletarTarefa(tarefa.id)" class="btn-deletar">Deletar</button>
        </td>
      </tr>
    </tbody>
  </table>

  <div *ngIf="tarefas.length === 0 && !carregando && !showForm" class="vazio">
    Nenhuma tarefa cadastrada
  </div>
</div>
```

#### TarefaFormComponent

`frontend/src/app/components/tarefa-form/tarefa-form.component.ts`

```typescript
import { Component, Input, Output, EventEmitter, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';
import { Tarefa } from '../../models/tarefa.model';
import { TarefaService } from '../../services/tarefa.service';

@Component({
  selector: 'app-tarefa-form',
  templateUrl: './tarefa-form.component.html',
  styleUrls: ['./tarefa-form.component.css']
})
export class TarefaFormComponent implements OnInit {
  @Input() tarefa: Tarefa | null = null;
  @Output() onSubmit = new EventEmitter<void>();
  @Output() onCancel = new EventEmitter<void>();

  form!: FormGroup;
  enviando = false;
  erro: string | null = null;

  constructor(
    private fb: FormBuilder,
    private tarefaService: TarefaService
  ) { }

  ngOnInit(): void {
    this.criarFormulario();
    if (this.tarefa) {
      this.form.patchValue(this.tarefa);
    }
  }

  criarFormulario(): void {
    this.form = this.fb.group({
      titulo: ['', Validators.required],
      descricao: [''],
      status: ['Pendente']
    });
  }

  salvar(): void {
    if (this.form.invalid) {
      this.erro = 'Preencha todos os campos obrigatórios';
      return;
    }

    this.enviando = true;
    this.erro = null;

    if (this.tarefa?.id) {
      this.tarefaService.updateTarefa(this.tarefa.id, this.form.value)
        .subscribe(
          () => {
            this.onSubmit.emit();
          },
          (erro) => {
            this.erro = 'Erro ao atualizar tarefa';
            this.enviando = false;
          }
        );
    } else {
      this.tarefaService.createTarefa(this.form.value)
        .subscribe(
          () => {
            this.onSubmit.emit();
          },
          (erro) => {
            this.erro = 'Erro ao criar tarefa';
            this.enviando = false;
          }
        );
    }
  }

  cancelar(): void {
    this.onCancel.emit();
  }
}
```

`frontend/src/app/components/tarefa-form/tarefa-form.component.html`

```html
<div class="form-container">
  <h2>{{ tarefa ? 'Editar Tarefa' : 'Nova Tarefa' }}</h2>

  <div *ngIf="erro" class="erro">{{ erro }}</div>

  <form [formGroup]="form" (ngSubmit)="salvar()">
    <div class="form-group">
      <label for="titulo">Título *</label>
      <input type="text" id="titulo" formControlName="titulo" placeholder="Digite o título">
      <span *ngIf="form.get('titulo')?.hasError('required')" class="erro-campo">
        Campo obrigatório
      </span>
    </div>

    <div class="form-group">
      <label for="descricao">Descrição</label>
      <textarea id="descricao" formControlName="descricao" placeholder="Digite a descrição"></textarea>
    </div>

    <div class="form-group">
      <label for="status">Status</label>
      <select id="status" formControlName="status">
        <option>Pendente</option>
        <option>Concluída</option>
      </select>
    </div>

    <div class="form-actions">
      <button type="submit" [disabled]="enviando" class="btn-salvar">
        {{ enviando ? 'Salvando...' : 'Salvar' }}
      </button>
      <button type="button" (click)="cancelar()" class="btn-cancelar">Cancelar</button>
    </div>
  </form>
</div>
```

### 3.7 Estilizar Aplicação

`frontend/src/styles.css`

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  padding: 20px;
}

.container {
  max-width: 1000px;
  margin: 0 auto;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

h1 {
  color: #333;
  margin-bottom: 20px;
}

.btn-novo {
  background-color: #4CAF50;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-bottom: 20px;
  font-size: 14px;
}

.btn-novo:hover {
  background-color: #45a049;
}

.btn-editar {
  background-color: #2196F3;
  color: white;
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-right: 5px;
}

.btn-deletar {
  background-color: #f44336;
  color: white;
  padding: 5px 10px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.tabela-tarefas {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.tabela-tarefas th, .tabela-tarefas td {
  border: 1px solid #ddd;
  padding: 12px;
  text-align: left;
}

.tabela-tarefas th {
  background-color: #f2f2f2;
  font-weight: bold;
}

.tabela-tarefas tr:hover {
  background-color: #f9f9f9;
}

.form-container {
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 4px;
  margin-top: 20px;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.form-group input,
.form-group textarea,
.form-group select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-family: Arial, sans-serif;
}

.form-actions {
  margin-top: 20px;
  display: flex;
  gap: 10px;
}

.btn-salvar {
  background-color: #4CAF50;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-cancelar {
  background-color: #ccc;
  color: #333;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.erro {
  background-color: #ffebee;
  color: #c62828;
  padding: 10px;
  border-radius: 4px;
  margin-bottom: 15px;
}

.erro-campo {
  color: #f44336;
  font-size: 12px;
}

.loading {
  text-align: center;
  padding: 20px;
  color: #666;
}

.vazio {
  text-align: center;
  padding: 20px;
  color: #999;
  font-style: italic;
}
```

### 3.8 Testar Front-end

```powershell
cd frontend

# Executar
ng serve

# Abrir navegador
# http://localhost:4200
```

✅ **Verificações:**
- [ ] Tela carrega sem erros
- [ ] Lista de tarefas aparece (vazia inicialmente)
- [ ] Botão "Nova Tarefa" funciona
- [ ] Formulário abre

---

## 4. Integração

### 4.1 Verificar Backend Rodando

```powershell
# Terminal 1 - Backend
cd backend/TarefasAPI
dotnet run

# Deve exibir:
# info: Microsoft.Hosting.Lifetime[14]
#       Now listening on: https://localhost:5001
```

### 4.2 Verificar Frontend Rodando

```powershell
# Terminal 2 - Frontend
cd frontend
ng serve

# Deve exibir:
# ✔ Compiled successfully.
# ✔ Compiled successfully with warnings.
```

### 4.3 Testar Full Stack

1. Abrir `http://localhost:4200`
2. Clicar "Nova Tarefa"
3. Preencher formulário
4. Clicar "Salvar"
5. ✅ Tarefa aparece na lista

### 4.4 Verificar Dados no Banco

```powershell
# SQL Server Management Studio
# Query:
SELECT * FROM TarefasDB.dbo.Tarefas

# Deve listar as tarefas criadas
```

---

## 5. Testes e Validação

### 5.1 Checklist de Funcionalidades

- [ ] **Criar**: Preencher form + salvar = tarefa na lista
- [ ] **Listar**: Page load automaticamente lista tarefas
- [ ] **Editar**: Clicar editar + modificar + salvar = lista atualiza
- [ ] **Deletar**: Clicar deletar + confirmar = tarefa sai da lista
- [ ] **Validação**: Campo obrigatório mostra erro

### 5.2 Testar com Postman

```
GET https://localhost:5001/api/tarefas
POST https://localhost:5001/api/tarefas
PUT https://localhost:5001/api/tarefas/1
DELETE https://localhost:5001/api/tarefas/1
```

### 5.3 Verificar Dados

```sql
-- SQL Server
SELECT * FROM TarefasDB.dbo.Tarefas;
DELETE FROM TarefasDB.dbo.Tarefas;  -- Limpar se necessário
```

### 5.4 Commits Git

```powershell
git add .
git commit -m "feat: criar backend com CRUD"
git commit -m "feat: criar frontend com componentes"
git commit -m "feat: integrar front com back"
```

---

## 🎯 Próximos Passos

1. ✅ Backend funcionando
2. ✅ Frontend funcionando
3. ✅ Integração completa
4. ⏳ Melhorias opcionais:
   - [ ] Filtro por status
   - [ ] Validação mais robusta
   - [ ] Mensagens toast
   - [ ] Paginação
   - [ ] Busca

---

**Dúvidas?** Consultar `ARQUITETURA.md` ou `ESPECIFICACOES.md`

