# Capítulo 4
## Listas: trabalhando com dados estruturados

### 1. Pares de Números

Em uma definição de tipo indutivo, cada construtor pode receber qualquer número
de argumentos -- nenhum como o `Bool`, `Empty` ou um como o `*Nat*` -- e
temos o `Pair` que recebe dois argumentos (podendo ser até mesmo outros dois
pares) e retorna um tipo:
```rust
   Pair (a: Type) (b: Type) : Type
```
Os dois argumentos recebidos são transformados no primeiro componente, o `fst`, e
o segundo, o `snd`. 
```rust
   type Pair <a: Type> <b: Type> {
      new (fst: a) (snd: b)
   }  
   ```
A forma de construir um par é a seguinte:
```rust 
Pair.new <a: Type> <b: Type> (fst: a) (snd: b) : (Pair a b)
```
Aplicando dois `*Nat*` ao nosso tipo `*Pair*`, onde ``**a**``  e `**b**` são dois números naturais, podemos contruir da seguinte forma:
```rust
Pair.new a b  : (Pair a b)
```
Aqui estão duas funções simples para extrair o primeiro e o segundo componentes de um par. As definições também ilustram como fazer a correspondência de padrões em dois argumentos construtores.

Exemplo 1: (Pair.fst Nat (List Nat) (Pair 2 [1,2,3])) ->  2
```rust
Pair.fst <a> <b> (pair: Pair a b) : a
Pair.fst a b (Pair.new p.a p.b fst snd) = fst
```
Exemplo 2: (Pair.snd Nat (List Nat) (Pair 2 [1,2,3])) -> [1,2,3]
```rust
Pair.snd <a> <b> (pair: Pair a b) : b
Pair.snd a b (Pair.new p.a p.b fst snd) = snd
```

### Algumas provas

Vamos tentar provar alguns fatos simples sobre pares. Se declararmos as coisas de uma maneira particular (e ligeiramente peculiar), podemos completar provas com apenas reflexividade:
```rust
Surjective_pairing (p: Pair Nat Nat) : (Equal p (Pair.new (Pair.fst p) (Pair.snd p)))
Surjective_pairing (Pair.new Nat Nat fst snd) = Equal.refl
```
Mas *Equal.***refl** não é suficiente caso a declaração seja:
```rust
Surjective_pairing (Pair.new Nat Nat fst snd) = Equal.refl
```
Uma vez que o Kind espera 
```rust
(Equal p (Pair.new (Pair.fst p) (Pair.snd p)))
```
E recebeu 
```rust
(Equal p p)
```
Nós devemos "expor" a estrutura interna do *par* para que o *Type Checker*
possa verificar se `p` é realmente igual a `Pair.new (Pair.fst p) (Pair.snd p)` 


### Execícios:

- 1.1
```rust
Snd_fst_is_swap (p: Pair Nat Nat )            : (Equal (Pair Nat Nat) (Pair.swap Nat Nat (Pair.swap Nat Nat p)) p)
Snd_fst_is_swap (Pair.new Nat Nat fst snd)    = ? 
```

- 1.2
```rust
Fst_swap_is_inverse (p: Pair Nat Nat) (a: Nat) (b: Nat) : (Equal (Pair Nat Nat) (Pair.swap Nat Nat (Pair.new a b) ) (Pair.new b a))
Fst_swap_is_inverse p a b = ?
```

## Listas de números

Generalizando a definição de pares, podemos descrever o tipo de listas de números assim: “Uma lista ou é a lista vazia ou então um conjuto de um elemento e outra Lista", esse tipo não é composto de um `head` e uma `tail`.
```rust
List (t: Type) : Type

type List <t: Type> {
   nil
   cons (head: t) (tail: List t)
} 
```

Outra forma de construir os tipos é com a seguinte notação, usando o caso do
tilo `List`
```rust 
List <a: Type> : Type
List.nil <a> : (List a)
List.cons <a> (head: a) (tail: List a) : (List a)
```

Podemos perceber que em ambas as notações, há um `head` e um `tail`, sendo que o
*head* recebe um elemento de um tipo e a *tail* recebe uma lista desse tipo. 

Por exemplo, uma lista de três números naturais 1, 2 e 3 seria escrita da
seguinte forma:

