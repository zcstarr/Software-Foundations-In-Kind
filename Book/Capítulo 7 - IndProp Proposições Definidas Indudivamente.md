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
k => Prop.Equal n (Data.Nat.double k)
```

o que é o mesmo que a declaração original, mas com ``n'`` em vez de ``n``. Na verdade, não é difícil convencer o Kind de que esse resultado intermediário é suficiente.
```rust, ignore
Ev_even (Data.Nat.succ (Data.Nat.succ n)) (Ev.ev_ss e) = Ev_even_ss n (Ev_even n e)
```


### Induction on Evidence
Se isso parece familiar, não é coincidência: encontramos problemas semelhantes no capítulo de Indução, ao tentar usar análise de casos para provar resultados que requeriam indução. E mais uma vez, a solução é... indução!

<!--TL:DR O comportamento da indução sobre evidências é o mesmo que o seu comportamento sobre dados: 
ela faz com que o Idris gere uma submeta para cada construtor que poderia ter sido usado para construir aquela evidência, ao mesmo tempo em que fornece uma hipótese de indução para cada ocorrência recursiva da propriedade em questão. -->

Vamos tentar nosso lema atual novamente:
```rust, ignore
#partial
Ev_even
  (n: Data.Nat)
  (e: Ev n) :
  (Data.Sigma Data.Nat(k => Prop.Equal n ( Data.Nat.double k)))
Ev_even Data.Nat.zero e = Data.Sigma.new 0n Prop.Equal.refl
Ev_even (Data.Nat.succ Data.Nat.zero) e = Data.Empty.absurd _
Ev_even (Data.Nat.succ (Data.Nat.succ n)) (Ev.ev_ss e) = Ev_even_ss n (Ev_even n e)
// Ev_even (Data.Nat.succ (Data.Nat.succ n)) Ev.ev_z = Caso impossível
```
<!--TL:DR
Aqui, podemos ver que o Idris produziu uma HI que corresponde a E', a única ocorrência recursiva de ev em sua própria definição. Como E' menciona n', a hipótese de indução fala sobre n', em oposição a n ou algum outro número. -->

A equivalência entre as segunda e terceira definições de paridade agora segue.

```rust,ignore 

Ev_even_equiv (n: Data.Nat)  : Equivalence (Ev n) (Data.Sigma Data.Nat (k => Prop.Equal n (Data.Nat.double k)))
Ev_even_equiv n         = Equivalence.new (x => Ev_even n x) (y => From_eee n y)

