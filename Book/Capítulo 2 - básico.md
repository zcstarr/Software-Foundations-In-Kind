# 2. Básico

## 2.1 Introdução

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

## 2.2 Tipos Enumerados

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

### 2.2.1 Dias da semana

A declaração a seguir diz para o Kind que estamos declarando um novo conjunto de dados - um Tipo.

```rust
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

```rust
ProximoDiaUtil (d: Dia) : Dia
```

Isso declara que temos uma função chamado `ProximoDiaUtil`, que recebe um argumento
chamado `d`, do tipo `Dia`, e retorna um `Dia`.
Continue a definição da função da seguinte forma:

```rust
ProximoDiaUtil Dia.segunda = ?
ProximoDiaUtil Dia.terca = ?
ProximoDiaUtil Dia.quarta = ?
ProximoDiaUtil Dia.quinta = ?
ProximoDiaUtil Dia.sexta = ?
ProximoDiaUtil Dia.sabado = ?
ProximoDiaUtil Dia.domingo = ?
```

O que estamos fazendo aqui é o que chamamos de *pattern matching*. Estamos
declarando como a função deve rodar para cada possibilidade da entrada `d`.
Nem sempre será necessário fazer isso, como será mostrado em exemplos mais a frente.

Por fim, complete as funções escrevendo o que cada uma deve retornar,
e use espaços para estilizar como preferir:

```rust
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

```rust
Main {
  // Dois dias úteis depois do sábado
  ProximoDiaUtil (ProximoDiaUtil Sabado)
}
```

Deve ser retornado pra você algo como:

```terminal
(Dia.terca)
```

Outro jeito de testar seu código, é dizer o que esperamos que o código retorne,
por meio de uma prova:

```rust
// O terceiro dia útil depois de uma segunda é uma quinta
TesteDiaUtil : Equal Dia (ProximoDiaUtil (ProximoDiaUtil (ProximoDiaUtil Dia.segunda))) Dia.quinta
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
```

### 2.3 Booleanos

Semelhantemente, podemos declarar o tipo `Bool`, para booleanos:

```rust
Bool : Type

Bool.true  : Bool
Bool.false : Bool
```

<!-- TODO mudar isso aqui caso tenhamos um gerenciador de pacotes -->
Nós estamos declarando nossos próprios booleanos só para demonstrar como fazer tudo do zero.
O Kind tem a sua implementação padrão de booleanos no pacote padrão (o [Wikind](github.com/Kindelia/Wikind)),
junto de várias outras estruturas e provas. Na verdade, no momento de escrita,
é necessário que você esteja trabalhando dentro da pasta Wikind para fazer
provas e teoremas, pois ainda não temos um gerenciador de pacote e os utilitários
de resolução de provas não são built-in.

Funções que funcionam sobre booleanos são definidas do mesmo jeito que visto anteriormente:

```rust
// Negação lógica
Notb (b: Bool) : Bool
Notb Bool.true  = Bool.false
Notb Bool.false = Bool.true

// E lógico
Andb (b1: Bool) (b2: Bool) : Bool
Andb Bool.true  b2 = b2
Andb Bool.false b2 = Bool.false

// OU lógico
Orb (b1: Bool) (b2: Bool) : Bool
Orb Bool.true  b2 = Bool.true
Orb Bool.false b2 = b2
```

As últimas duas funções demonstram como é a sintaxe do Kind para funções de
múltiplos argumentos, e também mostra que é possível fazer *pattern matching*
apenas em parte das variáveis da função, não necessariamente todas.

Os casos da última função podem ser testados exaustivamente (todas as possibilidades)
como mostrado a seguir, criando a tabela verdade da operação lógica.

```rust
TestOrb1 : Equal Bool (Orb Bool.true Bool.false) Bool.true
TestOrb1 = Equal.refl

TestOrb2 : Equal Bool (Orb Bool.false Bool.false) Bool.false
TestOrb2 = Equal.refl

TestOrb3 : Equal Bool (Orb Bool.false Bool.true) Bool.true
TestOrb3 = Equal.refl

TestOrb4 : Equal Bool (Orb Bool.true Bool.true) Bool.true
TestOrb4 = Equal.refl
```

