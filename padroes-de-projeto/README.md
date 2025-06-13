# Guia Introdutório de Padrões de Projeto e Arquitetura

Escrever código que funciona é apenas o começo. O verdadeiro desafio é criar software que seja fácil de manter, estender e dar para outras pessoas entenderem. É aqui que entram os princípios de arquitetura e os padrões de projeto: eles são o mapa para construir software de alta qualidade.

## 1. O Fundamento: Princípios SOLID

**SOLID** é um acrónimo que representa cinco princípios fundamentais de design de software orientado a objetos. Segui-los ajuda a evitar código "esparguete" e a criar sistemas mais robustos.

### S - Single Responsibility Principle (Princípio da Responsabilidade Única)

**A Ideia**: Uma classe deve ter apenas um motivo para mudar.  
**Tradução**: Cada classe deve ter apenas uma responsabilidade ou trabalho principal. Não crie "super classes" que fazem de tudo um pouco (como conectar ao banco de dados, validar dados e gerar relatórios).  
**Exemplo Simples**: Em vez de uma classe `Relatorio` que gera e envia o relatório por email, crie duas classes: uma `GeradorDeRelatorio` e outra `EnviadorDeEmail`.

### O - Open/Closed Principle (Princípio Aberto/Fechado)

**A Ideia**: O software deve ser aberto para extensão, mas fechado para modificação.  
**Tradução**: Você deve ser capaz de adicionar novas funcionalidades sem alterar o código fonte que já existe e funciona.  
**Exemplo Simples**: Se você tem uma função que calcula o preço total de uma compra, em vez de a modificar com um `if` sempre que um novo tipo de desconto aparece, você pode passar uma lista de "regras de desconto" que podem ser adicionadas sem alterar a função de cálculo principal.

### L - Liskov Substitution Principle (Princípio da Substituição de Liskov)

**A Ideia**: Subtipos devem ser substituíveis por seus tipos base sem quebrar o sistema.  
**Tradução**: Se você tem uma classe `Ave`, e uma classe `Pato` que herda de `Ave`, você deve poder usar um objeto `Pato` em qualquer lugar onde um objeto `Ave` é esperado, sem que o programa quebre. Isso soa óbvio, mas é violado com frequência. O exemplo clássico é o `Pinguim`: um pinguim é uma ave, mas não voa. Se sua classe `Ave` tem um método `voar()`, o `Pinguim` quebra este princípio.

### I - Interface Segregation Principle (Princípio da Segregação de Interface)

**A Ideia**: Os clientes não devem ser forçados a depender de interfaces que não utilizam.  
**Tradução**: É melhor ter várias interfaces pequenas e específicas do que uma única interface grande e genérica. Não obrigue uma classe a implementar métodos que ela não precisa.  
**Exemplo Simples**: Em vez de uma interface "Trabalhador" com os métodos `trabalhar()` e `almocar()`, talvez seja melhor ter duas interfaces separadas, pois um `RoboTrabalhador` poderia implementar `trabalhar()`, mas não `almocar()`.

### D - Dependency Inversion Principle (Princípio da Inversão de Dependência)

**A Ideia**: Módulos de alto nível não devem depender de módulos de baixo nível. Ambos devem depender de abstrações.  
**Tradução**: O seu código principal não deve depender de detalhes de implementação concretos. Por exemplo, a sua lógica de negócio não deve saber se os dados estão a ser guardados num banco de dados MySQL ou num ficheiro de texto. Ela deve depender de uma interface (uma "abstração"), como `RepositorioDeDados`, e a implementação concreta dessa interface é que lida com os detalhes.

## 2. As Receitas: Padrões de Projeto (Design Patterns)

Padrões de projeto são soluções testadas e comprovadas para problemas recorrentes no design de software. Eles são como receitas de culinária para programadores.

### Factory Method (Método Fábrica)

**Problema**: Você precisa de criar objetos, mas não quer que o seu código principal fique acoplado às classes concretas desses objetos.  
**Solução**: Crie uma "fábrica" (uma classe ou método) cujo único trabalho é criar e retornar objetos para você. O seu código principal apenas pede um objeto à fábrica, sem precisar de saber como ele foi construído.  
**Exemplo**: Num jogo, uma `FabricaDeInimigos` que pode criar diferentes tipos de inimigos (`Zumbi`, `Fantasma`) dependendo do nível do jogo.

### Singleton (Instância Única)

**Problema**: Você precisa de garantir que existe apenas uma única instância de uma classe em toda a aplicação.  
**Solução**: A própria classe controla a sua criação, garantindo que, após a primeira vez, todas as outras chamadas retornem a mesma instância já criada.  
**Exemplo**: Uma classe de `Configuracao` ou uma classe que gere a ligação à base de dados. Você só quer um objeto desses a rodar.

### Observer (Observador)

**Problema**: Você tem um objeto (o "sujeito") que precisa de notificar vários outros objetos (os "observadores") sempre que o seu estado muda, sem que o sujeito precise de saber quem são os observadores.  
**Solução**: Os observadores "assinam" o sujeito. Quando o estado do sujeito muda, ele simplesmente percorre a sua lista de assinantes e chama um método de notificação em cada um deles.  
**Exemplo**: Numa folha de cálculo, quando você muda o valor de uma célula (o sujeito), todos os gráficos e fórmulas que dependem dessa célula (os observadores) são notificados e atualizados automaticamente.

### Decorator (Decorador)

**Problema**: Você quer adicionar novas funcionalidades ou responsabilidades a um objeto dinamicamente, sem alterar a sua classe original.  
**Solução**: Crie uma classe "decoradora" que "embrulha" o objeto original. O decorador tem a mesma interface que o objeto original, mas adiciona o seu próprio comportamento antes ou depois de delegar a chamada para o objeto embrulhado.  
**Exemplo**: Imagine que você tem um objeto que envia uma notificação. Você pode "decorá-lo" com um `NotificadorComLog` para adicionar logging, e depois decorar esse resultado com um `NotificadorComCriptografia` para adicionar segurança, tudo isso sem tocar na classe de notificação original.
