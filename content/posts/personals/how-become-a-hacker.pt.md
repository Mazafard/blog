---
author: ["Eric Steven Raymond", "Maza Fard "] 
date: '2024-09-14'  
description: Um guia para se tornar um hacker e compreender a cultura hacker.  
PTtags:
  - hacking
  - cultura hacker
  - guia
PTcategories:
  - tecnologia
  - programação
PTseries:
  - tutorial de hacking
aliases:
  - como-se-tornar-um-hacker
ShowToc: true  
weight: 1  
title: Como Se Tornar um Hacker  
---

## Por Que Este Documento?

Como programador e empreendedor — e aspirante a hacker — após ler o documento do [Eric Steven Raymond](http://www.catb.org/~esr/faqs/hacker-howto.html), percebi que seria útil criar meu próprio guia, mais atualizado e com minhas próprias experiências incluídas. No entanto, também não faço nenhuma reivindicação de exclusividade sobre este tema; se não gostar do que lê aqui, escreva o seu próprio guia.

**Nota:** No final deste documento, você encontrará uma lista de Perguntas Frequentes (FAQ). Por favor, leia-as duas vezes antes de enviar quaisquer perguntas sobre este guia.

Além disso, considere apoiar os hackers cujos códigos você usa e valoriza. Muitas pequenas doações contínuas, quando somadas, podem criar um valor muito maior.

## O Que É Um Hacker?

Existe uma comunidade compartilhada e uma cultura de programadores altamente qualificados e magos de rede cuja história remonta há décadas, até os primeiros computadores de tempo compartilhado e os primeiros experimentos com a ARPAnet. Os membros dessa cultura inventaram o termo "hacker". Os hackers construíram a Internet. Os hackers construíram o Unix, moldando-o no sistema que vemos hoje. Os hackers criaram a World Wide Web. Se você pertence a essa cultura, se contribuiu para ela e outras pessoas a reconhecem e chamam você de hacker, então você é um hacker.

A mentalidade hacker não está limitada à cultura do software. Existem pessoas que aplicam essa atitude hacker a outras áreas, como eletrônica ou música—na verdade, você pode encontrar essa mentalidade nos níveis mais altos de qualquer ciência ou arte. Hackers de software reconhecem esses espíritos afins em outras áreas e podem chamá-los de hackers também—e alguns afirmam que ser hacker é realmente independente do meio específico em que o hacker trabalha. No entanto, neste documento, focaremos nas habilidades e atitudes dos hackers de software e nas tradições culturais compartilhadas que cunharam o termo "hacker".

Há outro grupo de pessoas que se autodenominam hackers, mas não são. Essas pessoas (geralmente adolescentes) gostam de invadir computadores e manipular sistemas telefônicos. Os verdadeiros hackers chamam essas pessoas de “crackers” e não querem nada com elas. Os hackers reais geralmente acham que os crackers são preguiçosos, irresponsáveis e não muito inteligentes, e se ofendem quando são colocados no mesmo grupo. Infelizmente, muitos jornalistas e escritores foram enganados e usam a palavra "hacker" para descrever crackers; isso irrita profundamente os hackers de verdade.

**A diferença fundamental é esta:** hackers constroem coisas, crackers as destroem.

Se você quer ser um hacker, continue lendo. Se você quer ser um cracker, vá para o grupo de notícias alt.2600 ou para The Nest, e esteja preparado para passar de cinco a dez anos na prisão—depois de perceber que você não é tão esperto quanto pensa. E isso é tudo o que tenho a dizer sobre crackers!

## Atitude Hacker

1. O mundo está cheio de problemas fascinantes esperando para serem resolvidos.
2. Nenhum problema deve precisar ser resolvido duas vezes.
3. Tédio e rotina são maus.
4. Liberdade é boa.
5. Atitude não substitui competência.

Hackers resolvem problemas e constroem coisas, e acreditam em liberdade e ajuda mútua voluntária. Para ser aceito como hacker, você precisa se comportar como se tivesse essa atitude. E para se comportar como se tivesse essa atitude, você realmente precisa acreditar nela.

No entanto, se você achar que cultivar essas atitudes é apenas uma maneira de ser aceito na cultura hacker, está perdendo o ponto. Tornar-se alguém que acredita nessas coisas é importante para você—para ajudar no seu aprendizado e manter sua motivação. Assim como em todas as artes criativas, a maneira mais eficaz de se tornar um mestre é imitar a mentalidade dos mestres—não apenas intelectualmente, mas também emocionalmente.

Ou, como diz o poema Zen moderno:

> Siga o caminho:  
> Olhe para o mestre,  
> Siga o mestre,  
> Caminhe com o mestre,  
> Veja pelos olhos do mestre,  
> Torne-se o mestre.

Portanto, se você quer ser um hacker, repita o seguinte até acreditar:

### 1. O mundo está cheio de problemas fascinantes esperando para serem resolvidos.

Ser hacker é divertido, mas também é trabalhoso. Esse trabalho requer motivação. Atletas de sucesso obtêm sua motivação de uma espécie de prazer físico ao superar seus limites. Da mesma forma, para ser um hacker, você precisa sentir um prazer básico ao resolver problemas, aprimorar suas habilidades e usar sua inteligência.

Se você não é naturalmente assim, precisará se tornar esse tipo de pessoa para ser bem-sucedido como hacker. Caso contrário, sua energia será drenada por distrações como dinheiro, sexo e aprovação social.

(Você também precisa desenvolver uma espécie de fé em sua capacidade de aprender—uma crença de que, mesmo que você não saiba tudo o que precisa para resolver um problema, se conseguir entender apenas uma parte dele, será capaz de aprender o suficiente para resolver a próxima parte, e assim por diante, até que o problema esteja resolvido.)

### 2. Nenhum problema deve precisar ser resolvido duas vezes.

Cérebros criativos são um recurso valioso e limitado. Eles não devem ser desperdiçados reinventando a roda quando há tantos problemas novos e interessantes esperando lá fora.

Para se comportar como um hacker, você precisa acreditar que o tempo de reflexão dos outros hackers é precioso—a ponto de ser quase um dever moral compartilhar informações, resolver problemas e depois dar as soluções para que outros possam resolver novos problemas em vez de refazer os antigos.

Entretanto, "nenhum problema deve precisar ser resolvido duas vezes" não significa que você tenha que considerar todas as soluções existentes como sagradas, ou que exista apenas uma solução correta para cada problema. Muitas vezes, aprendemos muito sobre um problema ao estudar a primeira tentativa de solução. Isso é bom e muitas vezes necessário para que possamos fazer melhor. O que não é bom são as barreiras técnicas, legais ou institucionais artificiais (como código-fonte fechado) que impedem o reuso de uma boa solução e forçam as pessoas a reinventar a roda.

(Você não precisa acreditar que está obrigado a compartilhar todos os seus produtos criativos, embora os hackers que fazem isso sejam os que ganham mais respeito dos seus colegas. É coerente com os valores hacker vender parte do que você cria para pagar sua comida, aluguel e computadores. Usar suas habilidades para sustentar sua família ou até mesmo enriquecer é ótimo, desde que você não se esqueça da sua lealdade à arte e aos seus colegas hackers.)

### 3. Tédio e rotina são maus.

Hackers (e pessoas criativas em geral) nunca devem se entediar ou ser forçados a realizar trabalhos repetitivos e sem sentido, pois, quando isso acontece, significa que eles não estão fazendo o que só eles podem fazer—resolver novos problemas. Isso é um desperdício de tempo e energia para todos. Portanto, o tédio e as tarefas repetitivas não são apenas desagradáveis, mas realmente ruins.

Para se comportar como um hacker, você deve acreditar nisso o suficiente para automatizar ao máximo as partes enfadonhas, não apenas para si mesmo, mas para todos (especialmente para outros hackers).

(Há uma aparente exceção. Às vezes, hackers fazem coisas que parecem repetitivas ou entediantes para um observador, como um exercício mental, ou para adquirir uma habilidade ou experiência específica que não podem obter de outra forma. Mas isso é uma escolha—ninguém que pode pensar deve ser forçado a ficar em uma situação que o entedie.)

### 4. Liberdade é boa.

Hackers são naturalmente anti-autoritários. Qualquer um que possa lhe dar ordens pode impedi-lo de resolver qualquer problema que o fascine—e, dado o funcionamento das mentes autoritárias, geralmente o farão por razões totalmente estúpidas. Portanto, para se comportar como um hacker, você deve desenvolver uma hostilidade instintiva contra a censura, o sigilo e o uso de força ou engano para controlar adultos responsáveis. E você precisa estar disposto a agir com base nessa crença.

### 5. Atitude não substitui competência.

Para ser um hacker, você precisa desenvolver algumas dessas atitudes. Mas ter atitude não fará de você um hacker, assim como não o tornará um atleta campeão ou uma estrela do rock. Tornar-se um hacker requer inteligência, prática, dedicação e trabalho duro.

Portanto, você deve aprender a desconfiar da atitude e a respeitar a competência de todos os tipos. Hackers não deixam impostores desperdiçarem seu tempo, mas reverenciam a competência—especialmente a competência em hacking, mas qualquer competência é valiosa. A competência em habilidades desafiadoras que poucos podem dominar é especialmente boa, e a competência em habilidades que exigem rigor mental, precisão e concentração é a melhor.

Se você respeitar a competência, desfrutará de desenvolvê-la em si mesmo—o trabalho árduo e a dedicação se tornarão um tipo de jogo intenso, em vez de rotina. E essa atitude é vital para se tornar um hacker.

## Habilidades Básicas de Hacking

1. **Aprenda a programar.**
2. **Instale um sistema Unix de código aberto e aprenda a usá-lo.**
3. **Aprenda a usar a Internet em toda a sua capacidade.**
4. **Se você não fala inglês funcional, aprenda.**

A mentalidade hacker é essencial, mas as habilidades são ainda mais. Atitude não substitui competência, e você precisa de um conjunto básico de habilidades antes que qualquer hacker sonhe em chamá-lo de hacker.

Essa caixa de ferramentas evolui lentamente com o tempo, pois a tecnologia cria novas habilidades e torna as antigas obsoletas. No momento, os requisitos essenciais são aprender a programar, usar e executar Unix, escrever código e ter uma boa compreensão do inglês funcional.

---

**Mais conteúdos nas próximas partes deste documento...**