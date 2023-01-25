# Estrutura de dados
## Listas: trabalhando com dados estruturados

A partir de agora, veremos dados estruturados, em especial as listas e pares, e que podem conter elementos de diversos tipos. Na definição do tipo, já mostraremos eles com tipos *polimórficos*, mas não se assombre, veremos sobre isso no próximo capítulo, apenas vamos ignorar o tipo e acompanhar a explicação, fará mais sentido ao decorrer do nosso estudo.

### Pares de Números

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
A forma de construir um par de *Nat* é a seguinte:
```rust
Pair.new Nat Nat a b  : (Pair a b)
```
Aqui estão duas funções simples para extrair o primeiro e o segundo componentes de um par. As definições também ilustram como fazer a correspondência de padrões em dois argumentos construtores.

Exemplo 1: (Fst Nat (List Nat) (Pair 2 [1n,2n,3n])) ->  2n
```rust
Fst (pair: Pair Nat Nat) : Nat
Fst (Pair.new Nat Nat fst snd) = fst
```
Exemplo 2: (Snd Nat (List Nat) (Pair 2 [1n,2n,3n])) -> [1n,2n,3n]
```rust
Snd (pair: Pair Nat Nat) : Nat
Snd (Pair.new Nat Nat fst snd) = snd
```

### Algumas provas

Vamos tentar provar alguns fatos simples sobre pares. Se declararmos as coisas de uma maneira particular (e ligeiramente peculiar), podemos completar provas com apenas reflexividade:
```rust
Surjective_pairing (p: Pair Nat Nat) : Equal (Pair Nat Nat) p (Pair.new (Fst p) (Snd p)))
Surjective_pairing (Pair.new Nat Nat fst snd) = Equal.refl
```
Mas *Equal.***refl** não é suficiente caso a declaração seja:
```rust
Surjective_pairing (Pair.new Nat Nat fst snd) = Equal.refl
```
Já que o Kind espera
```rust
Equal (Pair Nat Nat) p (Pair.new (Fst p) (Snd p)))
```
E recebeu
```rust
Equal p p)
```
Nós devemos "expor" a estrutura interna do *par* para que o *Type Checker*
possa verificar se `p` é realmente igual a `Pair.new (Fst p) (Snd p)` 


### Execícios:

1.1
```rust
Snd_fst_is_swap (p: Pair Nat Nat )         : Equal (Pair Nat Nat) (Pair.swap Nat Nat (Pair.swap Nat Nat p)) p)
Snd_fst_is_swap (Pair.new Nat Nat fst snd) = ? 
```

1.2
```rust
Fst_swap_is_inverse (p: Pair Nat Nat) (a: Nat) (b: Nat) : Equal (Pair Nat Nat) (Pair.swap Nat Nat (Pair.new a b) ) (Pair.new b a))
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
List (a: Type) : Type
List.nil <a> : (List a)
List.cons <a> (head: a) (tail: List a) : (List a)
```
Como vamos tratar de apenas um tipo, é interessante reescrever o tipo de lista para um definido, o escolhido foi o *Nat*:
```rust
NatList : Type
NatList.nil  : NatList
NatList.cons (head: Nat) (tail: NatList) : NatList
```
ou
```rust
NatList : Type
NatList = (List Nat)
```

Podemos perceber que em ambas as notações, há um `head` e um `tail`, sendo que o
*head* recebe um elemento do tipo *Nat* e a *tail* recebe uma lista do tipo *Nat*. 

Por exemplo, uma lista de três números naturais 1n, 2n e 3n seria escrita da
seguinte forma:

`[1n, 2n, 3n]`

O Kind, entretanto, lê de outra forma:

`[1n, [2n, 3n]]`

onde o `1n` é a head e o `[2n, 3n]` é a tail. Da mesma forma, ao olhar para uma
lista de 4 elementos `[1n, 2n, 3n, 4n]`, agora veremos da seguinte forma:
 
`[1n, [2n, [3n, 4n]]]`

