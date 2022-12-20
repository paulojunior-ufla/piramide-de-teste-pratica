# A Pirâmide de Teste Prática

Esta é uma tradução do artigo [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html?utm_source=pocket_reader), originalmente escrito por Ham Vocke.

---

A "Pirâmide de Teste" é uma metáfora que diz para agrupar testes de software em compartimentos (*buckets*) de diferentes granularidades. Ela também oferece uma ideia de quantos testes devemos ter em cada um desses compartimentos. Embora o conceito da pirâmide de teste exista há algum tempo, as equipes de desenvolvimento ainda lutam para colocá-la em prática de forma adequada. Este artigo revisita o conceito original da pirâmide de teste e apresenta como você pode colocá-la em prática. O artigo também mostra os tipos de teste que você deve procurar em diferentes níveis da pirâmide e oferece exemplos práticos sobre como eles podem ser implementados.

![A Pirâmide de Teste Prática](/assets/teaser.png "A Pirâmide de Teste Prática")

### Conteúdo

[A Importância da Automação (de Testes)](#sec-test-automation)

[A Pirâmide de Teste](#sec-test-pyramid)

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