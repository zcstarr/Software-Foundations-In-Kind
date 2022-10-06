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
podem ser tratadas desse jeito como dados habilita uma gama de comandos úteis.

Outras funcionalidades comuns de linguagens funcionais incluem *algebraic data types*
e *pattern matching*, o que torna fácil construir e manipular estruturas de dados,
e sistemas de tipo polimórficos sofisticados, suportando abstração e reuso de código.
Kind contém todas essas funcionalidades.

A primeira metade desse capítulo introduz os elementos mais básicos da linguagem
de programação funcional Kind. A segunda metade introduz algumas técnicas básicas
que podem ser usadas para provar propriedades em programas em Kind.

### 2.2 Tipos Enumerados

Um aspecto incomum do Kind, similar a outras linguagens de prova como Idris e Coq,
é que o seu conjunto de ferramentas built-in é bastante pequeno. Por exemplo,
ao invês de fornecer o leque usual de tipos primitivos (booleanos, listas, strings, etc),
Kind tem apenas um tipo primitivo (U60: números inteiros em 60 bits binários, sem sinal)
e oferece um mecanismo poderoso para definir novos tipos de dados do zero,
do qual pode-se derivar todos esses tipos já familiares e outros.

<!-- TODO Esse bloco de texto será relevante quando tivermos um sistema de pacotes apropriado -->
<!-- Naturally, the Idris distribution comes with extensive standard libraries providing
definitions of booleans, numbers, and many common data structures like lists and
hash tables (see the prelude and contrib packages), as well as the means to write
type-safe effectful code (see the effects package) and pruvlioj, a toolkit for proof
automation and program construction. But there is nothing magic or primitive
about these library definitions. To illustrate this, we will explicitly recapitulate
all the definitions we need in this course, rather than just getting them implicitly
from the library. -->

Para demonstrar como funciona o mecanismo de definição, vamos começar com um exemplo simples.

#### 2.2.1 Dias da semana

A declaração a seguir diz para o Kind que estamos declarando um novo conjunto de dados - um Tipo.

```rs
Dia : Type // Dia é um Tipo

Dia.segunda : Dia  // Segunda é um Dia
Dia.terca   : Dia  // Terça   é um Dia
Dia.quarta  : Dia  // Quarta  é um Dia
Dia.quinta  : Dia  // Quinta  é um Dia
Dia.sexta   : Dia  // Sexta   é um Dia
Dia.sabado  : Dia  // Sábado  é um Dia
Dia.domingo : Dia  // Domingo é um Dia
```

O tipo se chama `Dia`, e seus membros são `Segunda`, `Terca`, `Quarta`, etc.
A definição `<nome> : <tipo>` pode ser lida como: "nome é um tipo".
Acima temos tanto um exemplo de criar um tipo novo `Dia : Type`, quanto a de
declarar um elemento de um tipo existente `Quarta : Dia`.

Agora que temos definido o que é um Dia, podemos escrever funções que operam usando esse tipo.
Digite o seguinte:

```rs
ProximoDiaUtil (d : Dia) : Dia
```

Isso declara que temos uma função chamado `ProximoDiaUtil`, que recebe um argumento
chamado `d`, do tipo `Dia`, e retorna um `Dia`.
Continue a definição da função da seguinte forma:

```rs
ProximoDiaUtil Dia.segunda = ?DiaUtil_rhs_1
ProximoDiaUtil Dia.terca = ?DiaUtil_rhs_2
ProximoDiaUtil Dia.quarta = ?DiaUtil_rhs_3
ProximoDiaUtil Dia.quinta = ?DiaUtil_rhs_4
ProximoDiaUtil Dia.sexta = ?DiaUtil_rhs_5
ProximoDiaUtil Dia.sabado = ?DiaUtil_rhs_6
ProximoDiaUtil Dia.domingo = ?DiaUtil_rhs_7
```

O que estamos fazendo aqui é o que chamamos de *pattern matching*. Estamos
declarando como a função deve rodar para cada possibilidade da entrada `d`.
Nem sempre será necessário fazer isso, como será mostrado em exemplos mais a frente.