A lista possui o `head` `1n` e a `tail` `[2n, [3n, 4n]]`, que, por sua vez, possui a
`head` `2n` e a `tail` `[3n, 4n]` que também possui sua `head` `3n` e sua tail `4n`.

Pode parecer assustador, mas é um monstro amigável:

![img](../Imgs/listmonster.png)

[fonte da imagem: http://learnyouahaskell.com/starting-out]

### 2.1 Repeat
A função repeat recebe um número *n* e um valor, retornando uma lista de tamanho *n* onde todos os elementos é o valor declarado.
```rust
// Exemplo: (Repeat 3 Bool.true) -> [True, True, True]
Repeat (x: Nat) (count: Nat) : List Nat
Repeat x Nat.zero            = List.nil Nat 
Repeat x (Nat.succ count)    = List.cons Nat x (Repeat count x)
```

### 2.2 Length
A função *length* calcula o tamanho da lista 
```rust
// Exemplo: (Length [1,2,3]) -> 3
Length (xs: List Nat) : Nat
Length (List.nil)            = Nat.zero
Length (List.cons head tail) = (Nat.succ (Length tail))
```

### 2.3 App
A função append concatena (anexa) duas listas.
```rust
App (xs: List Nat) (ys: List Nat) : List Nat
App (List.nil)            ys = ys
App (List.cons head tail) ys = List.cons Nat head (App tail ys)
```

### 2.4 Head e Tail 
 A função head retorna o primeiro elemento (a “cabeça”) da
list, enquanto tail retorna tudo menos o primeiro elemento (a “cauda”). Claro, o
lista vazia não tem primeiro elemento, então devemos tratar esse caso com um tipo *Maybe*,
recebendo um *Maybe*.**none** caso a lista seja vazia ou um *Maybe*.**some**
caso tenha um valor.
```rust
// Exemplo: (Head 0n [1n,2n,3n]) -> 1n
Head (default: Nat) (xs: List Nat) :  Nat
Head default (List.nil)            = default
Head default (List.cons head tail) = head
```
```rust
// Exemplo: (Tail Nat [1,2,3]) -> [2,3]
Tail (xs: List Nat)        : List Nat
Tail (List.nil)            = List.nil Nat
Tail (List.cons head tail) = tail
```
```rust
Test_head1 : Equal Nat (Head 0n [1n,2n,3n]) 1n
Test_head1 = Equal.refl
```
```rust
Test_head2 : Equal Nat (Head 0n List.nil) 0n
Test_head2 = Equal.refl
```
```rust
Test_head3 : Equal (List Nat) (Tail [1n, 2n, 3n]) [2n, 3n])
Test_head3 = Equal.refl
```

### 2.5 Exercícios

- 2.5.1 Complete as definições de nonzeros, oddmembers e countoddmembers abaixo. Dê uma olhada nos testes para entender o que essas funções devem fazer.

```rust
Nonzeros (xs: List Nat) : List Nat
Nonzeros xs = ?
```
```rust
Test_nonzeros : Equal (List Nat) (Nonzeros [0n,1n,0n,2n,3n,0n,0n]) [1n,2n,3n]
Test_nonzeros = ?
```
```rust
Oddmembers (xs: List Nat) : List Nat
Oddmembers xs = ?
```
```rust
Test_oddmembers : Equal (List Nat) (Oddmembers [0n,1n,0n,2n,3n,0n,0n]) [1n,3n]
Test_oddmembers = ?
```
```rust
CountOddMembers (xs: List Nat)  : Nat
CountOddMembers xs = ?
```
```rust
Test_countoddmembers1 : Equal Nat (CountOddMembers [1n,0n,3n,1n,4n,5n]) 4n)
Test_countoddmembers1 = ?
```

- 2.5.2 Complete a definição de alternate, que “compacta” duas listas em uma, alternando entre os elementos tomados da primeira lista e elementos da segunda. Veja os testes abaixo para mais exemplos específicos.

```rust
Alternate (xs: List Nat) (ys: List Nat) : List Nat
Alternate xs ys = ?
```
```rust
Test_alternate1 : Equal (List Nat) (Alternate [1n,2n,3n] [4n,5n,6n]) [1n,4n,2n,5n,3n,6n]
Test_alternate1 = ?
```
```rust
Test_alternate2 : Equal (List Nat) (Alternate [1n] [4n,5n,6n]) [1n,4n,5n,6n]
Test_alternate2 = ?
```
```rust
Test_alternate3 : Equal (List Nat) (Alternate  [1n,2n,3n] [4n]) [1n,4n,2n,3n]
Test_alternate3 = ? 
```
```rust
Test_alternate4 : Equal (List Nat) (Alternate (List.nil Nat) [20n,30n]) [20n,30n]
Test_alternate4 = ?
```

- 2.6.1 Complete as seguintes definições para as funções count, sum, add, e member das listas de naturais

```rust
Count (v: Nat) (xs: List Nat) : Nat
Count v xs = ?
```
```rust
Test_count1 : Equal Nat (Count 1n [1n,2n,3n,1n,4n,1n]) 3n
Test_count1 = ?
```
```rust
Test_count2 : Equal Nat (Count 6n [1n,2n,3n,1n,4n,1n]) 0n
Test_count2 = ?
```
```rust
Sum (xs: List Nat) (ys: List Nat) : List Nat
Sum xs ys = ?
```
```rust
Test_sum1 : Equal Nat (Count 1n (Sum [1n,2n,3n] [1n,4n,1n])) 3n
Test_sum1 = ?
```
```rust
Add (n: Nat) (xs: List Nat) : List Nat
Add n xs = ?
```
```rust
Test_add1 : Equal Nat (Count 1n (Add 1n [1n,4n,1n])) 3n
Test_add1 = ?
```
```rust
Test_add2 : Equal Nat (Count 5n (Add 1n [1n,4n,1n])) 0n
Test_add2 = ?
```
```rust
Member (v: Nat) (xs: List Nat) : Bool
Member v xs = ?
```
```rust
Test_member1 : Equal Bool (Member 1n [1n,4n,1n]) Bool.true
Test_member1 = ?
```
```rust
Test_member2 : Equal Bool (Member 2n [1n,4n,1n]) Bool.false
Test_member2 = ?
```

- 2.6.2 Aqui estão mais algumas funções de `List Nat` para você praticar. Quando remove_one é aplicado a uma lista sem o número a ser removido, ele deve retornar a mesma lista inalterada

```rust
Remove_one (v: Nat) (xs: List Nat) : List Nat
Remove_one v xs = ?
```
```rust
Test_remove_one1 : Equal Nat (Count 5n (Remove_one 5n [2n,1n,5n,4n,1n])) 0n
Test_remove_one1 = ?
```
```rust
Test_remove_one2 : Equal Nat (Count 5n (Remove_one 5n [2n,1n,4n,1n])) 0n
Test_remove_one2 = ?
```
```rust
Test_remove_one3 : Equal Nat (Count 4n (Remove_one 5n [2n,1n,5n,4n,1n,4n])) 2n
Test_remove_one3 = ?
```
```rust
Test_remove_one4 : Equal Nat (Count 5n (Remove_one 5n [2n,1n,5n,4n,5n,1n,4n])) 1n
Test_remove_one4 = ?
```
```rust
Remove_all (v: Nat) (xs: List Nat) : List Nat
Remove_all v xs = ?
```
```rust
Test_remove_all1  : Equal Nat (Count 5n (Remove_all 5n [2n,1n,5n,4n,1n])) 0n
Test_remove_all1  = ?
```
```rust
Test_remove_all2  : Equal Nat (Count 5n (Remove_all 5n [2n,1n,4n,1n])) 0n
Test_remove_all2  = ?
```
```rust
Test_remove_all3  : Equal Nat (Count 4n (Remove_all 5n [2n,1n,5n,4n,1n,4n])) 2n
Test_remove_all3  = ?
```
```rust
Test_remove_all4  : Equal Nat (Count 5n (Remove_all 5n [2n,1n,5n,4n,5n,1n,4n,5n,1n,4n])) 0n
Test_remove_all4  = ?
```
```rust
Subset (xs: List Nat) (ys: List Nat)  : Bool
Subset xs ys = ?
```
```rust
Test_subset1 : Equal Bool (Subset [1n,2n] [2n,1n,4n,1n]) Bool.true
Test_subset1 = ?
```
```rust
Test_subset2 : Equal Bool (Subset [1n,2n,2n] [2n,1n,4n,1n]) Bool.false
Test_subset2 = ?
```

## 3 Raciocínio sobre listas

Assim como os números, fatos simples sobre funções de processamento de lista podem às vezes ser provado inteiramente por simplificação. Por exemplo, a simplificação realizada por
`Equal.refl` é suficiente para este teorema...

```Rust
Nil_app (xs: List Nat) : Equal (App (List.nil Nat) xs) xs
Nil_app xs = Equal.refl
```

... isso porque o Kind "vê" o  `List.nil` e já reduz automaticamente a igualdade da mesma forma que ocorre com os números naturais, com o `Nat.zero`

Além disso, como acontece com os números, às vezes é útil realizar uma análise de caso no possíveis formas (vazias ou não vazias) de uma lista desconhecida

```rust
Tl_length_pred (xs: List Nat)         : Equal Nat (Pred (Length xs)) (Length (Tail xs))
Tl_length_pred List.nil               = Equal.refl
Tl_length_pred (List.cons head tail)  = Equal.refl
```

Caso o usuário não abra os casos e use direto o `Equal.refl`, o Kind retorna um erro de tipo:

```diff
- ERROR  Type mismatch

   • Got      : Equal Nat (Nat.pred (Length xs)) (Nat.pred (Length xs))) 
   • Expected : Equal Nat (Nat.pred (Length xs)) (Length (Tail xs))) 

   • Context: 
   •   xs : (List Nat) 

   Tl_length_pred xs = Equal.refl
                       ┬─────────
                       └Here!
```
Da mesma forma, alguns teoremas precisam de indução para suas provas. 

- 3.0.1. Micro-Sermão. Simplesmente ler scripts de prova de exemplo não o levará muito longe! É importante trabalhar os detalhes de cada um, usando Kind e pensando no que cada passo alcança. Caso contrário, é mais ou menos garantido que os exercícios não farão sentido quando você chegar a eles. ( ಠ ʖ̯ ಠ)

## 3.1. Indução em Listas.

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
App_assoc (xs: List Nat) (ys: List Nat) (zs: List Nat) : Equal (App (App xs ys) zs) (App xs (App ys zs))
App_assoc List.nil                               ys zs = Equal.refl
App_assoc (List.cons Nat xs.head xs.tail)        ys zs = 
  let ind = App_assoc xs.tail ys zs
  let app = Equal.apply (x => (List.cons xs.head x)) ind
  app
```

Nós recebemos três listas `xs`, `ys` e `zs` e verificamos se a concatenação da de `xs` e `ys` com  `zs` é igual a de `xs` com a de `ys` com `zs`
Para isso nós verificamos para o caso de `xs` ser uma lista vazia, então recebemos uma reflexão da contenação é entre `ys` e `zs` e basta dar um `*Equal*.**refl**`

Em seguida nós "abrimos" o `xs` para obter o `xs.tail` para a nossa indução, e recebemos como objetivo:

``` 
 • Expected: Equal (List Nat) (List.cons Nat xs.head (App (App xs.tail ys) zs)) (List.cons Nat xs.head (App xs.tail (App ys zs)))) 
