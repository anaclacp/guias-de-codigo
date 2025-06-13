# Guia de CI/CD: Automatizando seu Fluxo de Trabalho com GitHub Actions

Escrever código e testes é fundamental, mas como garantir que cada nova alteração seja testada e entregue de forma rápida e segura? A resposta está em CI/CD (Integração Contínua e Entrega/Implantação Contínua).

Este guia explica o que é CI/CD e como você pode implementar um fluxo básico usando GitHub Actions.

## 1. O que é CI/CD?

CI/CD é uma filosofia e um conjunto de práticas que visam automatizar o ciclo de vida do desenvolvimento de software.

### CI - Continuous Integration (Integração Contínua)

**A Ideia**: Automatizar a integração e o teste do código sempre que um desenvolvedor envia uma alteração para o repositório.  
**O que faz**: A cada `git push`, um servidor de automação (como o GitHub Actions) é acionado para:

- Fazer o "build" da aplicação (compilar, se necessário).
- Executar todos os testes automatizados (unitários, de integração, etc.).
- Fornecer um feedback rápido se a alteração quebrou alguma coisa.

**Benefício Principal**: Encontrar e corrigir bugs muito mais cedo, evitando o "inferno da integração" onde todos tentam juntar o seu trabalho no final.

### CD - Continuous Delivery/Deployment (Entrega/Implantação Contínua)

**A Ideia**: Automatizar a entrega do software para os ambientes de produção ou de testes.

**Qual a diferença?**

- **Continuous Delivery (Entrega Contínua)**: Após os testes da CI passarem, a aplicação é automaticamente "empacotada" e preparada para o deploy. O deploy para produção é feito com um passo manual (um clique de botão).
- **Continuous Deployment (Implantação Contínua)**: É o passo seguinte. Se todos os testes passarem, a nova versão é automaticamente enviada para produção sem qualquer intervenção humana.

**Benefício Principal**: Lançar novas funcionalidades e correções de forma rápida, fiável e com menos risco.

## 2. A Ferramenta: GitHub Actions

GitHub Actions é a plataforma de CI/CD integrada diretamente no GitHub. É extremamente poderosa e tem um plano gratuito generoso, tornando-a perfeita para a maioria dos projetos.

**Workflows**: Você define a sua automação em ficheiros de texto no formato YAML.  
**Localização**: Estes ficheiros ficam dentro de uma pasta especial no seu repositório: `.github/workflows/`.

## 3. Exemplo Prático: Um Workflow de CI para uma API em Python

Vamos criar um workflow simples que, a cada push para a branch `main`, irá instalar as dependências e executar os testes da sua aplicação FastAPI.

### Passo 1: Crie a Estrutura de Pastas

No seu projeto, crie a seguinte estrutura de pastas e o ficheiro:

seu-projeto/
├── .github/
│ └── workflows/
│ └── ci.yml
└── ... (resto do seu código)


### Passo 2: Adicione o Conteúdo ao `ci.yml`

Copie e cole o seguinte código no seu ficheiro `ci.yml`:

```yaml
# Nome do seu workflow, que aparecerá na aba "Actions" do GitHub
name: CI para Aplicação Python

# Define o gatilho: quando este workflow deve rodar?
on:
  push:
    branches: [ "main" ] # Roda a cada push na branch main
  pull_request:
    branches: [ "main" ] # Roda também quando um Pull Request é aberto para a main

# Define os "trabalhos" (jobs) que serão executados
jobs:
  build-and-test:
    # Define a máquina virtual onde o trabalho vai rodar
    runs-on: ubuntu-latest

    # Define os passos que serão executados sequencialmente
    steps:
      # 1. Faz o "checkout" do seu código do repositório para a máquina virtual
      - name: Checkout do código
        uses: actions/checkout@v3

      # 2. Configura o ambiente Python na versão desejada
      - name: Configurar Python 3.10
        uses: actions/setup-python@v3
        with:
          python-version: "3.10"

      # 3. Instala as dependências do projeto
      - name: Instalar dependências
        run: |
          python -m pip install --upgrade pip
          if [ -f requirements.txt ]; then pip install -r requirements.txt; fi

      # 4. Executa os testes unitários (assumindo que você usa pytest)
      - name: Executar os testes
        run: |
          pip install pytest # Garante que o pytest está instalado
          pytest
```

### Passo 3: Envie para o GitHub
Faça o commit e o push deste novo ficheiro para o seu repositório.

```
git add .github/workflows/ci.yml
git commit -m "chore: adiciona workflow de CI com GitHub Actions"
git push
```

Agora, vá à aba "Actions" do seu repositório no GitHub. Você verá o seu workflow a ser executado automaticamente! Se tudo estiver correto, todos os passos ficarão verdes. Se um teste falhar, o passo correspondente ficará vermelho, e você será notificado.
