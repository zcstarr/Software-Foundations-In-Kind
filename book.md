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
* `Nat.zero` é do tipo Nat
* `Nat.succ` é um construtor que recebe um `Nat` e constrói outro `Nat`, ou seja, se n é `Nat`, então `(Nat.succ n)` também é `Nat`

Todo tipo definido indutivamente (`Nat`, `Bool`, `Day`, etc.) é um conjunto de expressões.
A definição de `Nat` diz como expressões do tipo `Nat` podem ser construídas:
* A expressão `Nat.zero` tem tipo `Nat`;
* Se n é uma expressão do tipo `Nat`, então `(Nat.succ n)` também é uma expressão do tipo `Nat`; e
* Expressões formadas dessas duas formas são as únicas do tipo `Nat`.

As mesmas regras se aplicam para nossas definições de `Day` e `Bool`. As anotações que usamos para seus construtures são análogas à do construtor `Nat.zero`, indicando que elas não recebem nenhum argumento.

Essas três condiçõesimplicam que a expressão `Nat.zero`, a expressão (Nat.succ Nat.zero), a expressão `(Nat.succ (Nat.succ Nat.zero))` e assim por diante tem tipo `Nat`, enquanto outras expressões como `Bool.true`, `(Bool.and Bool.true Bool.false)`, e `(Nat.succ (Nat.succ Bool.false))` não.

Nós podemos escrever funções simples que usam pattern matching em números naturais da mesma forma que fizemos acima - por exemplo, a função predecessor:

```rust
Pred (n: Nat) : Nat
Pred Nat.zero     = Nat.zero
Pred (Nat.succ k) = k
```
A segunda linha pode ser lida: "se n tem a forma `(Nat.succ k)` para algum k, então retorne k.
```rust
MinusTwo (n: Nat) : Nat
MinusTwo Nat.zero               = Nat.zero
MinusTwo (Nat.succ Nat.zero)    = Nat.zero
MinusTwo (Nat.succ (Nat.succ k) = k
```
Para a maioria das definições de funções de números, só pattern matching não é suficiente: nós precisaremos também da recursão. Por exemplo, para checar que um número n é pa, nós talvez precisemos checar recursivamente se `n-2` é par.
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
Você pode usar o patter matching em duas expressões ao mesmo tempo:
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
