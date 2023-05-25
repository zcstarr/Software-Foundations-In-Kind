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

###### *Nota: Já usamos essa estratégia antes, relembre do exercício*Problems.t3* do capítulo de indução, aqui a diferença é que há apenas um "Nat.succ" a mais na nossa indução

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

- Se a evidência for da forma `ev_z`, sabemos que `n = Nat.zero`. Portanto, é suficiente
mostrar que `Ev (Nat.pred (Nat.pred Nat.zero))` é válido. Pela definição de `Nat.pred`, isso é
equivalente a mostrar que `Ev Z` é válido, o que segue diretamente de `ev_0`.
- Caso contrário, a evidência deve ter a forma `ev_ss n e`, onde
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
Infelizmente, o segundo caso é mais difícil. Precisamos mostrar (k ** S (S n') = double k) ``Syntax sigma``, mas a única suposição disponível é ``e'``, que afirma que ``Ev n'`` é verdadeiro. Uma vez que isso não é diretamente útil, parece que estamos presos e que a análise de casos em ``Ev n``foi uma perda de tempo.

Se olharmos mais de perto para nosso segundo objetivo, no entanto, podemos ver que algo interessante aconteceu: ao realizar a análise de casos em ``Ev n``, fomos capazes de reduzir o resultado original a um semelhante que envolve uma evidência diferente para``Ev n: e'``. Mais formalmente, podemos concluir nossa prova mostrando que

```rust, ignore
k => Equal n (Nat.double k)
```

o que é o mesmo que a declaração original, mas com ``n'`` em vez de ``n``. Na verdade, não é difícil convencer o Kind de que esse resultado intermediário é suficiente.

```rust, ignore
Ev_even (Nat.succ (Nat.succ n)) (Ev.ev_ss e) = Ev_even_ss n (Ev_even n e)
```

### Induction on Evidence

Se isso parece familiar, não é coincidência: encontramos problemas semelhantes no capítulo de Indução, ao tentar usar análise de casos para provar resultados que requeriam indução. E mais uma vez, a solução é... indução!

<!--TL:DR O comportamento da indução sobre evidências é o mesmo que o seu comportamento sobre dados: 
ela faz com que o Kind gere uma submeta para cada construtor que poderia ter sido usado para construir aquela evidência, ao mesmo tempo em que fornece uma hipótese de indução para cada ocorrência recursiva da propriedade em questão. -->

Vamos tentar nosso lema atual novamente:

```rust, ignore
#partial
Ev_even
  (n: Nat)
  (e: Ev n) :
  (Sigma Nat(k => Equal n ( Nat.double k)))
Ev_even Nat.zero e = Sigma.new 0n Equal.refl
Ev_even (Nat.succ Nat.zero) e = Empty.absurd _
Ev_even (Nat.succ (Nat.succ n)) (Ev.ev_ss e) = Ev_even_ss n (Ev_even n e)
// Ev_even (Nat.succ (Nat.succ n)) Ev.ev_z = Caso impossível
```
<!--TL:DR
Aqui, podemos ver que o Kind produziu uma HI que corresponde a E', a única ocorrência recursiva de ev em sua própria definição. Como E' menciona n', a hipótese de indução fala sobre n', em oposição a n ou algum outro número. -->

A equivalência entre as segunda e terceira definições de paridade agora segue.

```rust,ignore

Ev_even_equiv (n: Nat)  : Equivalence (Ev n) (Sigma Nat (k => Equal n (Nat.double k)))
Ev_even_equiv n         = Equivalence.new (x => Ev_even n x) (y => From_eee n y)

From_eee (n: Nat) (s: Sigma Nat (k => Equal n (Nat.double k))) : Ev n
From_eee n (Sigma.new a b fst snd) =
  Equal.rewrite (Equal.mirror (specialize b into #0 in snd)) (x =>(Ev x)) (Ev_double fst)

Ev_double (n: Nat)      : Ev (Nat.double n)
Ev_double Nat.zero      = Ev.ev_z
Ev_double (Nat.succ n)  = Ev.ev_ss (Ev_double n)

```

Como veremos nos próximos capítulos, a indução sobre evidências é uma técnica recorrente em várias áreas, especialmente na formalização da semântica de linguagens de programação, onde muitas propriedades de interesse são definidas indutivamente.

Os exercícios a seguir fornecem exemplos simples dessa técnica, para ajudá-lo a se familiarizar com ela.

#### Ev_sum

```rust,ignore
Ev_sum (n: Nat) (m: Nat) (e1: Ev n) (e2: Ev m) : Ev (Nat.add n m)
Ev_sum n m e1 e2 = ?

```

#### Ev_alternate

Em geral, pode haver várias maneiras de definir uma propriedade indutivamente. Por exemplo, aqui está uma definição alternativa (um pouco forçada) para Ev:

```rust,ignore
type Evn ~ (n: Nat){
  z : Evn Nat.zero
  d : Evn (Nat.succ (Nat.succ Nat.zero))
  sum <n : Nat> <m: Nat> (evn: Evn n) (evm: Evn m) : Evn (Nat.add n m)
} 
```

Prove que essa definição é logicamente equivalente à antiga. (Você pode querer olhar para o teorema anterior quando chegar à etapa de indução.)

```rust,ignore
Ev_evn (n: Nat): Equivalence (Ev n) (Evn n)
Ev_evn n = ?

```

#### Ev_ev__ev

Encontrar a coisa apropriada para fazer a indução é um pouco complicado aqui:

```rust,ignore

Ev_ev_ev (n: Nat) (m: Nat) (e: Ev (Nat.add n m)) (en: Ev n) : Ev m
Ev_ev_ev Nat.zero m e en = ?
```

#### Ev_plus_plus

Este exercício requer apenas a aplicação de lemas existentes. Nenhuma indução ou até mesmo análise de casos é necessária, embora algumas das reescritas possam ser tediosas.

```rust,ignore
Ev_pp 
  <n: Nat> 
  <m: Nat> 
  <p: Nat> 
  (e1: Ev (Nat.add n m))
  (e2: Ev (Nat.add n p))
  : Ev (Nat.add m p)
Ev_pp Nat.zero m p e1 e2 =
```

## Inductive Relations

Uma proposição parametrizada por um número (como Ev) pode ser considerada como uma propriedade, ou seja, ela define um subconjunto de Nat, especificamente aqueles números para os quais a proposição é provável. Da mesma forma, uma proposição de dois argumentos pode ser considerada como uma relação, ou seja, ela define um conjunto de pares para os quais a proposição é provável.

Um exemplo útil é a relação "menor ou igual" entre números. A seguinte definição deve ser bastante intuitiva. Ela afirma que existem duas maneiras de fornecer evidências de que um número é menor ou igual a outro: observar que eles são o mesmo número ou fornecer evidências de que o primeiro é menor ou igual ao predecessor do segundo.

```rust,ignore
type Le (n: Nat) ~ (m: Nat) {
  n : Le n n
  S <m: Nat> (pred: (Le n m)) : (Le n (Nat.succ m)) 
}
```

Provas de fatos sobre ``Le`` usando os construtores ``n`` e ``S`` seguem os mesmos padrões que provas sobre propriedades, como ``Ev`` acima. Podemos aplicar os construtores para provar metas de Le (por exemplo, mostrar que ``Le 3n 3n`` ou ``Le 3n 6n``) e podemos usar correspondência de padrões para extrair informações das hipóteses de ``Le`` no contexto (por exemplo, provar que ``(Le 2n 1n) -> (Equal (Plus 2n 2n) 5n)``).

Aqui estão algumas verificações de sanidade sobre a definição. (Observe que, embora sejam do mesmo tipo de "testes simples" que demos para as funções de teste que escrevemos nas primeiras palestras, devemos construir suas provas explicitamente - Refl não faz o trabalho, porque as provas não são apenas uma questão de simplificar cálculos.)

```rust,ignore
Test_le1 : Le 3n 3n
Test_le1 = Le.n 3n

Test_le2 : Le 3n 6n
Test_le2 = Le.S (Le.S (Le.S Le.n))

Test_le3 (l: Le 2n 1n) : Equal (Nat.add 2n 2n) 5n
Test_le3 Le.n = Empty.absurd // TODO
Test_le3 (Le.S Le.n) = Empty.absurd // TODO
Test_le3 (Le.S (Le.S len)) = Empty.absurd // TODO
```

A relação "estritamente menor que" ``n < m`` agora pode ser definida em termos de ``Le``.

```rs,ignore
Lt (n: Nat) (m: Nat) : Type
Lt n m = Le (Nat.succ n) m
```

Aqui estão algumas outras relações simples sobre números:

```rs,ignore
type Square_of ~ (n: Nat) (m: Nat) {
  sq <n: Nat> : Square_of n (Mult n n)
}
```

```rs,ignore
type Next_Nat ~ (n: Nat) (m: Nat) {
  Nn <n: Nat> : Next_Nat n (Nat.succ n)
}
```

```rs,ignore
type Next_even ~ (n: Nat) (m: Nat) {
  1 <n: Nat> (pred: Ev (Nat.succ n)) : Next_even n (Nat.succ n)
  2 <n: Nat> (pred: Ev (Nat.succ (Nat.succ n))) : Next_even n (Nat.succ (Nat.succ n))
}
```

#### Total_relation (Opcional)

 Defina uma relação binária indutiva Total_relation que é válida para todos os pares de números naturais.

```rs,ignore
// TODO
```

#### Empty_relation (Opcional)

Defina uma relação binária indutiva Empty_relation (sobre números) que nunca é válida.

```rs,ignore
// TODO
```

#### Le_exercises (Opcional)

Aqui estão alguns fatos sobre as relações ``Le`` e ``Lt`` que vamos precisar mais adiante no curso. As provas são bons exercícios de prática.

```rust,ignore
Le.trans (n: Nat) (m: Nat) (o: Nat) (x: Le m n) (y: Le n o) : Le m o 
Le.trans n m o x y = ?

O_le_n (n: Nat) : Le Nat.zero n
O_le_n n = ?

N_le_m_sn_le_sm (n: Nat) (m: Nat) (l: Le n m) : Le (Nat.succ n) (Nat.succ m)
N_le_m_sn_le_sm n m l = ?

Sn_le_Sm_n_le_m (n: Nat) (m: Nat) (l: Le (Nat.succ n) (Nat.succ m)) : Le n m
Sn_le_Sm_n_le_m n m l = ?

Le_plus_l (a: Nat) (b: Nat) : Le a (Nat.add a b)
Le_plus_l a b = ?

Plus_lt (n1: Nat) (n2: Nat) (m: Nat) (lt: Lt (Nat.add n1 n2) m) : Pair (Lt n1 m) (Lt n2 m)
Plus_lt n1 n2 m lt = ?

Lt_S (n: Nat) (m: Nat) (l: Lt n m) : (Lt n (Nat.succ m))
Lt_S n m l = ?

Lte_complete (n: Nat) (m: Nat) (e: Equal (Nat.lte n m) Bool.true) :  (Le n m)
Lte_complete n m e = ? 
```

Dica: O próximo pode ser mais fácil de provar por indução em m.

```rust,ignore
Lte_correct (n: Nat) (m: Nat) (le: Le n m) : Equal Bool (Nat.lte n m) Bool.true
Lte_correct n m le = ?
```

Dica: Este teorema pode ser facilmente provado sem usar indução.

```rust,ignore
Lte_true_trans (n: Nat) (m: Nat) (o: Nat) 
  (l: Equal (Nat.lte n m) Bool.true) 
  (k: Equal (Nat.lte m o) Bool.true) 
  : Equal (Nat.lte n o) Bool.true
Lte_true_trans n m o l k = ?
```

#### lte_iff

```rust, ignore
Lte_iff (n: Nat) (m: Nat) : Iff (Equal (Nat.lte n m) Bool.true) (Le n m)
Lte_iff n m = ?
```

#### R_provability

We can define threeplace relations, four-place relations, etc., in just the same way as binary relations.

For example, consider the following three-place relation on numbers:

```rust,ignore
type R ~ (a: Nat) (b: Nat) (c: Nat) {
  C1 : R 0n 0n 0n
  C2 <m: Nat> <n: Nat> <o: Nat>
    (r: R m n o) 
  : R (Nat.succ m) n (Nat.succ o)
  C3 <m: Nat> <n: Nat> <o: Nat>
    (r: R m n o) 
  : R m (Nat.succ n) (Nat.succ o)
  C4 <m: Nat> <n: Nat> <o: Nat>
    (r: R (Nat.succ m) (Nat.succ n) (Nat.succ (Nat.succ o))) 
  C5 <m: Nat> <n: Nat> <o: Nat> 
   (r: R m n o) 
}
```

Which of the following propositions are provable?

- R 1n 1n 2n

```rs,ignore
// TODO
```

- R 2n 2n 6n

```rs,ignore
// TODO
```

- If we dropped constructor C5 from the definition of R, would the set of provable propositions change? Briefly (1 sentence) explain your answer.

```rs,ignore
// TODO
```

- If we dropped constructor C4 from the definition of R, would the set of provable propositions change? Briefly (1 sentence) explain your answer.

```rs,ignore
// TODO
```

#### R_fact (Optional)

The relation R above actually encodes a familiar function. Figure out which function; then state and prove this equivalence in Kind
  
```rust,ignore
F_R (m: Nat) (n: Nat) : Nat
F_R m n = ?

R_equiv_fR (m: Nat) (n: Nat) (o: Nat) : Iff (R m n o) (Equal (F_R m n) o)
R_equiv_fR m n o = ?
```

#### Subseq

Uma lista é uma subsequência de outra lista se todos os elementos da primeira lista ocorrem na mesma ordem na segunda lista, possivelmente com alguns elementos extras entre eles. Por exemplo, [1n,2n,3n] é uma subsequência de cada uma das listas [1n,2n,3n], [1n,1n,1n,2n,2n,3n], [1n,2n,7n,3n], [5n,6n,1n,9n,9n,2n,7n,3n,8n], mas não é uma subsequência de nenhuma das listas [1n,2n], [1n,3n], [5n,6n,2n,1n,7n,3n,8n].

- Defina um tipo indutivo ``Subseq`` em List Nat que capture o significado de ser uma subsequência. *(Dica: Você precisará de três casos.)*
- Prove subseq_refl, que a subsequência é reflexiva, ou seja, qualquer lista é uma subsequência de si mesma.
- Prove subseq_app, que para quaisquer listas l1, l2 e l3, se l1 é uma subsequência de l2, então l1 também é uma subsequência de (App l2 l3).
- (Opcional, mais difícil) Prove subseq_trans, que a subsequência é transitiva - ou seja, se l1 é uma subsequência de l2 e l2 é uma subsequência de l3, então l1 é uma subsequência de l3. *Dica: escolha sua indução com cuidado!*

#### R_provability2 (Opcional)

Suponha que demos a definição a seguir para o Kind:

```rust, ignore
type Rp ~ (n: Nat) (l: List Nat) {
  C1 
  : Rp 0n []
  C2 <n: Nat> <l: List Nat> 
  (r: Rp n l) 
  : Rp (Nat.succ n) (List.cons n l)
  C3 <n: Nat> <l: List Nat> 
  (r: Rp (Nat.succ n) l)
}
```

- Rp 2n [1n,0n]
- Rp 1n [1n,2n,1n,0n]
- Rp 6n [3n,2n,1n,0n]

## Case Study: Regular Expressions

A propriedade Ev fornece um exemplo simples para ilustrar definições indutivas e as técnicas básicas para raciocinar sobre elas, mas não é muito empolgante - afinal, é equivalente às duas definições não indutivas de paridade que já vimos, e não parece oferecer nenhum benefício concreto sobre elas. Para dar uma melhor noção do poder das definições indutivas, vamos mostrar como usá-las para modelar um conceito clássico da ciência da computação: expressões regulares.

Expressões regulares são uma linguagem simples para descrever strings, definidas da seguinte forma:

```rust,ignore
type Regexp (t: Type) {
    emptyset                              : Regexp t
    emptystr                              : Regexp t
    chr   (h: t)                          : Regexp t
    app   (st1: Regexp t) (st2: Regexp t) : Regexp t
    union (st1: Regexp t) (st2: Regexp t) : Regexp t
    star  (st1: Regexp t)                 : Regexp t
  }
```

Observe que essa definição é polimórfica: expressões regulares em ``Reg_exp t`` descrevem strings com caracteres extraídos de ``t`` - ou seja, listas de elementos de ``t``.

(Nós nos afastamos um pouco da prática padrão ao não exigir que o tipo t seja finito. Isso resulta em uma teoria um pouco diferente de expressões regulares, mas a diferença não é significativa para nossos propósitos.)

Conectamos expressões regulares e strings por meio das seguintes regras, que definem quando uma expressão regular corresponde a alguma string:

• A expressão EmptySet não corresponde a nenhuma string.
• A expressão EmptyStr corresponde à string vazia [].
• A expressão Chr x corresponde à string de um caractere [x].
• Se re1 corresponde a s1 e re2 corresponde a s2, então (App re1 re2) corresponde a (App s1 s2).
• Se pelo menos uma das expressões re1 e re2 corresponder a s, então Union re1 re2 corresponde a s.
• Finalmente, se podemos escrever alguma string s como a concatenação de uma sequência de strings s = (App s_1 (App... s_k...)), e a expressão re corresponde a cada uma das strings s_i, então Star re corresponde a s.

Como caso especial, a sequência de strings pode estar vazia, então Star re sempre corresponde à string vazia [] independentemente de qual seja re.

Podemos facilmente traduzir essa definição informal em uma definição de dados da seguinte forma:

```rust,ignore
#derive[match]
type Expmatch <t> ~(xs: List t) (r: Regexp t) {
  mempty              : Expmatch t []   Regexp.emptystr 
  mchar (x: t)        : Expmatch t [x] (Regexp.chr x)
  mapp 
    (s1: List t) 
    (r1: Regexp t) 
    (e1: Expmatch s1 r1)
    (s2: List t)
    (r2: Regexp t)
    (e2: Expmatch t s2 r2)
    : Expmatch t (List.concat s1 s2) (Regexp.app r1 r2)
  munionl
    (s1: List t)
    (s2: List t)
    (r1: Regexp t)
    (r2: Regexp t)
    : Expmatch t s1 (Regexp.union r1 r2)
  munionr
    (s1: List t)
    (s2: List t)
    (r1: Regexp t)
    (r2: Regexp t)
    : Expmatch t s2 (Regexp.union r1 r2)
  mstarz (r: Regexp t) : Expmatch t [] (Regexp.star r)  
  mstarapp 
    (s1: List t) 
    (s2: List t) 
    (r1: Regexp t)
    (e1: Expmatch s1 r1)
    (e2: Expmatch s2 (Regexp.star r1))
    : Expmatch t (List.concat s1 s2) (Regexp.star r1)
}
```

<!-- Novamente, para legibilidade, também podemos exibir essa definição usando notação de regras de inferência. Ao mesmo tempo, vamos introduzir uma notação de infixa mais legível.

-----------Latex

Observe que essas regras não são exatamente as mesmas das informais que demos no início da seção. Primeiro, não precisamos incluir uma regra que declare explicitamente que nenhuma string corresponde a EmptySet; simplesmente não incluímos nenhuma regra que teria o efeito de alguma string corresponder a EmptySet. (De fato, a sintaxe das definições indutivas nem mesmo nos permite fornecer tal "regra negativa".) -->

<!-- TL;DR Em segundo lugar, as regras informais para Union e Star correspondem a dois construtores cada: MUnionL / MUnionR e MStar0 / MStarApp. O resultado é logicamente equivalente às regras originais, 
mas mais conveniente de usar no Kind, uma vez que as ocorrências recursivas de Exp_match são fornecidas como argumentos diretos para os construtores, facilitando a indução com base nas evidências. (Os exercícios exp_match_ex1 e exp_match_ex2 abaixo pedem para provar que os construtores dados na declaração indutiva e os que surgiriam de uma transcrição mais literal das regras informais são de fato equivalentes.) -->

Vamos ilustrar essas regras com alguns exemplos.

```rust,ignore
Regexp_ex1 : Expmatch [1] (Regexp.chr 1)
Regexp_ex1 = Expmatch.mchar 1 

Regexp_ex2 : Expmatch [1, 2] (Regexp.app (Regexp.chr 1) (Regexp.chr 2))
Regexp_ex2 = Expmatch.mapp [1] (Regexp.chr 1) (Expmatch.mchar 1) [2] (Regexp.chr 2) (Expmatch.mchar 2)

```

Usando correspondência de padrões, também podemos mostrar que certas strings não correspondem a uma expressão regular:

```rust,ignore
Regexp_ex3 : Not (Expmatch [1, 2] (Regexp.chr 1))
Regexp_ex3 = Empty.absurd _
```

Podemos definir funções auxiliares para ajudar a escrever expressões regulares. A função reg_exp_of_list constrói uma expressão regular que corresponde exatamente à lista que recebe como argumento:

```rust,ignore


Regexp_of_list <t> (xs: List t)        : Regexp t
Regexp_of_list t List.nil              = Regexp.emptystr
Regexp_of_list t (List.cons xs.h xs.t) = Regexp.app (Regexp.chr xs.h) (Regexp_of_list t xs.t)

Regexp_ex4 : Expmatch [1, 2, 3] (Regexp_of_list [1, 2, 3])
Regexp_ex4 = 
  (Expmatch.mapp 
    [1] 
    (Regexp.chr 1) 
    (Expmatch.mchar 1) 
    [2, 3] 
    (Regexp_of_list [2, 3])
    (Expmatch.mapp
      [2]
      (Regexp.chr 2)
      (Expmatch.mchar 2) 
      [3]
      (Regexp_of_list [3])
      (Expmatch.mapp
        [3]
        (Regexp.chr 3)
        (Expmatch.mchar 3)
        []
        (Regexp_of_list [])
        (Expmatch.mempty)
      )
    )
  )


```

Também podemos provar fatos gerais sobre Exp_match. Por exemplo, o seguinte lema mostra que toda string s que corresponde a re também corresponde a Star re.

```rust,ignore
Mstar1 <t> <s: List t> <re: Regexp t> (e: Expmatch s re) : Expmatch s (Regexp.star re)  
Mstar1 t s re e = 
  let msz = Expmatch.mstarz re
  let mss = Expmatch.mstarapp s [] re e msz
  let lst = List_concat s
  let rwt = Equal.rewrite lst (x => (Expmatch t x (Regexp.star re))) mss
  rwt


```

(Obsere o uso de appendNilRightNeutral*** para alterar o objetivo do teorema para exatamente a mesma forma esperada por MStarApp.)

#### Exp_match_ex1

Os seguintes lemas mostram que as regras de correspondência informais fornecidas no início do capítulo podem ser obtidas a partir da definição indutiva formal.

```rust,ignore
  Munion 
  <t> 
  <s: List t> 
  <re1:  Regexp t> 
  <re2:  Regexp t> 
  (p: Pair (Expmatch s re1) (Expmatch s re2))
  : Expmatch s (Regexp.union re1 re2)
Munion t s re1 re2 (Pair.new fst snd) = 
  Expmatch.munionl t s [] re1 re2
```

O próximo lema é declarado em termos da função fold do capítulo Poly: Se ss: List (List t) representa uma sequência de strings s1, ..., sn, então fold (++) ss [] é o resultado da concatenação de todas elas.

```rust,ignore
Fold <x> <y> (f: x -> y -> y) (l: Data.List x) (b: y) : y
Fold x y f Data.List.nil b = b
Fold x y f (Data.List.cons xs.h xs.t) b = f xs.h (Fold f xs.t b) 

MStar_ <t>
  (ss : Data.List (Data.List t))
  (re : Regexp t)
  (construct_match : (s : Data.List t) -> (i : In s ss) -> Expmatch s re)
  : Expmatch (Concats ss) (Regexp.star re)
MStar_ ss re construct_match = ?¬

```

#### Reg_exp_of_list

Prove que reg_exp_of_list satisfaz a seguinte especificação:

```rust,ignore

reg_exp_of_list_spec:
```

Como a definição de Exp_match tem uma estrutura recursiva, é de se esperar que as provas envolvendo expressões regulares frequentemente exijam indução com base nas evidências. Por exemplo, suponha que quiséssemos provar o seguinte resultado intuitivo: se uma expressão regular re corresponde a alguma string s, então todos os elementos de s devem ocorrer em algum lugar de re. Para enunciar este teorema, primeiro definimos uma função re_chars que lista todos os caracteres que ocorrem em uma expressão regular:

```rust,ignore

re_chars:
```

Podemos então formular nosso teorema da seguinte forma:

```rust,ignore

destruct:
```

Algo interessante acontece no caso MStarApp. Obtemos duas hipóteses de indução: uma que se aplica quando x ocorre em s1 (o que corresponde a re) e uma segunda que se aplica quando x ocorre em s2 (o que corresponde a Star re). Isso ilustra por que precisamos da indução com base nas evidências para Exp_match, em oposição a re: este último forneceria apenas uma hipótese de indução para strings que correspondem a re, o que não nos permitiria raciocinar sobre o caso In x s2.

```rust,ignore

Left prf' ౬
```

4.0.3. Exercício: 4 estrelas (re_not_empty). Escreva uma função recursiva re_not_empty que testa se uma expressão regular corresponde a alguma string. Prove que sua função está correta.

```rust,ignore
Re_not_empty <t> (re: Regexp t)   : Data.Bool
Re_not_empty t re = ?

Re_not_empty_correct <t> <re: Regexp t> 
: Equivalence (Data.Sigma (Data.List t) (s => Expmatch s re)) (Prop.Equal (Re_not_empty re) Data.Bool.true)
Re_not_empty_correct t re = ?

```

### The remember Tactic

Reescrever a seção, o casamento de padrões dependentes resolve tudo isso.

Uma característica potencialmente confusa da tática de indução é que ela permite facilmente que você tente configurar uma indução sobre um termo que não é suficientemente geral. O efeito disso é perder informações (assim como destruct pode fazer) e deixá-lo incapaz de concluir a prova. Aqui está um exemplo:

```rust,ignore

star_app:
```

Apenas fazer uma inversão em H1 não nos levará muito longe nos casos recursivos (tente!). Portanto, precisamos de indução. Aqui está uma primeira tentativa ingênua:

```rust,ignore

induction H1.
as [|x'
```

Mas agora, embora tenhamos sete casos (como esperaríamos a partir da definição de Exp_match), perdemos uma informação muito importante de H1: o fato de que s1 correspondia a algo da forma Star re. Isso significa que temos que fornecer provas para todos os sete construtores dessa definição, embora todos, exceto dois deles (MStar0 e MStarApp), sejam contraditórios. Ainda é possível concluir a prova para alguns construtores, como MEmpty...

(*MEmpty*) simpl. intros H. apply H.
... mas a maioria dos casos fica parada. Por exemplo, para MChar, devemos mostrar que s2 => Char x' -> x' ௝௞ s2 => Char x', o que é claramente impossível.

(*MChar. Stuck...*) Abort.
O problema é que a indução sobre uma hipótese do tipo Type só funciona corretamente com hipóteses que são completamente gerais, isto é, aquelas em que todos os argumentos são variáveis, em oposição a expressões mais complexas, como Star re. (Nesse aspecto, a indução com base nas evidências se comporta mais como destruct do que como inversão.) Podemos resolver esse problema generalizando as expressões problemáticas com uma igualdade explícita:

```rust,ignore
Lemma star_app: forall

```

<!-- TL:DR
Agora podemos prosseguir realizando a indução diretamente com base nas evidências, porque o argumento da primeira hipótese é suficientemente geral, o que significa que podemos descartar a maioria dos casos invertendo a igualdade re' = Star re no contexto. Esse padrão é tão comum que o Kind fornece uma tática para gerar automaticamente tais equações para nós, evitando assim a necessidade de alterar as declarações de nossos teoremas. -->


#### Exp_match_ex2

O lema MStar'' abaixo (junto com o seu inverso, o exercício MStar' acima), mostra que a nossa definição de Exp_match para Star é equivalente àquela informalmente dada anteriormente.

#### pumping

Um dos primeiros teoremas realmente interessantes na teoria das expressões regulares é o chamado lema do bombeamento, que afirma, informalmente, que qualquer string suficientemente longa s que corresponde a uma expressão regular re pode ser "bombeada" repetindo alguma seção do meio de s um número arbitrário de vezes para produzir uma nova string também correspondente a re.

Para começar, precisamos definir o que significa "suficientemente longa". Uma vez que estamos trabalhando em uma lógica construtiva, na verdade precisamos ser capazes de calcular, para cada expressão regular re, o comprimento mínimo para as strings s garantirem a "bombeabilidade".

```rust,ignore

pumping_constant :
```

Em seguida, é útil definir uma função auxiliar que repete uma string (anexa a si mesma) um certo número de vezes.

```rust,ignore
napp :

```

Agora, o próprio lema do bombeamento afirma que, se s => re e se o comprimento de s for pelo menos a constante de bombeamento de re, então s pode ser dividida em três substrings s1 ++ s2 ++ s3 de tal forma que s2 pode ser repetida qualquer número de vezes e o resultado, quando combinado com s1 e s3, ainda corresponderá a re. Como s2 também está garantida a não ser a string vazia, isso nos dá uma maneira (construtiva!) de gerar strings correspondentes a re que são tão longas quanto desejarmos.

```rust,ignore

pumping :
```

Para agilizar a prova (que você deve preencher), a tática omega, que é habilitada pelo Require a seguir, é útil em vários lugares para completar automaticamente argumentos tediosos de baixo nível envolvendo igualdades ou desigualdades sobre números naturais. Voltaremos à tática omega em um capítulo posterior, mas sinta-se à vontade para experimentá-la agora, se quiser. O primeiro caso da indução dá um exemplo de como ela é usada.

```rust,ignore

pumping m le = ?pumping_rhs
```

## Estudo de Caso: Melhorando a Reflexão

Vimos no capítulo de Lógica que frequentemente precisamos relacionar computações booleanos com declarações em Tipo. No entanto, realizar essa conversão da forma como fizemos lá pode resultar em scripts de prova tediosos. Considere a prova do seguinte teorema:

```rust, ignore
filter_not_empty_In : {n : Nat} -> Not (filter ((==) n) l = []) -> In n l //terminar
```

No segundo caso, aplicamos explicitamente o lema beq_nat_true à equação gerada ao fazer um dependent match em n == x, para converter a suposição n == x = True em n = m.

Podemos simplificar isso definindo uma proposição indutiva que fornece um melhor princípio de análise de casos para n == m. Em vez de gerar uma equação como n == m = True, que geralmente não é diretamente útil, esse princípio nos fornece imediatamente a suposição que realmente precisamos: n = m.

Na verdade, vamos definir algo um pouco mais geral, que pode ser usado com propriedades arbitrárias (e não apenas igualdades):

```rust, ignore
type Reflect  (p: Type) (b: Bool) { //TODO: corrigir
  ReflectT    (p: Type) (b= True) : Reflect p b
  ReflectF    (p: Type) (n: Not p) (b= False) : Reflect p b
}
```

A propriedade "reflect" recebe dois argumentos: uma proposição p e um booleano b. Intuitivamente, ela afirma que a propriedade p é refletida em (ou seja, equivalente a) o booleano b: p é verdadeira se e somente se b = True. Para entender isso, observe que, por definição, a única maneira de obtermos evidências de que "Reflect p True" é verdadeiro é mostrando que p é verdadeira e usando o construtor "ReflectT". Se invertermos essa afirmação, isso significa que deve ser possível extrair evidências para p a partir de uma prova de "Reflect p True". Da mesma forma, a única maneira de mostrar "Reflect p False" é combinando evidências de "Not p" com o construtor "ReflectF".

É fácil formalizar essa intuição e mostrar que as duas afirmações são de fato equivalentes:

```rust, ignore
Equiv_reflect <b: Bool> (e: Equiv p  (b = True)) : Reflect p b
Equiv_reflect Bool.false (pb, _) = ReflectF (uninhabited . pb) Equal.refl
Equiv_reflect Bool.true (_, bp)  = ReflectT (bp Refl) Equal.refl
```

#### Reflect_equiv

```rust, ignore
Reflect_equiv (r: Reflect p b) : Equivalence p  (Bool.equal b Bool.true)
Reflect_equiv x = ?
```

A vantagem do "Reflect" sobre o conectivo normal "se e somente se" é que, ao destruir uma hipótese ou lema da forma "Reflect p b", podemos realizar uma análise de casos em b ao mesmo tempo em que geramos hipóteses apropriadas nos dois ramos (p no primeiro subobjetivo e Not p no segundo).

```rust, ignore
Beq_natP <n: Nat> <m : Nat> :  Reflect (Equal n m) (Nat.equal n m)
Beq_natP {n} {m} = iff_reflect (iff_sym (beq_nat_true_iff n m))
```

A nova prova de filter_not_empty_In agora segue da seguinte maneira. Observe como as chamadas a destruct e apply são combinadas em uma única chamada a destruct.

(Para ver isso claramente, observe as duas provas de filter_not_empty_In com Kind e observe as diferenças no estado da prova no início do primeiro caso do destruct.)

```rust, ignore
Filter_not_empty_In_ <n : Nat> <n: Not (filter ((x => y => Nat.equal x y) n) l = []) : In n l
```

#### Beq_natP_practice

Use beq_natP, como mencionado acima, para provar o seguinte:

```rust, ignore
Count (n : Nat) : (l : List Nat) : Nat
Count n List.nil = 0n
Count n (List.cons xs.h xs.t) = Nat.add (Bool.if (Nat.equal n xs.h) 1n 0n)  (Count n xs.t)

Beq_natP_practice (e: Equal (count n l) Nat.zero) : Not (In n l)
Beq_natP_practice e = ? 
```

Essa técnica nos proporciona apenas uma pequena vantagem em conveniência para as provas que vimos aqui, mas usar o "Reflect" de forma consistente geralmente resulta em scripts de prova perceptivelmente mais curtos e claros à medida que as provas se tornam maiores. Veremos muitos outros exemplos nos próximos capítulos.

O uso da propriedade "reflect" foi popularizado pelo SSReflect, uma biblioteca Coq que tem sido utilizada para formalizar resultados importantes em matemática, incluindo o teorema das 4 cores e o teorema de Feit-Thompson. O nome SSReflect significa "small-scale reflection" (reflexão de pequena escala), ou seja, o uso generalizado da reflexão para simplificar pequenos passos de prova com computações booleanas.

## Exercícios adicionais

#### Nostutter

Formular definições indutivas de propriedades é uma habilidade importante que você precisará neste curso. Tente resolver este exercício sem qualquer ajuda.

Dizemos que uma lista "gagueja" se ela repete o mesmo elemento consecutivamente. A propriedade "Nostutter minha_lista" significa que minha_lista não gagueja. Formule uma definição indutiva para Nostutter. (Isso é diferente da propriedade NoDup no exercício abaixo; a sequência [1,4,1] se repete, mas não gagueja).

```rust, ignore
type Nostutter <t> (l: List t) {
  RemoveMe : Nostutter []
}
```

Certifique-se de que cada um desses testes seja bem-sucedido, mas sinta-se à vontade para alterar a prova sugerida (em comentários) se ela não funcionar para você. Sua definição pode ser diferente da nossa e ainda estar correta, nesse caso, os exemplos podem precisar de uma prova diferente. (Você vai perceber que as provas sugeridas usam várias táticas sobre as quais não falamos, para torná-las mais robustas em relação às diferentes formas possíveis de definir Nostutter. Provavelmente, você pode apenas descomentá-las e usá-las como estão, mas também pode provar cada exemplo com táticas mais básicas.)

```rust, ignore
Test_nostutter_1 : Nostutter [3n,1n,4n,1n,5n,6n]
Test_nostutter_1 = ?
// Prova. repita o construtor; aplique beq_nat_false_iff; auto.

Test_nostutter_2 : Nostutter []
Test_nostutter_2 = ?
// Prova. repita o construtor; aplique beq_nat_false_iff; auto.

Test_nostutter_3 : Nostutter [5n]
Test_nostutter_3 = ?
// Prova. repita o construtor; aplique beq_nat_false; auto. Qed.

Test_nostutter_4 : Not (Nostutter [3n,1n,1n,4n])
Test_nostutter_4 = ?
// Prova. intro.
// repetir correspondência de metas com
// h: nostutter _ |- _ => inversão h; limpar h; subst
// end.
// contradição H1; auto.
```

#### Filter_challenge

Vamos provar que nossa definição de filter do capítulo Poly corresponde a uma especificação abstrata. Aqui está a especificação, escrita informalmente em inglês:

Uma lista l é uma "mescla em ordem" de l1 e l2 se ela contém todos os mesmos elementos de l1 e l2, na mesma ordem de l1 e l2, mas possivelmente intercalados. Por exemplo,

[1n,4n,6n,2n,3n]

é uma mescla em ordem de

[1n,6n,2n]

e

[4n,3n].

Agora, suponha que temos um conjunto t, uma função test: t -> Bool e uma lista l do tipo List t. Suponha ainda que l é uma mescla em ordem de duas listas, l1 e l2, de modo que cada item em l1 satisfaça test e nenhum item em l2 satisfaça test. Então, filter test l = l1.

Traduza esta especificação em um teorema em Kind e prove-o. (Você precisará começar definindo o que significa uma lista ser uma mescla de outras duas. Faça isso com um tipo de dados indutivo, não uma função.)

#### Filter_challenge_2

Uma maneira diferente de caracterizar o comportamento do filter é a seguinte: Entre todas as subsequências de l com a propriedade de que test avalia como Verdadeiro para todos os seus elementos, o filter test l é o mais longo. Formalize essa afirmação e prove-a.

#### Palindromes

Um palíndromo é uma sequência que é lida da mesma forma de trás para frente.

- Defina uma proposição indutiva Pal sobre List t que capture o que significa ser um palíndromo. (Dica: você precisará de três casos. Sua definição deve ser baseada na estrutura da lista; ter apenas um único construtor como

   ``` Rust, ignore
   C <t> (l : List t)  (rev: Equal l (Rev l)) : Pal l
   ```

   pode parecer óbvio, mas não funcionará muito bem.)

- Prove (pal_app_rev) que

    ```rust, ignore
    (l : List t) : Pal (List.concat l (Rev l))
    ```

- Prove (pal_rev) que

  ``` rust, ignore
    (l : List t) (p: Pal l) : Equal l (Rev l)
    ```

#### Palindrome_converse

Novamente, a direção contrária é significativamente mais difícil, devido à falta de evidência. Usando sua definição de Pal do exercício anterior, prove que

```rust, ignore
(l : List t) ( Equal l (Rev l)) Pal l
```

#### NoDup

Lembre-se da definição da propriedade In do capítulo Lógica, que afirma que um valor x aparece pelo menos uma vez em uma lista l:

```rust, ignore
In <t> (x : t) (l : List t) : Type
In x List.nil = Empty
In x (List.concat xs.h xs.t) = Either (Equal x xs.h)  (In x xs)
```

Sua primeira tarefa é usar *In* para definir uma proposição `Disjoint <t> l1 l2`, que deve ser comprovável exatamente quando *l1* e *l2* são listas (com elementos do tipo *t*) que não têm elementos em comum.

Em seguida, use *In* para definir uma proposição indutiva `NoDup <t> l`, que deve ser comprovável exatamente quando *l* é uma lista (com elementos do tipo *t*) em que cada membro é diferente de todos os outros. Por exemplo, `NoDup U60 [1,2,3,4]` e `NoDup Bool []` devem ser comprováveis, enquanto `NoDup Nat [1,2,1]` e `NoDup Bool [True,True]` não devem ser.

Por fim, declare e prove um ou mais teoremas interessantes que relacionam Disjoint, NoDup e List.

#### Pigeonhole principle

O princípio da gaiola de pombos afirma um fato básico sobre contagem: se distribuirmos mais do que n itens em n gaiolas de pombos, alguma gaiola de pombos deve conter pelo menos dois itens. Como frequentemente acontece, esse fato aparentemente trivial sobre números requer uma máquina não trivial para provar, mas agora temos o suficiente...
Primeiro, prove um lema fácil e útil.

```rust, ignore
In_split 
  <t> 
  <x: t> 
  <l: List t> 
  (i: In x l) 
  : ([l1] -> [l2] -> ((Equal l (List.concat l1  (List.cons x  l2)))))
In_split i = ?
```

Agora defina uma propriedade Repeats de forma que `Repeats <t> l` afirme que *l* contém pelo menos um elemento repetido (do tipo t).

```rust, ignore
type  Repeats <t> (l: List t) {
  -- PREENCHA AQUI
  RemoveMe' : Repeats [] -- necessário para verificação de tipo, os dados não devem estar vazios
}
```

Essa prova é muito mais fácil se você usar a hipótese *excluded_middle* para mostrar que *In* é decidível, ou seja, `Either (In x l) (Not (In x l))`. No entanto, também é possível fazer a prova sem assumir que *In* é decidível; se você conseguir fazer isso, não precisará da hipótese *excluded_middle*.

Aqui está uma maneira de formalizar o princípio da gaiola de pombos. Suponha que a lista *l2* represente uma lista de rótulos de gaiolas de pombos, e a lista *l1* represente os rótulos atribuídos a uma lista de itens. Se houver mais itens do que rótulos, pelo menos dois itens devem ter o mesmo rótulo - ou seja, a lista l1 deve conter repetições.

```rust, ignore
Pigeonhole_principle : 
  (f: (x : t) -> In x l1 -> In x l2)
  (prf: Lt (List.length l2) (List.length l1))
  : Repeats l1
pigeonhole_principle f prf = ?

Excluded_middle : (p : Type) : Either p (Not p)
```
