# 🌳 Git Flow - Guia de Organização de Branching

## O que é Git Flow?

Git Flow é um modelo de ramificação para Git que facilita o desenvolvimento paralelo, releases e hotfixes. Ele organiza o trabalho em branches específicas com propósitos bem definidos.

---

## 📊 Estrutura de Branches

### Branches Principais (Permanentes)

#### `main`
- **Propósito:** Produção (releases oficiais)
- **Pode criar:** Apenas de `release/*` e `hotfix/*`
- **Protegida:** ✅ Sim - Requer Pull Request
- **Regras:**
  - Sem commits diretos
  - Apenas merges de release e hotfix
  - Cada commit = versão identificada com tag

#### `develop`
- **Propósito:** Homologação/staging (desenvolvimento)
- **Pode criar:** Feature branches
- **Protegida:** ✅ Sim - Requer Pull Request
- **Regras:**
  - Nenhum commit direto (exceto emergências)
  - Recebe merges de `feature/*`
  - Sempre funcional e testado

---

### Branches Temporários (Descartáveis)

#### `feature/*`
- **Propósito:** Desenvolver novas funcionalidades
- **Nomenclatura:** `feature/nome-descritivo`
- **Origem:** Sai de `develop`
- **Destino:** Volta para `develop` via Pull Request
- **Tempo de vida:** 2-5 dias (típico)
- **Deletion:** Deletada após merge em `develop`

**Exemplos:**
```
feature/criar-crud-tarefas
feature/integrar-api-angular
feature/validacao-formulario
feature/filtro-por-status
```

#### `release/*`
- **Propósito:** Preparar nova versão para produção
- **Nomenclatura:** `release/v1.0.0`
- **Origem:** Sai de `develop`
- **Destino:** Merge em `main` + volta para `develop`
- **Atividades:** Apenas bug fixes, bump version, sem features
- **Tags:** Criar tag na merge com `main`

**Exemplos:**
```
release/v1.0.0
release/v1.1.0
release/v2.0.0
```

#### `hotfix/*`
- **Propósito:** Corrigir bugs críticos em produção
- **Nomenclatura:** `hotfix/nome-descritivo-v1.0.1`
- **Origem:** Sai de `main`
- **Destino:** Merge em `main` + `develop`
- **Tags:** Criar tag na merge com `main`

**Exemplos:**
```
hotfix/corrigir-erro-criacao-tarefa-v1.0.1
hotfix/seguranca-sql-injection-v1.0.1
```

---

## 🔄 Fluxo Prático de Trabalho

### 1. Iniciando uma Nova Feature

```bash
# Atualizar develop local
git checkout develop
git pull origin develop

# Criar branch de feature
git checkout -b feature/nome-da-feature

# Trabalhar e fazer commits
git add .
git commit -m "feat: implementar funcionalidade X"
git commit -m "feat: adicionar validação"
```

**Mensagens de commit recomendadas:**
```
feat:     Nova funcionalidade
fix:      Correção de bug
refactor: Refatoração de código
test:     Adição de testes
docs:     Documentação
chore:    Tarefas administrativas
style:    Formatação de código
```

### 2. Preparar Pull Request

```bash
# Sincronizar com develop (pode ter novos commits)
git fetch origin
git rebase origin/develop

# Enviador branch remoto
git push -u origin feature/nome-da-feature
```

### 3. Criar Pull Request (GitHub)

1. Ir ao repositório no GitHub
2. Clicar em "Pull requests" → "New pull request"
3. Comparar `develop` ← `feature/nome-da-feature`
4. Adicionar descrição detalhada:
   ```markdown
   ## Descrição
   Implementar CRUD de tarefas no backend
   
   ## Tipo de mudança
   - [x] Nova funcionalidade
   - [ ] Bug fix
   - [ ] Breaking change
   
   ## Testes realizados
   - [x] GET /api/tarefas
   - [x] POST /api/tarefas
   - [x] PUT /api/tarefas/{id}
   - [x] DELETE /api/tarefas/{id}
   
   ## Checklist
   - [x] Código segue as convenções do projeto
   - [x] Sem console.log ou debug code
   - [x] Testes passam
   ```

### 4. Merge via Pull Request

```bash
# No GitHub:
# - Revisar código
# - Comentar se necessário
# - Clicar "Merge pull request"
# - Selecionar "Squash and merge" ou "Create a merge commit"
```

### 5. Depois do Merge

```bash
# Limpar local
git checkout develop
git pull origin develop

# Deletar branch local
git branch -d feature/nome-da-feature

# Deletar branch remoto (ou GitHub faz automaticamente)
git push origin --delete feature/nome-da-feature
```

---

## 📋 Exemplo Completo: Criar Feature CRUD Backend

### Passo 1: Preparar
```bash
git checkout develop
git pull origin develop
git checkout -b feature/crud-tarefas-backend
```

### Passo 2: Desenvolver
```bash
# Criar projeto, modelos, controllers, etc
# Fazer vários commits

git add backend/
git commit -m "feat: criar estrutura ASP.NET Core"

git add backend/Models/
git commit -m "feat: adicionar modelo Tarefa"

git add backend/Controllers/
git commit -m "feat: implementar endpoints CRUD"
```

### Passo 3: Sincronizar
```bash
git fetch origin
git rebase origin/develop
git push -u origin feature/crud-tarefas-backend
```

