# Lógica em Kind

Nos capítulos anteriores, vimos muitos exemplos de afirmações factuais (proposições) e formas de apresentar evidências de sua veracidade (provas). Em particular, trabalhamos extensivamente com proposições de igualdade da forma e1 = e2, com implicações (p -> q), e com proposições quantificadas (x -> P(x)). Neste capítulo, veremos como Kind pode ser usado para realizar outras formas familiares de raciocínio lógico.

Antes de mergulhar nos detalhes, vamos falar um pouco sobre o status das declarações matemáticas em Kind. Lembre-se de que Kind é uma linguagem tipada, o que significa que toda expressão sensível em seu mundo tem um tipo associado. As afirmações lógicas não são exceção: qualquer afirmação que possamos tentar provar em Kind tem um tipo, ou seja, Type, o tipo de proposições. Podemos ver isso com o tipo *Booleano*:

```rust 
Bool: Type
Bool.true   : Bool
Bool.false  : Bool
``` 

No tipo *Bool* nós temos o Bool.**true** e o Bool.**false**. Para criar o
tipo Bool nós declaramos que ele é um tipo (Type) e depois que o
Bool.**true** e o Bool.**false** são do tipo Bool. Vendo como é, fica bem
mais simples. Muitas vezes nos assombramos ao ver o nome "funcional", mas
Kind é uma linguagem muito amigável, veremos bem sobre isso mais para a
frente.

Outro exemplo possível é o *Nat*, dos números naturais. Números
naturais são todos os números inteiros maiores ou igual a zero. Ou seja, eles começam com o número zero e vão até o infinito, mas não possuem valores decimais. Ou seja, o **3** é um número natural, mas o **3,14** não é, da mesma forma que o **-3** também não é. Então sabemos que o número natural é feito de *zero* e dos *sucessores* dele. Vamos ver como isso é no kind:


```rust
Nat: Type
Nat.zero              : Nat
Nat.succ (pred:Nat)   : Nat
```

Percebemos que a construção é similar aos *Booleanos*, mas há um
elemento extra no Nat.**succ**, que é o "(pred: Nat)", e isso se deve ao
fato de que, enquanto no outro nós tinhamos apenas duas opções de retorno
(*True* ou *False*), agora temos uma infinidade de números que o código
precisa compreender, além de que deve haver uma forma do código parar de rodar
(veremos mais sobre isso ao decorrer desse estudo), uma vez que um código que
nunca para de rodar é um código que nunca nos dará o resultado. 

Porém, de qualquer forma, percebemos que a estrutura do **Nat** é basicamente
a mesma do **Bool**, isso nos mostra que podemos criar qualquer tipo,
desde que sigamos a mesma estrutura. Vamos criar o tipo suco:

```rust
Suco : Type
Suco.laranja  : Suco
Suco.caju     : Suco
```

O suco de laranja possui dois construtores, o Suco.**laranja** e o
Suco.**caju**. Esse tipo é fictício, ele não existia até então, mas agora
podemos usar ele como um tipo e isso nos mostra que, graças ao design do Kind,
podemos criar uma infinidade de tipos, pois todo tipo é uma função.

Entender a construção dos *tipos* impedirá que alguns erros de sintaxe ocorram.

Apenas revisando, nosso elemento *Suco* é do tipo *Type* e o Suco.**laranja** é do tipo *Suco*. Desta forma, em termos leigos, temos que o elemento fica a direita dos dois pontos e o tipo a esquerda

`elemento : Tipo` 

Como dito antes, até mesmo os tipos são funções, logo podemos ter uma função
como tipo. Por exemplo:

```rust
Problema : (Equal Bool Bool.true Bool.true
```


Podemos perceber que temos um elemento chamado "Problema" e ele é do
tipo "(Equal Bool Bool.true Bool.true)". Nós já vimos essa estrutura diversas vezes nos últimos capítulos e agora é fácil entender que essa função é um tipo e, da mesma forma que **não escrevemos**
```rust
Suco: Type
Suco.laranja : Type
```
**Não podemos** simplesmente copiar a função para os construtores desse tipo.
O *Suco* é tipo *Type*, mas o *Suco*.**laranja** não é do tipo *Type*, ele é
do tipo *Suco*. 

Mas atenção, observe que todas as proposições sintaticamente bem formadas têm o tipo Type em Kind, independentemente de serem verdadeiras ou não. Simplesmente ser uma proposição é uma coisa; ser demonstrável é outra coisa!

De fato, as proposições não têm apenas tipos: elas são objetos de primeira classe que podem ser manipulados da mesma forma que as outras entidades no mundo de Kind. Até agora, vimos um local primário onde as proposições podem aparecer: nas assinaturas de tipo das funções.

```rust
Plus_2_2_is_4 : Equal (Plus 2n 2n) 4n
Plus_2_2_is_4 = Equal.refl
```

Mas as proposições podem ser usadas de muitas outras maneiras. Por exemplo, podemos dar um nome a uma proposição como um valor próprio, assim como demos nomes a expressões de outros tipos.