```

e a nossa variável `ind` é:
``` 
 • ind: Equal (List Nat) (App (App xs.tail ys) zs) (App xs.tail (App ys zs)))
```

bastando apenas aplicar um `List.cons xs.head` em ambos os lados da igualdade para ter o objetivo final e é isso o que fazemos no `app`:

``` 
 • app : Equal (List Nat) (List.cons Nat xs.head (App (App xs.tail ys) zs)) (List.cons Nat xs.head (App xs.tail (App ys zs))))
```

*OBSERVAÇÃO* 
O Type Check nos retorna tipos `t2`, `t3` e outros gerados no mesmo estilo e podemos ignorar e até mesmo apagar na hora de comparar o retorno das variáveis como vemos no seguinte caso:

```
 • Expected: Equal (List Nat) (List.cons Nat xs.head (App (App xs.tail ys) zs)) (List.cons Nat xs.head (App xs.tail (App ys zs)))) 
 •   app   : Equal (List Nat) (List.cons Nat xs.head (App (App xs.tail ys) zs)) (List.cons Nat xs.head (App xs.tail (App ys zs))))
```

<!-- e apagando os tipos gerados e os `holes`:

```
- Expected: Equal (List) (List.cons xs.head (App (App xs.tail ys) zs)) (List.cons xs.head (App xs.tail (App ys zs))))
- app : Equal (List) (List.cons xs.head (App (App xs.tail ys) zs)) (List.cons xs.head (App xs.tail (App ys zs))))
``` -->
<!-- TODO holes --> 
Dessa forma fica mais fácil perceber que o `app` e o `Expected` são identicos, então não é necessário se assustar ao ver esses tipos gerados 

## 3.1.1 Invertendo uma lista. 

Para um exemplo um pouco mais complicado de prova indutiva sobre listas, suponha que usamos `app` para definir uma função de reversão de lista `rev`:
```rust
Rev (xs: List Nat)        : List Nat
Rev List.nil              = List.nil Nat
Rev (List.cons head tail) = App (Rev tail) [head]

