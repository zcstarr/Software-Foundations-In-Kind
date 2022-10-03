# Kind Book

## 2. Básico

### 2.1 Introdução

O estilo de programação funcional traz programação mais perto da simples e
cotidiana matemática: Se um procedimento ou método não tem efeitos colaterais,
então (ignorando eficiência) só o que precisamos entender sobre ele é como mapear
entradas para saídas - ou seja, podemos pensar nele como um método concreto que
computa uma função matemática. Esse é um dos sentidos do "funcional" em
"programação funcional". A conexão direta entre programas e objetos matemáticos
simples suporta tanto a formalidade de provas de corretude quanto a racionalização
informal sobre o comportamento do programa.

O outro sentido na qual programação funcional é "funcional" é que ela enfatiza
o uso de funções "ou métodos" como valores de primeira classe - ou seja, valores
que podem ser passados como argumentos para outras funções, retornados como
resultados, incluídos em estruturas de dados, etc. O reconhecimento de que funções
podem ser tratadas desse jeito como dados habilita uma gama de idiomas úteis.

Outras funcionalidades comuns de linguagens funcionais incluem *algebraic data types*
e *pattern matching*, o que torna fácil construir e manipular estruturas de dados,
e sistemas de tipo polimórficos sofisticados, suportando abstração e reuso de código.
Kind contém todas essas funcionalidades.

A primeira metade desse capítulo introduz os elementos mais básicos da linguagem
de programação funcional Kind. A segunda metade introduz algumas técnicas básicas
que podem ser usadas para provar propriedades em programas em Kind.

### 2.2 Tipos Enumerados


### 2.6 Números
Os tipos que definimos até agora são exemplos de tipos enumerados: suas definições enumeram explicitamente um conjunto finito de elemento. Um jeito mais interessante de definir um tipo é estabelecer uma coleção de regras indutivas descrevendo seus elementos. Por exemplo, nós podemos definir os números naturais da seguinte maneira: 
```rust
Nat: Type
Nat.zero              : Nat
Nat.succ (pred:Nat)   : Nat
```
Essa definição pode ser lida:
* `Nat` é um tipo
* `Nat.zero` é do tipo `Nat`
* `Nat.succ` é um construtor que recebe um `Nat` e constrói outro `Nat`, ou seja, se n é `Nat`, então `(Nat.succ n)` também é `Nat`

Todo tipo definido indutivamente (`Nat`, `Bool`, `Day`, etc.) é um conjunto de expressões.
A definição de `Nat` diz como expressões do tipo `Nat` podem ser construídas:
* A expressão `Nat.zero` tem tipo `Nat`;
* Se n é uma expressão do tipo `Nat`, então `(Nat.succ n)` também é uma expressão do tipo `Nat`; e
* Expressões formadas dessas duas formas são as únicas do tipo `Nat`.

As mesmas regras se aplicam para nossas definições de `Day` e `Bool`. As anotações que usamos para seus construtures são análogas à do construtor `Nat.zero`, indicando que elas não recebem nenhum argumento.

Essas três condiçõesimplicam que a expressão `Nat.zero`, a expressão `(Nat.succ Nat.zero)`, a expressão `(Nat.succ (Nat.succ Nat.zero))` e assim por diante tem tipo `Nat`, enquanto outras expressões como `Bool.true`, `(Bool.and Bool.true Bool.false)`, e `(Nat.succ (Nat.succ Bool.false))` não.

Nós podemos escrever funções simples que usam pattern matching em números naturais da mesma forma que fizemos acima - por exemplo, a função predecessor:

