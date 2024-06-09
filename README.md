# Introdução

Existem inúmeros problemas de alto interesse em se desenvolver soluções, porém o que muitas vezes pode passar batido é que problemas distintos podem ser bem mais parecidos do que se pode achar, em primeiro momento. Nesses casos seria interessante de algum modo tirar proveito disto, de suas estruturas parecidas e buscar gerar um algoritmo mais genérico possível de modo que ambos possam ser solucionados o usando com o mínimo de alterações. Isso expõe ligações entre problemas que antes poderiam parecer ocultas e serve como base para futuros problemas que compartilhem essa mesma estrutura.

O trabalho consiste em explorar essa ideia de construcção de algoritmos genéricos simplesmente redefinindo as operações de soma e produto para multiplicação de matrizes. Alguns exemplos são dados, mas certamente não são os únicos. O arquivo em pdf detalha mais um pouco algumas coisas, inclusive trazendo um pouco mais de teoria, mas esta leitura já é o suficiente.

# Multiplicação matricial convencional

Antes de alterar o significado das operações, é importante se ter bem definido o algoritmo para multiplicação matricial convencional. Se tenho uma matriz $A_{m \times n}$ e uma matriz $B_{n \times p}$, o produto $AB$ resultará em uma matriz $C_{m \times p}$ onde cada entrada $c_{ij}$ é definida por

$$c_{ij} = \sum_{k=0}^{n}a_{ik}b_{kj}$$

onde $c_{ij}$ lê-se como "linha $i$, coluna $j$ de $C$". Racioncínio análogo para $a_{ik}$ e $b_{kj}$.

Portanto o produto de duas matrizes é definido utilizando unicamente somas e multiplicações de seus elementos. A ideia agora é demonstrar que, alterando o significado destas operações, uma gama de novos problemas podem ser resolvidos.

Usarei uma notação para representar as operações que estão sendo usadas, assim como o conjunto em que os elementos estão e os elementos neutros de cada operação. De exemplo, neste caso poderíamos ter

$$(\mathcal{R}, +, \times, 0, 1)$$

Com $\mathcal{R}$ sendo os números reais, $+$ e $\times$ sendo as operações de soma e multiplicação convencionais e $0$ e $1$ sendo seus elementos neutros, respectivamente. Elemento neutro pois $0 +$ qualquer número dá o próprio número e $1 \times$ qualquer número também dá o próprio número.

Em seguida mostro algumas aplicações.

# Algoritimos em Grafos

É um pouco surpreendente que apenas alterando as operações de soma e produto é possível obter soluções para inúmeros problemas modelados como grafos. Essa seção foca nestes problemas em específico.

## Soma dos caminhos

Antes de alterar as operações, é interessante entender o que de fato se tem com as operações usuais. Suponha um grafo qualquer e sua matriz de adjacências $G$. Caso as operações originais sejam mantidas, pode-se interpretar o produto de $G$ consigo mesma como a soma de caminhos do grafo de comprimento 2, multiplicando o peso das arestas neste caminho. Para ficar mais claro, tome o grafo da figura 1 como exemplo.

<div align="center">
  <image src="https://github.com/Mth0/Multiplicacao-Matricial-Magica/assets/49344106/a834342e-e81f-4d1e-83f5-377ad9700c87">
</div>

Sua matriz de adjcências será

$$G = \begin{bmatrix}
		0 & 1 & 2 & 0 & 0\\
		5 & 0 & 5 & 0 & 0\\
		0 & 0 & 0 & 4 & 0\\
		0 & 4 & 0 & 0 & 2\\
		0 & 0 & 3 & 0 & 0\\
	\end{bmatrix}$$

Onde $g_{ij}$ representa o peso a aresta do vértice $i$ para o vértice $j$.

e $G^{2}$ será

$$G^{2} = \begin{bmatrix}
		5 & 0 & 5 & 8 & 0\\
		0 & 5 & 10 & 20 & 0\\
		0 & 16 & 0 & 0 & 8\\
		20 & 0 & 26 & 0 & 0\\
		0 & 0 & 0 & 12 & 0\\
	\end{bmatrix}$$

 Note que, por exemplo $g_{4, 1}$ possui o valor $20$, que vem do único caminho  $4 \overset{4}{\to} 2$ e $2 \overset{5}{\to} 1$ de comprimento 2 entre o vértice $4$ e o vértice $1$. A ideia se segue para as outras entradas de $G^{2}$. Tente ver os outros resultados batendo!

 A ideia se mantém para outras potências de $G$ e caso se queira a soma de todos os caminhos de comprimento 1, 2, 3 e assim por diante até um valor $n$, pode-se somar essas potências

 $$\sum_{i=1}^{n} G^{n} = G^{1} + G^{2} + .... + G^{n}$$

 ### Backpropagation

 Uma aplicação direta do que foi visto acima é para o algoritmo de backpropagation, amplamente utilizando em redes neurais para atualização de pesos. Uma rede neural pode ser modelada como um grafo, onde as arestas são justamente os pesos da rede. Outro modo de representar isso é dizer que as arestas são as derivadas parciais do nó em que se está indo em relação ao nó em que se está saindo. Tome de exemplo a seguinte rede neural (em fato, esta última representação é um grafo computacional, mas não é necessário entrar em detalhes nisto).