Test_rev1 : Equal (List Nat) (Rev [1n,2n,3n])) [3n,2n,1n])
Test_rev1 = Equal.refl

Test_rev2 : Equal (Rev List.nil) List.nil
Test_rev2 = Equal.refl
```

## 3.2.1

Agora vamos provar alguns teoremas sobre o rev que acabamos de definir. Para algo um pouco mais desafiador do que vimos, vamos provar
que inverter uma lista não altera seu comprimento. Nossa primeira tentativa fica presa
o caso sucessor...
```rust
Rev_length_firsttry (xs: List Nat)              : Equal Nat (Length (Rev xs)) (Length xs))
Rev_length_firsttry List.nil                    = Equal.refl
Rev_length_firsttry (List.cons xs.head xs.tail) =
   let ind = Rev_length_firsttry xs.tail
   ?
```

O Type Check nos retorna o seguinte objetivo e contexto:
```diff
+ INFO  Inspection.

   • Expected: Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Nat.succ (Length tail))) 

   • Context: 
   •   head : Nat 
   •   tail : (List Nat) 
   •   ind  : Equal Nat (Length (Rev tail)) (Length tail)) 
   •   ind  = (Rev_length_firsttry tail) 

   let ind = Rev_length_firsttry tail
      ?
      ┬
      └Here!
