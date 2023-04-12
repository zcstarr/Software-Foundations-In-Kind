# Lógica em Kind

Nos capítulos anteriores, vimos muitos exemplos de afirmações factuais (proposições) e formas de apresentar evidências de sua veracidade (provas). Em particular, trabalhamos extensivamente com proposições de igualdade da forma *e1 = e2*, com implicações *(p -> q)*, e com proposições quantificadas *(x -> P(x))*. Neste capítulo, veremos como Kind pode ser usado para realizar outras formas familiares de raciocínio lógico.

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

#### 1.1.2

```rust
Proj2 <p> <q> (b: Pair p q) : q
Proj2 (Pair.new fst snd)    = ?
```

Por fim, às vezes precisamos reorganizar a ordem das conjunções e/ou agrupar as conjunções de múltiplas vias. Os seguintes teoremas de comutatividade e associatividade são úteis em tais casos.

```rust
And_commut <p> <q> (c: Pair p q) : Pair q p
And_commut (Pair.new fst snd)    = Pair.new snd fst
```

#### 1.1.3
```rust
And_assoc <p> <q> <r> (a: Pair p (Pair q r))  : Pair (Pair p q) r
And_assoc (Pair.new p (Pair q r) fst (Pair.new snd trd)) = ?
```

### 1.2 Disjunção
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

#### 1.2.1 
```rust
#axiom
Mult_eq_0 (n: Nat) (m: Nat) (e: Equal (Nat.mul n m ) 0n) : Either (Equal n 0n) (Equal m 0n)
Mult_eq_0 n m = ?
```

#### 1.2.2 
```rust
Or_commut <p> <q> (e: Either p q) : Either q p
Or_commut e: ?
```

### 1.3 Falsidade e Negação. 

Até agora, nos preocupamos principalmente em provar que certas coisas são verdadeiras – adição é comutativa, anexação de listas é associativa, etc. Claro, também podemos estar interessados em resultados negativos, mostrando que certas proposições não são verdadeiras. Em Kind, tais declarações negativas são expressas com a função de nível de tipo de negação *Not*.

Para ver como a negação funciona, relembre a discussão do princípio da explosão do capítulo anterior; ela afirma que, se assumirmos uma contradição, então qualquer outra proposição pode ser derivada. Seguindo essa intuição, poderíamos definir `Not p` como `q -> (p -> q)`. Kind realmente faz uma escolha ligeiramente diferente, definindo *Not* como *p -> Empty*, onde *Empty* é uma proposição contraditória específica definida na biblioteca padrão como um tipo de dados sem construtores.

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

#### 1.3.1 
Mostre que a definição da negação em Kind implica a intuitiva mencionada acima:

```rust
Not_implies_our_not <p> <q> (e: Not p) : q -> (p -> q)
Not_implies_our_not p q e = ?
```

É assim que usamos Not para afirmar que 0 e 1 são elementos diferentes de Nat:

```rust
Zero_not_one : Not (Equal Nat.zero (Nat.succ Nat.zero))
Zero_not_one = 
  (emp => 
    let app = Equal.apply (x => Nat.is_zero x) emp
    Equal.rewrite app (e => if e {Nat} else {Empty}) Nat.zero)
```

É preciso um pouco de prática para se acostumar a trabalhar com a negação em Kind. Mesmo que você possa ver perfeitamente por que uma afirmação envolvendo negação é verdadeira, pode ser um pouco complicado no começo colocar as coisas na configuração certa para que Kind possa entender! Aqui estão as provas de alguns fatos familiares para você se aquecer:

```rust
Not_false : Not Empty
Not_false = e => Empty.absurd e

Contradiction_implies_anythig <p> <q> (a: Pair p (Not p)) : q
Contradiction_implies_anythig p q (Pair.new fst snd) =
  let app = snd fst
  Empty.absurd app

Double_neg <p>  : p -> Not (Not p)
Double_neg p    = (x: p) => (y: Not p) => Contradiction_implies_anythig (Pair.new x y)

```

#### 1.3.2 Double_neg_inf
Escreva uma prova informal para *Double_neg*

Teorema: p implica *Not (Not p)* para qualquer p

#### 1.3.3 Contrapositivo

```rust
Contrapositive <p> <q>  : (p -> q) -> ((Not q) -> (Not p))
Contrapositive p q      = ?
```

#### 1.3.4 Not_both_true_and_false

```rust
Not_both_true_and_false <p> : Not (Pair p (Not p))
Not_both_true_and_false p   = ?
```

Da mesma forma, uma vez que a inequação envolve uma negação, é necessário um pouco de prática para trabalhar com ela fluentemente. Aqui está um truque útil. Se você está tentando provar um objetivo que é sem sentido (por exemplo, o estado do objetivo é Falso = Verdadeiro), aplique "absurdo" para mudar o objetivo para "Vazio". Isso facilita o uso de suposições da forma "Não p" que podem estar disponíveis no contexto - em particular, suposições da forma "Não (x=y)"inequação

### 1.4 Verdade 
Além de "Empty", a biblioteca padrão do Kind também define "Unit", uma proposição que é trivialmente verdadeira. Para prová-la, usamos a constante predefinida "()" da seguinte forma:

```rust
True_is_true : Unit
True_is_true = Unit.new 
```

Ao contrário de "Vazio", que é usado extensivamente, "Unit" é usado com pouca frequência em provas, uma vez que é trivial (e, portanto, sem interesse) provar como um objetivo e não carrega informações úteis como hipótese. No entanto, pode ser bastante útil ao definir provas complexas usando condicionais ou como parâmetro para provas de ordem superior. Veremos exemplos de tais usos de "Unit" mais tarde.

### 1.5 Equivalência lógica

O conectivo útil "se e somente se", que afirma que duas proposições têm o mesmo valor de verdade, é apenas a conjunção de duas implicâncias.

```rust
record Equiv (p) (q) {
  lft: p -> q
  rgt: q -> p
}

Equiv_sym <p> <q> (par: Pair (Equiv p q) (Equiv q p)) : Pair (Equiv q p) (Equiv p q)
Equiv_sym p q (Pair.new pq qp) = (Pair.new qp pq)
```