Por fim, complete as funções escrevendo o que cada uma deve retornar,
e use espaços para estilizar como preferir:

```rs
ProximoDiaUtil Dia.segunda = Dia.terca
ProximoDiaUtil Dia.terca   = Dia.quarta
ProximoDiaUtil Dia.quarta  = Dia.quinta
ProximoDiaUtil Dia.quinta  = Dia.sexta
ProximoDiaUtil Dia.sexta   = Dia.segunda
ProximoDiaUtil Dia.sabado  = Dia.segunda
ProximoDiaUtil Dia.domingo = Dia.segunda
```

Com a função finalizada, nós podemos checar o funcionamento dela com alguns exemplos.
O jeito principal de fazer isso em Kind é criar uma função `Main` no seu arquivo,
e rodando ele com o comando `kind2 run <file>`.

Por exemplo, se você escrever a seguinte `Main` e rodar o arquivo

```rs
Main {
  // Dois dias úteis depois do sábado
  ProximoDiaUtil (ProximoDiaUtil Sabado)
}
```

Deve ser retornado pra você algo como:

```terminal
(Dia.terca)
Rewrites: 2
```

Outro jeito de testar seu código, é dizer o que esperamos que o código retorne,
por meio de uma prova:

```rs
// O terceiro dia útil depois de uma segunda é uma quinta
TesteDiaUtil : Equal (ProximoDiaUtil (ProximoDiaUtil (ProximoDiaUtil Dia.segunda))) Dia.quinta
TesteDiaUtil = Equal.refl
```

Os detalhes de como provas funcionam serão explicados mais a frente. No momento,
o que precisa ser entendido disso é:

* Tem-se a constatação de que `(ProximoDiaUtil (ProximoDiaUtil (ProximoDiaUtil Dia.segunda)))` é igual a `Dia.quinta`
* Essa constatação foi nomeada `TesteDiaUtil`
* `TesteDiaUtil = Equal.refl` diz que constatação pode ser provada usando apenas simplificação nos dois lados

Para testar que essa prova (e qualquer outra prova adiante) está correta, você precisa checar o arquivo, usando o comando `kind2 check <file>`,
que deve te retornar algo como:

```terminal
All terms check.

Rewrites: 9435
```

### 2.3 Booleanos

Semelhantemente, podemos declarar o tipo `Bool`, para booleanos:

```rs
Bool : Type

Bool.true  : Bool
Bool.false : Bool
```

<!-- TODO mudar isso aqui caso tenhamos um gerenciador de pacotes -->
Nós estamos declarando nossos próprios booleano só pra demonstrar como fazer tudo do zero.
O Kind tem a sua implementação padrão de booleanos no pacote padrão (o [Wikind](github.com/Kindelia/Wikind)),
junto de várias outras estruturas e provas. Na verdade, no momento de escrita,
é necessário que você esteja trabalhando dentro da pasta Wikind para fazer
provas e teoremas, pois ainda não temos um gerenciador de pacote e os utilitários
de resolução de provas não são built-in.

Funções que funcionam sobre são definidas do mesmo jeito que visto anteriormente:

```rs
// Negação lógica
Notb (b : Bool) : Bool
Notb Bool.true  = Bool.false
Notb Bool.false = Bool.true

// E lógico
Andb (b1 : Bool) (b2 : Bool) : Bool
Andb Bool.true  b2 = b2
Andb Bool.false b2 = Bool.false

// OU lógico
Orb (b1 : Bool) (b2 : Bool) : Bool
Orb Bool.true  b2 = Bool.true
Orb Bool.false b2 = b2
```

As últimas duas funções demonstram como é a sintaxe do Kind para funções de
múltiplos argumentos, e também mostra que é possível fazer *pattern matching*
apenas em parte das variáveis da função, não necessariamente todas.

Os casos da última função podem ser testados exaustivamente (todas as possibilidades)
como mostrado a seguir, criando a tabela verdade da operação lógica.

```rs
TestOrb1 : Equal (Orb Bool.true Bool.false) Bool.true
TestOrb1 = Equal.refl

TestOrb2 : Equal (Orb Bool.false Bool.false) Bool.false
TestOrb2 = Equal.refl

TestOrb3 : Equal (Orb Bool.false Bool.true) Bool.true
TestOrb3 = Equal.refl

TestOrb4 : Equal (Orb Bool.true Bool.true) Bool.true
TestOrb4 = Equal.refl
```