```rust
Plus_fact : Type
Plus_fact = Equal (Plus 2n 2n) 4n
```

Posteriormente, podemos usar esse nome em qualquer situação em que uma proposição seja esperada – por exemplo, em uma declaração de função.

```rust
Plus_fact_is_true : Plus_fact
Plus_fact_is_true = Equal.refl
```
<!-- Também podemos escrever proposições parametrizadas – isto é, funções que recebem argumentos de algum tipo e retornam uma proposição. Por exemplo, a seguinte função pega um número e retorna uma proposição afirmando que esse número é igual a três: -->


Em Kind, diz-se que funções que retornam proposições definem propriedades de seus argumentos.
Por exemplo, aqui está uma propriedade (polimórfica) que define a noção familiar de uma função injetiva


```rust
Algebra.Laws.injectivity <a: Type> <b: Type> (f: a -> b) : Type
Algebra.Laws.injectivity a b f = (x: a) -> (y: a) -> (hyp: Equal b (f x) (f y)) -> (Equal a x y)
```

Nós podemos instanciar uma regra de injetividade com

```rust
Nat.succ_injective : Algebra.Laws.injectivity ((n: Nat) => Nat.succ n)
Nat.succ_injective =
  (a: Nat) =>
  (b: Nat) =>
  (hyp: Equal Nat (Nat.succ a) (Nat.succ b)) =>
  Equal.apply (x => Nat.pred x) hyp
```

e criar tipos que precisam de injetividade com

```rust
type Injective <a: Type> <b: Type> (f: a -> b) {
  new (p: Algebra.Laws.injectivity f)
}

InjectiveNat : Injective (n => Nat.succ n)
InjectiveNat = Injective.new Nat.succ_injective
```


## 1. Conectivos Lógicos
### 1.1. Conjunção. 
A conjunção (ou lógico *e*) em kind recebe duas proposições *a* e *b*, que devem retornar um valor *booleano* `true` ou `false`.  

```rust
Bool.and (a: Bool) (b: Bool) : Bool
Bool.and Bool.true  b = b
Bool.and Bool.false b = Bool.false
```

Se *a* é verdadeiro, basta apenas retornar o valor de *b*, agora se o *a* for falso, não há a necessidade de verificar o valor do segundo elemento. 

Por se tratar de um caso limitado (há apenas duas opções) dá para verificar com uma prova simples:
```rust
ConjuntiveBool : Equal Bool (Bool.and Bool.true Bool.false) Bool.false
ConjuntiveBool = Equal.refl
```

#### 1.1.1 Exercício:
```rust
And_exercise : Pair (Equal (Nat.add 3n 4n) 7n) (Equal (Nat.mul 2n 2n) 4n)
And_exercise = ?
```

Tanto para provar declarações conjuntivas. Para ir na outra direção – ou seja, usar uma hipótese conjuntiva para ajudar a provar outra coisa – empregamos o pattern matching.
Se o contexto de prova contiver uma hipótese h da forma (a,b), a divisão de caso irá substituí-la por um padrão de pares (a,b).

```rust
And_example2 (n: Nat) (m: Nat) (e: Pair (Equal n 0n) (Equal m 0n)) : Equal (Nat.add n m ) 0n
And_example2 Nat.zero Nat.zero e        = Equal.refl
And_example2 Nat.zero (Nat.succ m ) e   = 
    let p = (Equal.rewrite
    (Pair.snd e)
    (x => match Nat x {
        zero => Empty
        succ => Unit
    })
    (Unit.new))
    Empty.absurd p
And_example2 (Nat.succ n) m e           =
    let p = (Equal.rewrite
    (Pair.fst e)
    (x => match Nat x {
        zero => Empty
        succ => Unit 
    })
    (Unit.new))
    Empty.absurd p
```

Você pode se perguntar por que nos preocupamos em agrupar as duas hipóteses n = 0 e m = 0 em uma única conjunção, já que também poderíamos ter declarado o teorema com duas premissas separadas:

```rust
And_example2a (n: Nat) (m: Nat) (e1: Equal n 0n) (e2: Equal m 0n) : Equal (Nat.add n m) 0n
And_example2a Nat.zero Nat.zero e1 e2        = Equal.refl
And_example2a Nat.zero (Nat.succ m ) e1 e2   = 
    let p = (Equal.rewrite
    (e2)
    (x => match Nat x {
        zero => Empty
        succ => Unit
    })
    (Unit.new))
    Empty.absurd p
And_example2a (Nat.succ n) m e1 e2           =
    let p = (Equal.rewrite
    (e1)
    (x => match Nat x {
        zero => Empty
        succ => Unit 
    })
    (Unit.new))
    Empty.absurd p

```
Para este teorema, ambas as formulações são adequadas. Mas é importante entender como trabalhar com hipóteses conjuntivas porque as conjunções geralmente surgem de etapas intermediárias em provas, especialmente em desenvolvimentos maiores. Aqui está um exemplo simples:

