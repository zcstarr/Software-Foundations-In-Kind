# IndProp:  Proposições Definidas Indutivamente

## Proposições Definidas Indutivamente

No capítulo de Lógica, examinamos várias maneiras de escrever proposições, incluindo conjunção, disjunção e quantificadores. Neste capítulo, adicionamos uma nova ferramenta à mistura: definições indutivas.

Lembre-se de que vimos duas maneiras de afirmar que um número *n* é par: podemos dizer (1) ``Evenb n = Bool.true`` ou (2) `(k => Equal n (Nat.double k))`. Outra possibilidade é dizer que n é par se pudermos estabelecer sua paridade a partir das seguintes regras:

  - Regra *Ev_0*: O número 0 é par. 
  - Regra *Ev_SS*: Se *n* é par, então *Nat.succ (Nat.succ n)* é par.

Para ilustrar como essa definição de paridade funciona, vamos imaginar usá-la para mostrar que 4 é par. Pela regra *Ev_SS*, basta mostrar que 2 é par. Isso, por sua vez, é garantido novamente pela regra *Ev_SS*, desde que possamos mostrar que 0 é par. Mas esse último fato segue diretamente da regra *Ev_0*.

Veremos muitas definições como esta durante o restante do curso. Para fins de discussões informais, é útil ter uma notação leve que facilite a leitura e a escrita. Regras de inferência são uma dessas notações:

<br> 

$$ 
\frac{  }{Ev\ \ 0} \ \ ev\_0
$$

<br>

$$ 
\frac{\ \ \ \  ev\ \  \ \ \  }{Ev\ \ (Nat.succ \ (Nat.succ \ n))} \ \ ev\_SS
$$

<br>


Cada uma das regras textuais acima é reformatada aqui como uma regra de inferência; a leitura pretendida é que, se as premissas acima da linha forem todas válidas, então a conclusão abaixo da linha segue. Por exemplo, a regra *ev_SS* diz que, se *n* satisfaz *ev*, então `Nat.succ (Nat.succ n)` também satisfaz. Se uma regra não tiver premissas acima da linha, então sua conclusão é válida incondicionalmente.

Podemos representar uma prova usando essas regras combinando as aplicações de regras em uma árvore de prova. Aqui está como poderíamos transcrever a prova acima de que 4 é par:

$$
\frac{  }{Ev\ \ 0} \ \ ev\_0 \\
\ \ \ \frac{  }{Ev\ \ 2} \ \ ev\_SS \\
\ \ \ \frac{  }{Ev\ \ 4} \ \ ev\_SS \\
\ \ \ \frac{  }{Ev\ \ 6} \ \ ev\_SS \\
$$

<br>
Por que chamar isso de "árvore" (em vez de "pilha", por exemplo)? Porque, em geral, as regras de inferência podem ter várias premissas. Veremos exemplos disso abaixo.

<br>
Juntando tudo isso, podemos traduzir a definição de paridade em uma definição formal do Kind usando uma declaração de dados, em que cada construtor corresponde a uma regra de inferência:

```rust ignore
type Ev ~ (n: Nat){
  ev_0 : Ev Nat.zero
  ev_ss <n : Nat> (pred: Ev n) : Ev (Nat.succ (Nat.succ n))
}
```

Essa definição é diferente em um aspecto crucial em relação aos usos anteriores de *dados*: seu resultado não é um *Tipo*, mas sim uma função de *Nat* para *Tipo* - isto é, uma propriedade dos números. Note que já vimos outras definições indutivas que resultam em funções, como *List*, cujo tipo é *Type -> Type*. O que é novo aqui é que, como o argumento *Nat* de *Ev* não tem nome e aparece à direita dos dois pontos, é permitido que ele tome valores diferentes nos tipos de diferentes construtores: `Nat.zero` no tipo de *ev_z* e `Nat.succ (Nat.succ n)` no tipo de *ev_ss*.

Por outro lado, a definição de*List* nomeia o parâmetro *x* globalmente, forçando o resultado de *Nil* e *Cons* a ser o mesmo `(List x)`. Se tivéssemos tentado omitir o tipo `n : Nat` ao definir *ev_ss*, teríamos visto um erro:

```rust,ignore
type Wrong_ev ~ (n: Nat){
  wrong_ev_0 : Ev Nat.zero
  wrong_ev_ss (pred: Ev n) : Ev (Nat.succ (Nat.succ n))
}
```
Com o seguinte retorno:

