# ✅ Checklist do Projeto Desafio Técnico Avanade 2026

## 📋 Planejamento e Setup (FASE 1)

### Estrutura do Projeto
- [x] Criar pastas: frontend, backend, docs
- [x] Criar README.md principal
- [x] Criar especificações detalhadas
- [x] Criar documento de arquitetura
- [x] Criar guia passo a passo
- [x] Criar .gitignore
- [x] Commit inicial no Git

### Git Flow
- [x] Criar branch `develop`
- [x] Criar documentação Git Flow
- [x] Configurar branches no repositório
- [x] Atualizar README com Git Flow
- [ ] Configurar proteção de branches no GitHub

---

## 🔧 Desenvolvimento Back-end (FASE 2)

### Setup do Projeto
- [ ] Criar projeto ASP.NET Core Web API
- [ ] Instalar dependências NuGet (EF Core, SqlServer)
- [ ] Configurar appsettings.json

### Banco de Dados
- [ ] Criar modelo/entidade Tarefa
- [ ] Criar ApplicationDbContext
- [ ] Criar migração inicial (InitialCreate)
- [ ] Aplicar migração ao banco de dados SQL Server

### API REST
- [ ] Implementar GET /api/tarefas (listar todas)
- [ ] Implementar GET /api/tarefas/{id} (buscar por ID)
- [ ] Implementar POST /api/tarefas (criar nova)
- [ ] Implementar PUT /api/tarefas/{id} (atualizar)
- [ ] Implementar DELETE /api/tarefas/{id} (deletar)

### Testes Backend
- [ ] Testar GET /api/tarefas (lista vazia)
- [ ] Testar POST /api/tarefas (criar)
- [ ] Testar GET /api/tarefas/{id}
- [ ] Testar PUT /api/tarefas/{id}
- [ ] Testar DELETE /api/tarefas/{id}
- [ ] Validar dados no SQL Server

---

## 🎨 Desenvolvimento Front-end (FASE 3)

### Setup Angular
- [ ] Criar projeto Angular
- [ ] Instalar dependências (npm install)
- [ ] Configurar HttpClientModule
- [ ] Configurar FormsModule e ReactiveFormsModule

### Modelos e Serviços
- [ ] Criar interface Tarefa (model)
- [ ] Criar TarefaService com métodos HTTP
  - [ ] getTarefas()
  - [ ] getTarefaById()
  - [ ] createTarefa()
  - [ ] updateTarefa()
  - [ ] deleteTarefa()

### Componentes
- [ ] Criar TarefaListComponent
  - [ ] Carregar tarefas ao inicializar
  - [ ] Exibir lista com tabela
  - [ ] Botão "Nova Tarefa"
  - [ ] Botões editar e deletar
  
- [ ] Criar TarefaFormComponent
  - [ ] Formulário reativo (ReactiveFormsModule)
  - [ ] Validação básica (titulo obrigatório)
  - [ ] Modo criar nova tarefa
  - [ ] Modo editar tarefa existente
  
- [ ] Gerar TarefaItemComponent (opcional)

### Estilização
- [ ] Criar estilos globais (styles.css)
- [ ] Estilizar tabela de tarefas
- [ ] Estilizar formulário
- [ ] Estilizar botões
- [ ] Estilizar mensagens de erro

### Testes Frontend
- [ ] Tela carrega sem erros
- [ ] Lista vazia inicialmente
- [ ] Botão "Nova Tarefa" abre formulário
- [ ] Formulário valida titulo obrigatório
- [ ] Form submit fecha após sucesso

---

## 🔗 Integração (FASE 4)

### Configuração CORS
- [ ] Habilitar CORS no backend
- [ ] Testar requisições do frontend

### Testes End-to-End
- [ ] Criar tarefa via frontend
- [ ] Tarefa aparece na lista
- [ ] Tarefa aparece no banco SQL Server
- [ ] Editar tarefa via frontend
- [ ] Dados atualizados no banco
- [ ] Deletar tarefa via frontend
- [ ] Tarefa removida do banco
- [ ] Filtro por status (opcional)

### Tratamento de Erros
- [ ] Backend: Retorna erros apropriados (404, 400, 500)
- [ ] Frontend: Exibe mensagens de erro
- [ ] Frontend: Mostra loading durante requisições

---

## 📚 Documentação (FASE 5)