<div align="center">
  <image src="https://github.com/Mth0/Multiplicacao-Matricial-Magica/assets/49344106/587a71fb-34e9-4721-af40-fd884eda0c51">
</div>

Repare que calcular a derivada parcial de um nó $q_{i}$ em função de $q_{j}$ é justamente somar todos os caminhos $q_{j} \overset{w} \to q_{i}$ no grafo. Como o maior caminho neste grafo tem comprimento 5, basta calcular
$$\sum_{i=1}^{5}G^{i}$$
e checar a entrada $g_{ji}$ (índices trocados pois as arestas estão indo para a frente, mas o algoritmo de backpropagation propaga para trás)!

## Caminho mínimo

Agora o conjunto que será usado é $(\mathcal{R} \cup \{ +\infty \}, \min, +, +\infty, 0)$. A soma se torna tomar o mínimo entre dois valores e o produto se torna somar dois números normalmente. O novo elemento $+\infty$ faz o papel de elemento neutro, visto que o mínimo de qualquer número e ele dá o próprio número. Agora a matriz $G$ terá a seguinte cara

$$G = \begin{bmatrix}
		0 & 1 & 2 & +\infty & +\infty\\
		5 & 0 & 5 & +\infty & +\infty\\
		+\infty & +\infty & 0 & 4 & +\infty\\
		+\infty & 4 & +\infty & 0 & 2\\
		+\infty & +\infty & 3 & +\infty & 0\\
	\end{bmatrix}$$

 $0$ na diagonal, pois não há custo em ir de si para si mesmo e $+\infty$ sinalizando que não existe arestas entre dois vértices.

 $G^{2}$ agora possui os caminhos de comprimento 2 de menor custo entre cada par de vértices. Para se obter o caminho mínimo independente de seu tamanho, basta tirar o mínimo de todas as potências de $G$, isto é, calcular
 
$$\min_{i=0}^{+\infty}G^{i}$$

Por sorte, é possível calcular isso. Como o grafo tem apenas 5 vértices e não possui pesos negativos nas arestas, qualquer caminho com mais de 5 arestas irá aumentar o custo do caminho, logo não será mínimo. Essa soma gigantesca pode então ser reduzida para

$$\min_{i=0}^{5}G^{i}$$

Isso dá exatamente o algoritimo de Floyd-Warshall para caminhos mínimos!

### Caminho máximo

O que foi feito não é exclusivo para caminhos mínimos. Para se obter o caminho máximo, troque $\min$ por $\max$ e $+\infty$ por $-\infty$. Teria-se então $(\mathcal{R} \cup \{ -\infty \}, \max, +, -\infty, 0)$.

## Existência de caminhos entre vértices em um grafo

Imagine agora que queremos ser um pouco mais relaxados. Não nos interessa o caminho **mínimo** ou **máximo** entre dois vértices, mas sim se existe ou não algum caminho entre eles. Esse problema também pode ser modelado como um produto e soma de matrizes. Agora será utilizado $(Binario, \lor, \land, 0, 1)$, onde $Binario$ só possui os números $1$ e $0$ e $\lor$ e $\land$ são o "ou" e "e" lógicos, respectivamente. Se $0$ for considerado $\texttt{FALSE}$ e $1$ $\texttt{TRUE}$, então basicamente teremos operações booleanas. Agora toda entrada de $G$ que não era igual a $0$ será transformada em $1$, visto que o que interessa agora é saber se existe ou não caminho entre vértices e não seus pesos. G então fica

$$G = \begin{bmatrix}
		0 & 1 & 1 & 0 & 0\\
		1 & 0 & 1 & 0 & 0\\
		0 & 0 & 0 & 1 & 0\\
		0 & 1 & 0 & 0 & 1\\
		0 & 0 & 1 & 0 & 0\\
	\end{bmatrix}$$

 e $G^{2}$ responde a pergunta "existe algum caminho entre esses dois vértices de comprimento 2?". Para se obter a resposta geral de "existe algum caminho, **de qualquer comprimento**, entre esses dois vértices?", podemos simplesmente somar as potências de $G$ como já foi feito em outros exemplos antes.

$$\bigvee_{i=0}^{+\infty}G^{i}$$

Novamente, por sorte o maior caminho, sem entrar em ciclos, é 5, então essa soma pode ser reduzida para

$$\bigvee_{i=0}^{5}G^{i}$$

# Linguagens Formais e Teoria do Autômatos

Esta seção no momento está somente no pdf, que tenta se aprofundar um pouco mais na teoria dessa área. Recomendo a leitura deste para um melhor entendimento. Recomendo também a leitura da parte de semianéis e ponto fixo, linguajar usado nesta seção.