### *2.3.0.1 Exercício (nandb)*

Substitua o buraco "?", completando a função seguinte; então confira
se ela está correta usando as constatações a seguir
(Análogo a como foi feito para a função `Orb`).
A função retorna `Bool.true` se qualquer uma de suas entradas for `Bool.false`

```rust
Nandb (b1: Bool) (b2: Bool) : Bool
Nandb b1 b2 = ?

Test_nandb1 : Equal Bool (Nandb Bool.true Bool.false) Bool.true
Test_nandb1 = ?

Test_nandb2 : Equal Bool (Nandb Bool.false Bool.false) Bool.true
Test_nandb2 = ?

Test_nandb3 : Equal Bool (Nandb Bool.false Bool.true) Bool.true
Test_nandb3 = ?

Test_nandb4 : Equal Bool (Nandb Bool.true Bool.true) Bool.false
Test_nandb4 = ?
```

### *2.3.0.2 Exercício (and3)*

Faça o mesmo para a função `Andb3` abaixo. Essa função deve retornar `Bool.true`
se todas as entradas forem `Bool.true`, e `Bool.false` caso contrário

```rust
Andb3 (b1: Bool) (b2: Bool) (b3: Bool) : Bool
Andb3 b1 b2 b3 = ?

Test_andb3_1 Equal Bool (Andb3 Bool.true Bool.true Bool.true) Bool.true
Test_andb3_1 = ?

Test_andb3_2 Equal Bool (Andb3 Bool.false Bool.true Bool.true) Bool.false
Test_andb3_2 = ?

Test_andb3_3 Equal Bool (Andb3 Bool.true Bool.false Bool.true) Bool.false
Test_andb3_3 = ?

Test_andb3_4 Equal Bool (Andb3 Bool.true Bool.true Bool.false) Bool.false
Test_andb3_4 = ?
```

## 2.4 Tipos de função

Todas as expressões em Kind tem um tipo, descrevendo que tipo de coisa ela computa.
Por exemplo: `Bool.true` tem tipo `Bool`, assim como `Notb Bool.true` também tem tipo `Bool`.

Funções como `Notb`, antes de receberem argumentos, também tem um tipo, tal qual `Bool.true` ou `Bool.false`.
Os seus tipos são chamados de Tipo de Função, e são denotados com setas.

`Notb`, por exemplo, seria denotado como `Bool -> Bool`, que pode ser lido como
"uma função que recebe um `Bool` como entrada, e retorna um valor de tipo `Bool`".
Similarmente, o tipo da função `Andb` é `Bool -> Bool -> Bool`, significando
"uma função que recebe dois argumentos do tipo `Bool` e retorna um valor de tipo `Bool`".

## 2.5 Módulos

<!-- TODO preencher quando tivermos sistema de módulos -->
Não temos sistema de módulos ainda :pensive:. Para usar funções de outros arquivos,
precisa-se criar um arquivo dentro do mesmo diretório (Exemplo: a pasta raiz do Wikind).

## 2.6 Números

Os tipos que definimos até agora são exemplos de tipos enumerados: suas definições
enumeram explicitamente um conjunto finito de elemento. Um jeito mais interessante
de definir um tipo é estabelecer uma coleção de *regras indutivas* descrevendo seus
elementos. Por exemplo, nós podemos definir os números naturais da seguinte maneira:

