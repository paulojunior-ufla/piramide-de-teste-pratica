# A Pirâmide de Teste Prática

Esta é uma tradução do artigo [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader), originalmente escrito por Ham Vocke.

---

A "Pirâmide de Teste" é uma metáfora que diz para agrupar testes de software em compartimentos (*buckets*) de diferentes granularidades. Ela também oferece uma ideia de quantos testes devemos ter em cada um desses compartimentos. Embora o conceito da pirâmide de teste exista há algum tempo, as equipes de desenvolvimento ainda lutam para colocá-la em prática de forma adequada. Este artigo revisita o conceito original da pirâmide de teste e apresenta como você pode colocá-la em prática. O artigo também mostra os tipos de teste que você deve procurar em diferentes níveis da pirâmide e oferece exemplos práticos sobre como eles podem ser implementados.

![A Pirâmide de Teste Prática](/assets/teaser.png "A Pirâmide de Teste Prática")

### Conteúdo

[A Importância da Automação (de Testes)](#sec-test-automation)

[A Pirâmide de Teste](#sec-test-pyramid)

[Ferramentas e Bibliotecas que veremos](#sec-tools-and-libs)

[Aplicação de Exemplo](#sec-sample-app)


Um software pronto para produção requer testes antes de definitivamente entrar em produção. À medida que a área de desenvolvimento de software amadureceu, as abordagens para teste de software também amadureceram. Ao invés de se ter uma miríade de testadores manuais de software, as equipes de desenvolvimento passaram a automatizar a maior parte de seus esforços com teste de software. Automatizar os testes permite que as equipes saibam se seu software está "quebrado" em questão de segundos e minutos, em vez de dias e semanas. 

O ciclo de *feedback* drascitamente curto, alimentado por testes automatizados, anda de mãos dadas com práticas de desenvolvimento ágil, entrega contínua e cultura DevOps. Ter uma abordagem efetiva para teste de software permite que as equipes se movam rapidamente e com confiança.

Este artigo explora o que um portfólio de teste deve ter para ser considerado responsivo, confiável e manutenível - independentemente se você está construindo uma arquitetura de microsserviços, aplicativos móveis ou ecossistemas de IoT (*Internet of Things*). Também entraremos em detalhes sobre a criação de testes automatizados efetivos e legíveis. 

### <a id="sec-test-automation"></a>A Importância da Automação (de Testes)

O software tem se tornado parte essencial do mundo em que vivemos. Ele superou seu único propósito inicial de tornar negócios mais eficientes. Hoje, as empresas tentam encontrar maneiras de se tornarem empresas digitais de primeira classe. Como usuários, todos nós interagimos com uma quantidade cada vez maior de software diariamente. As rodas da inovação estão girando rapidamente.

Se você quiser manter o ritmo, terá que procurar maneiras de entregar software mais rapidamente, sem sacrificar sua qualidade. Entrega contínua, prática na qual você garante automaticamente que seu software pode ser colocado em produção (*released*) a qualquer momento, pode ajudá-lo com isso. Com a entrega contínua, você usa um *pipeline* de contrução para testar automaticamente seu software e implantá-lo em seus ambientes de teste e produção.

Contruir, testar e implantar manualmente uma quantidade cada vez maior de software logo se torna algo impossível - a menos que você queira gastar todo o seu tempo com trabalho manual e repetitivo, ao invés de entregar software funcionando. Automatizar tudo - da construção aos testes, implantação e infraestrutura - é o único caminho a seguir.

![Pipeline de contrução](/assets/buildPipeline.png "Pipeline de contrução")

**Figura 1**. Use *pipelines* de construção para colocar seu software em produção de forma automática e confiável.

Tradicionalmente, o teste de software era um trabalho  excessivamente manual, feito ao implantar a aplicação em um ambiente de teste e, então, realizar alguns testes no estilo caixa-preta, por exemplo, clicando através de sua interface de usuário para ver se alguma coisa quebrou. Geralmente, esse testes eram determinados por *roteiros de teste*, para garantir que os testadores fizessem verificações de forma consistente.  

É óbvio que testar todas as alterações manualmente é demorado, repetitivo e tedioso. Algo repetitivo é chato e o que é chato leva a erros e faz você procurar um emprego novo no final da semana.

Felizmente, existe um remédio para tarefas repetitivas: **automação**.

Automatizar seus testes repetitivos pode ser uma grande virada de jogo em sua vida como desenvolvedor de software. Automatize esses testes e você não terá mais que seguir protocolos de cliques, sem pensar, a fim de verificar se seu software ainda funciona corretamente. Automatize seus testes e você poderá alterar sua base de código sem titubear. Se você já tentou fazer uma refatoração em larga escala, sem uma suíte de teste adequada, aposto que você sabe o quão terrível esta experiência pode ser. Como você saberia se acidentalmente quebrasse alguma coisa ao longo do caminho? Bem, você "clica", de acordo com todos os seus *roteiros de teste* manuais; é assim. Mas, sejamos honestos: você realmente gosta disso? Que tal realizar alterações em larga escala e saber se você quebrou alguma coisa em segundos, enquanto toma um bom gole de café? Parece mais agradável, não é mesmo?

### <a id="sec-test-pyramid"></a>A Pirâmide de Teste

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

### <a id="sec-tools-and-libs"></a>Ferramentas e Bibliotecas que veremos

* [JUnit](http://junit.org/):  nosso executor de testes (*test runner*)
* [Mockito](http://site.mockito.org/): para "mockar" dependências
* [Wiremock](http://wiremock.org/): para criar *stubs* de serviços externos
* [Pact](https://docs.pact.io/): para escrever testes CDC
* [Selenium](http://docs.seleniumhq.org/): para escrever testes *end-to-end* executados a partir da interface de usuário
* [REST-assured](https://github.com/rest-assured/rest-assured): para escrever testes *end-to-end* executados a partir dos *endpoints* de uma API REST

### <a id="sec-sample-app"></a>Aplicação de Exemplo

Eu escrevi um [microsserviço simples](https://github.com/hamvocke/spring-testing), incluindo uma suíte de teste com testes para as diferentes camadas da pirâmide de teste.

A aplicação de exemplo apresenta as características de um típico microsserviço. Ela expõe uma interface REST, "conversa" com uma base de dados e busca informações de um serviço REST de terceiros. Ela foi implementada usando [Spring Boot](https://projects.spring.io/spring-boot/) e deve ser compreensível mesmo que você nunca tenha trabalho com Sring Boot antes.

Certifique-se de verificar o [código no Github](https://github.com/hamvocke/spring-testing). O *README* do projeto contém as instruções necessárias para você executar a aplicação, bem como seus testes automatizados, em sua máquina.