```rust
And_example3 (n: Nat) (m: Nat) (e: Equal (Nat.add n m) 0n) : Equal (Nat.mul n m) 0n
And_example3 Nat.zero m e =  Equal.refl
And_example3 (Nat.succ n) m e =
    let p = (Equal.rewrite 
        (e)
        (x => match Nat x {
            zero => Empty 
            succ => Unit                      
        })
        (Unit.new))
    Empty.absurd p
```

Outra situação comum com conjunções é que sabemos (a,b), mas em algum contexto precisamos apenas de a (ou apenas b). Os seguintes teoremas são úteis em tais casos:

```rust
Proj1 <p> <q> (a: Pair p q) : p
Proj1 (Pair.new fst snd)    = fst
```

### 1.1.2

```rust
Proj2 <p> <q> (b: Pair p q) : q
Proj2 (Pair.new fst snd)    = ?
```

Por fim, às vezes precisamos reorganizar a ordem das conjunções e/ou agrupar as conjunções de múltiplas vias. Os seguintes teoremas de comutatividade e associatividade são úteis em tais casos.

```rust
And_commut <p> <q> (c: Pair p q) : Pair q p
And_commut (Pair.new fst snd)    = Pair.new snd fst
```

### 1.1.3
```rust
And_assoc <p> <q> <r> (a: Pair p (Pair q r))  : Pair (Pair p q) r
And_assoc (Pair.new p (Pair q r) fst (Pair.new snd trd)) = ?
```

Outro conectivo importante é a disjunção, ou lógico, de duas proposições:
`Either` a b é verdadeiro quando a ou b é. O primeiro caso foi marcado com *left* e o segundo com *right*.
Para usar uma hipótese disjuntiva em uma prova, procedemos pela análise de caso, que, como para Nat ou outros tipos de dados, pode ser feita com correspondência de padrões. Aqui está um exemplo:

```rust
Or_example (n: Nat) (m: Nat) (e: (Either (Equal n 0n) (Equal m 0n))) : Equal (Nat.mul n m) 0n
Or_example Nat.zero m e     = Equal.refl
Or_example n Nat.zero e     = Mult_0_r n
Or_example (Nat.succ n) m (Either.left l r val) = 
    let p = (Equal.rewrite
        (val)
        ( x => match Nat x {
            zero => Empty 
            succ => Unit            
        })
        (Unit.new))
    Empty.absurd p
Or_example (Nat.succ n) (Nat.succ m) (Either.right l r val) = 
    let p = (Equal.rewrite 
        (val)
        ( x => match Nat x {
            zero => Empty
            succ => Unit                   
        })
        (Unit.new))
Empty.absurd p
```

Inversamente, para mostrar que uma disjunção é válida, precisamos mostrar que um de seus lados é válido. Isso pode ser feito por meio dos construtores Left e Right mencionados acima. Aqui está um uso trivial...

```rust
#axiom
Or_intro <a> <b> (c: Either a b)        : a
Or_intro a b (Either.left lft rgt val)  = val
```
... e um exemplo um pouco mais interessante exigindo ambos

```rust
Zero_or_succ (n: Nat)       : Either (Equal n Nat.zero) (Equal n (Nat.succ (Nat.pred n)))
Zero_or_succ Nat.zero       = Either.left  Equal.refl
Zero_or_succ (Nat.succ n)   = Either.right Equal.refl
```

### 1.2.1 
```rust
#axiom
Mult_eq_0 (n: Nat) (m: Nat) (e: Equal (Nat.mul n m ) 0n) : Either (Equal n 0n) (Equal m 0n)
Mult_eq_0 n m = ?
```

### 1.2.2 
```rust
Or_commut <p> <q> (e: Either p q) : Either q p
Or_commut e: ?
```

## Falsidade e Negação. 

Até agora, nos preocupamos principalmente em provar que certas coisas são verdadeiras – adição é comutativa, anexação de listas é associativa, etc. Claro, também podemos estar interessados em resultados negativos, mostrando que certas proposições não são verdadeiras. Em Kind, tais declarações negativas são expressas com a função de nível de tipo de negação *Not*.

Para ver como a negação funciona, relembre a discussão do princípio da explosão do capítulo anterior; ela afirma que, se assumirmos uma contradição, então qualquer outra proposição pode ser derivada. Seguindo essa intuição, poderíamos definir ``Not p`` como ``p -> Empty``. Kind realmente faz uma escolha ligeiramente diferente, definindo *Not* como *p -> Empty*, onde *Empty* é uma proposição contraditória específica definida na biblioteca padrão como um tipo de dados sem construtores.

```rust
Empty: Type

Not <p: Type>: Type
Not p = p -> Empty
```

Como *Empty* é uma proposição contraditória, o princípio da explosão também se aplica a ela. Se obtivermos *Empty* no contexto de prova, podemos chamá-lo de *Empty* ou absurd para completar qualquer objetivo:

```rust
Ex_falso_quodlibet <p>  : Empty -> p
Ex_falso_quodlibet p    = e => Empty.absurd e
```
O *latim ex falso quodlibet* significa, literalmente, “da falsidade segue o que você quiser”; este é outro nome comum para o princípio da explosão.

### 1.3.1 
Mostre que a definição da negação em Kind 