```

Agora nós temos que provar que o tamanho da concatenação do reverso do tail da lista e a head dela é igual ao sucessor do tamanho da tail, então precusaremos usar algumas outras provas, uma dela é que o tamanho da concatenação de duas listas é o mesmo da soma do damanho das de cada uma delas:
```rust
App_length (xs: List Nat) (ys: List Nat)  : Equal Nat (Length (App xs ys)) (Plus (Length xs) (Length ys)))
App_length List.nil ys                    = Equal.refl
App_length (List.cons xs.head xs.tail) ys =
   let ind = App_length xs.tail ys
   let app = Equal.apply (x => (Nat.succ x)) ind
   app
```

Além dessa prova, usaremos outras já provadas nos  capítulos anteriores:
```rust
Plus_n_z (n: Nat)     : Equal Nat n (Plus n Nat.zero)
Plus_n_sn (n: Nat) (m: Nat) : Equal Nat (Nat.succ (Plus n m)) (Plus n (Nat.succ m))
Plus_comm (n: Nat) (m: Nat) : Equal Nat (Plus n m) (Plus m n)
```

E agora é possível provar o nosso teorema:
```rust
Rev_length (xs: List Nat)               : Equal Nat (Length (Rev xs)) (Length xs)
Rev_length List.nil                     = Equal.refl
Rev_length (List.cons Nat head tail)  =
   let ind   = Rev_length tail
   ?
