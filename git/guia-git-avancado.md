# Guia Prático de Git: Merge, Cherry-pick, Rebase e Mais

Este guia aborda as operações mais importantes do Git com explicações práticas e exemplos do mundo real.

## 1. Merge: Juntando Branches

O **merge** é a forma mais comum de integrar mudanças de uma branch para outra.

### Tipos de Merge

#### Fast-Forward Merge
Acontece quando não há commits novos na branch de destino:

```bash
# Cenário: main não teve commits novos desde que criou a feature
git checkout main
git merge feature-login

# Resultado: Git simplesmente "avança" o ponteiro da main
```

**Antes:**
```
main:    A---B
              \
feature:       C---D
```

**Depois:**
```
main:    A---B---C---D
```

#### Three-Way Merge
Acontece quando ambas as branches têm commits novos:

```bash
git checkout main
git merge feature-login

# Git cria um commit de merge automaticamente
```

**Antes:**
```
main:    A---B---E---F
              \
feature:       C---D
```

**Depois:**
```
main:    A---B---E---F---G (commit de merge)
              \           /
feature:       C---D------
```

### Merge com Conflitos

Quando o Git não consegue juntar automaticamente:

```bash
git merge feature-payments
# Auto-merging payments.py
# CONFLICT (content): Merge conflict in payments.py
# Automatic merge failed; fix conflicts and then commit the result.

# Resolver conflitos manualmente no ficheiro
# Depois:
git add payments.py
git commit -m "resolve: conflito no sistema de pagamentos"
```

## 2. Rebase: Reescrevendo a História

O **rebase** move ou "reaplica" commits de uma branch sobre outra, criando uma história linear.

### Rebase Básico

```bash
# Em vez de merge, use rebase
git checkout feature-api
git rebase main

# Ou numa linha:
git rebase main feature-api
```

**Antes:**
```
main:    A---B---E---F
              \
feature:       C---D
```

**Depois:**
```
main:    A---B---E---F
                      \
feature:               C'---D' (commits reaplicados)
```

### Rebase Interativo: A Ferramenta Mais Poderosa

O rebase interativo permite editar, combinar, reordenar ou eliminar commits:

```bash
# Editar os últimos 3 commits
git rebase -i HEAD~3

# Ou desde um commit específico
git rebase -i abc1234
```

**Opções no rebase interativo:**
- `pick` - usar o commit como está
- `reword` - alterar a mensagem do commit
- `edit` - parar para editar o commit
- `squash` - combinar com o commit anterior
- `fixup` - como squash, mas descarta a mensagem
- `drop` - eliminar o commit

**Exemplo prático:**
```bash
pick f7f3f6d feat: adiciona login
squash 310154e fix: corrige bug no login
squash a5f4a0d fix: melhora validação
reword 4d4d4d4 feat: sistema completo de autenticação

# Resultado: 3 commits tornam-se 1 commit limpo
```

### Quando Usar Merge vs Rebase

**Use MERGE quando:**
- Trabalha em equipa e quer preservar contexto histórico
- A branch feature é partilhada com outros desenvolvedores
- Quer manter registo de quando features foram integradas

**Use REBASE quando:**
- Quer uma história linear e limpa
- Trabalha sozinho ou em branches privadas
- Quer "limpar" commits antes de fazer merge para main

## 3. Cherry-Pick: Copiando Commits Específicos

O **cherry-pick** copia um commit específico de uma branch para outra.

### Uso Básico

```bash
# Copiar um commit específico
git cherry-pick abc1234

# Copiar múltiplos commits
git cherry-pick abc1234 def5678

# Copiar um range de commits
git cherry-pick abc1234..def5678
```

### Cenários Práticos

#### Hotfix Urgente
```bash
# Cenário: bug crítico corrigido na develop, precisa ir para main
git checkout main
git cherry-pick 9fceb02  # commit com a correção

# Agora main tem a correção sem todas as outras mudanças da develop
```

#### Release Seletiva
```bash
# Só algumas features da develop devem ir para release
git checkout release-v2.1
git cherry-pick a1b2c3d  # feature A aprovada
git cherry-pick e4f5g6h  # feature C aprovada
# feature B fica para próxima release
```

### Cherry-Pick com Conflitos

```bash
git cherry-pick abc1234
# CONFLICT (content): Merge conflict in api.py

# Resolver conflito, depois:
git add api.py
git cherry-pick --continue

# Ou cancelar:
git cherry-pick --abort
```