```rust
Nat : Type
Nat.zero              : Nat
Nat.succ (pred: Nat) : Nat
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
enquanto outras expressões como `Bool.true`, `(Andb Bool.true Bool.false)`,
e `(Nat.succ (Nat.succ Bool.false))` não são.

Nós podemos escrever funções simples usando *pattern matching* em números naturais
da mesma forma que fizemos acima - por exemplo, a função predecessor:

```rust
Pred (n: Nat) : Nat
// Como números naturais são estritamente não-negativos,
// usamos como convenção que qualquer coisa que seria
// menor do que 0 retorna 0
Pred  Nat.zero    = Nat.zero
Pred (Nat.succ k) = k
```

O segundo pattern pode ser lido como: "se `n` tem a forma `(Nat.succ k)`
para algum k, retorne k."

```rust
MinusTwo (n: Nat) : Nat
MinusTwo  Nat.zero               = Nat.zero
MinusTwo (Nat.succ  Nat.zero)    = Nat.zero
MinusTwo (Nat.succ (Nat.succ k)) = k
```

<!-- TODO atualizar isso aqui pro sugar de números naturais, se vier a existir -->
Para evitar ter que escrever uma sequência de `Nat.succ` toda vez que você quiser
um `Nat`, é possível usar sufixo `n` ao final de número qualquer, exemplo o`5n`, que recebe um número escrito no tipo primitivo `U60` mais o sufixo `n` e retorna o `Nat` correspondente.
{...}
<!-- TODO -->

```rust
Test : Equal Nat 6n (Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ (Nat.succ Nat.zero))))))
Test = Equal.refl
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
Evenb (n: Nat) : Bool
Evenb  Nat.zero               = Bool.true
Evenb (Nat.succ  Nat.zero)    = Bool.false
Evenb (Nat.succ (Nat.succ k)) = Evenb k
```

Nós podemos definir `Oddb` (função para checar se um número é ímpar) com uma
declaração recursiva semelhante, mas também temos uma definição mais simples
e um pouco mais fácil de trabalhar:

```rust
Oddb (n: Nat) : Bool
Oddb n = Notb (Evenb n)
```

```rust
TestOddb1 : Equal Bool (Oddb 1n) Bool.true
TestOddb1 = Equal.refl

TestOddb2 : Equal Bool (Oddb 4n) Bool.false
TestOddb2 = Equal.refl
```

Naturalmente, nós também podemos definir funções com multiplos argumentos por recursão.

```rust
Plus (n: Nat) (m: Nat) : Nat
Plus  Nat.zero    m = m
Plus (Nat.succ k) m = Nat.succ (Plus k m)
```

Somar 3n e 2n retornará 5n como esperado.
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
Mult (n: Nat) (m: Nat) : Nat
Mult  Nat.zero    m = Nat.zero
Mult (Nat.succ k) m = Plus m (Mult k m)
```

```rust
TestMult1 : Equal Nat (Mult 3n 3n) 9n
TestMult1 = Equal.refl
```

Você também pode usar *pattern matching* em duas expressões ao mesmo tempo:

```rust
Minus (n: Nat) (m: Nat) : Nat
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
Exp (base: Nat) (power: Nat) : Nat
Exp base  Nat.zero    = Nat.succ Nat.zero
Exp base (Nat.succ k) = Mult base (Exp base k)
```

### *2.6.0.1 Exercício (factorial)*

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
TestFactorial1 : Equal Nat (Factorial 3n ) 6n
TestFactorial1 = ?

TestFactorial2 : Equal Nat (Factorial 5n) 120n
TestFactorial2 = ?
```

A função `Eql` testa a igualdade entre Naturais, retornando um booleano

```rust
Eql (n: Nat) (m: Nat) : Bool
Eql  Nat.zero     Nat.zero    = Bool.true
Eql  Nat.zero    (Nat.succ j) = Bool.false
Eql (Nat.succ k)  Nat.zero    = Bool.false
Eql (Nat.succ k) (Nat.succ j) = Eql k j
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
TestLte1 : Equal Bool (Lte 2n 2n) Bool.true
TestLte1 = Equal.refl

TestLte2 : Equal Bool (Lte 2n 4n) Bool.true
TestLte2 = Equal.refl

TestLte3 : Equal Bool (Lte 4n 2n) Bool.false
TestLte3 = Equal.refl
```

### *2.6.0.2 Exercício (blt_nat)*

A função `blt_nat` testa a relação "menor que" em numeros naturais.
Em vez de criar uma nova função recursiva, defina ela usando funções previamente definidas.

```rust
Blt_nat (n: Nat) (m: Nat) : Bool
Blt_nat n m = ?
```

```rust
Test_blt_nat_1 : Equal Bool (Blt_nat 2n 2n) Bool.false
Test_blt_nat_1 = ?

