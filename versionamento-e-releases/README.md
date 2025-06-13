# Guia de Versionamento e Releases com SemVer e Git

Quando criamos software, precisamos de uma forma de controlar as versões para que todos (utilizadores e outros desenvolvedores) saibam o que esperar de uma nova atualização. O método mais famoso e utilizado é o **Versionamento Semântico**, ou **SemVer**.

## 1. O que é Versionamento Semântico (SemVer)?

É um padrão simples de 3 números que comunica o tipo de mudança que uma nova versão traz. O formato é:

```
MAJOR.MINOR.PATCH
```

(Ex: 1.2.0)

Cada número tem um significado específico e só deve ser aumentado de acordo com regras claras.

## 2. Decifrando os Números: MAJOR.MINOR.PATCH

Vamos usar uma versão 1.2.0 como exemplo.

### MAJOR (1.2.0)

* **O que é:** Uma mudança **incompatível** com a versão anterior (em inglês, *breaking change*). É algo que quebra a forma como o software funcionava antes.
* **Quando usar:** Você só muda este número quando faz uma alteração que obriga os utilizadores a mudarem a forma como usam o seu programa.
* **Exemplo Prático:** Imagine que no seu webhook.py, você decide mudar o nome do campo "message" para "query_text" no corpo da requisição. Qualquer sistema que chamava a sua API usando "message" iria quebrar. Isso é uma mudança MAJOR. A sua próxima versão seria a 2.0.0.

### MINOR (1.2.0)

* **O que é:** Uma **nova funcionalidade** que é adicionada de forma **compatível** com a versão anterior.
* **Quando usar:** Quando você adiciona algo novo, mas não quebra nada do que já existia.
* **Exemplo Prático:** Adicionar uma nova rota à sua API ou uma nova capacidade a uma função existente sem alterar o comportamento antigo.

### PATCH (1.2.0)

* **O que é:** Uma **correção de bug** que é **compatível** com a versão anterior.
* **Quando usar:** Quando você corrige um comportamento incorreto, mas não adiciona nenhuma funcionalidade nova.
* **Exemplo Prático:** Imagine que você descobre que uma função falha se receber um valor nulo e você adiciona uma verificação para evitar o erro. Isso é uma correção. A sua próxima versão seria a 1.2.1.

## 3. O que é um "Hotfix"?

Um **hotfix** é simplesmente um PATCH feito com **extrema urgência**.

* **Cenário Típico:** Você lança a versão 1.2.0 para produção. Horas depois, um cliente importante descobre um bug crítico que impede o uso de uma funcionalidade principal.

* **Ação:** Você não pode esperar pela próxima release planeada. A sua equipa precisa de:
  1. Identificar o problema rapidamente.
  2. Criar uma branch a partir da versão em produção (ex: hotfix/critical-bug).
  3. Corrigir **apenas** aquele bug específico.
  4. Juntar a correção na main e lançar uma nova versão PATCH imediatamente.

* **Resultado:** A versão 1.2.1 é lançada como um hotfix para resolver o problema crítico.

## Resumo

| Tipo de Mudança        | Versão a Mudar | Exemplo           | Descrição                          |
|------------------------|----------------|-------------------|------------------------------------|
| **Breaking Change**    | MAJOR          | 1.5.2 -> 2.0.0   | Mudança incompatível na API        |
| **Nova Funcionalidade** | MINOR          | 1.5.2 -> 1.6.0   | Adicionou uma nova capacidade      |
| **Correção de Bug**   | PATCH          | 1.5.2 -> 1.5.3   | Corrigiu algo que não funcionava bem |
| **Correção Urgente**  | PATCH          | 1.5.2 -> 1.5.3   | Um **Hotfix** é um Patch urgente  |

## 4. Conventional Commits: Padronizando Mensagens de Commit

Para facilitar a identificação do tipo de mudança e automatizar o versionamento, existe um padrão chamado **Conventional Commits**. É uma convenção para escrever mensagens de commit que se integra perfeitamente com o SemVer.