## 4. Reset: Desfazendo Mudanças

O **reset** move o ponteiro da branch e pode alterar o working directory.

### Tipos de Reset

#### Soft Reset
Mantém mudanças no staging area:
```bash
git reset --soft HEAD~1
# Remove último commit, mas mantém ficheiros modificados prontos para commit
```

#### Mixed Reset (padrão)
Remove do staging, mantém no working directory:
```bash
git reset HEAD~1
# ou
git reset --mixed HEAD~1
# Remove último commit e unstage ficheiros, mas mantém modificações
```

#### Hard Reset ⚠️
Remove tudo - CUIDADO:
```bash
git reset --hard HEAD~1
# Remove último commit E todas as modificações não commitadas
```

### Cenários Práticos

```bash
# Desfazer último commit mas manter mudanças
git reset --soft HEAD~1

# Limpar staging area
git reset

# Voltar para estado específico (PERIGOSO)
git reset --hard abc1234
```

## 5. Stash: Guardando Mudanças Temporariamente

O **stash** guarda mudanças não commitadas para usar depois.

```bash
# Guardar mudanças atuais
git stash

# Guardar com mensagem descritiva
git stash push -m "work in progress na API"

# Ver lista de stashes
git stash list

# Aplicar último stash
git stash pop

# Aplicar stash específico
git stash apply stash@{2}

# Eliminar stash
git stash drop stash@{1}

# Limpar todos os stashes
git stash clear
```

### Cenários Práticos

```bash
# Mudança urgente no meio do desenvolvimento
git stash push -m "feature login meio feita"
git checkout main
git checkout -b hotfix-critical
# fazer correção urgente
git checkout feature-login
git stash pop  # continuar onde parou
```

## 6. Reflog: O "Seguro de Vida" do Git

O **reflog** mantém histórico de todas as mudanças nos ponteiros das branches.

```bash
# Ver histórico de mudanças
git reflog

# Recuperar commit "perdido" após reset hard
git reflog
git reset --hard HEAD@{5}

# Reflog de branch específica
git reflog show feature-api
```

## 7. Workflow Prático: Cenário Real

### Situação: Desenvolver Feature com História Limpa

```bash
# 1. Criar branch para feature
git checkout -b feature-payment-gateway

# 2. Desenvolver com vários commits
git commit -m "wip: inicia integração payment"
git commit -m "fix: corrige config"
git commit -m "feat: adiciona validação"
git commit -m "fix: typo na validação"
git commit -m "feat: finaliza payment gateway"

# 3. Limpar história antes do merge
git rebase -i HEAD~5
# Squash commits relacionados, melhora mensagens

# 4. Atualizar com main
git rebase main

# 5. Merge limpo para main
git checkout main
git merge feature-payment-gateway
```

## 8. Dicas de Ouro

### Aliases Úteis
```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
git config --global alias.visual '!gitk'
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

### Comandos para Emergências

```bash
# Recuperar ficheiro deletado
git checkout HEAD -- ficheiro.py

# Ver quem modificou cada linha
git blame ficheiro.py

# Encontrar commit que introduziu bug
git bisect start
git bisect bad HEAD
git bisect good v1.0

# Desfazer merge (se ainda não fez push)
git reset --merge ORIG_HEAD

# Ver diferenças entre branches
git diff main..feature-branch
```

### Regras de Ouro

1. **Nunca faça rebase de branches públicas** - só em branches privadas
2. **Use reset --hard com muito cuidado** - pode perder trabalho
3. **Commit frequentemente** - pequenos commits são mais fáceis de gerir
4. **Teste antes de fazer push** - especialmente após rebase
5. **Use mensagens de commit descritivas** - o "você do futuro" vai agradecer

## 9. Troubleshooting Comum

### "Detached HEAD"
```bash
# Criar branch no estado atual
git checkout -b nova-branch

# Ou voltar para branch anterior
git checkout main
```

### Merge Conflicts Complexos
```bash
# Ver ferramentas de merge disponíveis
git mergetool

# Abortar merge problemático
git merge --abort

# Ver estado atual
git status
```

### Commits no Branch Errado
```bash
# Mover último commit para outra branch
git checkout branch-correta
git cherry-pick main
git checkout main
git reset --hard HEAD~1
```