```diff ignore
   - ERROR  Cannot find the definition 'n'.

      ┌──[ev.kind2:9:25]
      │
    8 │      wrong_ev_z : Ev Nat.zero
    9 │      wrong_ev_ss (pred: Ev n) : Ev (Nat.succ (Nat.succ n))
      │                            ┬                           ┬
      │                            │                           └Here!
      │                            └Here!
   10 │    

```
("Parâmetro" aqui é jargão do Kind para um argumento à esquerda dos dois pontos em uma definição indutiva; "índice" é usado para se referir a argumentos à direita dos dois pontos.)

Podemos pensar na definição de Ev como definindo uma propriedade do Kind `Ev: Nat -> Type`, juntamente com os teoremas `ev_z: Ev 0` e `ev_ss <n : Nat> (pred: Ev n) : Ev (Nat.succ (Nat.succ n))`. Tais "teoremas construtores" têm o mesmo status que teoremas provados. Em particular, podemos aplicar nomes de regras como funções umas às outras para provar Ev para números específicos...

```rust ignore
Ev_4 : Ev 4n
Ev_4 = Ev.ev_ss 2n (Ev.ev_ss 0n Ev.ev_z)
```

Também podemos provar teoremas que têm hipóteses envolvendo Ev.

```rust ignore
Ev_plus5 (n: Nat) : Ev n -> Ev (Nat.add 4n n)
Ev_plus5 n = x => Ev.ev_ss (Ev.ev_ss x)
```

Mais geralmente, podemos mostrar que qualquer número multiplicado por 2 é par:


#### Ev_double

```rust ignore
Ev_double (n: Nat) : Ev (Nat.double n)
Ev_double n = ?
```

## Usando evidências em provas

 Além de construir evidências de que os números são pares, também podemos raciocinar sobre tais evidências. Introduzir *Ev* com uma declaração de dados diz ao *Kind* não apenas que os construtores `ev_z` e `ev_ss` são formas válidas de construir evidências de que algum número é par, mas também que esses dois construtores são as únicas maneiras de construir evidências de que os números são pares (no sentido de *Ev*).

Em outras palavras, se alguém nos dá uma evidência e para a afirmação Ev n, então sabemos que e deve ter uma das duas formas:

 - `e` é `ev_z` (e *n* é Nat.zero), ou
 - `e` é `ev_ss` aplicando a indução com `n`  e ele é igual ao sucessor do sucessor do `n`\*.     

###### * Nota: Já usamos essa estratégia antes, relembre do exercício *Problems.t3* do capítulo de indução, aqui a diferença é que há apenas um "Nat.succ" a mais na nossa indução.

Isso sugere que deve ser possível analisar uma hipótese da forma `Ev n` da mesma forma que fazemos com estruturas de dados definidas de forma indutiva; em particular, deve ser possível argumentar por *indução* e *análise de casos* sobre essa evidência. Vamos ver alguns exemplos para ver o que isso significa na prática.

### Pattern Matching  nas evidências

Suponha que estamos provando algum fato envolvendo um número *n* e nos é dada a hipótese `Ev n`. Já sabemos como realizar a *análise de casos* em *n* usando a tática de inversão, gerando submetas separadas para o caso em que `n = Nat.zero` e o caso em que `n = Nat.succ n` para algum *n*. Mas para algumas provas, podemos querer analisar diretamente a evidência de que `Ev n` é verdadeiro.

Pela definição de *Ev*, existem dois casos a considerar:
  - Se a evidência for da forma `ev_z`, sabemos que `n = Nat.zero`.
  - Caso contrário, a evidência deve ter a forma `ev_ss n e`, onde `n = Nat.succ (Nat.succ n)` e `e` é a evidência para `Ev n`.

Podemos realizar esse tipo de raciocínio em Kind, novamente usando o *pattern matching*. Além de permitir que raciocinemos sobre igualdades envolvendo construtores, a inversão fornece um princípio de análise de casos para proposições definidas de forma indutiva. <!-- Quando usada dessa forma, sua sintaxe é semelhante à da função destruct: passamos a ela uma lista de identificadores separados por caracteres | para nomear os argumentos de cada um dos possíveis construtores.-->

```rust ignore
Ev_minus2 (n: Nat) (e: Ev n) : Ev (Nat.pred (Nat.pred n))
Ev_minus2 Nat.zero e = e
Ev_minus2 (Nat.succ Nat.zero) e = Ev.ev_z
Ev_minus2 (Nat.succ (Nat.succ n)) (Ev.ev_ss e) = e
```

Em palavras, aqui está como o raciocínio de *pattern matching* funciona nesta prova:
  -  Se a evidência for da forma `ev_z`, sabemos que `n = Nat.zero`. Portanto, é suficiente