From_eee (n: Data.Nat) (s: Data.Sigma Data.Nat (k => Prop.Equal n (Data.Nat.double k))) : Ev n
From_eee n (Data.Sigma.new a b fst snd) =
  Prop.Equal.rewrite (Prop.Equal.mirror (specialize b into #0 in snd)) (x =>(Ev x)) (Ev_double fst)

Ev_double (n: Data.Nat)      : Ev (Data.Nat.double n)
Ev_double Data.Nat.zero      = Ev.ev_z
Ev_double (Data.Nat.succ n)  = Ev.ev_ss (Ev_double n)

```

Como veremos nos próximos capítulos, a indução sobre evidências é uma técnica recorrente em várias áreas, especialmente na formalização da semântica de linguagens de programação, onde muitas propriedades de interesse são definidas indutivamente.

Os exercícios a seguir fornecem exemplos simples dessa técnica, para ajudá-lo a se familiarizar com ela.

#### Ev_sum


```rust,ignore 
Ev_sum (n: Data.Nat) (m: Data.Nat) (e1: Ev n) (e2: Ev m) : Ev (Data.Nat.add n m)
Ev_sum n m e1 e2 = ?

```


#### Ev_alternate 
Em geral, pode haver várias maneiras de definir uma propriedade indutivamente. Por exemplo, aqui está uma definição alternativa (um pouco forçada) para Ev:

```rust,ignore 
type Evn ~ (n: Data.Nat){
  z : Evn Data.Nat.zero
  d : Evn (Data.Nat.succ (Data.Nat.succ Data.Nat.zero))
  sum <n : Data.Nat> <m: Data.Nat> (evn: Evn n) (evm: Evn m) : Evn (Data.Nat.add n m)
} 
```


Prove que essa definição é logicamente equivalente à antiga. (Você pode querer olhar para o teorema anterior quando chegar à etapa de indução.)

```rust,ignore 
Ev_evn (n: Data.Nat): Equivalence (Ev n) (Evn n)
Ev_evn n = ?

```

#### Ev_ev__ev 
Encontrar a coisa apropriada para fazer a indução é um pouco complicado aqui:

```rust,ignore 

Ev_ev_ev (n: Data.Nat) (m: Data.Nat) (e: Ev (Data.Nat.add n m)) (en: Ev n) : Ev m
Ev_ev_ev Data.Nat.zero m e en = ?
```

#### Ev_plus_plus 
Este exercício requer apenas a aplicação de lemas existentes. Nenhuma indução ou até mesmo análise de casos é necessária, embora algumas das reescritas possam ser tediosas.

```rust,ignore 
Ev_pp 
  <n: Data.Nat> 
  <m: Data.Nat> 
  <p: Data.Nat> 
  (e1: Ev (Data.Nat.add n m))
  (e2: Ev (Data.Nat.add n p))
  : Ev (Data.Nat.add m p)
Ev_pp Data.Nat.zero m p e1 e2 =
```

#### 3.1.3. Exercise: 3 stars, recommended (R_provability)

We can define threeplace relations, four-place relations, etc., in just the same way as binary relations.

For example, consider the following three-place relation on numbers:
```rust,ignore 
type R ~ (a: Data.Nat) (b: Data.Nat) (c: Data.Nat) {
  C1 : R 0n 0n 0n
  C2 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R m n o) :
    R (Data.Nat.succ m) n (Data.Nat.succ o)
  C3 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R m n o) :
    R m (Data.Nat.succ n) (Data.Nat.succ o)
  C4 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R (Data.Nat.succ m) (Data.Nat.succ n) (Data.Nat.succ (Data.Nat.succ o))) :
    R m n o
  C5 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R m n o) :
    R m n o
}
```
Which of the following propositions are provable?
• R 1n 1n 2n
• R 2n 2n 6n
• If we dropped constructor C5 from the definition of R, would the set of
provable propositions change? Briefly (1 sentence) explain your answer.
• If we dropped constructor C4 from the definition of R, would the set of
provable propositions change? Briefly (1 sentence) explain your answer.

// Solution(ommitted)
```rust,ignore 
type R ~ (a: Data.Nat) (b: Data.Nat) (c: Data.Nat) {
  C1 : R 0n 0n 0n
  C2 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R m n o) :
    R (Data.Nat.succ m) n (Data.Nat.succ o)
  C3 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R m n o) :
    R m (Data.Nat.succ n) (Data.Nat.succ o)
  C4 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R (Data.Nat.succ m) (Data.Nat.succ n) (Data.Nat.succ (Data.Nat.succ o))) :
    R m n o
  C5 <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
    (r: R m n o) :
    R m n o
}

R_1_1_2 : R 1n 1n 2n
R_1_1_2 = R.C3 (R.C2 R.C1)

R_2_2_6 : Prop.Not (R 2n 2n 6n)
R_2_2_6 = Data.Empty.absurd _ // TODO

// If we dropped constructor C5 from the definition of R, would the set of provable propositions change? Briefly (1 sentence) explain your answer.
// No, because C5 acts like an identity function, it won't change the proposition.

// If we dropped constructor C4 from the definition of R, would the set of provable propositions change? Briefly (1 sentence) explain your answer.
// No, because C4 acts like an inverse to C2 and C3, whenever those two are applied:
// C4 C2 C2 C3 C2 : R 2 0 2
// is the same as undoing the C4, C2 and C3
// C2 C2 : R 2 0 2
```

#### 3.1.4. Exercise: 3 stars, optional (R_fact).
The relation R above actually encodes a familiar function. Figure out which function; then state and prove this equivalence in Kind
  
```rust,ignore
F_R (m: Data.Nat) (n: Data.Nat) : Data.Nat
F_R m n = ?

R_equiv_fR (m: Data.Nat) (n: Data.Nat) (o: Data.Nat) : Equiv (R m n o) (Prop.Equal (F_R m n) o)
R_equiv_fR m n o = ?
```

<!-- Solution(ommited) -->
```rust,ignore 
record Equiv (p) (q) {
  lft: p -> q 
  rgt: q -> p
}