`[1, 2, 3]`

O Kind, entretanto, lê de outra forma:

`[1, [2, 3]]`

onde o `1` é a head e o `[2, 3]` é a tail. Da mesma forma, ao olhar para uma
lista de 4 elementos `[1, 2, 3, 4]`, agora veremos da seguinte forma:
 
`[1, [2, [3, 4]]]`

A lista possui o `head` `1` e a `tail` `[2, [3, 4]]`, que, por sua vez, possui a
`head` `2` e a `tail` `[3, 4]` que também possui sua `head` `3` e sua tail `4`.

Pode parecer assustador, mas é um monstro amigável:

![img](./listmonster.png)

[fonte da imagem: http://learnyouahaskell.com/starting-out]


### 2.1 List.repeat
A função repeat recebe um número *n* e um valor, retornando uma lista de tamanho *n* onde todos os elementos é o valor declarado.
```rust
// Exemplo: (List.repeat 3 Bool.true) -> [True, True, True]
List.repeat <a> (times: Nat) (val: a) : List a
List.repeat a Nat.zero        val = List.nil
List.repeat a (Nat.succ pred) val = List.cons val (List.repeat pred val)
```

### 2.2 List.length
A função *length* calcula o tamanho da lista 
```rust
// Exemplo: (List.length [1,2,3]) -> 3
List.length <a> (xs: List a) : Nat
List.length a (List.nil t)            = Nat.zero
List.length a (List.cons t head tail) = (Nat.succ (List.length a tail))
```

### 2.3 List.append
A função append concatena (anexa) duas listas.
```rust
List.append <a: Type> (xs: List a) (x: a) : List a
List.append a (List.nil xs.a)            x = List.pure x
List.append a (List.cons xs.a xs.h xs.t) x = List.cons xs.h (List.append xs.t x)
```

### 2.4 List.head e List.tail 
 A função head retorna o primeiro elemento (a “cabeça”) da
list, enquanto tail retorna tudo menos o primeiro elemento (a “cauda”). Claro, o
lista vazia não tem primeiro elemento, então devemos tratar esse caso com um tipo *Maybe*,
recebendo um *Maybe*.**none** caso a lista seja vazia ou um *Maybe*.**some**
caso tenha um valor.
```rust
// Exemplo: (List.head Nat [1,2,3]) -> (Maybe.some 1)
List.head <a> (xs: List a) : Maybe a
List.head a (List.nil t)            = Maybe.none
List.head a (List.cons t head tail) = Maybe.some head
```

```rust
// Exemplo: (List.tail Nat [1,2,3]) -> [2,3]
List.tail <a> (xs: List a) : List a
List.tail a (List.nil  t)           = List.nil
List.tail a (List.cons t head tail) = tail
```
```rust
Test_head1 (xs: List Nat) : (Equal (List.head (List.to_nat [1, 2, 3])) (Maybe.some(U60.to_nat 1)))
Test_head1                = Equal.refl
```
```rust
Test_head2 (xs: List Nat) : (Equal (List.head (List.nil)) (Maybe.none))
Test_head2                = Equal.refl
```
```rust
Test_head3 (xs: List Nat) : (Equal (List.tail (List.to_nat [1, 2, 3])) (List.to_nat [2, 3]))
Test_head3                = Equal.refl
```

### 2.5 Exercícios
- 2.5.1 Complete as definições de nonzeros, oddmembers e countoddmembers abaixo. Dê uma olhada nos testes para entender o que essas funções devem fazer.

`Dica: Use a função List.to_nat para usar números mais fáceis de ler

List.to_nat (xs : List U60) : List Nat
List.to_nat xs = List.map xs (x => U60.to_nat x)`

```rust
Nonzeros (l : List Nat) : List Nat

Test_nonzeros : Equal (Nonzeros (List.to_nat [0,1,0,2,3,0,0])) (List.to_nat [1,2,3])
Test_nonzeros = ?

Oddmembers (xs: List Nat) : List Nat

Test_oddmembers : (Equal ( Oddmembers (List.to_nat [0, 1, 0, 2, 3, 0, 0])) (List.to_nat [1, 3]))
Test_oddmembers = ?

CountOddMembers (xs: List Nat)  : Nat

Test_countoddmembers1 : (Equal Nat (CountOddMembers (List.to_nat [1, 0, 3, 1, 4, 5])) (U60.to_nat 4))
Test_countoddmembers1 = ?
```
- 2.5.2 Complete a definição de alternate, que “compacta” duas listas em uma, alternando entre os elementos tomados da primeira lista e elementos da segunda. Veja os testes abaixo para mais exemplos específicos.
```rust
Alternate (xs: List Nat) (ys: List Nat) : List Nat

Test_alternate1 : (Equal (List Nat) (Alternate (List.to_nat [1, 2, 3] ) (List.to_nat [4, 5, 6])) (List.to_nat [1, 4, 2, 5, 3, 6]))
Test_alternate1 = ?

Test_alternate2 : (Equal (List Nat) (Alternate (List.to_nat [1]) (List.to_nat [4, 5, 6]))  (List.to_nat [1, 4, 5, 6]))
Test_alternate2 = ?

Test_alternate3 : (Equal (List Nat) (Alternate (List.to_nat [1, 2, 3]) (List.to_nat [4])) (List.to_nat [1, 4, 2, 3]))
Test_alternate3 = ? 

Test_alternate4 : (Equal (List Nat) (Alternate (List.nil) (List.to_nat [20, 30])) (List.to_nat [20, 30]))
Test_alternate4 = ?
```

- 2.6.1 Complete as seguintes definições para as funções count, sum, add, e member das listas de naturais
```rust
Count (v: Nat) (xs: List Nat) : Nat

Test_count1 : (Equal Nat ( Count (U60.to_nat 1) (List.to_nat [1, 2, 3, 1, 4, 1])) (U60.to_nat 3))
Test_count1 = ?

Test_count2  : (Equal Nat (Count (U60.to_nat 6) (List.to_nat [1, 2, 3, 1, 4, 1])) (U60.to_nat 0))
Test_count2  = ?

Sum (xs: List Nat) (ys: List Nat) : List Nat

Test_sum1 : (Equal Nat (Count (U60.to_nat 1) (Sum (List.to_nat [1, 2, 3]) (List.to_nat [1, 4, 1]))) (U60.to_nat 3))
Test_sum1 = ?

Add (n: Nat) (xs: List Nat) : List Nat

Test_add : (Equal Nat (Count (U60.to_nat 1) (Add (U60.to_nat 1) (List.to_nat [1, 4, 1])))(U60.to_nat 3))
Test_add = ?

Test_add2 : (Equal Nat (Count (U60.to_nat 5) (Add (U60.to_nat 1) (List.to_nat [1, 4, 1])))(U60.to_nat 0))
Test_add2 = ?

Member (v: Nat) (xs: List Nat) : Bool

Test_member1 : (Equal Bool (Member (U60.to_nat 1) (List.to_nat [1, 4, 1])) Bool.true)
Test_member1 = ?

Test_member2 : (Equal Bool (Member (U60.to_nat 2) (List.to_nat [1, 4, 1])) Bool.false)
Test_member2 = ?
```

- 2.6.2 Aqui estão mais algumas funções de `List Nat` para você praticar. Quando remove_one é aplicado a uma lista sem o número a ser removido, ele deve retornar a mesma lista inalterada
```rust
Remove_one (v: Nat) (xs: List Nat) : List Nat

Test_remove_one1 : (Equal Nat (Count (U60.to_nat 5)(Remove_one (U60.to_nat 5) (List.to_nat [2, 1, 5, 4, 1])))(U60.to_nat 0))
Test_remove_one1 = ?

Test_remove_one2 : (Equal Nat (Count (U60.to_nat 5)(Remove_one (U60.to_nat 5) (List.to_nat [2, 1, 4, 1])))(U60.to_nat 0))
Test_remove_one2 = ?

Test_remove_one3 : (Equal Nat (Count (U60.to_nat 4)(Remove_one (U60.to_nat 5)(List.to_nat [2, 1, 5, 4, 1, 4])))(U60.to_nat 2))
Test_remove_one3 = ?

Test_remove_one4 : (Equal Nat (Count (U60.to_nat 5) (Remove_one (U60.to_nat 5) (List.to_nat [2,1,5,4,5,1,4])))(U60.to_nat 1))
Test_remove_one4 = ?

Remove_all (v: Nat) (xs: List Nat) : List Nat

Test_remove_all1  : (Equal Nat (Count (U60.to_nat 5) (Remove_all (U60.to_nat 5) (List.to_nat [2,1,5,4,1])))(U60.to_nat 0))
Test_remove_all1  = ?

Test_remove_all2  : (Equal Nat (Count (U60.to_nat 5) (Remove_all (U60.to_nat 5)(List.to_nat [2,1,4,1])))(U60.to_nat 0))
Test_remove_all2  = ?

Test_remove_all3  : (Equal Nat (Count (U60.to_nat 4)(Remove_all (U60.to_nat 5)(List.to_nat [2,1,5,4,1,4])))(U60.to_nat 2))
Test_remove_all3  = ?

Test_remove_all4  : (Equal Nat (Count (U60.to_nat 5)(Remove_all (U60.to_nat 5)(List.to_nat [2,1,5,4,5,1,4,5,1,4])))(U60.to_nat 0))
Test_remove_all4  = ?

Subset (xs: List Nat) (ys: List Nat)  : Bool

Test_subset1 : (Equal Bool (Subset (List.to_nat [1, 2])(List.to_nat [2,1,4,1])) Bool.true)
Test_subset1 = ?

Test_subset2 : (Equal Bool (Subset (List.to_nat [1, 2, 2])(List.to_nat [2, 1, 4, 1])) Bool.false)
Test_subset2 = ?
```

### 3 Raciocínio sobre listas

Assim como os números, fatos simples sobre funções de processamento de lista podem às vezes ser provado inteiramente por simplificação. Por exemplo, a simplificação realizada por
`Equal.refl` é suficiente para este teorema...

`Nil_app (xs: List Nat) : (Equal (List.concat List.nil xs) xs)
Nil_app xs = Equal.refl`

... isso porque o Kind "vê" o  `List.nil` e já reduz automaticamente a igualdade da mesma forma que ocorre com os números naturais, com o `Nat.zero`

Além disso, como acontece com os números, às vezes é útil realizar uma análise de caso no possíveis formas (vazias ou não vazias) de uma lista desconhecida
```rust
Tl_length_pred (xs: List Nat)               : (Equal Nat (Nat.pred (List.length xs)) (List.length (List.tail xs)))
Tl_length_pred List.nil                     = Equal.refl
Tl_length_pred (List.cons xs.head xs.tail)  = Equal.refl
```

Caso o usuário não abra os casos e use direto o `Equal.refl`, o Kind retorna um erro de tipo:
```bash
Type mismatch
- Expected: (Equal Nat (Nat.pred (List.length _ xs)) (List.length _ (List.tail _ xs)))
- Detected: (Equal Nat (Nat.pred (List.length _ xs)) (Nat.pred (List.length _ xs)))
Kind.Context:
- xs : (List Nat)
On 'rescunhos.kind2':
   6 | Tl_length_pred xs                     = Equal.refl

Rewrites: 23936
kind2 check rascunhos.kind2  0,11s user 0,03s system 90% cpu 0,157 total
```
Da mesma forma, alguns teoremas precisam de indução para suas provas. 

- 3.0.1. Micro-Sermão. Simplesmente ler scripts de prova de exemplo não o levará muito longe! É importante trabalhar os detalhes de cada um, usando Kind e pensando no que cada passo alcança. Caso contrário, é mais ou menos garantido que os exercícios não farão sentido quando você chegar a eles. ( ಠ ʖ̯ ಠ)


### 3.1. Indução em Listas. 

Provas por indução sobre tipos de dados como `List` são um pouco menos familiares do que a indução de números naturais padrão, mas a ideia é igualmente simples. Cada declaração de dados define um conjunto de valores de dados que podem ser construídos usando os construtores declarados: um booleano pode ser True ou False; um número pode ser Zero ou Succ aplicado a outro número; uma lista de naturais pode ser Nil ou Cons aplicado a um número e uma lista.

Além disso, as aplicações dos construtores declarados entre si são as únicas
possíveis formas que os elementos de um conjunto definido indutivamente podem ter, e este fato
diretamente dá origem a uma maneira de raciocinar sobre conjuntos indutivamente definidos: um número
é Zero ou então é Succ aplicado a um número menor; uma lista é Nil ou então
é um Cons aplicado a algum número e a alguma lista menor; etc. Então, se tivermos e mente
 alguma proposição `p` que menciona uma lista `l` e queremos argumentar que `p` vale
para todas as listas, podemos raciocinar da seguinte forma:

- Primeiro, mostre que `p` é verdadeiro para `l` quando `l` é `Nil`.
- Então mostre que `p` é verdadeiro para `l` quando `l` é `Cons n l` para algum número `n` e alguma lista menor `l`, assumindo que `p` é verdadeiro para `l`.

Como listas maiores só podem ser construídas a partir de listas menores, eventualmente chegando a `Nil`,
esses dois argumentos juntos estabelecem a verdade de `p` para todas as listas `l`. Aqui está um
exemplo concreto:
```rust
App_assoc <t> (xs : List t) (ys : List t) (zs : List t) : Equal (List.concat (List.concat xs ys) zs) (List.concat xs (List.concat ys zs))
App_assoc List.nil ys  zs                               = Equal.refl
App_assoc (List.cons xs.head xs.tail) ys zs             = 
  let ind = App_assoc xs.tail ys zs
  let app = Equal.apply (x => (List.cons xs.head x)) ind
  app
```

Nós recebemos três listas `xs`, `ys` e `zs` e verificamos se a concatenação da de `xs` e `ys` com  `zs` é igual a de `xs` com a de `ys` com `zs`
Para isso nós verificamos para o caso de `xs` ser uma lista vazia, então recebemos uma reflexão da contenação é entre `ys` e `zs` e basta dar um `*Equal*.**refl**`

Em seguida nós "abrimos" o `xs` para obter o `xs.tail` para a nossa indução, e recebemos como objetivo:

``` 
- Goal: (Equal _ (List.cons _ xs.head (List.concat _ (List.concat _ xs.tail ys) zs)) (List.cons _ xs.head (List.concat _ xs.tail (List.concat _ ys zs))))
```

e a nossa variável `ind` é:
``` 
- ind : (Equal _ (List.concat _ (List.concat _ xs.tail ys) zs) (List.concat _ xs.tail (List.concat _ ys zs)))
```

bastando apenas aplicar um `List.cons xs.head` em ambos os lados da igualdade para ter o objetivo final e é isso o que fazemos no `app`:

``` 
- app : (Equal (List t2_) (List.cons t2_ xs.head (List.concat _ (List.concat _ xs`tail ys) zs)) (List.cons t2_ xs.head (List.concat _ xs.tail (List.concat _ ys zs))))
```

*OBSERVAÇÃO* 
O Type Check nos retorna tipos `t2`, `t3` e outros gerados no mesmo estilo e podemos ignorar e até mesmo apagar na hora de comparar o retorno das variáveis como vemos no seguinte caso:

```
- Goal: (Equal (List t2_) (List.cons _ xs.head (List.concat _ (List.concat _ xs.tail ys) zs)) (List.cons _ xs.head (List.concat _ xs.tail (List.concat _ ys zs))))
- app : (Equal (List t2_) (List.cons t2_ xs.head (List.concat _ (List.concat _ xs.tail ys) zs)) (List.cons t2_ xs.head (List.concat _ xs.tail (List.concat _ ys zs))))
```
e apagando os tipos gerados e os `holes`:

```
- Goal: (Equal (List) (List.cons xs.head (List.concat (List.concat xs.tail ys) zs)) (List.cons xs.head (List.concat xs.tail (List.concat ys zs))))
- app : (Equal (List) (List.cons xs.head (List.concat (List.concat xs.tail ys) zs)) (List.cons xs.head (List.concat xs.tail (List.concat ys zs))))
```
Dessa forma fica mais fácil perceber que o `app` e o `goal` são identicos, então não é necessário se assustar ao ver esses tipos gerados 

### 3.1.1 Invertendo uma lista. 

Para um exemplo um pouco mais complicado de prova indutiva sobre listas, suponha que usamos `app` para definir uma função de reversão de lista `rev`:

```rust
Rev <a> (xs: List a)            : List a
Rev List.nil                    = List.nil 
Rev (List.cons xs.head xs.tail) = List.concat (Rev xs.tail) [xs.head]