### Passo 4: Pull Request
- URL: `base=develop` ← `compare=feature/crud-tarefas-backend`
- Descrição inclui o que foi feito
- Aguarda revisão

### Passo 5: Merge
```bash
# No GitHub, clicar merge
# Depois localmente:

git checkout develop
git pull origin develop
git branch -d feature/crud-tarefas-backend
```

---

## 🚀 Exemplo de Release

### Preparar Release v1.0.0

```bash
# Criar branch de release
git checkout develop
git pull origin develop
git checkout -b release/v1.0.0

# Corrigir bugs menores
git commit -m "fix: corrigir erro de validação"

# Atualizar versão
git commit -m "bump: versão 1.0.0"

# Merge para main
git checkout main
git pull origin main
git merge --no-ff release/v1.0.0
git tag -a v1.0.0 -m "Version 1.0.0"

# Volta para develop
git checkout develop
git merge --no-ff release/v1.0.0

# Deletar branch
git branch -d release/v1.0.0
git push origin --delete release/v1.0.0
```

---

## 🔥 Exemplo de Hotfix

### Corrigir Bug em Produção

```bash
# Criar a partir de main
git checkout main
git pull origin main
git checkout -b hotfix/seguranca-v1.0.1

# Corrigir bug crítico
git commit -m "fix: prevenir SQL injection na busca"

# Merge para main
git checkout main
git merge --no-ff hotfix/seguranca-v1.0.1
git tag -a v1.0.1 -m "Version 1.0.1"

# Merge para develop (para manter sincronizado)
git checkout develop
git merge --no-ff hotfix/seguranca-v1.0.1

# Deletar
git branch -d hotfix/seguracao-v1.0.1
git push origin --delete hotfix/seguranca-v1.0.1
```

---

## 📝 Convenções de Commit

### Formato
```
<tipo>(<escopo>): <assunto>

<corpo>

<rodapé>
```

### Tipos
- **feat**: Nova funcionalidade
- **fix**: Correção de bug
- **refactor**: Refatoração sem alterar comportamento
- **test**: Adição de testes
- **docs**: Documentação
- **style**: Formatação (não afeta código)
- **chore**: Tarefas administrativas (deps, config, etc)
- **ci**: Relacionado a CI/CD

### Exemplos

```bash
# Simples
git commit -m "feat: implementar endpoint GET tarefas"

# Com escopo
git commit -m "feat(api): implementar validação"

# Com descrição detalhada
git commit -m "feat: criar sistema de filtros

- Adicionar filtro por status
- Adicionar filtro por data
- Adicionar sorting"

# Fix
git commit -m "fix(frontend): corrigir bug ao editar tarefa"

# Docs
git commit -m "docs: atualizar README com instruções"
```

---

## 🛡️ Proteção de Branches

### Configurar no GitHub

1. Ir a **Settings** → **Branches**
2. Clicar "Add rule" para `main`
   - ✅ Require a pull request before merging
   - ✅ Require status checks to pass before merging
   - ✅ Require branches to be up to date before merging
   - ✅ Include administrators
   - ✅ Dismiss stale pull request approvals
   - ✅ Require code reviews before merging (at least 1)

3. Crear outra rule para `develop`
   - Similar a `main`, mas menos restritiva

---

## 📊 Estrutura Visual

```
                    release/v1.0.0
                          ↓
develop ←━━━━━━━━━━━━━━━━━┃━━━━━━━━━→ main (v1.0.0)
  ↑                        ↓               ↑
  │                   ┌────┴────┐         │
  └────FeatureBranch←→ │ Testing │        │
       ↓               └──────────┘     hotfix/v1.0.1
     merge                                 ↓
     back                                (v1.0.1)
     
feature/crud-tarefas
feature/frontend-angular
feature/validacao-forms
feature/filtro-status
```

---

## 🎯 Resumo das Regras

| Ação | Branch | Criar de | Descrever |
|------|--------|----------|-----------|
| **Nova Feature** | `feature/nome` | `develop` | Descrever funcionalidade |
| **Release** | `release/vX.Y.Z` | `develop` | Preparar produção |
| **Hotfix** | `hotfix/nome-vX.Y.Z` | `main` | Corrigir urgência |
| **Merge Feature** | PR `develop←feature` | - | Descrever mudanças |
| **Merge Release** | PR `main←release` | - | Release notes |
| **Merge Hotfix** | PR `main←hotfix` | - | Descrição do fix |

---

## ✅ Checklist de Boas Práticas

- [ ] Branch criado a partir do correto (`develop` ou `main`)
- [ ] Nomenclatura seguindo padrão (`feature/`, `release/`, `hotfix/`)
- [ ] Commits com mensagens descritivas
- [ ] Sem commits diretos em `main` ou `develop`
- [ ] Pull Request com descrição clara
- [ ] Código revisado antes de merge
- [ ] Testes passando antes de merge
- [ ] Branch deletado após merge
- [ ] Tag criada em releases (na `main`)

---

## 🔗 Referências

- [Git Flow Original](https://nvie.com/posts/a-successful-git-branching-model/)
- [GitHub Flow (simplificado)](https://guides.github.com/introduction/flow/)
- [Conventional Commits](https://www.conventionalcommits.org/)

---

**Data:** 08/04/2026  
**Versão:** 1.0