Test_blt_nat_2 : Equal Bool (Blt_nat 2n 4n) Bool.true
Test_blt_nat_2 = ?

Test_blt_nat_3 : Equal Bool (Blt_nat 4n 2n) Bool.false
Test_blt_nat_3 = ?
```

## 2.7 Prova por Simplificação

Agora que definimos alguns tipos de dados e funções, vamos começar a provar propriedades
de seus comportamentos. Na verdade, já estamos fazendo isso: cada função das seções
anteriores que começa com `Test`, faz uma afirmação precisa sobre o comportamento de
alguma função para algumas entradas especificas. As provas dessas afirmações foram
sempre a mesma: use `Equal.refl` para checar que ambos os lados são de fato idênticos.

O mesmo tipo de "prova por simplificação" pode ser usada para provar propriedades mais interessantes.
Por exemplo, o fato que o `Nat.zero` é um "elemento neutro" no lado esquerdo da
adição pode ser provado apenas observando que `Plus Nat.zero n` reduz para `n`,
independente do que é `n`, fato que pode ser lido diretamente na definição do `Plus`.

```rust
Plus_Z_n (n: Nat) : Equal Nat (Plus Nat.zero n) n
Plus_Z_n n = Equal.refl
```

Outros teoremas parecidos podem ser provados de forma parecida.

```rust
Plus_1_l (n: Nat) : Equal Nat (Plus (Nat.succ Nat.zero) n) (Nat.succ n)
Plus_1_l n = Equal.refl

Mult_0_l (n: Nat) : Equal Nat (Mult Nat.zero n) Nat.zero
Mult_0_l n = Equal.refl 
```

O `_l` indica que a prova envolve o valor no lado esquerdo. Por exemplo:
A prova da soma por 1 no lado esquerdo (`Plus_1_l`)
ou a prova da multilicação por zero no lado esquerdo (`Mult_0_l`)

Embora a simplificação seja poderosa o suficiente para provar alguns fatos gerais,
existem várias declarações que não podem ser demonstradas só com simplificação.
Por exemplo, não podemos usá-la para provar que `Nat.zero` é um elemento neutro para adição no lado direito.

```rust
Plus_n_Z (n: Nat) : Equal Nat n (Plus n Nat.zero)
Plus_n_Z n = Equal.refl
```
```diff
- ERROR Type mismatch  

   • Got      : Equal Nat n n) 
   • Expected : Equal Nat n (Plus n 0n)) 

   • Context: 
   •   n : Nat 

   Plus_n_Z n = Equal.refl
                ┬─────────
                └Here!
