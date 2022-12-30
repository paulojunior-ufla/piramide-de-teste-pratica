# A Pirâmide de Teste Prática

Esta é uma tradução do artigo [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader), originalmente escrito por Ham Vocke.

[numOfTranslatedSections]: 12
[amountOfSections]: 34

![35%](https://progress-bar.dev/35/?title=progresso)

## Como contribuir?

Para contribuir com a tradução deste artigo, siga as recomendações abaixo:

- Escolha uma seção do [artigo original](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader) que ainda não foi traduzida. Depois, informe sua intenção de traduzi-la criando uma *issue*, cujo título é o nome da própria seção.

- Enquanto realiza a tradução: (i) não se esqueça de atualizar a barra de progresso acima; e (ii) não traduza elementos de código, tais como nomes de classes, métodos e atributos (traduza apenas os comentários do código, quando necessário).

- Após concluir a traduação, faça um *commit*, cuja mensagem deve ser `trad: [NOME_DA_SECAO]`, onde `NOME_DA_SECAO` representa o nome da seção que foi traduzida. 

- Abra o *Pull Request* e aguarde o contato do mantenedor do projeto. 

*Obrigado por sua contribuição!*

# Início do artigo

A "Pirâmide de Teste" é uma metáfora que diz para agrupar testes de software em compartimentos (*buckets*) de diferentes granularidades. Ela também oferece uma ideia de quantos testes devemos ter em cada um desses compartimentos. Embora o conceito da pirâmide de teste exista há algum tempo, as equipes de desenvolvimento ainda lutam para colocá-la em prática de forma adequada. Este artigo revisita o conceito original da pirâmide de teste e apresenta como você pode colocá-la em prática. O artigo também mostra os tipos de teste que você deve procurar em diferentes níveis da pirâmide e oferece exemplos práticos sobre como eles podem ser implementados.

![A Pirâmide de Teste Prática](/assets/teaser.png "A Pirâmide de Teste Prática")

## Conteúdo

- [A Importância da Automação (de Testes)](#sec-test-automation)

- [A Pirâmide de Teste](#sec-test-pyramid)

- [Ferramentas e Bibliotecas que veremos](#sec-tools-and-libs)

- [Aplicação de Exemplo](#sec-sample-app)

    - [Funcionalidade](#sec-app-functionality)

    - [Estrutura em alto-nível](#sec-app-high-level-structure)

    - [Arquitetura interna](#sec-app-internal-structure)

- [Testes de Unidade](#sec-unit-tests)

    - [O que é Unidade?](#sec-what-is-a-unit)

    - [Sociável e Solitário](#sec-sociable-and-solitary)
    
    - [O que testar?](#sec-what-to-test)
    
    - [A estrutura de um teste](#sec-test-structure)
    
    - [Implementing a Unit Test](#sec-implementing-unit-test)

- [Integration Tests](#sec-integration-tests)

    - [Database Integration](#sec-database-integration)

    - [Integration With Separate Services](#sec-integration-separated-services)

- [Contract Tests](#sec-contract-tests)

    - [Consumer Test (our team)](#sec-consumer-test)

    - [Provider Test (the other team)](#sec-provider-test-other-team)

    - [Provider Test (our team)](#sec-provider-test-our-team)

- [UI Tests](#sec-ui-tests)

- [End-to-End Tests](#sec-end-to-end-tests)

    - [User Interface End-to-End Test](#sec-ui-end-to-end-tests)

    - [REST API End-to-End Test](#sec-api-end-to-end-tests)

- [Exploratory Testing](#sec-exploratory-tests)

- [The Confusion About Testing Terminology](#sec-terminology-confusion)

- [Putting Tests Into Your Deployment Pipeline](#sec-deployment-pipeline)

- [Avoid Test Duplication](#sec-test-duplication)

- [Writing Clean Test Code](#sec-clean-test-code)

- [Conclusion](#sec-conclusion)

- [Appendix](#sec-appendix)

    - [But I Really Need to Test This Private Method](#sec-private-method-tests)

    - [Specialised Test Helpers](#sec-test-helpers)


Um software pronto para produção requer testes antes de definitivamente entrar em produção. À medida que a área de desenvolvimento de software amadureceu, as abordagens para teste de software também amadureceram. Ao invés de se ter uma miríade de testadores manuais de software, as equipes de desenvolvimento passaram a automatizar a maior parte de seus esforços com teste de software. Automatizar os testes permite que as equipes saibam se seu software está "quebrado" em questão de segundos e minutos, em vez de dias e semanas. 

O ciclo de *feedback* drascitamente curto, alimentado por testes automatizados, anda de mãos dadas com práticas de desenvolvimento ágil, entrega contínua e cultura DevOps. Ter uma abordagem efetiva para teste de software permite que as equipes se movam rapidamente e com confiança.

Este artigo explora o que um portfólio de teste deve ter para ser considerado responsivo, confiável e manutenível - independentemente se você está construindo uma arquitetura de microsserviços, aplicativos móveis ou ecossistemas de IoT (*Internet of Things*). Também entraremos em detalhes sobre a criação de testes automatizados efetivos e legíveis. 

## <a id="sec-test-automation"></a>A Importância da Automação (de Testes)

O software tem se tornado parte essencial do mundo em que vivemos. Ele superou seu único propósito inicial de tornar negócios mais eficientes. Hoje, as empresas tentam encontrar maneiras de se tornarem empresas digitais de primeira classe. Como usuários, todos nós interagimos com uma quantidade cada vez maior de software diariamente. As rodas da inovação estão girando rapidamente.

Se você quiser manter o ritmo, terá que procurar maneiras de entregar software mais rapidamente, sem sacrificar sua qualidade. Entrega contínua, prática na qual você garante automaticamente que seu software pode ser colocado em produção (*released*) a qualquer momento, pode ajudá-lo com isso. Com a entrega contínua, você usa um *pipeline* de contrução para testar automaticamente seu software e implantá-lo em seus ambientes de teste e produção.

Contruir, testar e implantar manualmente uma quantidade cada vez maior de software logo se torna algo impossível - a menos que você queira gastar todo o seu tempo com trabalho manual e repetitivo, ao invés de entregar software funcionando. Automatizar tudo - da construção aos testes, implantação e infraestrutura - é o único caminho a seguir.

![Pipeline de contrução](/assets/buildPipeline.png "Pipeline de contrução")

**Figura 1**. Use *pipelines* de construção para colocar seu software em produção de forma automática e confiável.

Tradicionalmente, o teste de software era um trabalho  excessivamente manual, feito ao implantar a aplicação em um ambiente de teste e, então, realizar alguns testes no estilo caixa-preta, por exemplo, clicando através de sua interface de usuário para ver se alguma coisa quebrou. Geralmente, esse testes eram determinados por *roteiros de teste*, para garantir que os testadores fizessem verificações de forma consistente.  

É óbvio que testar todas as alterações manualmente é demorado, repetitivo e tedioso. Algo repetitivo é chato e o que é chato leva a erros e faz você procurar um emprego novo no final da semana.

Felizmente, existe um remédio para tarefas repetitivas: **automação**.

Automatizar seus testes repetitivos pode ser uma grande virada de jogo em sua vida como desenvolvedor de software. Automatize esses testes e você não terá mais que seguir protocolos de cliques, sem pensar, a fim de verificar se seu software ainda funciona corretamente. Automatize seus testes e você poderá alterar sua base de código sem titubear. Se você já tentou fazer uma refatoração em larga escala, sem uma suíte de teste adequada, aposto que você sabe o quão terrível esta experiência pode ser. Como você saberia se acidentalmente quebrasse alguma coisa ao longo do caminho? Bem, você "clica", de acordo com todos os seus *roteiros de teste* manuais; é assim. Mas, sejamos honestos: você realmente gosta disso? Que tal realizar alterações em larga escala e saber se você quebrou alguma coisa em segundos, enquanto toma um bom gole de café? Parece mais agradável, não é mesmo?

## <a id="sec-test-pyramid"></a>A Pirâmide de Teste

Se você quiser levar a sério os testes automatizados para o seu software, há um conceito-chave que você deve conhecer: a **Pirâmide de Teste**. Mike Cohn propôs esse conceito em seu livro *Succeeding with Agile: Software Development Using Scrum* (traduzido para o português com o título "Desenvolvimento de Software com Scrum: Aplicando Métodos Ágeis com Sucesso"). A pirâmide de teste é uma ótima metáfora visual, pois te faz pensar sobre as diferentes camadas de teste. Ela também informa a você quanto de teste realizar em cada camada. 

![Pirâmide de teste](/assets/testPyramid.png "Pirâmide de teste")

**Figura 2**. A Pirâmide de Teste.

A pirâmide de teste original de Mike Cohn consiste em três camadas, as quais sua suíte de teste deve contemplar (de baixo para cima):

* Testes de unidade
* Testes de serviço
* Testes de interface do usuário

Infelizmente, o conceito da pirâmide de teste fica um pouco aquém, se você olhar mais de perto. Algumas pessoas argumentam que a nomenclatura ou alguns aspectos conceituais da pirâmide de teste de Mike Cohn não são ideiais, e eu tenho que concordar. De um ponto de vista moderno, a pirâmide de teste parece excessivamente simplista e pode, portanto, ser enganosa.

Ainda assim, devido à sua simplicidade, a essência da pirâmide de teste serve como uma boa regra geral, quando se trata de estabelecer sua própria suíte de teste. O melhor que você pode fazer é se lembrar de duas coisas a respeito da pirâmide de teste original de Cohn:

* Escreva testes com diferentes granularidades
* Quanto mais alto o nível que você estiver, menos testes você deve escrever 

Atenha-se ao formato da pirâmide para criar uma suíte de teste saudável, rápida e manutenível: escreva muitos testes de unidade pequenos e rápidos. Escreva alguns testes mais granulares e muito poucos testes de alto-nível, que testam sua aplicação de ponta-a-ponta. Cuidado para não acabar com uma suíte de teste do tipo casquinha de sorvete ([*ice-cream cone tests*](https://watirmelon.blog/testing-pyramids/)), que será um pesadelo para manter e leva muito tempo para ser executada.

Não se apegue tanto aos nomes das camadas individuais da pirâmide de teste de Cohn. De fato, eles podem ser um pouco enganosos: por exemplo, "Testes de serviço" é uma expressão difícil de definir (o próprio Mike Cohn [fez uma declaração](https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid) de que muitos desenvolvedores ignoram completamente esta camada). Além disso, vivemos em dias em que  *frameworks* para SPA (*Single Page Applications*), tais como React, Angular, Ember.js, entre outros, demonstram que "Testes de interface do usuário" não precisam estar no nível mais alto de sua pirâmide - você pode perfeitamente realizar "Testes de unidade" em todos esses *frameworks*.

Dadas essas limitações dos nomes originais, é totalmente aceitável criar outros nomes para suas camadas de teste, desde que você o  s mantenha consistentes em sua base de código e nas discussões com sua equipe.

## <a id="sec-tools-and-libs"></a>Ferramentas e Bibliotecas que veremos

* [JUnit](http://junit.org/):  nosso executor de testes (*test runner*)
* [Mockito](http://site.mockito.org/): para "mockar" dependências
* [Wiremock](http://wiremock.org/): para criar *stubs* de serviços externos
* [Pact](https://docs.pact.io/): para escrever testes CDC
* [Selenium](http://docs.seleniumhq.org/): para escrever testes *end-to-end* executados a partir da interface de usuário
* [REST-assured](https://github.com/rest-assured/rest-assured): para escrever testes *end-to-end* executados a partir dos *endpoints* de uma API REST

## <a id="sec-sample-app"></a>Aplicação de Exemplo

Eu escrevi um [microsserviço simples](https://github.com/hamvocke/spring-testing), incluindo uma suíte de teste com testes para as diferentes camadas da pirâmide de teste.

A aplicação de exemplo apresenta as características de um típico microsserviço. Ela expõe uma interface REST, "conversa" com uma base de dados e busca informações de um serviço REST de terceiros. Ela foi implementada usando [Spring Boot](https://projects.spring.io/spring-boot/) e deve ser compreensível mesmo que você nunca tenha trabalho com Sring Boot antes.

Certifique-se de verificar o [código no Github](https://github.com/hamvocke/spring-testing). O *README* do projeto contém as instruções necessárias para você executar a aplicação, bem como seus testes automatizados, em sua máquina.

### <a id="sec-app-functionality"></a>Funcionalidade

A funcionalidade da aplicação é simples. Ela disponibiliza uma interface REST com três *endpoints*:

| *Endpoint*  | Funcionalidade |
| --- | --- |
| `GET /hello`  | Retorna "*Hello World*". Sempre.  |
| `GET /hello/{lastname}`  | Busca a pessoa com o sobrenome informado. Se existir, retorna "*Hello, {Firstname} {Lastname}*".  |
| `GET /weather`  | Retorna as condições meteorológicas atuais para *Hamburg*, *Alemanha*.

### <a id="sec-app-high-level-structure"></a>Estrutura de alto-nível

Em alto-nível, o sistema possui a seguinte estrutura:

![Estrutura do sistema em alto-nível](/assets/testService.png "Estrutura do sistema em alto-nível")

**Figura 3**. Estrutura em alto-nível do nosso microsserviço.

Nosso microsserviço oferece uma interface REST que pode ser invocada via HTTP. Para alguns *endpoints*, o serviço irá buscar informações de uma base de dados. Em outros casos, o serviço irá invocar uma API externa de previsão do tempo, a fim de recuperar e exibir as condições meteorológicas atuais.

### <a id="sec-app-internal-structure"></a>Arquitetura interna

Internamente, nosso microsserviço tem uma arquitetura típica de aplicações Spring:

![Arquitetura interna da aplicação](/assets/testArchitecture.png "Arquitetura interna da aplicação")

**Figura 4**. Estrutura interna do nosso microsserviço.

* As classes do tipo `Controller` provêm *endpoints* REST e lidam com requisições e respostas HTTP.
* As classes do tipo `Repository` interagem com o banco de dados e cuidam da escrita e leitura de dados no/do armazenamento persistente.
* As classes do tipo `Client` "conversam" com outras APIs, em nosso caso, elas buscam dados no formato JSON a partir de uma API de previsão do tempo, denominada *darksky.net* [^1].
* As classes do tipo `Domain` representam nosso modelo de domínio, incluindo a lógica de negócio (que, para ser justo, é bem trivial em nosso caso).

Os desenvolvedores Spring experientes devem notar que uma camada frequentemente utilizada está faltando aqui: inspirados pelo [*Domain-Drive Design*](https://en.wikipedia.org/wiki/Domain-driven_design) (DDD), muitos desenvolvedores constrõem uma camada para classes de serviço. Eu decidi não incluir uma camada de serviço nesta aplicação. Uma razão é que nossa aplicação é bastante simples, por isso, uma camada de serviço seria um nível de abstração desnecessário. Outro motivo é que eu acho que muitas pessoas exageram em suas camadas de serviço. Geralmente, eu encontro bases de código nas quais toda a regra de negócio está encapsulada em classes de serviço. O modelo de domínio torna-se apenas uma camada de dados, não de comportamentos (isto é, um [Modelo de Domínio Anêmico](https://en.wikipedia.org/wiki/Anemic_domain_model)). Para toda aplicação não-trivial, isso desperdiça muito potencial para manter seu código bem estruturado e testável e não utiliza totalmente o poder da orientação a objetos. 

Nossos repositórios são simples e diretos, oferecendo a funcionalidade de CRUD. Para manter o código simples, eu usei o [Spring Data](http://projects.spring.io/spring-data/). Spring Data nos fornece uma implementação de repositório CRUD simples e genérica, a qual podemos usar em vez de desenvolvê-la por nós mesmos. Ele também cuida da criação de um banco de dados em memória para nossos testes, em vez de usar um banco de dados PostgresSQL real, como seria em produção.

Dê uma olhada na base de código para se familiarizar com a estrutura interna. Isso será útil para nossa próxima etapa: Testar a aplicação!  

## <a id="sec-unit-tests"></a>Teste de Unidade

A base de sua suíte de teste deve ser composta por testes de unidade. Seus testes de unidade garantem que uma determinada unidade (*Subject Under Test - SUT*) de sua base de código funciona conforme esperado. Testes de unidade possuem o escopo mais restrito de todos os testes em sua suíte de teste. O número de testes de unidade em sua suíte de teste superará em muito o de qualquer outro tipo de teste.

![Testes de unidade](/assets/unitTest.png "Testes de unidade")

**Figura 5**. Um teste de unidade normalmente substitui seus colaboradores externos por dublês de teste (*test doubles*).


### <a id="sec-what-is-a-unit"></a>O que é Unidade?

Se você perguntar a três pessoas diferentes o que é "unidade" no contexto de testes de unidade, provavelmente, você receberá quatro respostas ligeriramente diferentes. Até certo ponto, é uma questão de definição pessoal e não há problema em não se ter uma resposta canônica.

Se você estiver trabalhando em uma linguagem funcional, uma unidade será, provavelmente, uma única função. Seus testes de unidade invocarão esta função com diferentes parâmetros e garantirão que ela retorne os valores esperados. Em uma linguagem orientada a objetos, uma unidade pode ser desde um único método até uma classe inteira.

### <a id="sec-sociable-and-solitary"></a>Sociável e Solitário

Algumas pessoas defendem que todos os colaboradores (por exemplo, outras classes que são chamadas pela classe sob teste) do seu *SUT* devem ser substituídas por *mocks* ou *stubs* a fim de se obter isolamento perfeito e evitar efeitos colaterais e uma configuração de teste complicada. Outros argumentam que apenas colaboradores que são "lentos" ou com efeitos colaterais maiores (por exemplo, classes que acessam o banco de dados ou fazem chamadas via rede) devem ser substituídas. 

[Geralmente](https://martinfowler.com/bliki/UnitTest.html), as pessoas nomeiam esses dois tipos de testes como **testes de unidade solitários**, para os testes que substituem todos os seus colaboradores, e **testes de unidade sociáveis**, para os testes que permitem "conversar" com colaboradores reais (Jay Fields cunhou esses termos em seu livro [*Working Effectively with Unit Tests*](https://leanpub.com/wewut)). Se você tiver algum tempo livre, [leia mais sobre](https://martinfowler.com/articles/mocksArentStubs.html) os prós e contras dessas diferentes escolas de pensamento.

No final das contas, não importa se você vai de testes de unidade solitários ou sociáveis. Escrever testes automatizados é o que importa. Pessoalmente, eu procuro usar ambas as abordagens o tempo todo. Se ficar estranho usar colaboradores reais, então eu os substituirei, sem cerimônia. Se eu sentir que envolver o colaborador real me dá mais confiança em um teste, então eu vou substituir apenas as partes mais externas do meu serviço.

### <a id="sec-what-to-test"></a>O que testar?

Uma coisa boa sobre testes de unidade é que você pode escrevê-los para todas as suas classes de código de produção, independentemente de sua funcionalidade ou qual de camada em sua estrutura interna elas pertencem. Você pode criar testes de unidade para controladores (*controllers*), da mesma forma que pode testar repositórios, classes de domínio ou leitores de arquivos. Simplesmente, siga a regra geral "**uma classe de teste por classe de produção**" e você terá um bom começo.

Uma classe de teste de unidade deve, pelo menos, **testar a interface pública da classe** [de produção]. Os métodos privados não podem ser testados, uma vez que você não pode simplesmente invocá-los a partir de uma classe de teste diferente. Métodos *protected* ou *package-private* são acessíveis a partir de uma classe de teste (considerando que a estrutura de pacotes da sua classe de teste é a mesma da sua classe de produção), mas testar esses métodos já é "ir longe demais" (para mais detalhes sobre esse assunto, leia a seção "[Mas eu realmente preciso testar este método privado](#sec-contract-tests)").

Há uma linha tênue quando se trata de escrever testes de unidade: eles devem garantir que todos os caminhos de código não-triviais estão testados (incluindo o caminho feliz e os casos limítrofes). Ao mesmo tempo, eles não devem estar muito próximos à sua implementação.

Por que isso?

Testes que estão muito próximos do código de produção se tornam rapidamente irritantes. Assim que você refatorar seu código de produção (recapitulação rápida: refatorar significa mudar a estrutura interna do seu código sem mudar seu comportamento visível externamente) seus testes de unidade "quebrarão".

Dessa forma, você perde o grande benefício dos testes de unidade: atuar como uma rede de segurança para alterações no código. Você, então, se torna farto desses testes estúpidos falhando toda vez que você refatora, causando mais trabalho do que ajudando; e de quem foi essa ideia estúpida de testar?

O que você faz então? Não reflita sua estrutura interna de código em seus testes de unidade. Em vez disso, teste o comportamento observável. Pense sobre

`se eu inserir os valores X e Y, o resultado será Z?`

ao invés de

`se eu inserir X e Y, o método invocará a classe A primeiro, depois invocará a classe B e retornará o resultado da classe A mais o resultado da classe B`

Métodos privado, em geral, devem ser considerados como um detalhe de implementação. É por isso que você nem deve ter vontade de testá-los.

Muitas vezes eu ouço opositores dos testes de unidade (ou TDD) argumentando que escrever testes de unidade é um trabalho sem sentido, uma vez que você precisa testar todos os seus métodos para obter uma alta cobertura de testes. Eles, geralmente, citam cenários nos quais líderes extremamente ansiosos os forçou a escrever testes de unidade para *getters* e *setters* e todos os outros tipos de código trivial, a fim de se obter 100% de cobertura de teste. 

Há tanta coisa errada nisso.

*Yes, you should test the public interface. More importantly, however, you don't test trivial code. Don't worry, Kent Beck said it's ok. You won't gain anything from testing simple getters or setters or other trivial implementations (e.g. without any conditional logic). Save the time, that's one more meeting you can attend, hooray!*

Sim, você deve testar a interface pública. Mais importante, no entanto, **você não testa código trivial**. Não se preocupe, [Kent Beck disse que que está tudo bem](https://stackoverflow.com/questions/153234/how-deep-are-your-unit-tests/). Você não ganhará nada ao testar simples *getters* e *setters* ou outras implementações triviais (por exemplo, sem nenhuma lógica condicional). Economize tempo, essa é mais uma reunião da qual você pode participar, viva!

### <a id="sec-test-structure"></a>A estrutura de um teste

Uma boa estrutura para todos os seus testes (não limitada apenas a testes de unidade) é esta:

* Configure os dados de teste
* Chame seu método sob teste
* Assegure (*assert*) que os resultados esperados são retornados

Há um mnemônico legal para lembrar essa estrutura: "[*Arrange, Act, Assert*](https://xp123.com/articles/3a-arrange-act-assert/)". Outro que você pode usar, inspirado no BDD (*Behavior Driven Development*), é a tríade "[*given, when, then*](https://martinfowler.com/bliki/GivenWhenThen.html)", onde "*given*" representa a configuração dos dados de teste, "*when*" representa o método chamado e "*then*" representa a parte da asserção. 

This pattern can be applied to other, more high-level tests as well. In every case they ensure that your tests remain easy and consistent to read. On top of that tests written with this structure in mind tend to be shorter and more expressive.

Este padrão pode ser aplicado a outros testes de nível mais alto também. Em todos os casos, eles garantem que seus testes permamençam consistentes e fáceis de ler. Além disso, testes escritos com essa estrutura em mente tendem a ser mais curtos e expressivos.


### <a id="sec-implementing-unit-test"></a>Implementing a Unit Test

## <a id="sec-integration-tests"></a>Integration Tests

### <a id="sec-database-integration"></a>Database Integration

### <a id="sec-integration-separated-services"></a>Integration With Separate Services

## <a id="sec-contract-tests"></a>Contract Tests

### <a id="sec-consumer-test"></a>Consumer Test (our team)

### <a id="sec-provider-test-other-team"></a>Provider Test (the other team)

### <a id="sec-provider-test-our-team"></a>Provider Test (our team)

## <a id="sec-ui-tests"></a>UI Tests

## <a id="sec-end-to-end-tests"></a>End-to-End Tests

### <a id="sec-ui-end-to-end-tests"></a>User Interface End-to-End Test

### <a id="sec-api-end-to-end-tests"></a>REST API End-to-End Test

## <a id="sec-exploratory-tests"></a>Exploratory Testing

## <a id="sec-terminology-confusion"></a>The Confusion About Testing Terminology

## <a id="sec-deployment-pipeline"></a>Putting Tests Into Your Deployment Pipeline

## <a id="sec-test-duplication"></a>Avoid Test Duplication

## <a id="sec-clean-test-code"></a>Writing Clean Test Code

## <a id="sec-conclusion"></a>Conclusion

## <a id="sec-appendix"></a>Appendix

### <a id="sec-private-method-tests"></a>But I Really Need to Test This Private Method

### <a id="sec-test-helpers"></a>Specialised Test Helpers

[^1]: **Nota do tradutor**: no README do [repositório](https://github.com/hamvocke/spring-testing) da aplicação de exemplo, o autor menciona ter trocado a API do *darksky.net* pela do *openweathermap.org*, depois que a primeira foi desativada para consulta pública à previsão do tempo.