```rust
Not_true_equiv_false (b: Bool) : Equiv (Not (Equal Bool b Bool.true)) (Equal b Bool.false)
Not_true_equiv_false b = Equiv.new (x => (Not_true_is_false b x)) (y => (Not_true_and_false b y))
```

dado que

```rust
Not_true_and_false (b: Bool) (h: Equal b Bool.false) : (Not (Equal b Bool.true))
Not_true_and_false (Bool.false) Equal.refl = 
  emp =>
    let p = (Equal.rewrite emp
    (x => match Bool x {
      false => Unit
      true  => Empty
    })
    (Unit.new)) 
  Empty.absurd p
Not_true_and_false Bool.true h = 
    let p = (Equal.rewrite h
    (x => match Bool x {
      false => Empty
      true  => Unit
    })
    (Unit.new)) 
  Empty.absurd p
```

#### 1.5.1 Equiv_properties
Prove que *Equiv* é reflexivo e transitivo

```rust
Equiv.refl <p>  : Equiv p p 
Equiv.refl p    = ?
```

```rust
#partial
Equiv.chain <p> <q> <r> (e0: Equiv p q) (e1: Equiv q r) : Equiv p r
Equiv.chain p q r e0 (Equiv.refl x) = ?
```

#### 1.5.2 
```rust
Or_distributes_over_and <p> <q> <r> : Equiv (Either p (Pair q r)) (Pair (Either p q) (Either p r))
Or_distributes_over_and p q r = ?
``` 

Alguns táticos de Kind tratam declarações de "Equiv" de forma especial, evitando a necessidade de manipulação de estado de prova de baixo nível. Em particular, "rewrite" e "refl" podem ser usados com declarações "Equiv", não apenas igualdades.

Aqui está um exemplo simples que demonstra como esses táticos funcionam com "Equiv". Primeiro, vamos provar algumas equivalências básicas de "Equiv"...

```rust

Mult_0 (n: Nat) (m: Nat) : Equiv (Equal Nat (Nat.mul n m) 0n) (Either (Equal Nat n 0n) (Equal Nat m 0n))
Mult_0 n m = Equiv.new (x => To_mult_0 n m x) (y => Or_example n m y)

To_mult_0 (n: Nat) (m: Nat) (e: Equal Nat (Nat.mul n m) 0n) : (Either (Equal Nat n 0n) (Equal Nat m 0n))
To_mult_0 Nat.zero Nat.zero Equal.refl  = Either.left   Equal.refl
To_mult_0 Nat.zero (Nat.succ n) e       = Either.left   Equal.refl
To_mult_0 (Nat.succ n) Nat.zero e       = Either.right  Equal.refl
To_mult_0 (Nat.succ n) (Nat.succ m) e   = 
  let plus_comm = Plus_comm (Nat.mul n (Nat.succ m)) (Nat.succ m)
  let chn_mir   = Equal.chain (Equal.mirror e) plus_comm 
  let absurdo   = 
  (Equal.rewrite chn_mir 
    (x => match Nat x {
      zero => Unit
      succ => Empty
    })
    (Unit.new))
  Empty.absurd absurdo


Or_assoc <p> <q> <r> : Equiv (Either p (Either q r)) (Either (Either p q) r)
Or_assoc p q r = Equiv.new (x => To_or_assoc x) (y => Fro_or_assoc y)

To_or_assoc <p> <q> <r> (e: Either p (Either q r))          : Either      (Either p q) r 
To_or_assoc (Either.left e)                                 = Either.left (Either.left e)
To_or_assoc (Either.right p (Either q r) (Either.left e))   = Either.left (Either.right e)
To_or_assoc (Either.right p (Either q r) (Either.right e))  = Either.right e

Fro_or_assoc <p> <q> <r> (e: Either (Either p q) r)         : Either p (Either q r)
Fro_or_assoc (Either.left (Either p q) r (Either.right e))  = Either.right (Either.left e)
Fro_or_assoc (Either.left (Either p q) r (Either.left  e))  = Either.left e
Fro_or_assoc (Either.right e)  
```

Agora, podemos usar esses fatos com "rewrite" e "refl" para fornecer provas suaves de declarações que envolvem equivalências. Aqui está uma versão ternária do resultado anterior "Mult_0":

```rust
Mult_0_3 (n: Nat) (m: Nat) (p: Nat) : Equiv (Equal (Nat.mul n (Nat.mul m p)) Nat.zero) (Either (Equal n Nat.zero) (Either (Equal m Nat.zero) (Equal p Nat.zero)))
Mult_0_3 n m p = Equiv.new (x => To_mult_0_3 n m p x) (y => Fro_mult_0_3 n m p y) 

To_mult_0_3 (n: Nat) (m: Nat) (p: Nat) (e: Equal (Nat.mul n (Nat.mul m p)) Nat.zero) : Either (Equal n Nat.zero) (Either (Equal m Nat.zero) (Equal p Nat.zero))
To_mult_0_3 n m p e = Equiv.lft (Or_assoc (Equal n Nat.zero) (Equal m Nat.zero) (Equal p Nat.zero))

Fro_mult_0_3 (n: Nat) (m: Nat) (p: Nat) (e: Either (Equal n Nat.zero) (Either (Equal m Nat.zero) (Equal p Nat.zero))) : (Equal (Nat.mul n (Nat.mul m p)) Nat.zero )
Fro_mult_0_3 Nat.zero m         p (Either.left Equal.refl)  = Equal.refl
Fro_mult_0_3 n        Nat.zero  p (Either.right Equal.refl) = Mult_0_r n
Fro_mult_0_3 n        m         Nat.zero (Either.right Equal.refl) = 
  let mult_0_r_m = Mult_0_r m
  let mult_0_r_n = Mult_0_r n
  let rwt = Equal.rewrite (Equal.mirror mult_0_r_m) (x => (Equal Nat (Nat.mul n x) 0n)) mult_0_r_n
  rwt
Fro_mult_0_3 (Nat.succ n) m p (Either.left e) =
  let emp = (Equal.rewrite e 
    (x => match Nat x {
      zero => Empty
      succ => Unit
    })
    (Unit.new))
  Empty.absurd emp
Fro_mult_0_3 n (Nat.succ m) p (e) =
  let mult_0_r_n = Mult_0_r n
  let mult_m_p_eq_0 = Mult_n_m_eq_z m p (Either.rgt (Either.rgt e))
  let rwt = Equal.rewrite (Equal.mirror (mult_m_p_eq_0)) (x => (Equal Nat (Nat.mul n x) 0n))  mult_0_r_n
  rwt
Fro_mult_0_3 n m (Nat.succ p) (Either.right a (Either b c) (Either.right e)) =
  let emp = (Equal.rewrite e
		(x => match Nat x {
		  zero => Empty
			succ => Unit
		}) 
		(Unit.new)) 
	Empty.absurd emp
```

