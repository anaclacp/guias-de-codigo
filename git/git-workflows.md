# Git Workflows - Estratégias de Branching

Este guia explica as principais estratégias de workflow com Git, focando no **GitFlow** e outras abordagens populares para organizar o desenvolvimento em equipa.

## 1. GitFlow: O Workflow Clássico

O **GitFlow** é uma estratégia de branching que define papéis específicos para diferentes tipos de branches e como elas interagem entre si.

### Estrutura das Branches

```
Master (main)    ──●──────●────────────────●──
                   │  v1.0 │              v1.2
                   │       │                 ↑
Hotfix         ────●───────┼─────────────────┤
                   │       │                 │
                   ↓       │                 │
Develop        ────●───────●─────●───────────●──
                   │           ↗ │       ↗
                   │         ↙   │     ↙
Feature        ────●───●───●─────●───●────────
```

### Tipos de Branch

#### 🏠 **Master/Main Branch**
- **Propósito:** Código sempre estável e pronto para produção
- **Proteção:** Nunca commitar diretamente aqui
- **Tags:** Cada commit representa uma versão release (v1.0, v1.1, v1.2)
- **Deploy:** Sempre deployable

```bash
# Master só recebe merges de Release ou Hotfix
git checkout main
git merge --no-ff release-v1.2
git tag v1.2
```

#### 🚧 **Develop Branch**
- **Propósito:** Branch de integração para desenvolvimento
- **Origem:** Criada a partir da master
- **Destino:** Features fazem merge aqui
- **Estado:** Pode estar instável durante desenvolvimento

```bash
# Criar develop a partir da master
git checkout main
git checkout -b develop

# Features fazem merge para develop
git checkout develop
git merge --no-ff feature-login
```

#### ✨ **Feature Branches**
- **Naming:** `feature/nome-da-funcionalidade` ou `feature-nome`
- **Origem:** Sempre criadas a partir da `develop`
- **Destino:** Merge de volta para `develop`
- **Ciclo de vida:** Apagadas após merge

```bash
# Criar feature branch
git checkout develop
git checkout -b feature/payment-integration

# Desenvolver...
git add .
git commit -m "feat: adiciona integração de pagamentos"

# Finalizar feature
git checkout develop
git merge --no-ff feature/payment-integration
git branch -d feature/payment-integration
```

#### 🚀 **Release Branches**
- **Naming:** `release/v1.2` ou `release-v1.2`
- **Propósito:** Preparar nova versão para produção
- **Origem:** Criada a partir da `develop`
- **Destino:** Merge para `main` E `develop`

```bash
# Criar release branch
git checkout develop
git checkout -b release/v1.2

# Correções finais, ajustes de versão
git commit -m "chore: bump version to 1.2.0"
git commit -m "fix: corrige bug menor antes do release"

# Finalizar release
git checkout main
git merge --no-ff release/v1.2
git tag v1.2

git checkout develop
git merge --no-ff release/v1.2

git branch -d release/v1.2
```

#### 🚨 **Hotfix Branches**
- **Naming:** `hotfix/v1.1.1` ou `hotfix-critical-bug`
- **Propósito:** Correções urgentes em produção
- **Origem:** Criada a partir da `main`
- **Destino:** Merge para `main` E `develop`

```bash
# Bug crítico em produção!
git checkout main
git checkout -b hotfix/v1.1.1

# Correção rápida
git commit -m "fix: resolve vulnerabilidade crítica de segurança"

# Deploy urgente
git checkout main
git merge --no-ff hotfix/v1.1.1
git tag v1.1.1

# Atualizar develop também
git checkout develop
git merge --no-ff hotfix/v1.1.1

git branch -d hotfix/v1.1.1
```

## 2. Workflow Completo: Exemplo Prático

### Cenário: Desenvolver Sistema de Pagamentos