mostrar que `Ev (Nat.pred (Nat.pred Nat.zero))` é válido. Pela definição de `Nat.pred`, isso é
equivalente a mostrar que `Ev Z` é válido, o que segue diretamente de `ev_0`.
  -  Caso contrário, a evidência deve ter a forma `ev_ss n e`, onde
`n = Nat.succ (Nat.succ n)` e `e` é a evidência para `Ev n`. Devemos então mostrar que
`Ev (Nat.pred (Nat.pred (Nat.succ (Nat.succ n))))` é válido, o que, após simplificação, segue
diretamente de `e`.

Suponha que quiséssemos provar a seguinte variação de *Ev_minus2*:

```rust ignore
Evss_ev (n: Nat) (e: Ev (Nat.succ (Nat.succ n))) : Ev n
```

Intuitivamente, sabemos que a evidência para a hipótese não pode consistir apenas do construtor `ev_z`, uma vez que `Nat.zero` e `Nat.succ` são construtores diferentes do tipo *Nat*; portanto, `ev_ss` é o único caso que se aplica. Infelizmente, o *pattern matching* não é inteligente o suficiente para perceber isso e ainda gera duas submetas. Ainda pior, ao fazê-lo, mantém a meta final inalterada, deixando de fornecer qualquer informação útil para completar a prova.

A tática de inversão, por outro lado, pode detectar (1) que o primeiro caso não se aplica e (2) que o *n* que aparece no caso `Ev_SS` deve ser o mesmo que `n`. Isso nos permite concluir a prova.

```rust, ignore
Evss_ev n (Ev.ev_ss e) = e 
```

Usando o pattern matching dependente, também podemos aplicar o princípio da explosão a hipóteses "obviamente contraditórias" que envolvem propriedades indutivas. Por exemplo:

```rust, ignore
One_not_even : Not (Ev 1n)

```

### Inversion_practice

Prove os seguintes resultados usando correspondência de padrões.

```rust, ignore
SSSSev__even: 
```
```rust, ignore
even5_nonsense: 
```

A maneira como usamos a inversão aqui pode parecer um pouco misteriosa no início. Até agora, só usamos a inversão em proposições de igualdade para utilizar a injetividade dos construtores ou para discriminar entre diferentes construtores. Mas vemos aqui que a inversão também pode ser aplicada para analisar evidências de proposições definidas indutivamente.

Aqui está como a inversão funciona em geral. Suponha que o nome **I** se refere a uma suposição **P** no contexto atual, onde **P** foi definido por uma declaração Indutiva. Então, para cada um dos construtores de **P**, a inversão de **I** gera uma submeta em que **I** foi substituído pelas condições exatas e específicas sob as quais este construtor poderia ter sido usado para provar **P**. Alguns desses submetas serão auto contraditórios; a inversão descarta esses. Aqueles que são deixados representam os casos que devem ser provados para estabelecer a meta original. Para estes, a inversão adiciona todas as equações ao contexto de prova que devem ser verdadeiras para os argumentos fornecidos a **P** (por exemplo, ``Nat.succ (Nat.succ k) = n`` na prova de evSS_ev).

O exercício ev_double acima mostra que nossa nova noção de paridade é implicada pelas duas anteriores (uma vez que, por even_bool_prop no capítulo Lógica, já sabemos que elas são equivalentes entre si). Para mostrar que as três coincidem, nós apenas precisamos do seguinte lema:

```rust, ignore

ev_even: 
```

Procedemos por análise de casos em Ev n. O primeiro caso pode ser resolvido trivialmente.

```rust, ignore

ev_even Ev_0 =
```
mudar 
Infelizmente, o segundo caso é mais difícil. Precisamos mostrar (k ** S (S n') = double k) ``Syntax sigma``, mas a única suposição disponível é ``e'``, que afirma que ``Ev n'`` é verdadeiro. Uma vez que isso não é diretamente útil, parece que estamos presos e que a análise de casos em ``Ev n ``foi uma perda de tempo.

Se olharmos mais de perto para nosso segundo objetivo, no entanto, podemos ver que algo interessante aconteceu: ao realizar a análise de casos em ``Ev n``, fomos capazes de reduzir o resultado original a um semelhante que envolve uma evidência diferente para`` Ev n: e'``. Mais formalmente, podemos concluir nossa prova mostrando que

```rust, ignore

(k ** n = double k) // mudar 
```

o que é o mesmo que a declaração original, mas com ``n'`` em vez de ``n``. Na verdade, não é difícil convencer o Kind de que esse resultado intermediário é suficiente.
```rust, ignore
ev_even (Ev_SS e') 

```

### 2.3 Induction on Evidence