```rust
Pred (n: Nat) : Nat
Pred Nat.zero     = Nat.zero
Pred (Nat.succ k) = k
```
A segunda linha pode ser lida: se n tem a forma `(Nat.succ k)` para algum k, então retorne k.
```rust
MinusTwo (n: Nat) : Nat
MinusTwo Nat.zero               = Nat.zero
MinusTwo (Nat.succ Nat.zero)    = Nat.zero
MinusTwo (Nat.succ (Nat.succ k) = k
```
Para evitar ter que escever uma sequencia de `Nat.succ` toda vez que quiser um `Nat` é possível usar a função `U60.to_nat`, que recebe um número escrito na forma usual (do tipo `U60`) e retorna o `Nat` correspondente. 
```rust
TestU60 : (Equal (U60.to_nat 6) (Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ Nat.zero))))))
TestU60 = Equal.refl
```
Para a maioria das definições de funções de números, só pattern matching não é suficiente: nós precisaremos também da recursão. Por exemplo, para checar que um número n é par, nós talvez precisemos checar recursivamente se `n-2` é par.
```rust
Evenb (n: Nat) : Bool
Evenb Nat.zero                = Bool.true
Evenb (Nat.succ Nat.zero)     = Bool.false
Evenb (Nat.succ (Nat.succ k)) = Evenb k
```
Nós podemos definir `Oddb` (função para checar se um número é ímpar) com uma declaração recursiva semelhante, mas aqui está uma definição mais simples
que é um pouco mais fácil de trabalhar:
```rust
Oddb (n: Nat) : Bool
Oddb n = Bool.not (Evenb n)
```
```rust
TestOddb1 : Equal (Oddb (Nat.succ Nat.zero)) Bool.true
TestOddb1 = Equal.refl

TestOddb2 : Equal (Oddb (Nat.succ (Nat.succ (Nat.succ (Nat.succ Nat.zero))))) Bool.false
TestOddb2 = Equal.refl
```
Naturalmente, nós também podemos definir funções com multiplos argumentos por recursão.
```rust
Plus (n: Nat) (m: Nat) : Nat
Plus Nat.zero     m = m
Plus (Nat.succ k) m = Nat.succ (Plus k m)
```
Adicionar 3 a 2 agora retornará 5 como esperado.
A simplificação que o Kind realiza para chegar a esse valor pode ser vizualizada assim:
```
Plus (Nat.succ (Nat.succ (Nat.succ Nat.zero))) (Nat.succ (Nat.succ Nat.zero)))
~> Nat.succ (Plus (Nat.succ (Nat.succ Nat.zero)) (Nat.succ (Nat.succ Nat.zero)))   pela segunda regra de Plus
~> Nat.succ (Nat.succ (Plus (Nat.succ Nat.zero)) (Nat.succ (Nat.succ Nat.zero)))   pela segunda regra de Plus
~> Nat.succ (Nat.succ (Nat.succ (Plus Nat.zero (Nat.succ (Nat.succ Nat.zero)))))   pela segunda regra de Plus
~> Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ Nat.zero))))    pela primeira regra de Plus
```
A multiplicação pode ser definida usando a definição de Plus, da seguinte forma:
```rust
Mult (n: Nat) (m: Nat) : Nat
Mult Nat.zero     m = Nat.zero
Mult (Nat.succ k) m = Plus m (Mult k m)
```
```rust
TestMult1: (Equal (Mult (U60.to_nat 3) (U60.to_nat 3)) (U60.to_nat 9))
TestMult1 = Equal.refl
```
Você pode usar o pattern matching em duas expressões ao mesmo tempo:
```rust
Minus (n: Nat) (m: Nat) : Nat
Minus Nat.zero     m            = Nat.zero
Minus n            Nat.zero     = n
Minus (Nat.succ k) (Nat.succ j) = Minus k j
```
O função Exp pode ser definida usando o Mult (de forma semelhante ao feito com `Mult` e `Plus`):
```rust
Exp (base: Nat) (power: Nat) : Nat
Exp base Nat.zero     = Nat.succ Nat.zero
Exp base (Nat.succ k) = Mult base (Exp base k)
```
#### 6.0.1. Exercício
Escreva a função fatorial em Kind2:
```rust
Factorial (n: Nat) : Nat
Factorial n = ?
```
```rust
TestFactorial1 : Equal (Factorial (U60.to_nat 3)) (U60.to_nat 6)
TestFactorial1 = ?

TestFactorial2 : Equal (Factorial (U60.to_nat 5)) (U60.to_nat 120)
TestFactorial2 = ?
```
A função Nat.equal testa a igualdade entre Naturais, retornando um booleano
```rust
Nat.equal (n: Nat) (m: Nat) : Bool
Nat.equal Nat.zero     Nat.zero     = Bool.true
Nat.equal Nat.zero     (Nat.succ j) = Bool.false
Nat.equal (Nat.succ k) Nat.zero     = Bool.false
Nat.equal (Nat.succ k) (Nat.succ j) = Nat.equal k j
```
A função `Lte` testa se o primeiro argumento é menor ou igual ao segundo, retornando um booleano
```rust
Lte (n: Nat) (m: Nat) : Bool
Lte Nat.zero     m            = Bool.true
Lte (Nat.succ k) Nat.zero     = Bool.false
Lte (Nat.succ k) (Nat.succ j) = Lte k j
```
```rust
TestLte1 : Equal (Lte (U60.to_nat 2) (U60.to_nat 2)) Bool.true
TestLte1 = Equal.refl

TestLte2 : Equal (Lte (U60.to_nat 2) (U60.to_nat 4)) Bool.true
TestLte2 = Equal.refl

TestLte3 : Equal (Lte (U60.to_nat 4) (U60.to_nat 2)) Bool.false
TestLte3 = Equal.refl
```
#### 6.0.2. Exercício
A função `blt_nat` testa numeros naturais para o menor ou igual. Em vez de criar uma nova função recursiva, defina em termos de funções previamente definidas.
```rust
Blt_nat (n: Nat) (m: Nat) : Bool
Blt_nat n m = ?
```
```rust
Test_blt_nat_1 : Equal (Blt_nat (U60.to_nat 2) (U60.to_nat 2)) Bool.false
Test_blt_nat_1 = ?

Test_blt_nat_2 : Equal (Blt_nat (U60.to_nat 2) (U60.to_nat 4)) Bool.true
Test_blt_nat_2 = ?

Test_blt_nat_3 : Equal (Blt_nat (U60.to_nat 4) (U60.to_nat 2)) Bool.false
Test_blt_nat_3 = ?
```
### 2.7 Prova por Simplificação
Agora que definimos alguns tipos de dados e funções, vamoscomeçar a provar propriedades de seus comportamentos. Na verdade, ja começamos a fazer isso: cada função que começa com `Test` nas seções anteriores, fazem uma afirmação precisa sobre o comportamento de alguma função para algumas entradas especificas. As provas dessas afirmações foram sempre a mesma: use `Equal.refl` para checar que ambos os lados contém valores idênticos.