#### *2.3.0.1 Exercício (nandb)*

Substitua o buraco ?nandb_rhs, completando a função seguinte; então confira
se ela está correta usando as constatações a seguir
(Análogo a como foi feito para a função `Orb`).
A função retorna `Bool.true` se qualquer uma de suas entradas for `Bool.false`

```rs
Nandb (b1 : Bool) (b2 : Bool) : Bool
Nandb b1 b2 = ?nandb_rhs

Test_nandb1 : Equal (Nandb Bool.true Bool.false) Bool.true
Test_nandb1 = ?test_nandb1_rhs

Test_nandb2 : Equal (Nandb Bool.false Bool.false) Bool.true
Test_nandb2 = ?test_nandb2_rhs

Test_nandb3 : Equal (Nandb Bool.false Bool.true) Bool.true
Test_nandb3 = ?test_nandb3_rhs

Test_nandb4 : Equal (Nandb Bool.true Bool.true) Bool.false
Test_nandb4 = ?test_nandb4_rhs
```

#### *2.3.0.2 Exercício (and3)*

Faça o mesmo para a função `Andb3` abaixo. Essa função deve retornar `Bool.true`
se todas as entradas forem `Bool.true`, e `Bool.false` caso contrário

```rs
Andb3 (b1 : Bool) (b2 : Bool) (b3 : Bool) : Bool
Andb3 b1 b2 b3 = ?andb3_rhs

Test_andb3_1 Equal (Andb3 Bool.true Bool.true Bool.true) Bool.true
Test_andb3_1 = ?test_andb31_rhs

Test_andb3_2 Equal (Andb3 Bool.false Bool.true Bool.true) Bool.false
Test_andb3_2 = ?test_andb32_rhs

Test_andb3_3 Equal (Andb3 Bool.true Bool.false Bool.true) Bool.false
Test_andb3_3 = ?test_andb33_rhs

Test_andb3_4 Equal (Andb3 Bool.true Bool.true Bool.false) Bool.false
Test_andb3_4 = ?test_andb34_rhs
```

### 2.4 Tipos de função

Todas as expressões em Kind tem um tipo, descrevendo que tipo de coisa ela computa.
Por exemplo: `Bool.true` tem tipo `Bool`, assim como `Notb Bool.true` também tem tipo `Bool`.

Funções como `Notb`, antes de receberem argumentos, também tem um tipo, tal qual `Bool.true` ou `Bool.false`.
Os seus tipos são chamados de Tipo de Função, e são denotados com setas.

`Notb`, por exemplo, seria denotado como `Bool -> Bool`, que pode ser lido como
"uma função que recebe um `Bool` como entrada, e retorna um valor de tipo `Bool`".
Similarmente, o tipo da função `Andb` é `Bool -> Bool -> Bool`, significando
"uma função que recebe dois argumentos do tipo `Bool` e retorna um valor de tipo `Bool`".

### 2.5 Módulos

<!-- TODO preencher quando tivermos sistema de módulos -->
Não temos sistema de módulos ainda :pensive:. Para usar funções de outros arquivos,
precisa-se criar um arquivo dentro do mesmo diretório (Exemplo: a pasta raiz do Wikind).

### 2.6 Números

Os tipos que definimos até agora são exemplos de tipos enumerados: suas definições
enumeram explicitamente um conjunto finito de elemento. Um jeito mais interessante
de definir um tipo é estabelecer uma coleção de *regras indutivas* descrevendo seus
elementos. Por exemplo, nós podemos definir os números naturais da seguinte maneira:

```rs
Nat : Type
Nat.zero              : Nat
Nat.succ (pred : Nat) : Nat
```

Essa definição pode ser lida como:

* `Nat.zero` é um número natural;
* `Nat.succ` é um construtor que recebe um número natural, construindo outro número natural;
  * Ou seja, se `n` é um número natural, então `(Nat.succ n)` também será.

