# A Pirâmide de Teste Prática

Esta é uma tradução do artigo [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader), originalmente escrito por Ham Vocke.

[numOfTranslatedSections]: 16
[amountOfSections]: 34

![47%](https://progress-bar.dev/47/?title=progresso)

## Como contribuir?

Para contribuir com a tradução deste artigo, siga as recomendações abaixo:

- Escolha uma seção do [artigo original](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader) que ainda não foi traduzida. Depois, informe sua intenção de traduzi-la criando uma *issue*, cujo título é o nome da própria seção.

- Enquanto realiza a tradução: (i) não se esqueça de atualizar a barra de progresso acima; e (ii) não traduza elementos de código, tais como *strings* literais, nomes de classes, métodos e atributos, entre outros (traduza apenas comentários de código, quando necessário).

- Após concluir a tradução, faça um *commit*, cuja mensagem deve ser `trad: [NOME_DA_SECAO]`, onde `NOME_DA_SECAO` representa o nome da seção que foi traduzida. 

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
    
    - [Implementando um Teste de Unidade](#sec-implementing-unit-test)

- [Testes de Integração](#sec-integration-tests)

    - [Integração com o banco de dados](#sec-database-integration)

    - [Integração com serviços separados](#sec-integration-separated-services)

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

O ciclo de *feedback* drasticamente curto, alimentado por testes automatizados, anda de mãos dadas com práticas de desenvolvimento ágil, entrega contínua e cultura DevOps. Ter uma abordagem efetiva para teste de software permite que as equipes se movam rapidamente e com confiança.

Este artigo explora o que um portfólio de teste deve ter para ser considerado responsivo, confiável e manutenível - independentemente se você está construindo uma arquitetura de microsserviços, aplicativos móveis ou ecossistemas de IoT (*Internet of Things*). Também entraremos em detalhes sobre a criação de testes automatizados efetivos e legíveis. 

## <a id="sec-test-automation"></a>A Importância da Automação (de Testes)

O software tem se tornado parte essencial do mundo em que vivemos. Ele superou seu único propósito inicial de tornar negócios mais eficientes. Hoje, as empresas tentam encontrar maneiras de se tornarem empresas digitais de primeira classe. Como usuários, todos nós interagimos com uma quantidade cada vez maior de software diariamente. As rodas da inovação estão girando rapidamente.

Se você quiser manter o ritmo, terá que procurar maneiras de entregar software mais rapidamente, sem sacrificar sua qualidade. Entrega contínua, prática na qual você garante automaticamente que seu software pode ser colocado em produção (*released*) a qualquer momento, pode ajudá-lo com isso. Com a entrega contínua, você usa um *pipeline* de construção para testar automaticamente seu software e implantá-lo em seus ambientes de teste e produção.

Construir, testar e implantar manualmente uma quantidade cada vez maior de software logo se torna algo impossível - a menos que você queira gastar todo o seu tempo com trabalho manual e repetitivo, ao invés de entregar software funcionando. Automatizar tudo - da construção aos testes, implantação e infraestrutura - é o único caminho a seguir.

![Pipeline de construção](/assets/buildPipeline.png "Pipeline de construção")

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

Infelizmente, o conceito da pirâmide de teste fica um pouco aquém, se você olhar mais de perto. Algumas pessoas argumentam que a nomenclatura ou alguns aspectos conceituais da pirâmide de teste de Mike Cohn não são ideais, e eu tenho que concordar. De um ponto de vista moderno, a pirâmide de teste parece excessivamente simplista e pode, portanto, ser enganosa.

Ainda assim, devido à sua simplicidade, a essência da pirâmide de teste serve como uma boa regra geral, quando se trata de estabelecer sua própria suíte de teste. O melhor que você pode fazer é se lembrar de duas coisas a respeito da pirâmide de teste original de Cohn:

* Escreva testes com diferentes granularidades
* Quanto mais alto o nível que você estiver, menos testes você deve escrever 

Atenha-se ao formato da pirâmide para criar uma suíte de teste saudável, rápida e manutenível: escreva muitos testes de unidade pequenos e rápidos. Escreva alguns testes mais granulares e muito poucos testes de alto-nível, que testam sua aplicação de ponta-a-ponta. Cuidado para não acabar com uma suíte de teste do tipo casquinha de sorvete ([*ice-cream cone tests*](https://watirmelon.blog/testing-pyramids/)), que será um pesadelo para manter e leva muito tempo para ser executada.

Não se apegue tanto aos nomes das camadas individuais da pirâmide de teste de Cohn. De fato, eles podem ser um pouco enganosos: por exemplo, "Testes de serviço" é uma expressão difícil de definir (o próprio Mike Cohn [fez uma declaração](https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid) de que muitos desenvolvedores ignoram completamente esta camada). Além disso, vivemos em dias em que  *frameworks* para SPA (*Single Page Applications*), tais como React, Angular, Ember.js, entre outros, demonstram que "Testes de interface do usuário" não precisam estar no nível mais alto de sua pirâmide - você pode perfeitamente realizar "Testes de unidade" em todos esses *frameworks*.

Dadas essas limitações dos nomes originais, é totalmente aceitável criar outros nomes para suas camadas de teste, desde que você o  s mantenha consistentes em sua base de código e nas discussões com sua equipe.

## <a id="sec-tools-and-libs"></a>Ferramentas e Bibliotecas que veremos

* [JUnit](http://junit.org/):  nosso executor de testes (*test runner*)
* [Mockito](http://site.mockito.org/): para substituir (*mock*) dependências
* [Wiremock](http://wiremock.org/): para criar *stubs* de serviços externos
* [Pact](https://docs.pact.io/): para escrever testes CDC
* [Selenium](http://docs.seleniumhq.org/): para escrever testes *end-to-end* executados a partir da interface de usuário
* [REST-assured](https://github.com/rest-assured/rest-assured): para escrever testes *end-to-end* executados a partir dos *endpoints* de uma API REST

## <a id="sec-sample-app"></a>Aplicação de Exemplo

Eu escrevi um [microsserviço simples](https://github.com/hamvocke/spring-testing), incluindo uma suíte de teste com testes para as diferentes camadas da pirâmide de teste.

A aplicação de exemplo apresenta as características de um típico microsserviço. Ela expõe uma interface REST, "conversa" com uma base de dados e busca informações de um serviço REST de terceiros. Ela foi implementada usando [Spring Boot](https://projects.spring.io/spring-boot/) e deve ser compreensível mesmo que você nunca tenha trabalho com Spring Boot antes.

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

Os desenvolvedores Spring experientes devem notar que uma camada frequentemente utilizada está faltando aqui: inspirados pelo [*Domain-Drive Design*](https://en.wikipedia.org/wiki/Domain-driven_design) (DDD), muitos desenvolvedores constroem uma camada para classes de serviço. Eu decidi não incluir uma camada de serviço nesta aplicação. Uma razão é que nossa aplicação é bastante simples, por isso, uma camada de serviço seria um nível de abstração desnecessário. Outro motivo é que eu acho que muitas pessoas exageram em suas camadas de serviço. Geralmente, eu encontro bases de código nas quais toda a regra de negócio está encapsulada em classes de serviço. O modelo de domínio torna-se apenas uma camada de dados, não de comportamentos (isto é, um [Modelo de Domínio Anêmico](https://en.wikipedia.org/wiki/Anemic_domain_model)). Para toda aplicação não-trivial, isso desperdiça muito potencial para manter seu código bem estruturado e testável e não utiliza totalmente o poder da orientação a objetos. 

Nossos repositórios são simples e diretos, oferecendo a funcionalidade de CRUD. Para manter o código simples, eu usei o [Spring Data](http://projects.spring.io/spring-data/). Spring Data nos fornece uma implementação de repositório CRUD simples e genérica, a qual podemos usar em vez de desenvolvê-la por nós mesmos. Ele também cuida da criação de um banco de dados em memória para nossos testes, em vez de usar um banco de dados PostgresSQL real, como seria em produção.

Dê uma olhada na base de código para se familiarizar com a estrutura interna. Isso será útil para nossa próxima etapa: Testar a aplicação!  

## <a id="sec-unit-tests"></a>Teste de Unidade

A base de sua suíte de teste deve ser composta por testes de unidade. Seus testes de unidade garantem que uma determinada unidade (*Subject Under Test - SUT*) de sua base de código funciona conforme esperado. Testes de unidade possuem o escopo mais restrito de todos os testes em sua suíte de teste. O número de testes de unidade em sua suíte de teste superará em muito o de qualquer outro tipo de teste.

![Testes de unidade](/assets/unitTest.png "Testes de unidade")

**Figura 5**. Um teste de unidade normalmente substitui seus colaboradores externos por dublês de teste (*test doubles*).


### <a id="sec-what-is-a-unit"></a>O que é Unidade?

Se você perguntar a três pessoas diferentes o que é "unidade" no contexto de testes de unidade, provavelmente, você receberá quatro respostas ligeiramente diferentes. Até certo ponto, é uma questão de definição pessoal e não há problema em não se ter uma resposta canônica.

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

Este padrão pode ser aplicado a outros testes de nível mais alto também. Em todos os casos, eles garantem que seus testes permaneçam consistentes e fáceis de ler. Além disso, testes escritos com essa estrutura em mente tendem a ser mais curtos e expressivos.


### <a id="sec-implementing-unit-test"></a>Implementando um Teste de Unidade

Agora que nós sabemos o que testar e como estruturar nossos testes de unidade, nós podemos, finalmente, ver um exemplo real.

Vamos pegar uma versão simplificada da classe `ExempleController`:

```Java
@RestController
public class ExampleController {

    private final PersonRepository personRepo;

    @Autowired
    public ExampleController(final PersonRepository personRepo) {
        this.personRepo = personRepo;
    }

    @GetMapping("/hello/{lastName}")
    public String hello(@PathVariable final String lastName) {
        Optional<Person> foundPerson = personRepo.findByLastName(lastName);

        return foundPerson
                .map(person -> String.format("Hello %s %s!",
                        person.getFirstName(),
                        person.getLastName()))
                .orElse(String.format("Who is this '%s' you're talking about?",
                        lastName));
    }
}
```

Um teste de unidade para o método `hello(lastname)` pode ser assim:

```Java
public class ExampleControllerTest {

    private ExampleController subject;

    @Mock
    private PersonRepository personRepo;

    @Before
    public void setUp() throws Exception {
        initMocks(this);
        subject = new ExampleController(personRepo);
    }

    @Test
    public void shouldReturnFullNameOfAPerson() throws Exception {
        Person peter = new Person("Peter", "Pan");
        given(personRepo.findByLastName("Pan"))
            .willReturn(Optional.of(peter));

        String greeting = subject.hello("Pan");

        assertThat(greeting, is("Hello Peter Pan!"));
    }

    @Test
    public void shouldTellIfPersonIsUnknown() throws Exception {
        given(personRepo.findByLastName(anyString()))
            .willReturn(Optional.empty());

        String greeting = subject.hello("Pan");

        assertThat(greeting, is("Who is this 'Pan' you're talking about?"));
    }
}
```

Estamos escrevemos os testes de unidade usando [JUnit](http://junit.org/), o *framework* de testes "padrão" para Java. Nós usamos [Mockito](http://site.mockito.org/) para substituir a classe `PersonRepository` real por um *stub* em nosso teste. Este *stub* nos permite criar repostas pré-definidas a serem retornadas pelo método substituído neste teste. Utilizar *stubs* torna o teste mais simples, previsível e nos permite configurar facilmente os dados de teste.   

Seguindo a estrutura "*arrange, act, assert*", nós escrevemos dois testes - um caso positivo e um caso em que a pessoa procurada não foi encontrada. O primeiro caso de teste cria um novo objeto pessoa e diz ao repositório fictício (*mocked*) para retornar esse objeto quando for invocado com "Pan" como o valor para o parâmetro `lastName`. O teste então chama o método que deve ser testado. Finalmente, ele assegura (*asserts*) que a resposta seja igual à resposta esperada.

O segundo caso de teste trabalha de forma similar, mas testa o cenário em que o método testado não encontra uma pessoa para o parâmetro informado.

## <a id="sec-integration-tests"></a>Testes de Integração

Todas as aplicações não-triviais se integrarão com algumas outras partes (bancos de dados, sistemas de arquivos, chamadas de rede para outras aplicações). Ao escrever testes de unidade, estas são, geralmente, as partes que você deixa de fora para obter um melhor isolamento e testes mais rápidos. Ainda assim, sua aplicação irá interagir com outras partes e isso precisa ser testado. **[Testes de Integração](https://martinfowler.com/bliki/IntegrationTest.html)** estão aí para ajudar. Eles testam a integração da sua aplicação com todas as partes que "vivem fora" da sua aplicação.

Para seus testes automatizados, isso significa que você não precisa apenas rodar sua própria aplicação, mas também o componente com o qual você está integrando. Se você estiver testando a integração com um banco de dados, você precisará "executar" um banco de dados ao executar seus testes. Para testar se você pode ler arquivos de um disco, você precisa salvar um arquivo em seu disco e carregá-lo em seu teste de integração.

Eu mencionei antes que "testes de unidade" é um termo vago, isso é ainda mais verdade para "testes de integração". Para algumas pessoas, teste de integração significa testar toda a *stack* de sua aplicação conectada a outras aplicações. Eu gosto de tratar dos testes de integração de forma mais restrita e testar um ponto de integração de cada vez, substituindo serviços externos e bancos de dados separados por dublês de teste. Juntamente com os testes de contrato e a execução de testes de contrato contra esses dublês de testes, bem como as implementações reais, você pode criar testes de integração mais rápidos, mais independentes e geralmente mais fáceis de entender. 

Testes de integração restritos vivem na fronteira do seu serviço. Conceitualmente, eles estão sempre desencadeando uma ação que leva à integração com a parte externa (sistemas de arquivo, banco de dados, serviços externos). Um teste de integração de banco de dados ficaria assim:

* Inicie um banco de dados
* Conecte sua aplicação ao banco de dados
* Execute uma função em seu código que escreva dados no banco de dados
* Verifique se os dados esperados foram escritos no banco de dados, lendo os dados do banco de dados

![Teste de integração de banco de dados](/assets/dbIntegrationTest.png "Teste de integração de banco de dados")

**Figura 6**. Um teste de integração de banco de dados integra seu código com um banco de dados real.

Outro exemplo, testar se seu serviço se integra com um serviço externo via API REST poderia ser assim:

* Inicie sua aplicação
* Inicie uma instância do seu serviço externo (ou um dublê de teste com mesma interface)
* Execute uma função em seu código que lê a partir da API do serviço externo
* Verifique se sua aplicação pode tratar (*parse*) a resposta corretamente

![Teste de integração de serviço externo](/assets/httpIntegrationTest.png "Teste de integração de serviço externo")

**Figura 7**. Este tipo de teste de integração verifica se sua aplicação pode se comunicar corretamente com um serviço externo.

Seus testes de interação - tais como testes de unidade - pode ser bastante "caixa-branca". Alguns *frameworks* permitem que você inicie sua aplicação, substituindo (*mock*) algumas partes dela, para que você possa verificar se as interações ocorreram corretamente.

Escreva testes de integração para todas aspartes do código em que você serializa ou desserializa dados. Isso acontece com mais frequência do que você imagina. Pense em:

* Chamadas para seus serviços via API REST
* Ler e escrever em bancos de dados
* Chamar APIs de outras aplicações
* Ler e escrever em filas
* Escrever no sistema de arquivo

Escrever testes de integração em torno dessas fronteiras garante que a gravação e a leitura de dados desses colaboradores externos funcionam bem.

Ao escrever *testes de integração restritos*, você deve tentar executar suas dependências externas localmente: crie um banco de dados MySQL local, teste em um sistemas de arquivos ext4 local. Se você estiver integrando com um serviço externo, execute uma instância desse serviço localmente ou crie e execute uma versão falsa (*fake*) que imita o comportamento do serviço real.

Se não houver uma maneira de executar o serviço de terceiro localmente, você deve optar por executar uma instância dedicada para testes e apontar para essa instância de teste ao executar seus testes de integração. Evite integrar o sistema de produção real com seus testes automatizados. "Explodir" milhares de requisições de teste em um sistema de produção é uma maneira infalível de deixar as pessoas com raiva, pois você está sobrecarregando seus *logs* (no melhor caso) ou mesmo gerando um ataque DoS em seus serviços (no pior caso). A integração com serviços pela rede é uma característica típica de um teste de integração amplo, que torna seus testes mais lentos e geralmente mais difíceis de escrever.

Com relação à pirâmide de teste, os testes de integração estão em um nível mais alto do que os testes de unidade. A integração de partes lentas, como sistemas de arquivo e bancos de dados, tende a ser muito mais lento do que executar testes de unidade com essas partes substituídas. Eles também podem ser mais difíceis de escrever do que testes de unidade pequenos e isolados, afinal você tem que se preocupar em configurar uma parte externa como uma das etapas dos seus testes. Ainda assim, eles têm a vantagem de dar a você a confiança de que sua aplicação pode funcionar corretamente com todas as partes externas com as quais ela precisam comunicar. Os testes de unidade não podem ajudá-lo com isso.   

### <a id="sec-database-integration"></a>Integração com o banco de dados

`PersonRepository` é a única classe de repositório na base de código. Ela depende do *Spring Data* e não possui implementação real. Ela apenas herda da interface `CrudRepository` e provê um único cabeçalho de método. O resto é "mágica" do Spring.

```Java
public interface PersonRepository extends CrudRepository<Person, String> {
    Optional<Person> findByLastName(String lastName);
}
```

Com a interface `CrudRepository`, o Spring Boot oferece um repositório CRUD totalmente funcional com os métodos `findOne`, `findAll`, `save`, `update` e `delete`. Nossa definição de método personalizado (`findByLastName()`) estende essa funcionalidade básica e nos dá uma maneira de buscar pessoas a partir de seu sobrenome. O Spring Data analisa o tipo de retorno e o nome do método e verifica o nome do método em relação a uma convenção de nomenclatura para descobrir o que ele deve fazer. 

Embora o Spring Data faça o trabalho pesado de implementar repositórios de banco de dados, eu escrevi um teste de integração de banco de dados. Você pode argumentar que isso é testar o *framework*, o que é algo que eu devo evitar, pois não é nosso código que estamos testando. Ainda assim, eu acredito que ter pelo menos um teste de integração aqui é crucial. Primeiro, porque ele testa se nosso método personalizado `findByLastName` realmente se comporta conforme o esperado. Em segundo lugar, ele prova que nosso repositório usou a configuração do Spring corretamente e pode se conectar ao banco de dados. 

Para facilitar a execução dos testes em sua máquina (sem precisar instalar um banco de dados PostgreSQL), nosso teste se conecta um um banco de dados em memória, denominado H2.

Eu defini o H2 como uma dependência de teste no arquivo de construção `build.grade`. O arquivo `application.properties` no diretório de teste não define qualquer propriedade `spring.datasource`. Isso diz ao Spring Data para usar um banco de dados em memória. Ao encontrar o H2 no `classpath` da aplicação, ele simplesmente o utiliza durante a execução dos nossos testes.

Ao executar uma aplicação real com o perfil `int` (por exemplo, definindo `SPRING_PROFILES_ACTIVE=int` como uma variável de ambiente), ela irá se conectar ao banco de dados PostgreSQL conforme especificado no arquivo `application-int.properties`.

Eu sei, há muitos detalhes específicos do *framework* Spring para conhecer e entender. Para isso, você terá que vascular muita [documentação](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-sql.html#boot-features-embedded-database-support). O código resultante é fácil de visualizar, mas difícil de entender se você não conhece os detalhes do Spring.

Além disso, utilizar um banco de dados em memória é um negócio arriscado. Afinal, nossos testes de integração são executados em um tipo de banco de dados diferente daquele utilizado em produção. Vá em frente e decida por si mesmo se você prefere a "mágica" do Spring e o código simples em vez de uma implementação explícita e mais verbosa.

Enough explanation already, here's a simple integration test that saves a Person to the database and finds it by its last name:

Já chega de explicação, aqui está um teste de integração simples que salva uma `Person` no banco de dados e a encontra pelo sobrenome:

```Java
@RunWith(SpringRunner.class)
@DataJpaTest
public class PersonRepositoryIntegrationTest {
    @Autowired
    private PersonRepository subject;

    @After
    public void tearDown() throws Exception {
        subject.deleteAll();
    }

    @Test
    public void shouldSaveAndFetchPerson() throws Exception {
        Person peter = new Person("Peter", "Pan");
        subject.save(peter);

        Optional<Person> maybePeter = subject.findByLastName("Pan");

        assertThat(maybePeter, is(Optional.of(peter)));
    }
}
```

Você pode ver que nosso teste de integração segue a mesma estrutura *Arrange, Act, Assert* dos testes de unidade. Eu lhe disse que este era um conceito universal!


### <a id="sec-integration-separated-services"></a>Integração com serviços separados

Nosso microsserviço se comunica com o [*darksky.net*](https://darksky.net/), uma API REST de meteorologia. É claro que queremos garantir que nosso serviço mande requisições e analise as respostas corretamente.

Queremos evitar acessar os servidores reais do *darksky* ao executar testes automatizados. Os limites de cota do nosso plano gratuito são apenas parte do motivo. A verdadeira razão é desacoplamento. Nossos testes devem ser executados independentemente do que as pessoas amáveis do [*darksky.net*](https://darksky.net/) estejam fazendo. Mesmo quando sua máquina não consiga acessar os servidores da *darksky* ou os servidores da *darksky* estejam fora do ar para manutenção.

Podemos evitar acessar os servidores reais da *darksky* executando nosso próprio servidor falso enquanto executamos nossos testes de integração. Isso pode soar como uma tarefa enorme. Graças a ferramentas como o [Wiremock](http://wiremock.org/), é fácil fácil. Veja só:

```Java
@RunWith(SpringRunner.class)
@SpringBootTest
public class WeatherClientIntegrationTest {

    @Autowired
    private WeatherClient subject;

    @Rule
    public WireMockRule wireMockRule = new WireMockRule(8089);

    @Test
    public void shouldCallWeatherService() throws Exception {
        wireMockRule.stubFor(get(urlPathEqualTo("/some-test-api-key/53.5511,9.9937"))
                .willReturn(aResponse()
                        .withBody(FileLoader.read("classpath:weatherApiResponse.json"))
                        .withHeader(CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
                        .withStatus(200)));

        Optional<WeatherResponse> weatherResponse = subject.fetchWeather();

        Optional<WeatherResponse> expectedResponse = Optional.of(new WeatherResponse("Rain"));
        assertThat(weatherResponse, is(expectedResponse));
    }
}
```

Para usar o Wiremock, instanciamos um `WireMockRule` em uma porta fixa `(8089)`. Usando o DSL, podemos configurar o servidor Wiremock, definir os *endpoints* que ele deve escutar e definir os modelos de resposta com os quais ele deve responder.

Em seguida, chamamos o método que queremos testar, aquele que chama o serviço externo e verificamos se o resultado foi analisado corretamente.

É importante entender como o teste sabe que deve chamar o servidor Wiremock falso em vez da API verdadeira do *darksky*. O segredo está em nosso arquivo `application.properties` contido em `src/test/resources`. Este é o arquivo de propriedades que o Spring carrega ao executar testes. Neste arquivo, substituímos configurações como chaves de API e URLs por valores adequados para nossos propósitos de teste, por exemplo, chamando o servidor Wiremock falso ao invés do real:

```java
weather.url = http://localhost:8089
```

Observe que a porta definida aqui deve ser a mesma que definimos ao instanciar o `WireMockRule` em nosso teste. Substituir o URL real da API de tempo por um falso em nossos testes é possível injetando o URL no construtor de nossa classe `WeatherClient`:

```Java
@Autowired
public WeatherClient(final RestTemplate restTemplate,
                     @Value("${weather.url}") final String weatherServiceUrl,
                     @Value("${weather.api_key}") final String weatherServiceApiKey) {
    this.restTemplate = restTemplate;
    this.weatherServiceUrl = weatherServiceUrl;
    this.weatherServiceApiKey = weatherServiceApiKey;
}
```
Desta forma, pedimos ao nosso `WeatherClient` para ler o valor do parâmetro `weatherUrl` da propriedade `weather.url` que definimos nas propriedades de nosso aplicativo.

Escrever testes de integração restritos para um serviço separado é bastante fácil com ferramentas como o Wiremock. Infelizmente, há uma desvantagem nessa abordagem: como garantimos que o servidor falso que configuramos se comporta como o servidor real? Com a implementação atual, o serviço separado poderia mudar sua API e nossos testes ainda passariam. No momento, estamos apenas testando se nosso `WeatherClient` pode analisar as respostas que o servidor falso envia. Isso é um começo, mas é muito frágil. Usar testes de ponta a ponta e executá-los em uma instância de teste do serviço real ao invés de usar um serviço falso resolveria esse problema, mas nos tornaria dependentes da disponibilidade do serviço de teste. 

Felizmente, há uma solução melhor para esse dilema: executar testes de contrato de encontro ao servidor falso, assim, o servidor real garante que o falso que usamos em nossos testes de integração seja um dublê fiel. Vamos ver como isso funciona em seguida.

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

Falando sobre diferentes classificações de teste é sempre difícil. O que eu quero dizer quando digo sobre testes unitários pode ser um pouco diferente do seu entendimento. Com testes integrados é ainda pior. Para algumas pessoas, integração de testes é uma atividade muito ampla que testa por vários partes diferentes de um sistema inteiro. Para mim, é 

## <a id="sec-deployment-pipeline"></a>Putting Tests Into Your Deployment Pipeline

## <a id="sec-test-duplication"></a>Avoid Test Duplication

## <a id="sec-clean-test-code"></a>Writing Clean Test Code

## <a id="sec-conclusion"></a>Conclusion

## <a id="sec-appendix"></a>Appendix

### <a id="sec-private-method-tests"></a>But I Really Need to Test This Private Method

### <a id="sec-test-helpers"></a>Specialised Test Helpers

[^1]: **Nota do tradutor**: no README do [repositório](https://github.com/hamvocke/spring-testing) da aplicação de exemplo, o autor menciona ter trocado a API do *darksky.net* pela do *openweathermap.org*, depois que a primeira foi desativada para consulta pública à previsão do tempo.