(A função *Either.rgt* é uma função auxiliar criada para extrair o valor da direita do *Either*, fica como exercício ao leitor o desenvolvimento dela e da *Either.lft*)


```rust
Apply_equiv_example (n: Nat) (m: Nat) (e: Equal (Nat.mul n m) Nat.zero) : Either (Equal n Nat.zero) (Equal m Nat.zero)
Apply_equiv_example n m e = Equiv.rgt (Mult_0 n m)
```

### 1.6 Quantificação existencial 
Outra conectiva lógica importante é a quantificação existencial. Para dizer que há algum *x* do tipo *t* tal que alguma propriedade *p* é verdadeira para *x*, escrevemos *Sigma t x p*, onde p é *x -> t*. A anotação de tipo: t pode ser omitida se o Kind for capaz de inferir a partir do contexto qual deve ser o tipo de x.

Para provar uma afirmação da forma *Sigma x p*, devemos mostrar que p é verdadeira para alguma escolha específica de valor para x, conhecido como o testemunho do existencial. Isso é feito em dois passos: Primeiro, declaramos um *Sigma.new*, que pode ser escrito com o sugar syntax `$`, depois dizemos explicitamente ao Kind qual testemunho t temos em mente. Então, provamos que p é verdadeira depois que todas as ocorrências de x são substituídas por t.

```rust
Four_is_even : Sigma Nat (n => (Equal Nat 4n (Nat.add n n)))
Four_is_even = $ 2n Equal.refl
```

Por outro lado, se tivermos uma hipótese existencial *Sigma x p* no contexto, podemos fazer correspondência de padrões nela para obter uma testemunha x e uma hipótese afirmando que p é verdadeiro para x.

<!-- TODO: completar aqui-->
```rust
Exists_example_2 (n: Nat) (m: Sigma Nat (m => (Equal Nat n (Nat.add 4n m)))) : Sigma Nat (o => (Equal Nat n (Nat.add 2n o)))
Exists_example_2 n (Sigma.new fst snd) = $ fst ? 
```
 
#### 1.6.1 Dist_exists_or
```rust
Dist_not_exists <a> <p: a -> Type> (f: (x: a) -> (p x)) : Not (Sigma a (x => ( Not (p x))))
Dist_not_exists a p f = ?
```

#### 1.6.2 Dist_exist_or 
```rust
Dist_exists_or <a> <p: a -> Type> <q: a -> Type> : Equiv (Sigma a (x => (Either (p x) (q x)))) (Either (Sigma a (x => (p x))) (Sigma a (x => (q x))))
Dist_exist_or a p q = ?
```


## 2 Programação com Proposições
As conectivas lógicas que vimos fornecem um vocabulário rico para definir proposições complexas a partir de proposições mais simples. Para ilustrar, vamos ver como expressar a afirmação de que um elemento x ocorre em uma lista l. Observe que essa propriedade tem uma estrutura recursiva simples:
  * Se l for a lista vazia, então x não pode ocorrer nela, portanto, a propriedade "x aparece em l" é simplesmente falsa.
  * Caso contrário, l tem a forma *List.cons xh xt*. Nesse caso, x ocorre em l se ele é igual a xh ou se ocorre em xt.
  
Podemos traduzir isso diretamente em uma função recursiva simples que recebe um elemento e uma lista e retorna uma proposição:

```rust
In <a> (x: a) (xs: List a)    : Type
In a x List.nil               = Empty
In a x (List.cons xs.h xs.t)  = Either (Equal x xs.h) (In a x xs.t)
```

Quando In é aplicado a uma lista concreta, ele se expande em uma sequência concreta de disjunções aninhadas.
```rust
In_example_1 : In 4n [1n, 2n, 3n, 4n, 5n]
In_example_1 = (Either.right (Either.right (Either.right (Either.left Equal.refl))))

In_example_2 (n: Nat) (i: In n [2n, 4n])  : Sigma Nat (m => Equal n (Nat.mul 2n m))
In_example_2 n (Either.left e)            = $ 1n e
In_example_2 n (Either.right e)           = $ 2n (Either.lft e)
```

Também podemos provar lemas mais genéricos e de nível superior sobre In. Observe, no próximo exemplo, como In começa sendo aplicado a uma variável e só é expandido quando fazemos análise de casos nessa variável:


```rust ignore
In_map <a> <b> (f: a -> b) (xs: List a) (x: a) (i: In x xs) : In (f x) (List.map xs f) 
In_map a b f (List.nil) x i = Empty.absurd i
In_map a b f (List.cons xs.h xs.t) x (Either.right e) = Either.right (In_map f xs.t x e)
In_map a b f (List.cons xs.h xs.t) x (Either.left e)  = 
    (Equal.rewrite e 
    (y => (Either (Equal (f x) (f y)) (In (f x) (List.map xs.t f)))) 
    (Either.left Equal.refl))
```

Essa forma de definir proposições recursivamente, embora conveniente em alguns casos, também tem algumas desvantagens. Em particular, está sujeita às restrições usuais do Kind em relação à definição de funções recursivas, por exemplo, o requisito de que elas sejam "obviamente terminantes". No próximo capítulo, veremos como definir proposições indutivamente, uma técnica diferente com seu próprio conjunto de pontos fortes e limitações.