```
```diff
+ INFO  Inspection.

   • Expected: Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Nat.succ (Length tail))) 

   • Context: 
   •   head : Nat 
   •   tail : (List Nat) 
   •   ind  : Equal Nat (Length (Rev tail)) (Length tail)) 
   •   ind  = (Rev_length tail) 

   let ind   = Rev_length tail
      ?
      ┬
      └Here!
```

Nós criamos uma variavel com nossa auxiliar `App_length`:
```rust
Rev_length (xs: List Nat)             : Equal Nat (Length (Rev xs)) (Length xs)
Rev_length List.nil                   = Equal.refl
Rev_length (List.cons Nat head tail)  =
   let ind  = Rev_length tail
   let aux1 = App_length (Rev xs.tail) [xs.head]
   ?
```
Recebemos um novo contexto para nos auxiliar, o 
```
 • aux1: Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Plus (Length (Rev tail)) 1n))
```

A `aux1` é igual ao lado esquerdo do nosso `Expected`, então metade do trabalho já foi resolvido, basta o outro lado da igualdade e para isso nós criamos uma nova variável, a `aux2`:
```rust 
let aux2 = Plus_comm (Length (Rev xs.tail)) (1n)
```

Agora nosso contexto está ainda melhor: 
```rust
 • aux2: Equal Nat (Plus (Length (Rev tail)) 1n) (Nat.succ (Length (Rev tail)))) 
```

Como estamos progredindo nas provas formais, é possível perceber que o lado esquerdo da `aux2` é igual ao direito da `aux1` e podemos encadear um no outro com o `Equal.chain`:
```rust
let chn = Equal.chain aux1 aux2
```

Ao dar o Type Check, vemos nosso novo contexto:

```Terminal
 • chn : Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Nat.succ (Length (Rev tail))))
```

Nossa variável `chn` é praticamente identica ao nosso `Expected` só diferindo na parte final, pois `Expected` espera um `Nat.succ (Length xs.tail)` e o `chn` nos dá `Nat.succ (Length (Rev xs.tail))`, mas nós temos a variável `ind` que nos retorna essa igualdade. Vamos relembrar:

```Terminal
 • ind: Equal Nat (Length (Rev tail)) (Length tail)) 
```

Incrivel, não é? Ela nos retorna exatamente o que precisamos, que o tamanho do reverso da `tail` é igual ao tamanho da `tail`, então basta reescrever a variável `ind` na nossa `chn`:
```rust
let rrt = Equal.rewrite ind (x => Equal Nat (Length (App (Rev tail) (List.cons head (List.nil)))) (Nat.succ x ))) chn
```
Vamos ver nosso novo contexto, apenas ocultando os tipos para uma leitura mais fácil:
```diff
+ INFO  Inspection.

   • Expected: Equal Nat (Length (App (Rev tail) (List.cons _ head (List.nil _)))) (Nat.succ (Length tail))) 

   • Context: 
   •   head : Nat 
   •   tail : (List Nat) 
   •   ind  : Equal Nat (Length (Rev tail)) (Length tail)) 
   •   ind  = (Rev_length tail) 
   •   aux1 : Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Plus (Length (Rev tail)) 1n)) 
   •   aux1 = (App_length (Rev tail) (List.cons Nat head (List.nil Nat))) 
   •   aux2 : Equal Nat (Plus (Length (Rev tail)) 1n) (Nat.succ (Length (Rev tail)))) 
   •   aux2 = (Plus_comm (Length (Rev tail)) 1n) 
   •   chn  : Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Nat.succ (Length (Rev tail)))) 
   •   chn  = Equal.chain Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Plus (Length (Rev tail)) 1n) (Nat.succ (Length (Rev tail))) aux1 aux2) 
   •   rrt  : Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Nat.succ (Length tail))) 
   •   rrt  = Equal.rewrite Nat (Length (Rev tail)) (Length tail) ind (x => Equal Nat (Length (App (Rev tail) (List.cons Nat head (List.nil Nat)))) (Nat.succ x))) chn)