O mesmo tipo de "prova por simplificação" pode ser usada para provar propriedades mais interessasntes. Por exemplo, o fato que o `Nat.zero` é um "elemento neutro" para a adição no lado esquerdo pode ser provado apenas observando que `Plus Nat.zero n` reduz para n, independente do que é n, fato esse que pode ser lido diretamente da definição de `Plus`.
```rust
Plus_Z_n (n: Nat) : Equal (Plus Nat.zero n) n
Plus_Z_n n = Equal.refl
```
Outros teoremas parecidos podem ser provados de forma parecida.

```rust
Plus_1_l (n: Nat) : Equal (Plus (Nat.succ Nat.zero) n) (Nat.succ n)
Plus_1_l n = Equal.refl

Mult_0_l (n: Nat) : Equal (Mult Nat.zero n) Nat.zero
Mult_0_l n = Equal.refl 
```
Embora a simplificação seja poderosa o suficiente para provar alguns fatos bastante gerais, existem muitas declarações que não podem ser demonstradas só com a simplificação. Por exemplo, não podemos usá-la para provar que `Nat.zero` é um elemento neutro para a dição no lado direito.
```rust
Plus_n_Z (n: Nat) : Equal n (Plus n Nat.zero)
Plus_n_Z n = Equal.refl
```
```rust
Type mismatch.
- Expected: (Equal _ n (Plus n Nat.zero))
- Detected: (Equal _ n n)
Context:
- n : Nat
```
Você consegue explicar por que isso acontece?

O próximo capítulo vai introduzir a indução, uma técnica poderosa que pode ser usada para demonstrar esse teorema. Por agora, vamos ver mais alguns tipos de prova. 