```

(Você consegue explicar por que isso acontece?)

O próximo capítulo vai introduzir o conceito de indução,
uma técnica poderosa que pode ser usada para demonstrar esse teorema.
Por agora, no entanto, vamos ver mais alguns tipos simples de prova.

## 2.8 Prova por aplicação

A nossa primeira ferramenta para resolver provas que não são reduzíveis de cara
será a aplicação dos dois lados. Para isso, usaremos a função `Equal.apply`, que
recebe uma igualdade (um `Equal`) e uma função, e aplica essa função dos dois lados
da igualdade, gerando uma nova igualdade.

Por exemplo:

```rust
Example_apply (n: Nat) (m: Nat) (e: Equal Nat m n) : Equal Nat (Nat.succ m) (Nat.succ n)
Example_apply n m e = ?
```

O que exatamente temos aqui? Temos uma prova que recebe como argumento uma outra
prova/igualdade. Isso quer dizer que vamos realizar a nossa prova supondo que a
prova dada como argumento também é verdadeira. Então, lendo a declaração da prova,
temos que: "Dados dois naturais, `m` e `n`, e uma prova de que eles são iguais,
provar que `Nat.succ m` e `Nat.succ n` também são iguais".

Nós aprendemos, nas nossas aulas de matématica, que aplicar uma função dos dois
lados de uma igualdade mantém a igualdade (`x/2 = 3 -> 2*x/2 = 2*3`), e podemos
ver que para provar o que a gente quer, precisamos aplicar a função `Nat.succ`
nos dois lados de `e`, usando `Equal.apply`

![Example_apply](./Imgs/example-apply.png)

Como o `Equal.apply` funciona: Ele recebe como primeiro argumento a função a ser
aplicada dos dois lados, e como segundo argumento a igualdade aonde aplicar a função.
Se você não entendeu muito bem a passagem da função de argumento (`x => Nat.succ x`),
ela é o que chamamos de função lambda, e é também conhecida como função anônima.
Funções lambda são identificadas pela sua seta `=>`, sendo que do lado esquerdo
da seta fica o nome do argumento da função - use o nome que quiser - e do lado direito
fica o corpo da função: o que ela retorna. A nossa função lambda atual é uma função
que recebe um `x` qualquer e retorna `Nat.succ x`.

Podemos ver o resultado disso dando `check` no arquivo:

![Example_apply](./Imgs/example-apply-ind.png)

Como `e_apply` é uma igualdade do tipo `Equal Nat (Nat.succ m) (Nat.suuc n)`,
a prova que procuramos, é só retornar ele e concluiremos a nossa prova.

```rust
Example_apply (n: Nat) (m: Nat) (e: Equal Nat m n) : Equal Nat (Nat.succ m) (Nat.succ n)
Example_apply n m e =
  let e_apply = Equal.apply (x => Nat.succ x) e
  e_apply
```

```terminal
  All terms checked.
```

## 2.9 Prova por análise de casos

A próxima ferramenta de provas formais será análise de casos, que significa usar *pattern matching* na prova.
Por exemplo, vamos provar que o E lógico de qualquer coisa e Falso sempre é falso:

```rust
Example_case_analysis (b: Bool) : Equal Bool (Andb b1 Bool.false) Bool.false
Example_case_analysis b = ?
```

Apesar de parecer uma prova que deveria ser resolvida simplesmente com `Equal.refl`,
não é o caso. Isso é por conta de a função `Andb` fazer *pattern matching* no primeiro
argumento, e nós não temos o valor dele na prova, então ele fica "agarrado".

Para darmos um valor pra ele, e mostrar que a prova está correta para ambos os valores de `Bool`,
nós fazemos *pattern matching* na prova, criando assim duas provas diferentes:
uma pra quando `b` for `Bool.true` e uma pra quando for `Bool.false`.

```rust
Example_case_analysis (b: Bool) : Equal Bool (Andb b Bool.false) Bool.false
Example_case_analysis Bool.true  = ?
Example_case_analysis Bool.false = ?
```

E ambas essas provas são resolvivéis diretamente com `Equal.refl`, pois o *type checker*
consegue reduzir ambos para `Equal Bool.false Bool.false` direto.

```rust
Example_case_analysis (b: Bool) : Equal Bool (Andb b Bool.false) Bool.false
Example_case_analysis Bool.true  = Equal.refl
Example_case_analysis Bool.false = Equal.refl
```

<!-- TODO Reescrita no Kind2 é uma caixa de minhocas por si só,
talvez colocar mais pro final do capítulo,
ao talvez até colocar depois desse capítulo -->
## 2.10 Prova por Reescrita

Esse teorema é um pouco mais interessante que anteriores:

```rust
Plus_id_example (n: Nat) (m: Nat) (e: Equal Nat n m) : Equal Nat (Plus n n) (Plus m m)
```

Assim como mostrado anteriormente, essa é uma prova que dentro de seus argumentos temos
uma outra prova, ou hipótese: no caso, temos `Equal n m` - ou seja, `n` e `m` são iguais.

Como n e m são números arbitrários, não podemos só usar simplificação para demonstrar o teorema.
Em vez disso, nós observando que, já que assumimos `Equal n m`, poderiamos substituir `n` por `m`
no objetivo e os dois lados ficarão iguais. A função que usamos para fazer essa substituição é a `Equal.rewrite`.

<!-- TODO revisar esse parágrafo -->
Como não podemos reescrever diretamente no objetivo, nós usamos uma outra igualdade e fazemos ela ser igual ao objetivo.
No nosso caso, usaremos um `Equal.apply` em `e` para conseguir essa igualdade.

```rust
Plus_id_example (n: Nat) (m: Nat) (e: Equal Nat n m) : Equal Nat (Plus n n) (Plus m m)