```bash
# 1. Criar feature
git checkout develop
git checkout -b feature/payment-system

# 2. Desenvolver (vários commits)
git commit -m "feat: estrutura básica do payment service"
git commit -m "feat: integração com Stripe API"
git commit -m "test: adiciona testes para payment service"
git commit -m "feat: finaliza sistema de pagamentos"

# 3. Finalizar feature
git checkout develop
git merge --no-ff feature/payment-system
git branch -d feature/payment-system

# 4. Preparar release
git checkout -b release/v2.0

# 5. Ajustes finais
git commit -m "chore: atualiza versão para 2.0.0"
git commit -m "docs: atualiza changelog"

# 6. Release para produção
git checkout main
git merge --no-ff release/v2.0
git tag v2.0

git checkout develop
git merge --no-ff release/v2.0
git branch -d release/v2.0

# 7. BUG CRÍTICO descoberto!
git checkout main
git checkout -b hotfix/v2.0.1

git commit -m "fix: corrige falha crítica no processamento de pagamentos"

# 8. Deploy urgente
git checkout main
git merge --no-ff hotfix/v2.0.1
git tag v2.0.1

git checkout develop
git merge --no-ff hotfix/v2.0.1
git branch -d hotfix/v2.0.1
```

## 3. Outras Estratégias Populares

### GitHub Flow (Mais Simples)

**Estrutura:**
- Apenas `main` + `feature branches`
- Deploy contínuo
- Pull Requests obrigatórios

```bash
# Criar feature
git checkout main
git checkout -b feature-user-auth

# Desenvolver e push
git push -u origin feature-user-auth

# Pull Request → Review → Merge → Deploy automático
```

**Quando usar:**
- Equipas pequenas
- Deploy contínuo
- Projetos web simples

### GitLab Flow

**Estrutura:**
- `main` → `pre-production` → `production`
- Environment branches
- Merge requests entre environments

**Quando usar:**
- Múltiplos ambientes
- Processo de release mais controlado
- Necessidade de testing em staging

## 4. Comandos Úteis para GitFlow

### Configuração Inicial

```bash
# Inicializar GitFlow (com git-flow extension)
git flow init

# Ou manualmente
git checkout main
git checkout -b develop
git push -u origin develop
```

### Automatização com Git-Flow Extension

```bash
# Instalar git-flow
# Ubuntu: sudo apt-get install git-flow
# Mac: brew install git-flow

# Comandos simplificados
git flow feature start payment-system
git flow feature finish payment-system

git flow release start v1.2
git flow release finish v1.2

git flow hotfix start v1.1.1
git flow hotfix finish v1.1.1
```

### Scripts de Automação

```bash
# script: new-feature.sh
#!/bin/bash
FEATURE_NAME=$1
git checkout develop
git pull origin develop
git checkout -b feature/$FEATURE_NAME
echo "Feature branch feature/$FEATURE_NAME criada!"

# script: finish-feature.sh
#!/bin/bash
CURRENT_BRANCH=$(git branch --show-current)
git checkout develop
git merge --no-ff $CURRENT_BRANCH
git branch -d $CURRENT_BRANCH
git push origin develop
echo "Feature $CURRENT_BRANCH finalizada!"
```

## 5. Boas Práticas

### ✅ Do's

- **Proteger branches principais** (main, develop)
- **Usar Pull/Merge Requests** para code review
- **Testes automatizados** antes de merge
- **Conventional Commits** para histórico limpo
- **Tags para releases** com versionamento semântico
- **CI/CD pipelines** para automação

### ❌ Don'ts

- **Never commit diretamente na main**
- **Never fazer rebase de branches públicas**
- **Never misturar tipos de mudança** numa feature
- **Never deixar features branches muito tempo sem merge**
- **Never fazer hotfix sem atualizar develop**

## 6. Configuração de Proteção no GitHub

```json
// .github/branch-protection.json
{
  "required_status_checks": {
    "strict": true,
    "contexts": ["continuous-integration"]
  },
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "required_approving_review_count": 2,
    "dismiss_stale_reviews": true
  },
  "restrictions": null
}
```

## 7. Quando Usar Cada Strategy

### GitFlow é ideal para:
- **Produtos com releases programadas**
- **Equipas grandes** (5+ desenvolvedores)
- **Projetos complexos** com múltiplas features em paralelo
- **Necessidade de hotfixes** frequentes
- **Ambientes de staging/production** bem definidos

### GitHub Flow é ideal para:
- **Deploy contínuo**
- **Equipas pequenas** (2-4 desenvolvedores)
- **Projetos web** com releases frequentes
- **Startups** com desenvolvimento ágil
- **Open source projects**

### Resumo Visual

```
Complexidade do Projeto
         ↑
GitFlow  |  ████████████
         |  
Mixed    |      ██████
         |      
GitHub   |  ████
Flow     |________________→
         Frequência de Deploy
```

O importante é escolher a estratégia que melhor se adapta à tua equipa e projeto. Começa simples e evolui conforme a necessidade!