```
Agora é muito mais fácil perceber que nosso `rrt` é exatamente o nosso `Expected`, então nossa prova fica assim:
```rust
Rev_length (xs: List Nat)            : Equal Nat (Length (Rev xs)) (Length xs))
Rev_length List.nil                  = Equal.refl
Rev_length (List.cons Nat head tail) =
   let ind   = Rev_length tail
   let aux1  = App_length (Rev tail) [head]
   let aux2  = Plus_comm (Length (Rev tail)) (1n)
   let chn   = Equal.chain aux1 aux2
   let rrt = Equal.rewrite ind (x => Equal Nat (Length (App (Rev tail) (List.cons head (List.nil)))) (Nat.succ x ))) chn
   rrt
```
# 3.3
### 3.3.1
Vamos praticar um pouco mais com as listas:
```rust
App_nil_r (xs: List Nat) : Equal (App xs List.nil) xs)
App_nil_r xs = ?

App_assoc (xs: List Nat) (ys: List Nat) (zs: List Nat) : Equal (App (App xs ys) zs) (App xs (App ys zs))
App_assoc xs ys zs = ?

Rev_app_distr (xs: List Nat) (ys: List Nat) : Equal (Rev (App xs ys)) (App (Rev ys) (Rev xs)))
Rev_app_distr xs ys = ?

Rev_involutive (xs: List Nat) : Equal (Rev (Rev xs)) xs)
Rev_involutive xs = ?
```

Há uma solução curta para a próxima. Se você estiver achando muito difícil ou começar a ficar longo demais,
recue e tente procurar uma maneira mais simples.
```rust
App_assoc4 (l1: List Nat) (l2: List Nat) (l3: List Nat) (l4: List Nat) : Equal (List Nat) (App l1 (App l2 (App l3 l4))) (App (App (App l1 l2) l3) l4))
App_assoc4 l1 l2 l3 l4 = ? 
```
Um exercício sobre sua implementação de *nonzeros*:
```rust
Nonzeros_app (xs: List Nat) (ys: List Nat) : Equal (List Nat) (Nonzeros (App xs ys)) (App (Nonzeros xs) (Nonzeros ys)))
Nonzeros_app xs ys = ?
```
### 3.3.2 
Preencha a definição de beq_NatList, que compara listas de números para igualdade. Prove que beq_NatList xs ys produz *Bool.true* para cada lista.
```rust
Beq_NatList (xs: List Nat) (ys: List Nat) : Bool
Beq_NatList xs ys = ? 

Test_beq_natlist1 : Equal Bool (Beq_list List.nil List.nil) Bool.true
Test_beq_natlist1 = ?

Test_beq_natlist2 : Equal Bool (Beq_list [1n,2n,3n] [1n,2n,3n]) Bool.true
Test_beq_natlist2 = ?

Test_beq_natlist3 : Equal Bool (Beq_list [1n,2n,3n] [1n,2n,4n]) Bool.false
Test_beq_natlist3 = ?