Plus_id_example n m e =
  let app = Equal.apply (k => Plus k n) e
  ? 
```
```diff
+ INFO  Inspection.

   • Expected: Equal Nat (Plus n n) (Plus m m)) 

   • Context: 
   •   n   : Nat 
   •   m   : Nat 
   •   e   : Equal Nat n m) 
   •   app : Equal Nat (Plus n n) (Plus m n)) 
   •   app = Equal.apply Nat Nat n m (k => (Plus k n)) e) 
    
   let app = Equal.apply (k => Plus k n) e
      ?
      ┬
      └Here!
```

Esse `app` será do tipo `Equal (Plus n n) (Plus m n)`, como mostrado no comentário.
Com isso feito, precisamos trocar o `n` por `m` no lado direito da igualdade, e pra isso usamos o rewrite:

```rust
Plus_id_example (n: Nat) (m: Nat) (e: Equal Nat n m) : Equal Nat (Plus n n) (Plus m m)
Plus_id_example n m e =
  let app = Equal.apply (k => Plus k n) e
  let rrt = Equal.rewrite e (x => Equal (Plus n n) (Plus m x)) app
  rrt
```
```diff  
+ INFO  Inspection.

   • Expected: Equal Nat (Plus n n) (Plus m m)) 

   • Context: 
   •   n   : Nat 
   •   m   : Nat 
   •   e   : Equal Nat n m) 
   •   app : Equal Nat (Plus n n) (Plus m n)) 
   •   app = Equal.apply Nat Nat n m (k => (Plus k n)) e) 
   •   rrt : Equal Nat (Plus n n) (Plus m m)) 
   •   rrt = Equal.rewrite Nat n m e (x => Equal Nat (Plus n n) (Plus m x))) app) 
```

O retorno da operação `Equal.rewrite` já será a prova que precisamos,
então só retornamos direto o resultado da função.

#### *Exercício 2.10.0.1 (plus_id_exercise)*

Prove que:

```rust
Plus_id_exercise (n: Nat) (m: Nat) (o: Nat) (e1: Equal Nat n m) (e2: Equal Nat m o) : Equal Nat (Plus n m) (Plus m o)
Plus_id_exercise n m o e1 e2 = ?
```

### 2.11 `Equal.chain` e `Equal.mirror`

Nessa parte não falaremos de nenhuma ferramenta inerentemente nova,
mas sim de alguns utilitários de provas para facilitar o uso das ferramentas anteriores.

Imagine o exemplo:

```rust
Example_mirror (a: Nat) (b: Nat) (e: Equal Nat a b) : Equal Nat b a
```

Parece um exemplo trivial. Se `a` é igual a `b`, `b` é igual a `a`, correto?
Apesar de correto, o *type checker* do Kind não reconhece essa igualdade, pois para ele, a ordem é importante.
Para esse tipo de situação, temos a função `Equal.mirror`, que simplesmente troca os lados de uma igualdade.

```rust
Example_mirror (a: Nat) (b: Nat) (e: Equal Nat a b) : Equal Nat b a
Example_mirror a b e = 
   let mir = Equal.mirror e
   mir
```
```diff
+ INFO  Inspection.

   • Expected: Equal Nat b a) 

   • Context: 
   •   a   : Nat 
   •   b   : Nat 
   •   e   : Equal Nat a b) 
   •   mir : Equal Nat b a) 
   •   mir = Equal.mirror Nat a b e) 