Equiv.mirror <p> <q> (e: Equiv p q) : Equiv q p
Equiv.mirror p q (Equiv.new lft rgt) = (Equiv.new rgt lft)

Equiv.refl <p> : Equiv p p
Equiv.refl p = (Equiv.new p p (p => p) (p => p))

F_R (m: Data.Nat) (n: Data.Nat) : Data.Nat
F_R m n = Data.Nat.add m n

R_equiv_fR (m: Data.Nat) (n: Data.Nat) (o: Data.Nat) : Equiv (R m n o) (Prop.Equal (F_R m n) o)
R_equiv_fR m n o = Equiv.new (r => R_to_FR r) (eq => FR_to_R eq)

R_to_FR
  <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
  (r: R m n o) :
  (Prop.Equal (F_R m n) o)
R_to_FR m n o R.C1 =
  let eq0 = Prop.Equal.refl :: Prop.Equal (Data.Nat.add m n) (Data.Nat.add m n)
  let eq1 = Prop.Equal.refl :: Prop.Equal m 0n
  let eq2 = Prop.Equal.refl :: Prop.Equal 0n o
  let eq3 = Prop.Equal.rewrite eq1 (x => Prop.Equal (Data.Nat.add m n) (Data.Nat.add x n)) eq0
  let eq4 = Prop.Equal.rewrite eq2 (x => Prop.Equal (Data.Nat.add m n) x) eq3
  eq4
  R_to_FR m n o (R.C2 m_ _ o_ r_) =
  let ind = R_to_FR m_ n o_ r_
  let add_succ_eq = Add_succ_eq ind
  let prf = Prop.Equal.rewrite (Succ_add_eq_add_succ m m_ n (Prop.Equal.refl)) (x => Prop.Equal x o) add_succ_eq
  prf
R_to_FR m n o (R.C3 _ n_ o_ r_) =
  let ind = R_to_FR m n_ o_ r_
  let app = Prop.Equal.apply (x => Data.Nat.succ x) ind
  let eq0 = Prop.Equal.mirror (Data.Nat.add.comm.succ m n_)
  let aux = Prop.Equal.rewrite eq0 (x => Prop.Equal _ x o) app
  aux
R_to_FR m n o (R.C4 r_) =
  let ind = R_to_FR r_
  let app = Prop.Equal.apply (x => Data.Nat.pred x) ind
  let eq0 = (Data.Nat.add.comm.succ m n)
  let aux = Prop.Equal.rewrite eq0 (x => Prop.Equal _ x (Data.Nat.succ o)) app
  let prf = Prop.Equal.apply (x => Data.Nat.pred x) aux
  prf
R_to_FR m n o (R.C5 r) = R_to_FR r

Succ_add_eq_add_succ (m: Data.Nat) (m_: Data.Nat) (n: Data.Nat)
  (eq: Prop.Equal m (Data.Nat.succ m_)) :
  (Prop.Equal Data.Nat
    (Data.Nat.succ (Data.Nat.add m_ n))
    (Data.Nat.add m n))
    Succ_add_eq_add_succ m m_ n eq =
 let app = Prop.Equal.apply (x => Data.Nat.add x n) eq
 let mir = Prop.Equal.mirror app
 mir

Add_succ_eq
  <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
  (eq: Prop.Equal (Data.Nat.add m n) o) :
  Prop.Equal (Data.Nat.add (Data.Nat.succ m) n) (Data.Nat.succ o)
Add_succ_eq m n o eq =
  let app = Prop.Equal.apply (x => Data.Nat.succ x) eq
  app

FR_to_R
  <m: Data.Nat> <n: Data.Nat> <o: Data.Nat>
  (eq: Prop.Equal (F_R m n) o) :
  R m n o
FR_to_R Data.Nat.zero Data.Nat.zero Data.Nat.zero eq = R.C1
FR_to_R (Data.Nat.succ m) n (Data.Nat.succ o) eq =
let ind = FR_to_R m n o (Prop.Equal.apply (x => Data.Nat.pred x) eq)
  R.C2 ind
FR_to_R m (Data.Nat.succ n) (Data.Nat.succ o) eq =
let eq0 = (Data.Nat.add.comm.succ m n)
  let aux = Prop.Equal.rewrite eq0 (x => Prop.Equal _ x (Data.Nat.succ o)) eq
  let ind = FR_to_R m n o (Prop.Equal.apply (x => Data.Nat.pred x) aux)
  R.C3 ind