### README.md
- [x] Descrição do projeto
- [x] Tecnologias utilizadas
- [x] Pré-requisitos
- [x] Instruções de setup
- [x] Como executar
- [x] Exemplos de requisições
- [ ] Troubleshooting completo

### Documentação Técnica
- [x] ESPECIFICACOES.md - Requisitos detalhados
- [x] ARQUITETURA.md - Diagrama e componentes
- [x] GUIA_DESENVOLVIMENTO.md - Passo a passo
- [ ] API_DOCS.md - Documentação de endpoints

### Código
- [ ] Comentários explicativos
- [ ] Nomes descritivos de variáveis
- [ ] Código limpo e organizado

---

## 🎯 Git e Deploy (FASE 6)

### Branches e Git Flow
- [x] `main` criada
- [x] `develop` criada
- [x] Documentação Git Flow criada
- [ ] Branches protegidas no GitHub (main/develop)
- [ ] Workflow de feature branches validado

### Versionamento
- [ ] Commit: "chore: setup inicial projeto" ✅ PRONTO
- [ ] Todos commits em feature branches (não direto em main/develop)
- [ ] feature/crud-backend - "feat: criar backend CRUD"
- [ ] feature/frontend-angular - "feat: criar frontend angular"
- [ ] release/v1.0.0 - "bump: versão 1.0.0"
- [ ] Tag v1.0.0 criada em main

### Validação Final
- [ ] Código compila sem erros
- [ ] Aplicação executa corretamente
- [ ] CRUD funciona completo
- [ ] Testes manuais passam
- [ ] README tem instruções claras
- [ ] Repositório está público no GitHub

---

## ✨ Funcionalidades Opcionais (BÔNUS)

### Features Adicionais
- [ ] Validação de formulário avançada
- [ ] Mensagens de sucesso/erro com toast
- [ ] Filtro de tarefas por status
- [ ] Ordenação de tarefas
- [ ] Busca de tarefas
- [ ] Paginação
- [ ] Temas claro/escuro
- [ ] Responsividade melhorada
- [ ] Testes unitários
- [ ] Testes de integração

### Melhorias de UX
- [ ] Confirmação antes de deletar
- [ ] Animações ao adicionar/remover
- [ ] Indicador de loading
- [ ] Notificações visuais
- [ ] Atalhos de teclado

---

## 🚀 Qualidade de Código (FASE 7)

### Code Review
- [ ] Usar pedaços de código reutilizável
- [ ] Evitar repetição (DRY)
- [ ] Naming conventions consistentes
- [ ] Código auto-explicativo
- [ ] Pull requests com descrição

### Performance
- [ ] Backend: Queries otimizadas
- [ ] Frontend: Change detection otimizado
- [ ] Compressão de assets

### Segurança
- [ ] Validação server-side
- [ ] Sanitização de input
- [ ] CORS configurado corretamente
- [ ] Sem credentials no código

---

## ❌ O que NÃO será feito (Conforme requisitos)

- ❌ Autenticação / Login
- ❌ Autorização / Permissões
- ❌ Design avançado
- ❌ Arquitetura complexa (DDD, CQRS)
- ❌ Padrões enterprise
- ❌ Multiple databases
- ❌ Microserviços

---

## 📊 Estimativa de Horas

| Fase | Atividade | Estimado | Realizado |
|------|-----------|----------|-----------|
| 1 | Planejamento e Setup | 10h | - |
| 2 | Desenvolvimento Backend | 25h | - |
| 3 | Desenvolvimento Frontend | 25h | - |
| 4 | Integração | 15h | - |
| 5 | Documentação | 5h | - |
| 6 | Testes e Validação | 5h | - |
| 7 | Ajustes Finais | 5h | - |
| **TOTAL** | | **90h** | **-h** |

---

## 📝 Notas importantes

1. **Ordem de desenvolvimento:** Backend primeiro, Frontend depois
2. **Testes:** Testar cada funcionalidade ao terminar
3. **Commits:** Fazer commits frequentes com mensagens descritivas
4. **Banco de dados:** Usar migrations do EF Core
5. **CORS:** Configurar apenas para localhost em desenvolvimento

---

## 🔗 Referências

- [Especificações Detalhadas](docs/ESPECIFICACOES.md)
- [Arquitetura do Sistema](docs/ARQUITETURA.md)
- [Guia Passo a Passo](docs/GUIA_DESENVOLVIMENTO.md)

---

**Última atualização:** 08/04/2026  
**Status:** 🟡 Em Andamento - Fase Planejamento Completa