```

Apesar de não parecer muito útil no momento, essa operação é muito útil para nosso segundo utilitário: `Equal.chain`.
`Equal.chain` é um caso específico do `Equal.rewrite`, no qual você reescreve um lado inteiro de uma igualdade usando outra.

```rust
Example_chain (a: Nat) (b: Nat) (c: Nat) (e1: Equal Nat b (Plus a a)) (e2 : Equal Nat c (Plus a a)) : Equal Nat b c
```

Como nós já conhecemos o `Equal.rewrite`, poderiamos usar ele para resolver esse teorema, mas ao invês disso vamos usar o `Equal.chain`.
`Equal.chain` funciona "encadeando" duas igualdades que tenham a mesma expressão no lado direito da primeira igualdade e no lado esquerdo da segunda,
"grudando" essas igualdades pela expressão em comum, gerando uma nova igualdade com as outras duas expressões (`Equal.chain (a=b) (b=c) = (a=c)`).
Por exemplo, no nosso exemplo, o lado direito das duas igualdades é igual. Se usarmos `Equal.mirror` em uma delas, podemos dar `Equal.chain` nelas:

```rust
Example_chain (a: Nat) (b: Nat) (c: Nat) (e1: Equal Nat b (Plus a a)) (e2 : Equal Nat c (Plus a a)) : Equal Nat b c
Example_chain a b c e1 e2 =
  let eql = Equal.mirror e2
  let chn = Equal.chain e1 e3

```
```diff
+ INFO  Inspection.

   • Expected: Equal Nat b c) 

   • Context: 
   •   a   : Nat 
   •   b   : Nat 
   •   c   : Nat 
   •   e1  : Equal Nat b (Plus a a)) 
   •   e2  : Equal Nat c (Plus a a)) 
   •   e3  : Equal Nat (Plus a a) c) 
   •   e3  = Equal.mirror Nat c (Plus a a) e2) 
   •   chn : Equal Nat b c) 
   •   chn = Equal.chain Nat b (Plus a a) c e1 e3) 
```
## 2.12 Mais exercícios

### *2.12.0.1 (boolean_functions)*

Use os conhecimentos ensinados até aqui para resolver o teorema:

```rust
Identity_fn_applied_twice (f: Bool -> Bool) (e: (x: Bool) -> Equal Bool (f x) x)) (b : Bool) : Equal Bool (f (f b)) b
Identity_fn_applied_twice f e b = ?
```

Depois, resolva o teorema `negation_fn_applied_twice`, que é o mesmo que o anterior,
mas mudando a hipótese para `Equal (f x) (Not x)`

### *2.12.0.2 (andb_eq_orb)*

Prove o seguinte teorema (Lembre-se que voce pode provar teoremas intermediários separadamente)

```rust
Andb_eq_orb (b: Bool) (c: Bool) (e: Equal Bool (Andb b c) (Orb b c)) : Equal Bool b c
Andb_eq_orb b c prf = ?
```

### *2.12.0.3 (binary)*

Considere uma representação diferente de números naturais, usando um sistema binário ao
invês de unário. Ou seja, ao invês de termos apenas zero ou um sucessor de um número,
nós podemos ter:

* zero;
* o dobro de um número;
* o dobro de um número mais 1.

1. Primeiro, escreva uma definição indutiva desse tipo, chamando-o de `Bin`.
(Lembre-se que, no fundo, a própria definição de `Nat` como `zero` ou `succ n`
não tem sentido intríseco. Ela só diz que um elemento de `Nat` pode ser um `zero`
ou um `succ n` se `n` também for `Nat`. A interpretação disso como um sistema de valores
0, 1, 2, etc, vem de como nós trabalhamos com esse tipo `Nat`. A sua definição de `Bin`
idealmente também será tão simples quanto. Serão as funções que você fizer sobre `Bin`
que darão sentido matemático a ele).
2. Então, escreva uma função `Incr` para incrementar um `Bin`, e uma função
`Bin_to_nat` para converter de `Bin` para `Nat`.
3. Escreva cinco provas que testam suas funções de incremento e de conversão.
Note que incrementar um binário e então convertê-lo deve ser chegar no mesmo
resultado que convertê-lo primeiro e então incrementar o Nat