Test_rev1 : (Equal (List Nat) (Rev (List.to_nat [1, 2, 3]))(List.to_nat [3, 2, 1]))
Test_rev1 = Equal.refl

Test_rev2 : (Equal (Rev List.nil) List.nil)
Test_rev2 = Equal.refl
```

### 3.2.1

Agora vamos provar alguns teoremas sobre o rev que acabamos de definir. Para algo um pouco mais desafiador do que vimos, vamos provar
que inverter uma lista não altera seu comprimento. Nossa primeira tentativa fica presa
o caso sucessor...

```rust
Rev_length_firsttry (xs: List Nat)              : (Equal Nat (List.length (Rev xs)) (List.length xs))
Rev_length_firsttry List.nil                    = Equal.refl
Rev_length_firsttry (List.cons xs.head xs.tail) =
   let ind = Rev_length_firsttry xs.tail
   ?
```

O Type Check nos retorna o seguinte objetivo e contexto:
```bash
Inspection.
- Goal: (Equal Nat (List.length _ (List.concat _ (Rev _ xs.tail) (List.cons _ xs.head (List.nil _)))) (Nat.succ (List.length _ xs.tail)))
Kind.Context:
- t1_     : Type
- t1_     = Nat
- xs.head : t1_
- xs.tail : (List t1_)
- ind     : (Equal Nat (List.length _ (Rev _ xs.tail)) (List.length _ xs.tail))
- ind     = (Rev_length_firsttry xs.tail)
On 'rascunhos.kind2':
   40 |   ?