#### 2.0.1 In_map_equiv
```rust ignore
In_map_equiv <a> <b> (f: a -> b) (l: List a) (y: b) :
   Equivalence (In y (List.map l f)) (Sigma a (x => (Pair (Equal (f x) y) (In x l))))
In_map_equiv a b f l y = ?
```

#### 2.0.2 In_app_equiv
```rust ignore
In_app_equiv <a> (x: a) (l1: List a) (l2: List a) :
  (Equivalence (In x (List.concat l1 l2)) (Either (In x l1) (In x l2)))
In_app_equiv a x l1 l2 = ?
```

#### 2.0.3 All
Lembre-se de que funções que retornam proposições podem ser vistas como propriedades de seus argumentos. Por exemplo, se *p* tem o tipo `Nat -> Type`, então `p n` afirma que a propriedade p é verdadeira para n.

Inspirado em *In*, escreva uma função recursiva *All* afirmando que alguma propriedade *p* é verdadeira para todos os elementos de uma lista *l*. Para garantir que sua definição esteja correta, prove o lema *All_In* abaixo. (É claro que sua definição não deve apenas repetir o lado esquerdo de *All_In*.)

```rust ignore
All <t> (p: t -> Type) (l: List t)  : Type
All t p l = ?

All_in <t> (p: t -> Type) (l: List t) : Equivalence ((x: t) -> (i: In x l) -> p x) (All p l)
All_in t p l = ?
```

#### 2.0.4 Combine_odd_even
Complete a definição da função combine_odd_even abaixo. Ela recebe como argumentos duas propriedades de números, podd e peven, e deve retornar uma propriedade p tal que p n é equivalente a podd n quando n é ímpar e equivalente a peven n caso contrário.

```rust ignore
Combine_odd_even (podd: Nat -> Type) (peven: Nat -> Type) : Nat -> Type
Combine_odd_even podd peven = ?
```

Para testar sua definição, prove os seguintes teoremas:

```rust
Combine_odd_even_intro
  (n: Nat)
  (podd:  Nat -> Type)
  (peven: Nat -> Type)
  (p1: (Equal (Nat.is_odd n) Bool.true)  -> podd  n)
  (p2: (Equal (Nat.is_odd n) Bool.false) -> peven n) : (Combine_odd_even (podd) (peven)) n
Combine_odd_even_intro n podd peven p1 p2 = ?

Combine_odd_even_elim_odd
  (n: Nat)
  (podd:  Nat -> Type)
  (peven: Nat -> Type)
  (p: (Combine_odd_even podd peven) n)
  (e: Equal (Nat.is_odd n) Bool.true) : podd n
Combine_odd_even_elim_odd n podd peven p e = ?

Combine_odd_elim_even
  (n: Nat)
  (podd: Nat -> Type)
  (peven: Nat -> Type)
  (p: (Combine_odd_even podd peven) n)
  (e: Equal (Nat.is_odd n) Bool.false) : peven n
Combine_odd_elim_even n podd peven p e = ?
```

## Aplicando Teoremas a Argumentos

Uma característica do Kind que o distingue de muitos outros assistentes de prova é que ele trata provas como objetos de primeira classe.

Há muito a ser dito sobre isso, mas não é necessário entender em detalhes para usar o Kind. Esta seção oferece apenas uma amostra, enquanto uma exploração mais profunda pode ser encontrada no capítulo *ProofObjects*.

Vimos que podemos usar o comando *check* para pedir ao Kind que imprima o tipo de uma expressão. Também podemos usar *check* para perguntar a qual teorema um identificador particular se refere.

```rust
PlusCommutative (m: Nat) (n: Nat) : Equal (Nat.add n m) (Nat.add m n)
PlusCommutative m n = ?
```
Kind imprime a declaração do teorema plusCommutative da mesma forma que imprime o tipo de qualquer termo que pedimos para verificar. Por quê?

A razão é que o identificador plusCommutative se refere na verdade a um objeto de prova - uma estrutura de dados que representa uma derivação lógica estabelecendo a verdade da declaração *(n: Nat) (m: Nat) : n + m = m + n*. O tipo desse objeto é a declaração do teorema do qual é uma prova.
Intuitivamente, isso faz sentido porque a declaração de um teorema nos diz para que podemos usá-lo, assim como o tipo de um objeto computacional nos diz o que podemos fazer com esse objeto - por exemplo, se temos um termo do tipo Nat -> Nat -> Nat, podemos dar a ele dois Nats como argumentos e obter um Nat de volta. Da mesma forma, se temos um objeto do tipo n = m -> n + n = m + m e fornecemos a ele um "argumento" do tipo n = m, podemos derivar n + n = m + m.
Operacionalmente, essa analogia vai ainda mais longe: aplicando um teorema, como se fosse uma função, a hipóteses com tipos correspondentes, podemos especializar seu resultado sem ter que recorrer a afirmações intermediárias. Por exemplo, suponha que quiséssemos provar o seguinte resultado:

```rust
plus_comm3: (n: Nat) (m: Nat) (p: Nat) : Equal (Nat.add n (Nat.add m p)) (Nat.add (Nat.add p m) n)
```

À primeira vista, parece que deveríamos ser capazes de provar isso abrindo os casos, pra caso *zero* e *succ _*, mas isso nos daria um trabalho desnecessário. Vejamamos um exemplo:

```rust
Plus_comm3a Nat.zero n p = ?
```


Nesse caso, o nosso objetivo é provar:
```bash
(Equal _ (Nat.add n p) (Nat.add (Nat.add p 0n) n)) 
```

Mas veja, para isso nós precisamos de outra prova, a de que 
```bash
Equal Nat (Nat.add p 0n) p
```
Para resolver essa prova seria esse o caminho:
```rust
Add_n_z (n: Nat)       : (Equal (Nat.add n Nat.zero) n)
Add_n_z Nat.zero       = Equal.refl
Add_n_z (Nat.succ n)   =
     let ind = Add_n_z n
     let app = (Equal.apply (x => (Nat.succ x)) ind)
     app
```
Agora vamos provar:
```rust
Plus_comm3a (n: Nat) (m: Nat) (p: Nat) : Equal (Nat.add n (Nat.add m p)) (Nat.add (Nat.add p m) n)
Plus_comm3a Nat.zero n p = 
  let pzr = Add_z_r p
  let com = Plus_comm n p
  let rwt = Equal.rewrite (Equal.mirror pzr) (x =>(Equal _ (Nat.add n p) (Nat.add (x) n))) com
  rwt
```
Agora só nos resta provar para o caso de *Nat.succ n*