Todo tipo definido indutivamente (Como `Nat`, `Bool` ou `Dia`) é um conjunto de expressões.
A definição de `Nat` diz como expressões do tipo `Nat` podem ser construídas:

* A expressão `Nat.zero` pertence ao conjunto dos `Nat`;
* Se `n` é uma expressão do conjunto dos `Nat`, então `(Nat.succ n)` também é uma expressão do conjunto dos `Nat`; e
* Expressões formadas dessas duas formas são as únicas que pertencem à `Nat`.

As mesmas regras se aplicam para nossas definições de `Dia` e `Bool`.
As anotações que usamos para eles são análogas à do construtor
`Nat.zero`, indicando que não recebem nenhum argumento.

Essas três condições demonstram o poder das declarações indutivas. Elas implicam
que a expressão `Nat.zero`, a expressão `(Nat.succ Nat.zero)`, a expressão
`(Nat.succ (Nat.succ Nat.zero))` e assim por diante, são do conjunto `Nat`,
enquanto outras expressões como `Bool.true`, `(Bool.and Bool.true Bool.false)`,
e `(Nat.succ (Nat.succ Bool.false))` não são.

Nós podemos escrever funções simples usando *pattern matching* em números naturais
da mesma forma que fizemos acima - por exemplo, a função predecessor:

```rust
Pred (n : Nat) : Nat
// Como números naturais são estritamente não-negativos,
// usamos como convenção que qualquer coisa que seria
// menor do que 0 retorna 0
Pred  Nat.zero    = Nat.zero
Pred (Nat.succ k) = k
```

O segundo pattern pode ser lido como: "se `n` tem a forma `(Nat.succ k)`
para algum k, retorne k."

```rust
MinusTwo (n : Nat) : Nat
MinusTwo  Nat.zero               = Nat.zero
MinusTwo (Nat.succ  Nat.zero)    = Nat.zero
MinusTwo (Nat.succ (Nat.succ k)) = k
```

<!-- TODO atualizar isso aqui pro sugar de números naturais, se vier a existir -->
Para evitar ter que escrever uma sequência de `Nat.succ` toda vez que você quiser
um `Nat`, é possível usar a função `U60.to_nat`, que recebe um número escrito
no tipo primitivo `U60` e retorna o `Nat` correspondente.

```rust
TestU60 : Equal (U60.to_nat 6) (Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ Nat.zero))))))
TestU60 = Equal.refl
```

<!-- TODO conferir q eu não estou delirando nesse parágrafo -->
O construtor `Nat.succ` tem tipo `Nat -> Nat`, assim como as funções `MinusTwo`
e `Pred`. Todos eles são coisas que, ao serem aplicadas a um `Nat`, retornam
um `Nat`. A diferença essencial entre o `Nat.succ` e os outros dois, no entanto,
é que funções vem com regras de redução - por exemplo, `Pred (Nat.succ Nat.zero)`
é reduzível para `Nat.zero` - enquanto que o `Nat.succ` não. Apesar de ele ser
uma função aplicável a um argumento, ela não computa nada.

Para a maioria das definições de funções de números, só *pattern matching* não
é suficiente: nós precisaremos também de recursão. Por exemplo, para checar
que um número `n` é par, nós podemos checar recursivamente se `n-2` é par.

```rust
Evenb (n : Nat) : Bool
Evenb  Nat.zero               = Bool.true
Evenb (Nat.succ  Nat.zero)    = Bool.false
Evenb (Nat.succ (Nat.succ k)) = Evenb k
```

Nós podemos definir `Oddb` (função para checar se um número é ímpar) com uma
declaração recursiva semelhante, mas também temos uma definição mais simples
e um pouco mais fácil de trabalhar:

```rust
Oddb (n : Nat) : Bool
Oddb n = Bool.not (Evenb n)
```

```rust
TestOddb1 : Equal (Oddb (U60.to_nat 1)) Bool.true
TestOddb1 = Equal.refl

TestOddb2 : Equal (Oddb (U60.to_nat 4)) Bool.false
TestOddb2 = Equal.refl
```

Naturalmente, nós também podemos definir funções com multiplos argumentos por recursão.