Beq_natlist_refl (xs: List Nat) : Equal Bool Bool.true (Beq_list xs xs)
Beq_natlist_refl xs = ?
```
## 3.4 Exercícios de Listas, parte 2
### 3.4.1
Prove o seguinte teorema, ele ajudará você na prova seguinte
```rust
Ble_n_succ_n (n: Nat) : Equal Bool (Lte n (Nat.succ n)) Bool.true
Ble_n_succ_n n = ? 
```
Aqui estão mais alguns pequenos teoremas para provar sobre suas definições sobre listas.
```rust
Count_member_nonzero (xs: List Nat) : Equal Bool (Lte 1n (Count 1n (List.cons 1n xs))) Bool.true
Count_member_nonzero xs = ?
```

### 3.4.2
Prove que a função *Rev* é injetiva - isto é
```rust
Rev_injective (xs: List Nat) (ys: List Nat) (e: Equal (List Nat) (Rev xs) (Rev ys)) :tail Equal (List Nat) xs ys
Rev_injective xs ys e = ?  
```

## 4 Maybe

Suponha que a gente queira escrever uma função que retorna o enésimo número de uma lista.
Nós então definimos um número que é aplicado a uma lista de naturais e então retorna o número que ocupa essa posição. Dessa forma, nós precisamos definir um número para ser retornado caso o número seja maior do que o tamanho da lista.
```rust
Nth_bad (n: Nat) (xs: List Nat)            : Nat
Nth_bad n List.nil                         = 42n // Valor arbitrário 
Nth_bad Nat.zero (List.cons head tail)     = head
Nth_bad (Nat.succ n) (List.cons head tail) = Nth_bad n tail
```
Esta solução não é tão boa: se nth_bad retornar 0, não podemos dizer se esse valor
realmente aparece na entrada sem processamento adicional. Uma alternativa melhor é
para alterar o tipo de retorno de nth_bad para incluir um valor de erro como um possível resultado.
Chamamos esse tipo *Maybe*, pois ele pode ou não ter algo, se tiver é um *Maybe.some* desse algo, se não tiver, é um *Maybe.none*.
```rust
NatMaybe : Type
NatMaybe = (Maybe Nat)
```
Podemos então alterar a definição acima de nth_bad para retornar None quando a lista for
muito curto e Some a quando a lista tem membros suficientes e aparece na posição
n. Chamamos essa nova função de nth_error para indicar que pode resultar em um erro.

Essa prova ainda serve pra nos apresentar outro recurso do Kind, expressões condicionais, o *if* e *else*
```rust
Nth_error (n: Nat) (xs: List Nat) : Maybe Nat
Nth_error n List.nil              = Maybe.none
Nth_error n (List.cons head tail) = 
  let ind = Nth_error (Pred n) tail
  Bool.if (Eql n 0n) (Maybe.some Nat head) (ind)

Test_nth_error1 : Equal (Nth_error 0n [4n,5n,6n,7n]) (Maybe.some 4n)
Test_nth_error1 = Equal.refl

Test_nth_error2 : Equal (Nth_error 3n [4n,5n,6n,7n]) (Maybe.some 7n)
Test_nth_error2 = Equal.refl

Test_nth_error3 : Equal (Nth_error 9n [4n,5n,6n,7n]) Maybe.none
Test_nth_error3 = Equal.refl
```
<!-- TODO -->
```rust
Extract (d: Nat) (o: Maybe Nat) : Nat
Extract d (Maybe.some k)        = k
Extract d (Maybe.none)          = d
```

### 4.0.1

```rust
Head_error (xs: List Nat) : Maybe Nat
Head_error xs = ?

Test_head_error1 : Equal (Head_error List.nil) Maybe.none
Test_head_error1 = ?

Test_head_error2 : Equal (Head_error [1n]) (Maybe.some Nat 1n)
Test_head_error2 = ?

Test_head_error3 : Equal (Head_error  [5n,6n]) (Maybe.some Nat 5n)
Test_head_error3 = ?
```

### 4.0.2
```rust
Extract_hd (l: List Nat) (default: Nat) : Equal Nat (Head default l)  (Extract default (Head_error l))
Extract_hd l default = ?
```