```rust
Plus_comm3a (Nat.succ m) n p = ?
```
e o nosso objetivo é `(Equal _ (Nat.succ (Nat.add m (Nat.add n p))) (Nat.add (Nat.add p n) (Nat.succ m)))` e para isso precisariamos de outras provas, como a que 
```rust
Plus_n_sm (n: Nat) (m: Nat) : (Equal Nat (Nat.succ (Nat.add n m))(Nat.add n (Nat.succ m)))
Plus_n_sm Nat.zero m        = Equal.refl
Plus_n_sm (Nat.succ n) m    = (Equal.apply (x => (Nat.succ x)) (Plus_n_sm n m))
```
E reescrever na prova da comutatividade da adição entre *n* e *p* e depois usar a prova da comutatividade para isso tudo, um trabalho cansativo e, posso dizer, desnecessário. 

Ao invés de abrir os casos, vamos trabalhar com eles como variáveis puras, quase sem valor

```rust
Plus_comm3 (m: Nat) (n: Nat) (p: Nat) : Equal (Nat.add n (Nat.add m p)) (Nat.add (Nat.add p m) n)
Plus_comm3 m n p = ?
```
Nosso objetivo continua sendo `(Equal _ (Nat.add n (Nat.add m p)) (Nat.add (Nat.add p m) n))` e para entender isso, vamos analisar o nosso problema e os próximos passos serão triviais demais:

* *n + (m + p) = (p + m) + n* 

Isso é exatamente a comutatividade da adição, então basta reescrever a nossa prova *Plus_comm* duas vezes, uma dentro da outra
```rust
let a = Equal.rewrite (Plus_comm p m) (x => (Equal (Nat.add n (Nat.add m p)) (Nat.add (x) n))) (Plus_comm3 m n p)
```
e veja o que nossa variável *a* retorna
```bash
(Equal Nat (Nat.add n (Nat.add m p)) (Nat.add (Nat.add m p) n))
```
Estamos quase lá, basta apenas reescrever o segundo *Plus_comm* na adição mais interna do lado direito
```rust
let b = Equal.rewrite (Plus_comm m p) (x => (Equal Nat (Nat.add n (Nat.add m p)) (Nat.add (x) n))) a
```
e o nosso *b* é exatamente igual ao nosso objetivo
```bash
(Equal Nat (Nat.add n (Nat.add m p)) (Nat.add (Nat.add p m) n)) 
```
A prova completa fica:
```rust
Plus_comm3 (m: Nat) (n: Nat) (p: Nat) : Equal (Nat.add n (Nat.add m p)) (Nat.add (Nat.add p m) n)
Plus_comm3 m n p = 
  let a = Equal.rewrite (Plus_comm p m) (x => (Equal (Nat.add n (Nat.add m p)) (Nat.add (x) n))) (Plus_comm3 m n p)
  let b = Equal.rewrite (Plus_comm m p) (x => (Equal Nat (Nat.add n (Nat.add m p)) (Nat.add (x) n))) a
  b
```
Muito mais simples e elegante, não precisava de tanto trabalho, uma breve leitura do problema praticamente já nos entregava a solução. Perceba, isso não foi diferente de tudo o que já fizemos até aqui, é até uma repetição dos passos anteriores, é semelhante à aplicação de uma função polimórfica a um argumento de tipo.

Você pode "usar teoremas como funções" desta maneira com quase todas as táticas que recebem um nome de teorema como argumento. Note também que a aplicação de teorema usa os mesmos mecanismos de inferência que a aplicação de função; portanto, é possível, por exemplo, fornecer wildcards como argumentos a serem inferidos, ou declarar algumas hipóteses de um teorema como implícitas por padrão. Esses recursos são ilustrados na prova abaixo.


```rust
Lemma_app_ex 
  (n: Nat) 
  (xs: List Nat) 
  (i: In n (List.map xs (x => Nat.mul x Nat.zero)))   : Equal Nat n 0n
Lemma_app_ex n List.nil i                             = Empty.absurd i
Lemma_app_ex n (List.cons xs.h xs.t) (Either.left i)  = 
  let mult_0_r_xsh = Mult_0_r xs.h
  let rwt = Equal.rewrite (Equal.mirror i) (x => Equal Nat x 0n) mult_0_r_xsh
  rwt
Lemma_app_ex n (List.cons xs.h xs.t) (Either.right i) = Lemma_app_ex n xs.t i
```
Veremos muitos mais exemplos dos estilos desta seção nos capítulos posteriores.


## Kind vs Teoria dos Conjuntos

O núcleo lógico do Coq, o Cálculo das Construções Indutivas, difere de algumas maneiras importantes de outros sistemas formais que são usados pelos matemáticos para escrever provas precisas e rigorosas. Por exemplo, na fundação mais popular para a matemática em papel e lápis convencional, a Teoria dos Conjuntos de Zermelo-Fraenkel (ZFC), um objeto matemático pode potencialmente ser membro de muitos conjuntos diferentes; um termo na lógica de Kind, por outro lado, é membro de no máximo um tipo. Essa diferença muitas vezes leva a formas ligeiramente diferentes de capturar conceitos matemáticos informais, mas estes são, em grande parte, bastante naturais e fáceis de trabalhar. Por exemplo, em vez de dizer que um número natural n pertence ao conjunto de números pares, diríamos em Kind que ev n é verdadeiro, onde ev: Nat -> Tipo é uma propriedade que descreve os números pares.

No entanto, há alguns casos em que traduzir o raciocínio matemático padrão para Kind pode ser tanto trabalhoso quanto, às vezes, até impossível, a menos que enriqueçamos a lógica central com axiomas adicionais. Concluímos este capítulo com uma breve discussão de algumas das diferenças mais significativas entre os dois mundos.