Rewrites: 76033
```

Agora nós temos que provar que o tamanho da concatenação do reverso do tail da lista e a head dela é igual ao sucessor do tamanho da tail, então precusaremos usar algumas outras provas, uma dela é que o tamanho da concatenação de duas listas é o mesmo da soma do damanho das de cada uma delas:

```rust
App_length <a> (xs: List a) (ys: List a)  : (Equal Nat (List.length (List.concat xs ys)) (Nat.add (List.length xs) (List.length ys)))
App_length List.nil ys                    = Equal.refl
App_length (List.cons xs.head xs.tail) ys =
   let ind = App_length xs.tail ys
   let app = Equal.apply (x => (Nat.succ x)) ind
   app
```

Além dessa prova, usaremos outras já provadas nos  capítulos anteriores:
```rust
Plus_n_z (n: Nat)     : (Equal Nat n (Nat.add n Nat.zero))
Plus_n_sn (n: Nat) (m: Nat) : (Equal Nat (Nat.succ (Nat.add n m))(Nat.add n (Nat.succ m)))
Plus_comm (n: Nat) (m: Nat) : (Equal Nat (Nat.add n m) (Nat.add m n))
```

E agora é possível provar o nosso teorema:
```rust
Rev_length <a> (xs: List a)             : (Equal Nat (List.length (Rev xs)) (List.length xs))
Rev_length List.nil                     = Equal.refl
Rev_length (List.cons xs.head xs.tail)  =
   let ind   = Rev_length xs.tail
   ?
