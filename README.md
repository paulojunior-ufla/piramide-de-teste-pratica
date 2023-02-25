# A Pirâmide de Teste Prática

Esta é uma tradução do artigo [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader), originalmente escrito por Ham Vocke.

[numOfTranslatedSections]: 19
[amountOfSections]: 34

![56%](https://progress-bar.dev/56/?title=progresso)

## Como contribuir?

Para contribuir com a tradução deste artigo, siga as recomendações abaixo:

- Escolha uma seção do [artigo original](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader) que ainda não foi traduzida. Depois, informe sua intenção de traduzi-la criando uma *issue*, cujo título é o nome da própria seção.

- Enquanto realiza a tradução, não traduza elementos de código, tais como *strings* literais, nomes de classes, métodos e atributos, entre outros (traduza apenas comentários de código, quando necessário).

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

- [Testes de Contrato](#sec-contract-tests)

    - [Teste de consumidor (nosso time)](#sec-consumer-test)

    - [Teste de provedor (outro time)](#sec-provider-test-other-team)

    - [Teste de provedor (nosso time)](#sec-provider-test-our-team)

- [UI Tests](#sec-ui-tests)

- [Testes End-to-End](#sec-end-to-end-tests)

    - [User Interface End-to-End Test](#sec-ui-end-to-end-tests)

    - [REST API End-to-End Test](#sec-api-end-to-end-tests)

- [Exploratory Testing](#sec-exploratory-tests)

- [The Confusion About Testing Terminology](#sec-terminology-confusion)

- [Inserindo Testes em seu Pipeline de Deploy](#sec-deployment-pipeline)

- [Evitando duplicação de testes](#sec-test-duplication)

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

## <a id="sec-contract-tests"></a>Testes de Contrato

Organizações de desenvolvimento de software mais modernas encontraram maneiras de escalar seus esforços de desenvolvimento distribuindo o desenvolvimento de um sistema entre diferentes equipes. As equipes individuais constroem serviços individuais, fracamente acoplados, sem interferir uns nos outros, e integram esses serviços em um grande sistema coeso. O recente interesse em torno dos microsserviços foca exatamente nisso.

Dividir o sistema em muitos serviços pequenos muitas vezes significa que esses serviços precisam se comunicar entre si por meio de interfaces específicas (esperançosamente bem definidas, às vezes crescidas de forma acidental).

As interfaces entre diferentes aplicativos podem vir em diferentes formatos e tecnologias. Os mais comuns são:

* REST e JSON via HTTPS
* RPC usando algo como [gRPC](https://grpc.io/)
* construção de uma arquitetura orientada a eventos usando filas

Para cada interface, há duas partes envolvidas: o fornecedor e o consumidor. O **fornecedor** serve dados aos consumidores. O **consumidor** processa dados obtidos de um fornecedor. Em um mundo REST, um fornecedor constrói uma API REST com todos os *endpoints* necessários; um consumidor faz chamadas a essa API REST para buscar dados ou acionar mudanças no outro serviço. Em um mundo assíncrono orientado a eventos, um fornecedor (geralmente chamado de **publicador**) publica dados em uma fila; um consumidor (geralmente chamado de **assinante**) se inscreve nessas filas e lê e processa dados.

![Interface para um contrato](/assets/contract_tests.png "Especificação de interface para um contrato")

**Figura 8**. Cada interface tem um fornecedor (ou publicador) e um consumidor (ou assinante). A especificação de uma interface pode ser considerada um contrato.

Como os serviços de consumo e fornecimento são frequentemente distribuídos por diferentes equipes, você se encontra na situação em que precisa especificar claramente a interface entre esses serviços (o chamado contrato). Tradicionalmente, as empresas abordaram esse problema da seguinte maneira:

1. Escrever uma especificação de interface longa e detalhada (o contrato)
2. Implementar o serviço fornecedor de acordo com o contrato definido
3. Jogar a especificação de interface para a equipe de consumo
4. Aguardar até que eles implementem a parte deles do consumo da interface
5. Executar alguns testes manuais em larga escala do sistema para ver se tudo funciona
6. Esperar que ambas as equipes sigam a definição da interface para sempre e não estraguem tudo

As equipes de desenvolvimento de software mais modernas substituíram as etapas 5 e 6 por algo mais automatizado: [Testes de contrato](https://martinfowler.com/bliki/ContractTest.html) automatizados garantem que as implementações no lado do consumidor e do fornecedor ainda sigam o contrato definido. Eles funcionam como uma boa suíte de teste de regressão e garantem que as variações do contrato serão notadas precocemente.

Em uma organização mais ágil, você deve seguir a rota mais eficiente e menos desperdiçadora. Você desenvolve suas aplicações dentro da mesma organização. Realmente não deve ser muito difícil falar diretamente com os desenvolvedores de outros serviços em vez de lançar documentação excessivamente detalhada por cima do muro. Afinal, eles são seus colegas de trabalho e não um fornecedor terceirizado com o qual você só pode falar via suporte ao cliente ou contratos à prova de balas.

**Os testes de Contrato Impulsionados pelo Consumidor** (CDC - Consumer-Driven Contract tests) permitem que os [consumidores conduzam a implementação de um contrato](https://martinfowler.com/articles/consumerDrivenContracts.html). Usando CDC, os consumidores de uma interface escrevem testes que verificam a interface para todos os dados que eles precisam dessa interface. A equipe consumidora, então, publica esses testes para que a equipe provedora possa buscar e executar esses testes facilmente. A equipe fornecedora agora pode desenvolver sua API executando os testes CDC. Depois que todos os testes passam, eles sabem que implementaram tudo o que a equipe consumidora precisa.


![Testes de contrato](/assets/cdc_tests.png "Especificação de testes de contrato")

**Figura 9**. Os testes de contrato garantem que o provedor e todos os consumidores de uma interface sigam o contrato de interface definido. Com os testes CDC, os consumidores de uma interface publicam seus requisitos na forma de testes automatizados; os provedores buscam e executam esses testes continuamente.

Esta abordagem permite que a equipe provedora implemente apenas o que é realmente necessário (mantendo as coisas simples, YAGNI e tudo mais). A equipe que fornece a interface deve buscar e executar continuamente esses testes CDC (em seu pipeline de build) para detectar imediatamente quaisquer alterações significativas. Se eles quebrarem a interface, seus testes CDC falharão, impedindo que as alterações entrem em produção. Enquanto os testes permanecerem verdes, a equipe pode fazer quaisquer mudanças que desejar sem se preocupar com outras equipes. A abordagem de Contrato Impulsionado pelo Consumidor deixaria você com um processo parecido com este:

* A equipe consumidora escreve testes automatizados com todas as expectativas do consumidor
* Eles publicam os testes para a equipe provedora
* A equipe provedora executa os testes CDC continuamente e os mantém verdes
* Ambas as equipes conversam entre si quando os testes CDC falham

Se sua organização adota uma abordagem de microsserviços, ter testes CDC é um grande passo para estabelecer equipes autônomas. Os testes CDC são uma maneira automatizada de fomentar a comunicação entre as equipes. Eles garantem que as interfaces entre equipes estejam funcionando o tempo todo. Testes CDC falhando são um bom indicador de que você deve conversar com a equipe afetada, falar sobre quaisquer mudanças de API iminentes e descobrir como você quer seguir em frente.

Uma implementação ingênua de testes CDC pode ser tão simples quanto enviar solicitações contra uma API e afirmar que as respostas contêm tudo o que você precisa. Então, você empacota esses testes como um executável (.gem, .jar, .sh) e os armazena em algum lugar onde a outra equipe possa acessá-lo (por exemplo, um repositório de artefatos como o [Artifactory](https://jfrog.com/artifactory/)).

Nos últimos anos, a abordagem CDC se tornou cada vez mais popular e várias ferramentas foram construídas para tornar a escrita e a troca delas mais fáceis.

[Pact](https://github.com/realestate-com-au/pact) é provavelmente o mais proeminente nos dias de hoje. Ele tem uma abordagem sofisticada de escrever testes para o lado do consumidor e do fornecedor, fornece stubs para serviços separados prontos para uso e permite que você troque testes CDC com outras equipes. O Pact foi portado para muitas plataformas e pode ser usado com linguagens JVM, Ruby, .NET, JavaScript e muitas outras.

Se você quer começar com os CDCs e não sabe como, o Pact pode ser uma escolha sensata. A [documentação](https://docs.pact.io/) pode ser esmagadora no começo. Seja paciente e trabalhe nisso. Isso ajuda a ter uma compreensão sólida dos CDCs, o que por sua vez torna mais fácil para você defender o uso de CDCs ao trabalhar com outras equipes.

Os testes de contrato orientados pelo consumidor podem ser uma mudança real de jogo para estabelecer equipes autônomas que possam se mover rapidamente e com confiança. Faça um favor a si mesmo, leia sobre esse conceito e experimente. Um conjunto sólido de testes CDC é inestimável para poder se mover rapidamente sem quebrar outros serviços e causar muita frustração com outras equipes.


### <a id="sec-consumer-test"></a>Teste de consumidor (nosso time)


Nosso microserviço consome a API do clima. Portanto, é nossa responsabilidade escrever um **teste do consumidor** que defina nossas expectativas para o contrato (a API) entre nosso microserviço e o serviço de clima.

Primeiro, incluímos uma biblioteca para escrever testes de consumidores do Pact em nosso build.gradle:

````JAVA
testCompile('au.com.dius:pact-jvm-consumer-junit_2.11:3.5.5')
````

Graças a essa biblioteca, podemos implementar um teste do consumidor e usar os serviços simulados do Pact:

````JAVA
@RunWith(SpringRunner.class)
@SpringBootTest
public class WeatherClientConsumerTest {

    @Autowired
    private WeatherClient weatherClient;

    @Rule
    public PactProviderRuleMk2 weatherProvider =
            new PactProviderRuleMk2("weather_provider", "localhost", 8089, this);

    @Pact(consumer="test_consumer")
    public RequestResponsePact createPact(PactDslWithProvider builder) throws IOException {
        return builder
                .given("weather forecast data")
                .uponReceiving("a request for a weather request for Hamburg")
                    .path("/some-test-api-key/53.5511,9.9937")
                    .method("GET")
                .willRespondWith()
                    .status(200)
                    .body(FileLoader.read("classpath:weatherApiResponse.json"),
                            ContentType.APPLICATION_JSON)
                .toPact();
    }

    @Test
    @PactVerification("weather_provider")
    public void shouldFetchWeatherInformation() throws Exception {
        Optional<WeatherResponse> weatherResponse = weatherClient.fetchWeather();
        assertThat(weatherResponse.isPresent(), is(true));
        assertThat(weatherResponse.get().getSummary(), is("Rain"));
    }
}

````

Se você observar atentamente, verá que o `WeatherClientConsumerTest` é muito semelhante ao `WeatherClientIntegrationTest`. Em vez de usar o `Wiremock` para o stub do servidor, usamos o Pact desta vez. Na verdade, o teste do consumidor funciona exatamente como o teste de integração; substituímos o servidor real de terceiros por um stub, definimos a resposta esperada e verificamos se nosso cliente pode analisar a resposta corretamente. Nesse sentido, o `WeatherClientConsumerTest` é um teste de integração estreito em si. A vantagem sobre o teste baseado em wiremock é que este teste gera um arquivo pact (encontrado em `target / pacts / & pact-name> .json`) sempre que é executado. Este arquivo pact descreve nossas expectativas para o contrato em um formato JSON especial. Esse arquivo pact pode então ser usado para verificar se nosso servidor de stub se comporta como o servidor real. Podemos pegar o arquivo pact e entregá-lo à equipe que fornece a interface. Eles pegam esse arquivo pact e escrevem um teste do provedor usando as expectativas definidas lá. Dessa forma, eles testam se sua API atende a todas as nossas expectativas.

Você vê que é aqui que a parte orientada pelo consumidor do CDC vem. O consumidor orienta a implementação da interface descrevendo suas expectativas. O provedor deve garantir que eles atendam a todas as expectativas e pronto. Sem exagero, sem YAGNI e coisas assim.

Obter o arquivo pact para a equipe fornecedora pode acontecer de várias maneiras. Uma simples é verificá-los no controle de versão e dizer à equipe provedora para sempre buscar a versão mais recente do arquivo pact. Uma mais avançada é usar um repositório de artefatos, um serviço como o S3 da Amazon ou o broker do pact. Comece simples e cresça conforme sua necessidade.

Em sua aplicação do mundo real, você não precisa de um teste de integração e um teste do consumidor para uma classe de cliente. A base de código de exemplo contém ambos para mostrar como usar um ou outro. Se você deseja escrever testes CDC usando pact, recomendo aderir ao último. O esforço de escrever os testes é o mesmo. Usar pact tem a vantagem de que você automaticamente obtém um arquivo pact com as expectativas do contrato que outras equipes podem usar para implementar facilmente seus testes de provedor. Claro, isso só faz sentido se você puder convencer a outra equipe a usar o pact também. Se isso não funcionar, usar a combinação de teste de integração e wiremock é um plano B decente.

### <a id="sec-provider-test-other-team"></a>Teste de provedor (outro time)

O teste do provedor precisa ser implementado pelas pessoas que fornecem a API do clima. Estamos consumindo uma API pública fornecida pela darksky.net. Em teoria, a equipe da darksky implementaria o teste do provedor em seu próprio sistema para verificar se não estão quebrando o contrato entre sua aplicação e nosso serviço.

Obviamente, eles não se importam com nossa modesta aplicação de exemplo e não vão implementar um teste CDC para nós. Essa é a grande diferença entre uma API voltada ao público e uma organização adotando microserviços. APIs públicas não podem considerar todos os consumidores lá fora, caso contrário, ficariam impossibilitadas de avançar. Dentro de sua própria organização, você pode - e deve. Seu aplicativo provavelmente atenderá a um punhado, talvez algumas dúzias de consumidores no máximo. É recomendável que você escreva testes do provedor para essas interfaces, a fim de manter um sistema estável.

A equipe fornecedora recebe o arquivo pact e o executa contra seu serviço de fornecimento. Para isso, eles implementam um teste do provedor que lê o arquivo pact, define alguns dados de teste e executa as expectativas definidas no arquivo pact em seu serviço.

Os desenvolvedores do Pact escreveram várias bibliotecas para implementar testes do provedor. Seu [repositório](https://github.com/pact-foundation/pact-jvm) principal no GitHub fornece uma boa visão geral de quais bibliotecas de consumidor e de provedor estão disponíveis. Escolha aquela que melhor se adapta à sua pilha tecnológica.

Para simplificar, vamos assumir que a API da darksky é implementada em Spring Boot também. Nesse caso, eles poderiam usar o [provedor de contrato do Spring](https://github.com/DiUS/pact-jvm/tree/master/pact-jvm-provider-spring), que se integra perfeitamente aos mecanismos do MockMVC do Spring. Um teste de provedor hipotético que a equipe da darksky.net implementaria poderia ser assim:


````JAVA
@RunWith(RestPactRunner.class)
@Provider("weather_provider") // mesmo que o "provider_name" em nosso clientConsumerTest
@PactFolder("target/pacts") // informa ao pacto onde estão os arquivos pact que devem ser carregados
public class WeatherProviderTest {
    @InjectMocks
    private ForecastController forecastController = new ForecastController();

    @Mock
    private ForecastService forecastService;

    @TestTarget
    public final MockMvcTarget target = new MockMvcTarget();

    @Before
    public void before() {
        initMocks(this);
        target.setControllers(forecastController);
    }

    @State("weather forecast data") // mesmo que "given()" no nosso clientConsumerTest
    public void weatherForecastData() {
        when(forecastService.fetchForecastFor(any(String.class), any(String.class)))
                .thenReturn(weatherForecast("Rain"));
    }
}

````

Você percebe que tudo o que o teste do provedor tem que fazer é carregar um arquivo de pacto (por exemplo, usando a anotação @PactFolder para carregar arquivos de pacto previamente baixados) e, em seguida, definir como os dados de teste para estados predefinidos devem ser fornecidos (por exemplo, usando mockitos). Não há teste personalizado a ser implementado. Todos são derivados do arquivo de pacto. É importante que o teste do provedor tenha correspondentes correspondentes ao nome do provedor e ao estado declarado no teste do consumidor.

### <a id="sec-provider-test-our-team"></a>Teste de provedor (nosso time)

Vimos como testar o contrato entre nosso serviço e o provedor de clima. Com esta interface, nosso serviço age como consumidor, e o serviço de clima age como provedor. Pensando um pouco mais, veremos que nosso serviço também age como provedor para outros: fornecemos uma API REST que oferece alguns endpoints prontos para serem consumidos por outros.

Como acabamos de aprender que os testes de contrato são a moda do momento, é claro que escrevemos um teste de contrato para este contrato também. Felizmente, estamos usando contratos orientados pelo consumidor, então há todas as equipes consumidoras nos enviando seus Pactos que podemos usar para implementar nossos testes de provedor para nossa API REST.

Vamos primeiro adicionar a biblioteca de provedor Pact para Spring ao nosso projeto:

````JAVA
testCompile('au.com.dius:pact-jvm-provider-spring_2.12:3.5.5')
````

Implementar o teste do provedor segue o mesmo padrão descrito anteriormente. Por uma questão de simplicidade, eu simplesmente adicionei o arquivo Pact do nosso [consumidor simples](https://github.com/hamvocke/spring-testing-consumer) ao repositório do nosso serviço. Isso torna mais fácil para o nosso propósito, mas em um cenário da vida real, provavelmente você usaria um mecanismo mais sofisticado para distribuir seus arquivos Pact.

````JAVA
@RunWith(RestPactRunner.class)
@Provider("person_provider")// same as in the "provider_name" part in our pact file
@PactFolder("target/pacts") // tells pact where to load the pact files from
public class ExampleProviderTest {

    @Mock
    private PersonRepository personRepository;

    @Mock
    private WeatherClient weatherClient;

    private ExampleController exampleController;

    @TestTarget
    public final MockMvcTarget target = new MockMvcTarget();

    @Before
    public void before() {
        initMocks(this);
        exampleController = new ExampleController(personRepository, weatherClient);
        target.setControllers(exampleController);
    }

    @State("person data") // same as the "given()" part in our consumer test
    public void personData() {
        Person peterPan = new Person("Peter", "Pan");
        when(personRepository.findByLastName("Pan")).thenReturn(Optional.of
                (peterPan));
    }
}

````

O ExampleProviderTest mostrado precisa fornecer o estado de acordo com o arquivo de contrato que nos é fornecido, é isso. Assim que executamos o teste do provedor, o Pact irá pegar o arquivo de contrato e disparar uma solicitação HTTP contra nosso serviço, que responderá de acordo com o estado que configuramos.

## <a id="sec-ui-tests"></a>UI Tests

## <a id="sec-end-to-end-tests"></a>Testes End-to-End

Testar sua aplicação por meio de sua interface de usuário é a maneira mais completa de testar sua aplicação. Os testes de UI orientados por _webdriver_ descritos anteriormente são um bom exemplo de testes _end-to-end_.

![Testes end-to-end](/assets/e2etests.png "Testes end-to-end")

**Figura 11**. Testes _end-to-end_ testam completamente o seu sistema

Os testes _end-to-end_ (também chamados de [Broad Stack Tests](https://martinfowler.com/bliki/BroadStackTest.html) ) fornecem a maior confiança quando você precisa decidir se seu software está funcionando ou não. O [Selenium](https://www.selenium.dev/) e o [protocolo WebDriver](https://www.w3.org/TR/webdriver/) permitem que você automatize seus testes conduzindo automaticamente um navegador (headless) contra seus serviços implantados, realizando cliques, inserindo dados e verificando o estado de sua interface de usuário. Você pode usar o Selenium diretamente ou usar ferramentas construídas sobre ele, [Nightwatch](https://nightwatchjs.org/) sendo uma delas.

Os testes _end-to-end_ vêm com seus próprios tipos de problemas. Eles são notoriamente incertos e muitas vezes falham por razões inesperadas e imprevisíveis. Frequentemente, sua falha é um falso positivo. Quanto mais sofisticada sua interface de usuário, mais inconstantes os testes tendem a se tornar. Peculiaridades do navegador, problemas de tempo, animações e caixas de diálogo pop-up inesperadas são apenas alguns dos motivos que me fizeram gastar mais tempo com depuração do que gostaria de admitir.

Em um mundo de microsserviços existe também a grande questão sobre quem está encarregado de escrever estes testes. Já que são abrangidos vários serviços (o sistema inteiro) não existe apenas um time responsável pela escrita de testes _end-to-end_.

Se você dispõe de um time centralizado de garantia de qualidade (_Quality Assurance_) este pareceria uma opção conveniente. Mas novamente, lançar mão de um time centralizado de QA é um grande anti-padrão e não deveria acontecer em um universo DevOps onde as suas equipes deveriam ser realmente multifuncionais. Não existe resposta fácil sobre quem deveriar estar encarregado de testes _end-to-end_. Talvez sua organização tenha uma comunidade de prática ou um _Quality Guild_ que poderia cuidar disso. Encontrar a resposta correta depende muito da sua Organização.

Além disso, testes _end-to-end_ requerem muito mais manutenção e são de execução lenta. Se imaginarmos uma situação com pouco mais que alguns microsserviços em execução não seria possível nem rodar os testes _end-to-end_ localmente - já que isso necessitaria que todos os microsserviços também fossem executados localmente. Boa sorte tentando rodar centenas de aplicações na sua máquina sem fritar sua RAM.

Devido aos altos custos de manutenção você deve ter como objetivo reduzir o número de testes _end-to-end_ ao mínimo possível.

Pense nas interações de alto valor que os usuários terão com seu aplicativo. Tente criar jornadas do usuário que definam o valor principal do seu produto e traduza as etapas mais importantes dessas jornadas em testes automatizados _end-to-end_.

Se você estiver construindo um site de _e-commerce_, sua jornada de cliente mais valiosa pode ser um usuário que procura um produto, o coloca na cesta de compras e faz o _check-out_. É isso. Enquanto esta jornada funcionar, você não deve ter muitos problemas. Talvez você encontre mais uma ou duas jornadas de usuário cruciais que possam ser traduzidas em testes  _end-to-end_. Tudo a mais do que isso provavelmente será mais doloroso do que útil.

Lembre-se: você tem muitos níveis inferiores em sua pirâmide de teste, onde já testou todos os tipos de casos dos extremos e integrações com outras partes do sistema. Não há necessidade de repetir esses testes em um nível superior. Alto esforço de manutenção e muitos falsos positivos irão atrasá-lo e fazer com que você perca a confiança em seus testes, mais cedo ou mais tarde.

### <a id="sec-ui-end-to-end-tests"></a>User Interface End-to-End Test

### <a id="sec-api-end-to-end-tests"></a>REST API End-to-End Test

## <a id="sec-exploratory-tests"></a>Exploratory Testing

## <a id="sec-terminology-confusion"></a>The Confusion About Testing Terminology

## <a id="sec-deployment-pipeline"></a>Inserindo Testes em seu Pipeline de Deploy

Se você está usando Continuous Integration ou Continuous Delivery, você terá um [Deployment Pipeline](https://martinfowler.com/bliki/DeploymentPipeline.html) em funcionamento que executará testes automatizados sempre que você fizer uma alteração em seu software. Geralmente, este pipeline é dividido em várias etapas que, gradualmente, lhe dão mais confiança de que seu software está pronto para ser implantado em produção. Ao ouvir falar sobre todos esses diferentes tipos de testes, você provavelmente está se perguntando como deve colocá-los em seu Deployment Pipeline. Para responder a isso, basta pensar em um dos valores fundamentais da Continuous Delivery (de fato, um dos principais [valores da Extreme Programming](http://www.extremeprogramming.org/values.html) e também do desenvolvimento ágil de software): **Fast Feedback**.

Um bom pipeline de construção lhe informa que você errou o mais rápido possível. Você não quer esperar uma hora apenas para descobrir que sua última mudança quebrou alguns testes unitários simples. É provável que você já tenha ido para casa se seu pipeline demorar muito para lhe dar esse feedback. Você poderia obter essa informação em questão de segundos, talvez alguns minutos, colocando os testes de execução rápida nas primeiras etapas de seu pipeline. Consequentemente, você coloca os testes de execução mais longos - geralmente os com escopo mais amplo - nas etapas posteriores para não adiar o feedback dos testes de execução rápida. Percebe então, que definir as etapas do seu Deployment Pipeline não é uma tarefa impulsionada pelos tipos de testes, mas sim pela velocidade e escopo dos testes a serem realizados. Com isso em mente, pode ser uma decisão boa colocar alguns testes de integração com escopos estreitos e testes de execução rápida na mesma etapa que seus testes unitários - simplesmente porque eles lhe dão um feedback mais rápido (Fast Feedback) e não porque você quer traçar a linha ao longo do tipo de seus testes.

## <a id="sec-test-duplication"></a>Evitando duplicação de testes

Agora que você sabe que deve escrever diferentes tipos de testes, há mais uma armadilha a evitar: duplicar testes em diferentes camadas da pirâmide. Embora seu instinto possa dizer que nunca é demais testar, deixe-me garantir que há um limite. Cada teste em sua suíte de testes é uma carga adicional e não é gratuito. Escrever e manter testes leva tempo. Ler e entender testes de outras pessoas leva tempo. E, é claro, executar testes leva tempo.

Assim como no código de produção, você deve buscar a simplicidade e evitar a duplicação. No contexto da implementação da sua pirâmide de testes, você deve ter em mente duas regras básicas:

1. Se um teste de nível superior identificar um erro e não houver falha em um teste de nível inferior, você precisa escrever um teste de nível inferior.

2. Empurre seus testes o mais para baixo possível na pirâmide de testes.

A primeira regra é importante porque os testes em níveis inferiores permitem que você identifique erros com mais precisão e os replique de forma isolada. Eles serão executados mais rapidamente e serão menos inchados quando você estiver depurando o problema em questão. Eles também servirão como um bom teste de regressão para o futuro. A segunda regra é importante para manter sua suíte de testes rápida. Se você testou todas as condições com confiança em um teste em nível inferior, não há necessidade de manter um teste em nível superior na sua suíte de testes. Isso simplesmente não adiciona mais confiança de que tudo está funcionando. Ter testes redundantes se tornará irritante em seu trabalho diário. Sua suíte de testes será mais lenta e você precisará alterar mais testes quando alterar o comportamento do seu código.

Vamos dizer de outra forma: se um teste em nível superior lhe dá mais confiança de que sua aplicação está funcionando corretamente, você deve tê-lo. Escrever um teste unitário para uma classe *Controller* ajuda a testar a lógica dentro do *Controller* em si. No entanto, isso não lhe dirá se o *endpoint* REST que esse *Controller* fornece realmente responde a solicitações HTTP. Então você sobe na pirâmide de testes e adiciona um teste que verifica exatamente isso - mas nada mais. Você não testa toda a lógica condicional e casos de borda que seus testes em níveis inferiores já cobrem novamente no teste em nível superior. Certifique-se de que o teste em nível superior se concentra na parte que os testes em níveis inferiores não puderam cobrir.

Eu sou rigoroso quando se trata de eliminar testes que não fornecem nenhum valor. Eu excluo testes em níveis mais altos que já são cobertos em um nível inferior (desde que não forneçam valor adicional). Eu substituo testes em níveis superiores por testes em níveis inferiores, se possível. Às vezes isso é difícil, especialmente se você sabe que criar um teste foi um trabalho árduo. Cuidado com a falácia do custo afundado e aperte a tecla delete. Não há razão para desperdiçar mais tempo precioso em um teste que deixou de fornecer valor.

## <a id="sec-clean-test-code"></a>Escrevendo Código de Teste Limpo

Assim como escrever código em geral, criar um código de teste bom e limpo exige muito cuidado. Aqui estão mais algumas dicas para criar um código de teste de fácil manutenção antes de prosseguir com sua suíte de testes automatizados:

1.  Código de teste é tão importante quanto código de produção. Dê o mesmo nível de atenção e cuidado para
    ele. "*Isso é apenas código de teste*" não é uma desculpa válida para justificar um código desleixado
2.  Teste uma condição por teste. Isso vai ajudar a manter os seus testes curtos e fáceis de racionar
3.  "*Preparar, Rodar, Verificar*" ou "*Dado, Quando, Então*" são bons mnemônicos para manter seus testes
    bem estruturados
4.  Legibilidade importa. Não tente ser excessivamente DRY. Duplicação é aceitável, se melhorar a
    legibilidade. Tente encontrar um equilíbrio entre códigos [DRY e DAMP](https://stackoverflow.com/questions/6453235/what-does-damp-not-dry-mean-when-talking-about-unit-tests)
5.  Quando estiver com dúvida use a [Rule of Three](https://blog.codinghorror.com/rule-of-three/) para
    decidir quando refatorar. *Use antes de reutilizar*

## <a id="sec-conclusion"></a>Conclusion

## <a id="sec-appendix"></a>Appendix

### <a id="sec-private-method-tests"></a>But I Really Need to Test This Private Method

### <a id="sec-test-helpers"></a>Specialised Test Helpers

[^1]: **Nota do tradutor**: no README do [repositório](https://github.com/hamvocke/spring-testing) da aplicação de exemplo, o autor menciona ter trocado a API do *darksky.net* pela do *openweathermap.org*, depois que a primeira foi desativada para consulta pública à previsão do tempo.