### Extensão Funcional. 
As afirmações de igualdade que vimos até agora dizem principalmente respeito a elementos de tipos indutivos (Nat, Bool, etc.). Mas como o operador de igualdade de Kind é polimórfico, essas não são as únicas possibilidades - em particular, podemos escrever proposições que afirmam que duas funções são iguais uma à outra:

```rust 
Function_equality_ex1 : Equal (Nat.succ 3n) (Nat.succ (Nat.pred 4n))
Function_equality_ex1 = Equal.refl
```

Na prática matemática comum, duas funções f e g são consideradas iguais se produzem as mesmas saídas:

(∀𝑥, 𝑓(𝑥) = 𝑔(𝑥)) → 𝑓 = 𝑔

Isto é conhecido como o princípio da extensão funcional.

De forma informal, uma "propriedade extensional" é aquela que diz respeito ao comportamento observável de um objeto. Assim, a extensão funcional significa simplesmente que a identidade de uma função é completamente determinada pelo que podemos observar a partir dela - isto é, em termos de Kind, os resultados que obtemos após aplicá-la.

A extensão funcional não faz parte dos axiomas básicos do Kind. Isso significa que algumas proposições "razoáveis" não são prováveis.

```rust
Function_equality_ex2 : Equal ((x: Nat) => Nat.add x 1n) ((x: Nat) => Nat.add 1n x)
Function_equality_ex2 = ?
```

No entanto, podemos declarar um teorema e pular a sua prova ou usar um buraco

```rust
Functional_extensionality <a><b> (f: a -> b) (g: a -> b) (e: (x: a) -> Equal (f x) (g x)) : Equal f g
```

Agora podemos invocar a extensionalidade funcional em provas:
```rust
Function_equality_ex2 : Equal ((x: Nat) => Nat.add x 1n) ((x: Nat) => Nat.add 1n x)
Function_equality_ex2 =
  Functional_extensionality ((x: Nat) => Nat.add x 1n) ((x: Nat) => Nat.add 1n x) (x => Plus_comm x 1n)
```

Naturalmente, devemos ter cuidado ao adicionar novos axiomas à lógica do Kind, pois eles podem torná-la inconsistente - ou seja, podem tornar possível provar todas as proposições, incluindo Void!

Infelizmente, não há uma maneira simples de saber se um axioma é seguro para adicionar: geralmente é necessário um trabalho árduo para estabelecer a consistência de qualquer combinação particular de axiomas.

No entanto, sabe-se que adicionar a extensionalidade funcional, em particular, é consistente.

#### Tr_rev 
Um problema com a definição da função de reversão de lista "rev" que temos é que ela realiza uma chamada a "++" a cada passo. Executar "++" leva tempo assintoticamente linear no tamanho da lista, o que significa que "rev" tem tempo de execução quadrático.
Podemos melhorar isso com a seguinte definição:

```rust
Rev_append <x> (l1: List x) (l2: List x)  : List x
Rev_append x List.nil l2                  = l2
Rev_append x (List.cons xs.h xs.t) l2     = Rev_append xs.t (List.cons xs.h l2)

Tr_rev <x> (l: List x) : List x
Tr_rev x l = Rev_append x l List.nil1
```

Esta versão é dita ser tail-recursive, porque a chamada recursiva à função
é a última operação que precisa ser executada (ou seja, não precisamos executar ++
após a chamada recursiva); um compilador decente irá gerar um código muito eficiente neste
caso. Prove que as duas definições são realmente equivalentes.

```rust
Tr_rev_correct <a> (xs: List a) : Equal (Tr_rev xs) (Rev xs)
Tr_rev_correct a xs = ?
```

### Proposições e Booleans. 

Vimos duas maneiras diferentes de codificar fatos lógicos em Kind: com booleanos (do tipo Bool) e com proposições (do tipo Type).
Por exemplo, para afirmar que um número n é par, podemos dizer que
• (1) evenb n retorna True, ou
• (2) existe um k tal que n = double k. De fato, essas duas noções de paridade são equivalentes, como pode ser facilmente mostrado com um par de lemas auxiliares.
Muitas vezes dizemos que o booleano evenb n reflete a proposição (n => Equal n (double k)).

```rust
Evenb_double (k: Nat)     : Equal (Nat.is_even (Nat.double k)) Bool.true
Evenb_double Nat.zero     = Equal.refl
Evenb_double (Nat.succ k) = Evenb_double k
```

#### Evenb_double_conv
```rust
Evenb_double_conv (n: Nat):
  Sigma Nat (k => (Equal n (Bool.if (Evenb n) (Nat.double k) (Nat.succ (Nat.double k)))))
Evenb_double_conv n = ?
```

TODO: terminar `even_bool_prop`
```rust
Even_bool_prop (n: Nat): 
  Equivalence (Equal (Evenb n) Bool.true) (Sigma Nat (k => Equal n (Nat.double k)))
```

Da mesma forma, para afirmar que dois números *n* e *m* são iguais, podemos dizer (1) que `n == m` retorna `Bool.true` ou (2) que `n = m`. Essas duas noções são equivalentes.