FR_to_R Data.Nat.zero Data.Nat.zero (Data.Nat.succ o) eq = Data.Empty.absurd _ // impossible
FR_to_R Data.Nat.zero (Data.Nat.succ n) Data.Nat.zero eq = Data.Empty.absurd _ // impossible
FR_to_R (Data.Nat.succ m) Data.Nat.zero Data.Nat.zero eq = Data.Empty.absurd _ // impossible
FR_to_R (Data.Nat.succ m) (Data.Nat.succ n) Data.Nat.zero eq = Data.Empty.absurd _ // impossible
```

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
type Expmatch <t> ~(xs: Data.List t) (r: Regexp t) {
  mempty              : Expmatch t []   Regexp.emptystr 
  mchar (x: t)        : Expmatch t [x] (Regexp.chr x)
  mapp 
    (s1: Data.List t) 
    (r1: Regexp t) 
    (e1: Expmatch s1 r1)
    (s2: Data.List t)
    (r2: Regexp t)
    (e2: Expmatch t s2 r2)
    : Expmatch t (Data.List.concat s1 s2) (Regexp.app r1 r2)
  munionl
    (s1: Data.List t)
    (s2: Data.List t)
    (r1: Regexp t)
    (r2: Regexp t)
    : Expmatch t s1 (Regexp.union r1 r2)
  munionr
    (s1: Data.List t)
    (s2: Data.List t)
    (r1: Regexp t)
    (r2: Regexp t)
    : Expmatch t s2 (Regexp.union r1 r2)
  mstarz (r: Regexp t) : Expmatch t [] (Regexp.star r)  
  mstarapp 
    (s1: Data.List t) 
    (s2: Data.List t) 
    (r1: Regexp t)
    (e1: Expmatch s1 r1)
    (e2: Expmatch s2 (Regexp.star r1))
    : Expmatch t (Data.List.concat s1 s2) (Regexp.star r1)
}
```

<!-- Novamente, para legibilidade, também podemos exibir essa definição usando notação de regras de inferência. Ao mesmo tempo, vamos introduzir uma notação de infixa mais legível.

-----------Latex

Observe que essas regras não são exatamente as mesmas das informais que demos no início da seção. Primeiro, não precisamos incluir uma regra que declare explicitamente que nenhuma string corresponde a EmptySet; simplesmente não incluímos nenhuma regra que teria o efeito de alguma string corresponder a EmptySet. (De fato, a sintaxe das definições indutivas nem mesmo nos permite fornecer tal "regra negativa".) -->

<!-- TL;DR Em segundo lugar, as regras informais para Union e Star correspondem a dois construtores cada: MUnionL / MUnionR e MStar0 / MStarApp. O resultado é logicamente equivalente às regras originais, 
mas mais conveniente de usar no Idris, uma vez que as ocorrências recursivas de Exp_match são fornecidas como argumentos diretos para os construtores, facilitando a indução com base nas evidências. (Os exercícios exp_match_ex1 e exp_match_ex2 abaixo pedem para provar que os construtores dados na declaração indutiva e os que surgiriam de uma transcrição mais literal das regras informais são de fato equivalentes.) -->

Vamos ilustrar essas regras com alguns exemplos.

```rust,ignore
Regexp_ex1 : Expmatch [1] (Regexp.chr 1)
Regexp_ex1 = Expmatch.mchar 1 

Regexp_ex2 : Expmatch [1, 2] (Regexp.app (Regexp.chr 1) (Regexp.chr 2))
Regexp_ex2 = Expmatch.mapp [1] (Regexp.chr 1) (Expmatch.mchar 1) [2] (Regexp.chr 2) (Expmatch.mchar 2)

```

Usando correspondência de padrões, também podemos mostrar que certas strings não correspondem a uma expressão regular:

```rust,ignore
Regexp_ex3 : Prop.Not (Expmatch [1, 2] (Regexp.chr 1))
Regexp_ex3 = Data.Empty.absurd _
```

Podemos definir funções auxiliares para ajudar a escrever expressões regulares. A função reg_exp_of_list constrói uma expressão regular que corresponde exatamente à lista que recebe como argumento:

```rust,ignore


Regexp_of_list <t> (xs: Data.List t)        : Regexp t
Regexp_of_list t Data.List.nil              = Regexp.emptystr
Regexp_of_list t (Data.List.cons xs.h xs.t) = Regexp.app (Regexp.chr xs.h) (Regexp_of_list t xs.t)

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
Mstar1 <t> <s: Data.List t> <re: Regexp t> (e: Expmatch s re) : Expmatch s (Regexp.star re)  
Mstar1 t s re e = 
  let msz = Expmatch.mstarz re
  let mss = Expmatch.mstarapp s [] re e msz
  let lst = List_concat s
  let rwt = Prop.Equal.rewrite lst (x => (Expmatch t x (Regexp.star re))) mss
  rwt


```
(Obsere o uso de appendNilRightNeutral*** para alterar o objetivo do teorema para exatamente a mesma forma esperada por MStarApp.)


#### Exp_match_ex1
Os seguintes lemas mostram que as regras de correspondência informais fornecidas no início do capítulo podem ser obtidas a partir da definição indutiva formal.

```rust,ignore
  Munion 
  <t> 
  <s: Data.List t> 
  <re1:  Regexp t> 
  <re2:  Regexp t> 
  (p: Data.Pair (Expmatch s re1) (Expmatch s re2))
  : Expmatch s (Regexp.union re1 re2)
Munion t s re1 re2 (Data.Pair.new fst snd) = 
  Expmatch.munionl t s [] re1 re2
```

O próximo lema é declarado em termos da função fold do capítulo Poly: Se ss: List (List t) representa uma sequência de strings s1, ..., sn, então fold (++) ss [] é o resultado da concatenação de todas elas.


```rust,ignore

fold:
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

re_not_empty
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

(* MEmpty *) simpl. intros H. apply H.
... mas a maioria dos casos fica parada. Por exemplo, para MChar, devemos mostrar que s2 => Char x' -> x' ௝௞ s2 => Char x', o que é claramente impossível.

(* MChar. Stuck... *) Abort.
O problema é que a indução sobre uma hipótese do tipo Type só funciona corretamente com hipóteses que são completamente gerais, isto é, aquelas em que todos os argumentos são variáveis, em oposição a expressões mais complexas, como Star re. (Nesse aspecto, a indução com base nas evidências se comporta mais como destruct do que como inversão.) Podemos resolver esse problema generalizando as expressões problemáticas com uma igualdade explícita:

```rust,ignore
Lemma star_app: forall

```


<!-- TL:DR
Agora podemos prosseguir realizando a indução diretamente com base nas evidências, porque o argumento da primeira hipótese é suficientemente geral, o que significa que podemos descartar a maioria dos casos invertendo a igualdade re' = Star re no contexto. Esse padrão é tão comum que o Idris fornece uma tática para gerar automaticamente tais equações para nós, evitando assim a necessidade de alterar as declarações de nossos teoremas. -->

Invocar a tática remember e as x faz com que o Idris (1) substitua todas as ocorrências da expressão e pela variável x e (2) adicione uma equação x = e ao contexto.

Veja como podemos usá-la para mostrar o resultado acima:

Abort.

```rust,ignore

Lemma star_app: forall
```

.

intros T s1 s2 re H1.

remember (Star re) as re'.

Agora temos Heqre' : re' = Star re.

generalize dependent s2.

induction H1

as

A Heqre' é contraditória na maioria dos casos, o que nos permite concluir imediatamente.

(* MEmpty *) inversion Heqre'.

(* MChar *) inversion Heqre'.

(* MApp *) inversion Heqre'.

(* MUnionL *) inversion Heqre'.

(* MUnionR *) inversion Heqre'.

Os casos interessantes são aqueles que correspondem a Star. Note que a hipótese de indução IH2 no caso MStarApp menciona uma premissa adicional Star re" = Star re', que resulta da igualdade gerada pelo remember.

(* MStar0 *)
inversion Heqre'. intros s H. apply H.

(* MStarApp *)
inversion Heqre'. rewrite H0 in IH2, Hmatch1.

intros s2 H1. rewrite <౐ app_assoc.

apply MStarApp.

apply Hmatch1.

apply IH2.

reflexivity.

apply H1.

Qed.

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