### 2.8 Prova por Reescrita
Esse teorema é mais interessante que os outros que vimos:
```rust
Plus_id_example (n: Nat) (m: Nat) (e : Equal n m) : Equal (Plus n n) (Plus m m)
```
Em vez de fazer uma afirmação sobre todos os naturais `n` e `m`, ele fala sobre uma propriedade mais especializada que so vale quando `Equal n m`.

Como nos teoremas anteriores, nós precisamos assumir a existencia dos numeros `n` e `m`. Também precisamos assumir a hipótese `Equal n m`.

Como n e m são números arbitrários, não podemos simplesmente usar a simplificação para demonstrar esse teorema. Em vez disso, provamos observando que, se estamos assumindo `Equal n m`, então pode substituir n por m no objetivo e obter uma igualdade com a mesma expressão em ambos os lados. A função que usamos para fazer isso é a `Equal.rewrite`.


# CAPÍTULO 3
## Indução: Prova por Indução
### módulo de indução



Nesse capítulo nós veremos sobre provas por indução, mas antes de prosseguirmos
para a indução em si, podemos analisar casos simples onde apenas a reflexão do
caso já prova o teorema.

```rust
Problems.t0 (n: Nat) : (Equal Nat (Nat.add Nat.zero n) n)
```

Ao verificar verificar o objetivo do teorema, recebemos a seguinte resposta:

```bash
Inspection.
- Goal: (Equal Nat n n)
Kind.Context:
- n : Nat
On 'problems0.kind2':
   64 | Problems.t0 n              = ?
```

No *Problems.t0* o Kind reduz a soma de "*0 + n*" automaticamente para *n* e
que devemos provar a igualdade entre *n* e *n*. Nesse caso basta escrever "*Equal*.**refl**" e obtemos a resposta de
confirmação: 

"*All terms check.*"

```rust
Problems.t1 (n: Nat) : (Equal Nat (Nat.add n Nat.zero) n)
```

Feito o primeiro problema, o seguinte é muito similar, é a soma de "*n + 0 = n*" e essa similaridade pode nos levar a crer que basta invocar a reflexão. Entretanto, no primeiro caso o Kind reduz automaticamente e nesse nós obtemos o seguinte retorno:

```bash
Inspection.
- Goal: (Equal _ (Nat.add n Nat.zero) n)
Kind.Context:
- n : Nat
On 'problems01.kind2':
   67 | Problems.t1 n = ?
```

No primeiro caso o Kind reduz pois o *zero* está à direita e o *Type
Checker* já reduz automaticamente, a soma de entre *0* e *n* para *n*.
Entretanto, quando o primeiro *input* é uma variável, o Kind necessita
verificar para cada caso e como é um número natural, há infinitos casos a serem
testatos, isto é, de zero a infinito.

De início podemos pensar que são tantos casos e que é impossível analisar todos
eles, já que são infinitos, mas logo percebemos que é possível reduzir a dois,
um é o número *zero* e o outro é um número que *sucede* o *zero* *n* vezes
depois.

Analizando para o caso de *zero* nosso objetivo é provar que *zero* é igual
a *zero*:

```rust
- Goal: (Equal _ Nat.zero Nat.zero)
```

Agora basta dar o *Equal*.**refl** e o caso zero já foi comprovado, basta
apenas responder para o *sucessor* de *zero*

Nosso objetivo é provar que para todo número *n*, ao adicionar *0* o
resultado será *n*, mas já temos uma nova ferramenta que nos auxilia nessa
prova e é a prova para o caso *zero*, basta reduzir *n* até que o
necessário seja apenas a reflexão e podemos fazer isso por meio da
recursão e para isso definimos o novo *n* como o antecessor dele. No Kind nós
podemos fazer isso simplesmente definindo o *n* atual como sendo o sucessor
do próximo *n* e chamar a função para n recursivamente. Isso é feito da
seguinte forma:

```rust
Problems.t1 (Nat.succ n)   = ?
```

e temos como novo objetivo provar que o sucessor da soma entre *n* e *0* é
igual ao sucessor de *n* 


```rust
- Goal: (Equal _ (Nat.succ (Nat.add n Nat.zero)) (Nat.succ n))
```