```rust
Beq_nat_true_equiv (n1: Nat) (n2: Nat) : Equivalence (Equal (Nat.equal n1 n2) Bool.true) (Equal n1 n2)
Beq_nat_true_equiv n1 n2 = Equivalence.new (x => To_beq_nat_true n1 n2 x) (y => Fro_beq_nat_true n1 n2 y)

To_beq_nat_true (n1: Nat) (n2: Nat) (e: Equal (Nat.equal n1 n2) Bool.true) : Equal n1 n2  
To_beq_nat_true Nat.zero Nat.zero Equal.refl = Equal.refl
To_beq_nat_true Nat.zero (Nat.succ n2) e = 
  let emp = (Equal.rewrite e 
    (x => match Bool x {
      true  => Empty
      false => Unit
    })
    (Unit.new))
  Empty.absurd emp
To_beq_nat_true (Nat.succ n1) Nat.zero e = 
  let emp = (Equal.rewrite e 
    (x => match Bool x {
      true  => Empty
      false => Unit
    })
    (Unit.new))
  Empty.absurd emp
To_beq_nat_true (Nat.succ n1) (Nat.succ n2) e = Equal.apply (x => Nat.succ x) (Extract_equal n1 n2 e)

Fro_beq_nat_true (n1: Nat) (n2: Nat) (e: Equal  n1 n2) : Equal (Nat.equal n1 n2) Bool.true
Fro_beq_nat_true Nat.zero Nat.zero Equal.refl = Equal.refl
Fro_beq_nat_true Nat.zero (Nat.succ n2) e = 
  let emp = (Equal.rewrite e 
    (x => match Nat x {
      zero => Unit
      succ => Empty
    })
    (Unit.new))
  Empty.absurd emp
Fro_beq_nat_true (Nat.succ n1) Nat.zero e = 
  let emp = (Equal.rewrite e 
    (x => match Nat x {
      zero => Empty
      succ => Unit
    })
    (Unit.new))
  Empty.absurd emp
Fro_beq_nat_true (Nat.succ n1) (Nat.succ n2) e =
  let e2  = Succ_n1_n2 n1 n2 e
  let ind = Fro_beq_nat_true n1 n2 e2
  ind

  Succ_n1_n2 (n1: Nat) (n2: Nat) (e : (Equal Nat (Nat.succ n1) (Nat.succ n2))) : Equal Nat n1 n2
Succ_n1_n2 Nat.zero Nat.zero e            = Equal.refl
Succ_n1_n2 (Nat.succ n1) Nat.zero e       = Equal.apply (x => Nat.pred x) e
Succ_n1_n2 Nat.zero (Nat.succ n2) e       = Equal.apply (x => Nat.pred x) e
Succ_n1_n2 (Nat.succ n1) (Nat.succ n2) e  = Equal.apply (x => Nat.pred x) e

Extract_equal (n1: Nat) (n2: Nat) (e: Equal (Nat.equal n1 n2) Bool.true) : Equal n1 n2
Extract_equal Nat.zero Nat.zero (Equal.refl) = Equal.refl
Extract_equal Nat.zero (Nat.succ n2) (e) = 
  let emp = (Equal.rewrite e 
    (x => match Bool x {
      true  => Empty
      false => Unit
    })
    (Unit.new))
  Empty.absurd emp
Extract_equal (Nat.succ n1) Nat.zero (e) = 
  let emp = (Equal.rewrite e 
    (x => match Bool x {
      true  => Empty
      false => Unit
    })
    (Unit.new))
  Empty.absurd emp
Extract_equal (Nat.succ n1) (Nat.succ n2) e = Equal.apply (x => Nat.succ x) (Extract_equal n1 n2 e)
```

No entanto, enquanto as formulações booleanas e proposicionais de uma afirmação são equivalentes do ponto de vista puramente lógico, elas não precisam ser equivalentes operacionalmente. A igualdade fornece um exemplo extremo: saber que n == m = Verdadeiro geralmente é de pouca ajuda direta no meio de uma prova envolvendo n e m; no entanto, se convertermos a declaração para a forma equivalente n = m, podemos reescrevê-la.

O caso dos números pares também é interessante. Lembre-se de que, ao provar a direção inversa de even_bool_prop (ou seja, evenb_double, indo da afirmação proposicional para a booleana), usamos uma indução simples em k. Por outro lado, a conversa (o exercício evenb_double_conv) exigiu uma generalização inteligente, uma vez que não podemos provar diretamente `(k => Equal n (Nat.double k)) = Bool.true`

Para esses exemplos, as afirmações proposicionais são mais úteis do que suas contrapartes booleanas, mas nem sempre é o caso. Por exemplo, não podemos testar se uma proposição geral é verdadeira ou não em uma definição de função; como consequência, o seguinte trecho de código é rejeitado:

```rust
Is_even_prime : Nat -> Bool
Is_even_prime = (n: Nat) => Bool.if (Equal n 2n) Bool.true Bool.false
```

O *Kind* reclama que `n = 2` tem o tipo *Type*, enquanto espera um elemento de *Bool* (ou algum outro tipo indutivo com dois elementos). A razão para esta mensagem de erro tem a ver com a natureza computacional da linguagem central de *Kind*, que é projetada de forma que cada função que ela possa expressar seja computável e total. Uma razão para isso é permitir a extração de programas executáveis a partir dos desenvolvimentos de *Kind*. Como consequência, em *Kind*, Type não tem uma operação de análise de caso universal que diga se uma dada proposição é verdadeira ou falsa, já que tal operação permitiria escrever funções não computáveis.

Embora propriedades gerais não computáveis não possam ser formuladas como computações booleanas, vale ressaltar que muitas propriedades computáveis ​​são mais fáceis de expressar usando *Type* do que *Bool*, já que definições de funções recursivas estão sujeitas a restrições significativas em *Kind*. Por exemplo, o próximo capítulo mostra como definir a propriedade de que uma expressão regular corresponde a uma determinada string usando *Type*. Fazer o mesmo com Bool seria equivalente a escrever um verificador de expressão regular, o que seria mais complicado, mais difícil de entender e mais difícil de raciocinar.

Por outro lado, um importante benefício adicional de afirmar fatos usando booleanos é habilitar alguma automação de prova por meio de computação com termos em Kind, uma técnica conhecida como prova por reflexão. Considere a seguinte afirmação:

```rust
Even_1000 : Sigma Nat (k => Equal 1000n (Nat.double k))
```

A prova mais direta desse fato é fornecer o valor de k explicitamente.

```rust
Even_1000 = $ 500n Equal.refl
```

Por outro lado, a prova da correspondente afirmação booleana é ainda mais simples:
```rust
Even_1000a : Equal (Evenb 1000n) Bool.true
Even_1000a = Equal.refl
```

O interessante é que, como as duas noções são equivalentes, podemos usar a formulação booleana para provar a outra sem mencionar explicitamente o valor 500:
```rust
Even_1000b : Sigma Nat (k => Equal 1000n (Nat.double k))
Even_1000b = ? //TODO: Terminar aqui
```