```rust
Plus (n : Nat) (m : Nat) : Nat
Plus  Nat.zero    m = m
Plus (Nat.succ k) m = Nat.succ (Plus k m)
```

Somar 3 e 2 retornará 5 como esperado.
A simplificação que o Kind realiza para chegar a esse valor pode ser vizualizada assim:

```terminal
Plus (Nat.succ (Nat.succ (Nat.succ Nat.zero))) (Nat.succ (Nat.succ Nat.zero))

> Nat.succ (Plus (Nat.succ (Nat.succ Nat.zero)) (Nat.succ (Nat.succ Nat.zero)))
pela segunda regra de Plus

> Nat.succ (Nat.succ (Plus (Nat.succ Nat.zero)) (Nat.succ (Nat.succ Nat.zero)))
pela segunda regra de Plus

> Nat.succ (Nat.succ (Nat.succ (Plus Nat.zero (Nat.succ (Nat.succ Nat.zero)))))
pela segunda regra de Plus

> Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ Nat.zero))))
pela primeira regra de Plus
```

A multiplicação pode ser definida usando a definição de Plus, da seguinte forma:

```rust
Mult (n : Nat) (m : Nat) : Nat
Mult  Nat.zero    m = Nat.zero
Mult (Nat.succ k) m = Plus m (Mult k m)
```

```rust
TestMult1 : Equal (Mult (U60.to_nat 3) (U60.to_nat 3)) (U60.to_nat 9)
TestMult1 = Equal.refl
```

Você também pode usar *pattern matching* em duas expressões ao mesmo tempo:

```rust
Minus (n : Nat) (m : Nat) : Nat
Minus  Nat.zero     m           = Nat.zero
Minus  n            Nat.zero    = n
Minus (Nat.succ k) (Nat.succ j) = Minus k j
```

<!-- TODO conferir se wildcard no lhs já está funcionando -->
<!-- The _ in the first line is a wildcard pattern. Writing _ in a
pattern is the same as writing some variable that doesn’t get used on the
right-hand side. This avoids the need to invent a bogus variable name. -->

O função `Exp` pode ser definida usando `Mult`
(de forma análoga a como se define `Mult` usando `Plus`):

```rust
Exp (base : Nat) (power : Nat) : Nat
Exp base  Nat.zero    = Nat.succ Nat.zero
Exp base (Nat.succ k) = Mult base (Exp base k)
```

#### *2.6.0.1 Exercício (factorial)*

Lembrando da definição matemática básica de fatorial:

$$
fatorial(n) = \begin{cases}
1, & \text{se $n$} = 0\\
n * fatorial(n-1), & \text{caso contrário}
\end{cases}
$$

Traduza a função fatorial para Kind2:

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
Nat.equal  Nat.zero     Nat.zero    = Bool.true
Nat.equal  Nat.zero    (Nat.succ j) = Bool.false
Nat.equal (Nat.succ k)  Nat.zero    = Bool.false
Nat.equal (Nat.succ k) (Nat.succ j) = Nat.equal k j
```

A função `Lte` testa se o primeiro argumento é menor ou igual ao segundo,
retornando um booleano

```rust
Lte (n: Nat) (m: Nat) : Bool
Lte  Nat.zero     m           = Bool.true
Lte (Nat.succ k)  Nat.zero    = Bool.false
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

#### *2.6.0.2 Exercício (blt_nat)*

A função `blt_nat` testa a relação "menor que" em numeros naturais.
Em vez de criar uma nova função recursiva, defina ela usando funções previamente definidas.

```rust
Blt_nat (n : Nat) (m : Nat) : Bool
Blt_nat n m = ?
```

```rust
Test_blt_nat_1 : Equal (Blt_nat (U60.to_nat 2) (U60.to_nat 2)) Bool.true
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
Embora a simplificação seja poderosa o suficiente para provar alguns fatos bastante gerais, existem muitas declarações que não podem ser demonstradas só com a simplificação. Por exemplo, não podemos usá-la para provar que `Nat.zero` é um elemento neutro para adição no lado direito.
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

Como nos teoremas anteriores, alem de assumir a existencia dos numeros n e m. Também precisamos assumir a hipótese Equal n m..

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