### Formato Básico

```
<tipo>[escopo opcional]: <descrição>

[corpo opcional]

[rodapé opcional]
```

### Principais Tipos de Commit

| Tag        | Significado                | Impacto na Versão | Exemplo                                    |
|------------|----------------------------|-------------------|--------------------------------------------|
| `feat`     | Nova funcionalidade       | MINOR             | `feat: adiciona endpoint de autenticação`  |
| `fix`      | Correção de bug           | PATCH             | `fix: corrige validação de email nulo`     |
| `docs`     | Mudanças na documentação  | Nenhum            | `docs: atualiza README com novos exemplos` |
| `style`    | Formatação, espaços, etc. | Nenhum            | `style: corrige indentação no main.py`     |
| `refactor` | Refatoração de código     | Nenhum            | `refactor: extrai lógica para utils`       |
| `test`     | Adiciona ou corrige testes| Nenhum            | `test: adiciona testes para webhook API`   |
| `chore`    | Tarefas de manutenção     | Nenhum            | `chore: atualiza dependências`             |
| `perf`     | Melhoria de performance   | PATCH             | `perf: otimiza consulta ao banco de dados` |
| `ci`       | Mudanças no CI/CD         | Nenhum            | `ci: adiciona workflow de deploy`          |
| `build`    | Sistema de build          | Nenhum            | `build: atualiza configuração do Docker`   |
| `revert`   | Reverte commit anterior   | Depende           | `revert: desfaz mudança no endpoint`       |

### Breaking Changes

Para indicar uma **breaking change** (mudança MAJOR), adicione `!` após o tipo ou use `BREAKING CHANGE:` no rodapé:

```bash
# Com exclamação
feat!: muda formato de resposta da API

# Com rodapé
feat: adiciona novo sistema de autenticação

BREAKING CHANGE: remove suporte ao token antigo
```

### Exemplos Práticos de Commits

```bash
# Nova funcionalidade (MINOR)
git commit -m "feat(api): adiciona endpoint para upload de ficheiros"

# Correção de bug (PATCH)
git commit -m "fix(webhook): corrige timeout em requisições longas"

# Breaking change (MAJOR)
git commit -m "feat!: remove compatibilidade com API v1"

# Documentação (sem impacto na versão)
git commit -m "docs: adiciona exemplos de uso da API"

# Hotfix crítico (PATCH)
git commit -m "fix: resolve vulnerabilidade de segurança crítica"
```

## 5. Automatização com Ferramentas

### Semantic Release

Uma ferramenta popular que automatiza todo o processo:

1. **Analisa commits** desde a última release
2. **Determina automaticamente** o próximo número de versão
3. **Gera changelog** baseado nos commits
4. **Cria tags** e releases no GitHub
5. **Publica pacotes** (npm, PyPI, etc.)

### Configuração Básica (.releaserc.json)

```json
{
  "branches": ["main"],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/github"
  ]
}
```

## 6. Workflow Completo: Da Mudança à Release

### Cenário: Adicionando uma Nova Funcionalidade

1. **Desenvolver a funcionalidade** numa branch feature
2. **Commit com conventional commit:**
   ```bash
   git commit -m "feat(api): adiciona validação de dados de entrada"
   ```
3. **Merge para main** via Pull Request
4. **Ferramenta de semantic release:**
   - Detecta o commit `feat`
   - Incrementa versão MINOR: 1.2.0 → 1.3.0
   - Gera changelog automaticamente
   - Cria tag e release no GitHub

### Cenário: Hotfix Crítico

1. **Criar branch hotfix** a partir da versão em produção
2. **Corrigir o bug** rapidamente
3. **Commit:**
   ```bash
   git commit -m "fix: resolve falha crítica no processamento de pagamentos"
   ```
4. **Merge direto para main** e deploy imediato
5. **Versão incrementada:** 1.3.0 → 1.3.1