```
```bash
Inspection.
- Goal: (Equal Nat (List.length _ (List.concat _ (Rev _ xs.tail) (List.cons _ xs.head (List.nil _)))) (Nat.succ (List.length _ xs.tail)))
Kind.Context:
- a3_     : Type
- t2_     : Type
- t2_     = a3_
- xs.head : t2_
- xs.tail : (List t2_)
- ind     : (Equal Nat (List.length _ (Rev _ xs.tail)) (List.length _ xs.tail))
- ind     = (Rev_length t2_ xs.tail)
On 'aula04.kind2':
   289 |   ?
   ```

Nós criamos uma variavel com nossa auxiliar `App_length`:
```rust
Rev_length <a> (xs: List a)             : (Equal Nat (List.length (Rev xs)) (List.length xs))
Rev_length List.nil                     = Equal.refl
Rev_length (List.cons xs.head xs.tail)  =
   let ind   = Rev_length xs.tail
   let aux1  = App_length (Rev xs.tail) [xs.head]
   ?
```
Recebemos um novo contexto para nos auxiliar, o 
```bash
- aux1    : (Equal Nat (List.length _ (List.concat _ (Rev t2_ xs.tail) (List.cons t2_ xs.head (List.nil t2_)))) (Nat.add (List.length _ (Rev t2_ xs.tail)) (Nat.succ Nat.zero)))
```

A `aux1` é igual ao lado esquerdo do nosso `Goal`, então metade do trabalho já foi resolvido, basta o outro lado da igualdade e para isso nós criamos uma nova variável, a `aux2`:
```rust 
let aux2  = Plus_comm (List.length (Rev xs.tail)) (Nat.succ Nat.zero)
```

Agora nosso contexto está ainda melhor: 
```bash
- aux2    : (Equal Nat (Nat.add (List.length t2_ (Rev t2_ xs.tail)) (Nat.succ Nat.zero)) (Nat.succ (List.length t2_ (Rev t2_ xs.tail))))
```

Como estamos progredindo nas provas formais, é possível perceber que o lado esquerdo da `aux2` é igual ao direito da `aux1` e podemos encadear um no outro com o `Equal.chain`:
```rust
let chn   = Equal.chain aux1 aux2
```

Ao dar o Type Check, vemos nosso novo contexto:
```bash
- chn     : (Equal Nat (List.length _ (List.concat _ (Rev t2_ xs.tail) (List.cons t2_ xs.head (List.nil t2_)))) (Nat.succ (List.length t2_ (Rev t2_ xs.tail))))
```

Nossa variável `chn` é praticamente identica ao nosso `Goal` só diferindo na parte final, pois `Goal` espera um `Nat.succ (List.length xs.tail)` e o `chn` nos dá `Nat.succ (List.length (Rev xs.tail))`, mas nós temos a variável `ind` que nos retorna essa igualdade. Vamos relembrar:

```bash
ind     : (Equal Nat (List.length (Rev xs.tail)) (List.length xs.tail))
```

Incrivel, não é? Ela nos retorna exatamente o que precisamos, que o tamanho do reverso da `tail` é igual ao tamanho da `tail`, então basta reescrever a variável `ind` na nossa `chn`:
```rust
let rwt   = Equal.rewrite ind (x => (Equal Nat (List.length (List.concat (Rev xs.tail) (List.cons xs.head (List.nil)))) (Nat.succ x ))) chn
```
Vamos ver nosso novo contexto, apenas ocultando os tipos para uma leitura mais fácil:
```bash
Inspection.
- Goal: (Equal Nat (List.length (List.concat (Rev xs.tail) (List.cons xs.head (List.nil)))) (Nat.succ (List.length xs.tail)))
Kind.Context:
- ind : (Equal Nat (List.length (Rev xs.tail)) (List.length xs.tail))
- aux1: (Equal Nat (List.length (List.concat (Rev xs.tail) (List.cons xs.head (List.nil)))) (Nat.add (List.length (Rev xs.tail)) (Nat.succ Nat.zero)))
- aux2: (Equal Nat (Nat.add (List.length (Rev xs.tail)) (Nat.succ Nat.zero)) (Nat.succ (List.length (Rev xs.tail))))
- chn : (Equal Nat (List.length (List.concat (Rev xs.tail) (List.cons xs.head (List.nil)))) (Nat.succ (List.length (Rev xs.tail))))
- rwt : (Equal Nat (List.length (List.concat (Rev xs.tail) (List.cons xs.head (List.nil)))) (Nat.succ (List.length xs.tail)))
```
Agora é muito mais fácil perceber que nosso `rwt` é exatamente o nosso `Goal`, então nossa prova fica assim:
```rust
Rev_length <a> (xs: List a)             : (Equal Nat (List.length (Rev xs)) (List.length xs))
Rev_length List.nil                     = Equal.refl
Rev_length (List.cons xs.head xs.tail)  =
   let ind   = Rev_length xs.tail
   let aux1  = App_length (Rev xs.tail) [xs.head]
   let aux2  = Plus_comm (List.length (Rev xs.tail)) (Nat.succ Nat.zero)
   let chn   = Equal.chain aux1 aux2
   let rwt   = Equal.rewrite ind (x => (Equal Nat (List.length (List.concat (Rev xs.tail) (List.cons xs.head (List.nil)))) (Nat.succ x ))) chn
   rwt
```