Embora não tenhamos ganhado muito em termos de tamanho de prova neste caso, provas maiores podem ser consideravelmente simplificadas pelo uso da reflexão. Como um exemplo extremo, a prova do teorema dos 4 cores em Coq usa reflexão para reduzir a análise de centenas de casos diferentes a uma computação booleana. Não abordaremos a reflexão em grande detalhe, mas ela serve como um bom exemplo que mostra as forças complementares dos booleanos e proposições gerais.

#### Logical_connectives 
Os seguintes lemas relacionam os conectivos proposicionais estudados neste capítulo com as operações booleanas correspondentes.

```rust
Andb_true_equiv 
  (b1: Bool) 
  (b2: Bool) : Equivalence (Equal (Bool.and b1 b2) Bool.true) (Pair (Equal b1 Bool.true) (Equal b2 Bool.true))
Andb_true_equiv b1 b2 = ?


Orb_true_equiv 
  (b1: Bool) 
  (b2: Bool): Equivalence (Equal (Bool.or b1 b2) Bool.true) (Either (Equal b1 Bool.true) (Equal b2 Bool.true))
Orb_true_equiv b1 b2 = ?
```

#### Beq_nat_false_equiv
O teorema a seguir é uma formulação alternativa "negativa" de beq_nat_true_equiv que é mais conveniente em certas situações (veremos exemplos em capítulos posteriores).

```rust
Beq_nat_false_equiv (n1: Nat) (n2: Nat) : Equivalence (Equal (Nat.equal n1 n2) Bool.false) (Not (Equal n1 n2))
Beq_nat_false_equiv n1 n2 = ?
```

#### Beq_list 
Dado um operador booleano beq para testar a igualdade de elementos de algum tipo a, podemos definir uma função beq_list beq para testar a igualdade de listas com elementos em a. Complete a definição da função beq_list abaixo. Para garantir que sua definição está correta, prove o lema beq_list_true_equiv.

```rust
Beq_list <a> (beq: a -> a -> Bool) (xs: List a) (ys: List a) : Bool
Beq_list a beq  xs ys = ?

Beq_list_true_equiv <a> 
  (beq: a -> a -> Bool) 
  (a1: a) 
  (a2: a) 
  (e: Equivalence (Equal (beq a1 a2) Bool.true) (Equal a1 a2))
  (xs: List a)
  (ys: List a): Equivalence (Equal (Beq_list beq xs ys) Bool.true) (Equal xs ys)
Beq_list_true_equiv a beq a1 a2 e xs ys = ?
```

#### All_forallb

```rust
Forallb <x> (t: x -> Bool) (xs: List x) : Bool
Forallb x t List.nil = Bool.true
Forallb x t (List.cons xs.h xs.t) = Bool.and (t xs.h) (Forallb t xs.t)

```
Prove o teorema abaixo, que relaciona forallb à propriedade All do exercício acima.


```rust
Forallb_true_equiv <x> 
  (t: x -> Bool) 
  (xs: List x) : 
  Equivalence (Equal (Forallb t xs) Bool.true) ((All ((k: x) => Equal (t k) Bool.true) xs))
Forallb_true_equiv x t xs = ?
```
Existem alguma propriedades importantes da função forallb que não são capturadas por esta especificação?

#### Lógica Clássica vs. Lógica Construtiva. 

Vimos que não é possível testar se uma proposição p é verdadeira ou não ao definir uma função Kind. Você pode se surpreender ao descobrir que uma restrição semelhante se aplica às provas! Em outras palavras, o seguinte princípio de raciocínio intuitivo não é derivável em Kind:

```rust
Excluded_middle <p>: Either p (Not p)
```
Para entender operacionalmente por que esse é o caso, lembre-se de que, para provar uma declaração da forma  `Either p q`, usamos as correspondências de padrão *Left* e *Right*, que exigem saber qual lado da disjunção é verdadeiro. Mas a proposição p universalmente quantificada em *Excluded_middle* é uma proposição arbitrária, sobre a qual não sabemos nada. Não temos informações suficientes para escolher qual de Left ou Right aplicar, assim como *Kind* não tem informações suficientes para decidir mecanicamente se p é verdadeira ou não dentro de uma função.

No entanto, se soubermos que *p* é refletida em algum termo booleano *b*, saber se ela é verdadeira ou não é trivial: basta verificar o valor de b.


```rust
Restricted_excluded_middle <p> <q> (b: Bool)(e: Equivalence p (Equal b Bool.true)) : Either p (Not p) 
Restricted_excluded_middle p q Bool.true  (Equivalence.new pb bp) = Either.left (bp Equal.refl)
Restricted_excluded_middle p q Bool.false (Equivalence.new pb bp) = Either.right (Empty.absurd (Not_implies_our_not pb))
```

Em particular, o terceiro excluído é válido para equações n = m, entre números naturais n e m.

```rust
//TODO: Terminar aqui
Restricted_excluded_middle_eq (n: Nat) (m: Nat) : Either (Equal n m) (Not (Equal n m))
Restricted_excluded_middle_eq n m = ?

To_reme (n: Nat) (m: Nat) (e: Equal n m) : Equal (Nat.equal n m) Bool.true
To_reme Nat.zero Nat.zero e         = Equal.refl
To_reme Nat.zero (Nat.succ m) e     = Empty.absurd (Not_implies_our_not e)
To_reme (Nat.succ n) Nat.zero e     = Empty.absurd (Not_implies_our_not e)
To_reme (Nat.succ n) (Nat.succ m) e = To_reme n m (Succ_injective n m e)

From_reme (n: Nat) (m: Nat) (e: Equal (Nat.equal n m) Bool.true) : Equal n m
From_reme Nat.zero Nat.zero e         = Equal.refl
From_reme Nat.zero (Nat.succ m) e     = Empty.absurd (Not_implies_our_not e)
From_reme (Nat.succ n) Nat.zero e     = Empty.absurd (Not_implies_our_not e)
From_reme (Nat.succ n) (Nat.succ m) e = Equal.apply (x => Nat.succ x) (From_reme n m e)