Para trabalhar com a indução nessa recursão, devemos definir uma variável para
o caso original de *n* 


```rust
Problems.t1 (n: Nat)       : (Equal (Nat.add n Nat.zero) n)
Problems.t1 Nat.zero       = Equal.refl
Problems.t1 (Nat.succ n)   =
     let ind = Problems.t1 n
     ?
```

Ao dar o *Type Check* temos como retorno a seguinte resposta: 


```bash
Inspection.
- Goal: (Equal _ (Nat.succ (Nat.add n Nat.zero)) (Nat.succ n))
Kind.Context:
- n   : Nat
- ind : (Equal _ (Nat.add n Nat.zero) n)
- ind = (Problems.t1 n)
On 'problems01.kind2':
   72 |     ?
```

Ao analizar nosso objetivo e a indução, percebemos que a única diferença entre
o objetivo e a nossa variável *ind* é o *Nat*.**succ**, basta então
incrementar a variável *ind* com o *Nat*.**succ**, para isso nós criamos uma
nova variável e usamos uma função *lambda*:


```rust
let app = (Equal.apply (x => (Nat.succ x)) ind)
```

No caso acima nós chamamos a função *Equal*.**apply** para aplicar a nossa
função *lambda* ao *ind*. A função `x => (Nat.succ x)` serve para adicionar
*Nat*.**succ** a todo elemento recebido na variável. Como nossa variável *ind*
é uma função que recebe uma outra variável *n*, a nossa função *lambda*
incrementa a *n* com *Nat*.**succ**, o que retorna exatamente o nosso
objetivo:

```bash
Inspection.
- Goal: (Equal Nat (Nat.succ (Nat.add n Nat.zero)) (Nat.succ n))
Kind.Context:
- n   : Nat
- ind : (Equal Nat (Nat.add n Nat.zero) n)
- ind = (Problems.t1 n)
- app : (Equal Nat (Nat.succ (Nat.add n Nat.zero)) (Nat.succ n))
- app = (Equal.apply Nat Nat (Nat.add n Nat.zero) n (x => (Nat.succ x)) ind)
On 'problems01.kind2':
   72 |     ?
```

Podemos perceber que o *app* é exatamente igual ao *Goal*, que é o nosso
objetivo e basta apenas retornar ele, o app para que o *Type Check* valide
a nossa prova:
*All terms check.* 

### Outro caso

Vamos verificar se a a igualdade "n +(*m* + 1) = 1 + (*n* + *m*)" é verdadeira

Primeiro, o nosso problema:
```rust
Problems.t2 (n: Nat) (m: Nat)  : (Equal Nat (Nat.add n (Nat.succ m)) (Nat.succ(Nat.add n m))) 
```
Verificamos o primeiro caso, quando *n* é zero:
```rust
Problems.t2 Nat.zero m         = Equal.refl
```
e partimos para o caso seguinte
```rust
Problems.t2 (Nat.succ n) m     = ?
```

e o nosso objetivo atual vira:
```rust
Goal: (Equal Nat (Nat.succ (Nat.add n (Nat.succ m))) (Nat.succ (Nat.succ (Nat.add n m))))
```

Traduzindo, o sucessor da adição de *n* e o sucessor de *m* é igual ao
sucessor do sucessor da adição de *n* e *m*. Para resolver esse problema,
invocaremos a indução:

```rust
let ind = Problems.t16 n m
```
e o nosso objetivo atual é provar que:
```rust
Goal: (Equal Nat (Nat.succ (Nat.add n (Nat.succ m))) (Nat.succ (Nat.succ (Nat.add n m))))
```
Traduzindo novamente, que o *sucessor* da adição de *n* e o *sucessor* de *m* é igual ao *sucessor* do *sucessor* da adição de *n* e *m*. 

mas agora nós temos uma ferramenta muito útil, a nossa variável ind que é:
```rust
(Equal Nat (Nat.add n (Nat.succ m)) (Nat.succ (Nat.add n m)))
```
Ora, analisando o nosso objetivo e a nossa variável ind, podemos perceber que
basta dar um *Nat*.**succ** em ambos os lados da indução e ela ficará
exatamente igual ao nosso objetivo, para isso usaremos uma função
*lambda*:

```rust
let app = (Equal.apply (x => (Nat.succ x)) ind)
```
E a nossa variável *app* retornará o nosso objetivo:
```rust
(Equal Nat (Nat.succ (Nat.add n (Nat.succ m))) (Nat.succ (Nat.succ (Nat.add n m))))
```
Bastando apenas retornar o *app* para e o Kind nos retornará o tão almejado
*All terms check*.


## Usando outros teoremas

Em Kind, como na matemática informal, grandes provas são frequentemente divididas em uma sequência de
teoremas, com provas posteriores referindo-se a teoremas anteriores. Mas às vezes uma prova
exigirá algum fato variado que é muito trivial e de muito pouco geral
interesse em dar-lhe o seu próprio nome de nível superior. Nesses casos, é conveniente
ser capaz de simplesmente enunciar e provar o “sub-teorema” necessário exatamente no ponto
onde é usado.

Analisemos o seguinte teorema da comutação da adição: 
```rust
Problems.t3 (n: Nat) (m: Nat)      : (Equal Nat (Nat.add n  m) (Nat.add m n))
```
No primeiro caso, para *n* e *m* igual a zero nós temos uma reflexão:
```rust
Problems.t3 Nat.zero Nat.zero      = Equal.refl
```
Então partimos para o próximo caso:
```rust
Problems.t3 (Nat.succ n) m         = ?
```

E aqui parece que temos um novo problema: 
```rust
Goal: (Equal Nat (Nat.succ (Nat.add n m)) (Nat.add m (Nat.succ n)))
```
Ao analisar o problema, percebemos que dentro dele há um teorema já provado, de
que o *sucessor* da adição de dois números é igual a adição de um número com o
*sucessor* dele, então podemos usar isso ao nosso
favor.

Começaremos aplicando um *Nat*.**succ** no nosso problema original:
```rust
let inda    = (Equal.apply (x => (Nat.succ x)) (Problems.t3 n m ))
```


Depois invocaremos nosso problema já resolvido, o *Problems*.**t2**:
```rust 
let indb    = Problems.t2 m n
```

Ao dar o *Type Check*, o terminal nos retorna:
```bash
Aperte ENTER ou digite um comando para continuar
Inspection.
- Goal: (Equal Nat (Nat.succ (Nat.add n m)) (Nat.add m (Nat.succ n)))
Kind.Context:
- n    : Nat
- m    : Nat
- inda : (Equal Nat (Nat.succ (Nat.add n m)) (Nat.succ (Nat.add m n)))
- inda = (Equal.apply Nat Nat (Nat.add n m) (Nat.add m n) (x => (Nat.succ x)) (Problems.t3 n m))
- indb : (Equal Nat (Nat.add m (Nat.succ n)) (Nat.succ (Nat.add m n)))
- indb = (Problems.t2 m n)
On 'problems01.kind2':
   8 |    ?
```

Agora podemos perceber que a primeira parte da *inda* é igual ao inverso da
primeira parte do nosso
objetivo e a primeira parte da *indb* é igual a segunda do objetivo, basta
apenas organizar e juntar as partes necessárias. Para isso usaremos a
*Equal*.**mirror** e a *Equal*.**chain**.

```rust
let indc    = Equal.chain indb (Equal.mirror inda)
```


E o indc nos retorna um valor similar ao desejado:
```rust
Goal: (Equal Nat (Nat.succ (Nat.add n m)) (Nat.add m (Nat.succ n)))
indc : (Equal Nat (Nat.add m (Nat.succ n)) (Nat.succ (Nat.add n m)))
```

Podemos perceber que um é o outro espelhado, para torná-los iguais, usaremos o
*Equal*.**mirror** novamente:
```rust
let app     = Equal.mirror indc
```

Ao chamar o *app* o *Type Check* nos retorna a mensagem *All terms check* e
desta forma provamos, por meio da indução e usando uma outra prova, a comutação
da adição, ou seja, que a soma de *n* e *m* é igual a soma de *m* e *n*.



