# Gerador de Números Pseudo-Aleatórios com LFSR
Atividade final da disciplina Introdução à Microeletrônica, do curso de Engenharia da Computação - UFPB.

Alunos: Giovanni Bruno Travassos de Carvalho; Rafael de Melo Oliveira 

_tags: microeletronics, microeletronica, geradornpa, PRNG, ufpb, LFSR_

## 1. Introdução 
O objetivo desse repositório é mostrar o processo de desenvolvimento do projeto final da disciplina de Introdução à Microeletrônica no semestre 2023.1, que busca construir um
gerador de números pseudo-aleatórios (PRNG, _pseudorandom number generator_) de 8 bits com realimentação linear. O trabalho começou com uma descrição comportamental em alto nível e uma geração de padrões de teste e passou por vários passos, que serão especificados, até chegar em um layout físico e uma simulação precisa da operação de geração dos números aleatórios. Todos os arquivos obtidos estarão anexados a esse repositório. 

## 2. Metodologia 
Para criação do gerador de números pseudo-aleatórios precisamos construir um registrador de deslocamento com realimentação linear (LFSR, _linear feedback shift register_), utilizando a função XOR. O LFSR é um registrador de deslocamento cujo bit de entrada é uma função linear de seu estado anterior. O valor inicial do LFSR é chamado de seed (semente) e, como a operação do registrador é determinística, o fluxo de valores produzido pelo registrador é completamente determinado pelo seu estado atual (ou anterior). Da mesma forma, como o registrador possui um número finito de estados possíveis, ele deverá eventualmente entrar em um ciclo de repetição. No entanto, um LFSR com uma função de
feedback bem escolhida pode produzir uma sequência de bits que parece aleatória e tem um ciclo muito longo.
O polinômio de realimentação usado para um registrador de 8 bits é definido por: 
<p float="center">
 <img src="https://github.com/GiovanniBru/" width="200" />
</p>
Esse polinômio é utilizado para máxima realimentação linear possível com 8 bits. Seu período é definido por (2^𝑛 − 1) onde 𝑛 é o número de bits, portanto o período é igual à 255, ou seja, o gerador irá criar 255 números pseudo-aleatórios antes de repetir algum número. Esse resultado será provado no final.

O gerador NPA construído funciona basicamente pegando um número binário de 8 bits como entrada e aplicando a função XOR em alguns de seus bits levando em consideração o polinômio escolhido, de maneira que o número não irá se repetir. Esse novo número gerado é usado como entrada de realimentação do mesmo circuito.

<p float="center">
 <img src="https://github.com/GiovanniBru/" width="200" />
</p>
<p float="center"> Figura 1: Ilustração do funcionamento do LFSR </p>

Com o método de construção para o gerador estabelecido, antes de passar para o desenvolvimento de uma de linguagem de descrição de hardware, primeiro será obtido um padrão de testes para confirmar uma sequência de saídas esperadas pelo circuito. Por meio da biblioteca “_genpat.h_” é possível obter uma simulação de forma menos complexa para conseguir ter um parâmetro comparativo ao se criar o layout físico.
Após compilar o código por meio do comando “_alliance-genpat -v {nome do arquivo}_” é gerado o arquivo _.pat_ com as saídas processadas sendo possível agora utilizá-las para visualização e método de comparação.
Utilizamos a abordagem _Top-Down_ para concepção do circuito, então começamos com uma descrição em VHDL comportamental de alto nível, sem muita preocupação com a implementação, que será convertida para um circuito implementável em VHDL comportamental, e finalmente, criado o layout físico das células padrão.
Com essa descrição comportamental do circuito feita, utilizamos a ferramenta _vasy_ para convertê-la para um conjunto de VHDL implementável pelas demais ferramentas que serão utilizadas (“_.vbe_”).
O arquivo _.vbe_ obtido foi otimizado através da ferramenta _boom_, que otimiza uma descrição comportamental usando uma representação RBDD (_Reduced Ordered Binary Decision Diagram_) de sua função lógica. Além disso, provamos a equivalência entre os arquivos obtidos com a ferramenta _proof_.

<p float="center">
 <img src="https://github.com/GiovanniBru/" width="200" />
</p>
<p float="center"> Figura 2: Otimização e prova de equivalência </p>

Após, usamos a ferramenta _boog_ (_Binding and Optimizing on Gates_) para mapear uma descrição comportamental em uma biblioteca de células padrão predefinidas. Essa ferramenta constrói uma rede booleana equivalente à descrição otimizada obtida com _boom_. Então, para cada função booleana de cada nó da rede ele tenta encontrar uma célula ou conjunto de células que implemente aquela função. O resultado é um arquivo “_.vst_” que será uma descrição estrutural baseada nas células da biblioteca _sxlib_ da Alliance. Em seguida otimizamos esse arquivo utilizando a ferramenta _loon_ (_Light Optimizing On Nets_).

<p float="center">
 <img src="https://github.com/GiovanniBru/" width="200" />
</p>
<p float="center"> Figura 3: Resultados da otimização do atraso crítico </p>

Podemos observar que a otimização aumentou a área, o não otimizado tem área de 74 mil lambda, já o otimizado tem área de 75 mil lambda, e o número de nós continua o mesmo. Porém o caminho crítico de dados diminui, de 274 ps no não otimizado passa para 66 ps no otimizado, ou seja, o atraso máximo é menor no otimizado. Isso acontece pois os componentes no caminho crítico são bufferizados ao aumentar a largura desses componentes.

## 3. Resultados



