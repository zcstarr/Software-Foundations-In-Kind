# Kind Book
# Capítulo 4
## Listas: trabalhando com dados estruturados
zsh:1: command not found: book.md

A partir de agora, veremos dados estruturados, em especial as listas e pares, e que podem conter elementos de diversos tipos. Na definição do tipo, já mostraremos eles com tipos *polimórficos*, mas não se assombre, veremos sobre isso no próximo capítulo, apenas vamos ignorar o tipo e acompanhar a explicação, fará mais sentido ao decorrer do nosso estudo.

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

![img](./Imgs/listmonster.png)

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

### 3 Raciocínio sobre listas

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

### 3.1.1 Invertendo uma lista. 

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

### 3.2.1

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
## 3.3
#### 3.3.1
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
#### 3.3.2 
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
### 3.4 Exercícios de Listas, parte 2
#### 3.4.1
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

#### 3.4.2
Prove que a função *Rev* é injetiva - isto é
```rust
Rev_injective (xs: List Nat) (ys: List Nat) (e: Equal (List Nat) (Rev xs) (Rev ys)) :tail Equal (List Nat) xs ys
Rev_injective xs ys e = ?  
```
### 4 Maybe
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

#### 4.0.1

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

#### 4.0.2
```rust
Extract_hd (l: List Nat) (default: Nat) : Equal Nat (Head default l)  (Extract default (Head_error l))
Extract_hd l default = ?
```

# Capítulo 5
### 1 Polimorfismo
Neste capítulo, continuamos nosso desenvolvimento de conceitos básicos de programação. Nossos novos princípios essenciais são o polimorfismo (funções de abstração sobre o
tipos de dados que eles manipulam) e funções de ordem superior (funções de tratamento como dados). Começamos com o polimorfismo.

- 1.1. Listas Polimórficas.

Nos últimos dois capítulos, trabalhamos com listas polimórficas, você pode só não ter percebido. Obviamente, programas interessantes também precisam ser capazes de
manipular listas com elementos de outros tipos – listas de strings, listas de booleanos,
listas de listas, etc. Poderíamos apenas definir um novo tipo de dados indutivo para cada um deles,
por exemplo...
```rust
BoolList : Type
BoolList = (List Bool)
```

.. mas isso rapidamente se tornaria tedioso, em parte porque temos que compensar
nomes de construtor diferentes para cada tipo de dados, mas principalmente porque também
precisamos definir novas versões de todas as nossas funções de manipulação de listas (length, rev, etc.)
para cada nova definição de tipo de dados.

Para evitar toda essa repetição, o *Kind* suporta definições de tipos indutivos polimórficos.
Por exemplo, aqui está um tipo de dados de lista polimórfica e que já vimos no capítulo anterior:
```rust
List <a: Type> : Type
List.nil <a> : (List a)
List.cons <a> (head: a) (tail: List a) : (List a)
```

Esse tipo já exite no Kind e podemos perceber que ele é idententico ao BoolList , mas com um tipo `a`, esse *tipo* recebe qualquer outro tipo, seja *Nat*, *Bool*, *Maybe* e etc.
Não precisamos criar um tipo de lista para cada um dos tipos de dados, podemos usar esse que adota todas as formas existentes.

Que tipo de coisa é a própria Lista? Uma boa maneira de pensar sobre isso é que List é
uma função de Tipos para defi   nições indutivas; ou, em outras palavras, List é
uma função de Tipos para Tipos. Para qualquer tipo específico x, o tipo List x é um
conjunto indutivamente definido de listas cujos elementos são do tipo x.

Com esta definição, quando usamos os construtores Nil e Cons para construir listas,
precisamos dizer ao Kind o tipo dos elementos nas listas que estamos construindo - isto
é, que Nil e Cons agora são construtores polimórficos. Observe os tipos de
construtores:
```rust
Pair (a: Type) (b: Type) : Type
Pair.new <a> <b> (fst: a) (snd: b) : (Pair a b)
```
Nosso tipo *Pair* recebe outros dois tipos, o `a` e o `b` e retorna um par dos dois tipos. Não foi necessário definir se o par era de números naturais,
booleanos, listas, bits ou outros pares, nós deixamos a função apta a tratar todos os pares possíveis e isso é graças ao *polimorfismo*.

Agora podemos voltar e fazer versões polimórficas de todas as listas de processamento
funções que escrevemos antes. Aqui está a repetição, por exemplo:
```rust
Repeat <a: Type> (x: a) (count: Nat) : List a
Repeat a x Nat.zero                  = List.nil
Repeat a x (Nat.succ count)          = List.cons x (Repeat x count)
 ```

Tal como acontece com Nil e Contras, podemos usar repeat aplicando-o primeiro a um tipo e depois a
seu argumento de lista:
```rust
Test_repeat1 : Equal (Repeat 4n 2n) (List.cons 4n (List.cons 4n List.nil))
Test_repeat1 = Equal.refl
```

Para usar repeat para construir outros tipos de listas, simplesmente instanciamos com um parâmetro de tipo apropriado:
```rust
Test_repeat2 : Equal (Repeat Bool.false 1n) (List.cons Bool.false List.nil)
Test_repeat2 = Equal.refl
```


1.1.2 Inferência de anotação de tipo.

Vamos escrever a definição do `repeat` novamente, mas dessa vez omitindo o tipo, mas atenção, essa não é uma boa prática usar o `hole`, servirá apenas para compreender o poder do Kind e como ele pode ajudar o usuário a encontrar o que deseja.
```rust
Repeat (x: _) (count: Nat) : List _
Repeat x Nat.zero          = ?
```
Ao rodar o *Type Check* o terminal nos retorna:
```diff
+ INFO  Inspection.

   • Expected: (List _) 

   • Context: 
   •   x : _ 

   Repeat x Nat.zero = ?
                       ┬
                       └Here!
```
Para o caso do contador ser zero, que é o nosso ponto de parada, nós precisamos retornar uma lista do tipo não definido. 
Como fizemos quando nosso tipo era definido, estamos criando uma lista que não repete o termo nenhuma vez, retornamos um *List.nil*, depois nós verificamos para o caso de uma lista que repetirá o valor *count* vezes, para isso nós usaremos a recursão por meio do `Nat.succ pred`, isto é, o nosso *count* é igual ao sucessor do predecessor dele. 
```rust
Repeat x (Nat.succ count) = ?
```

E o o *Type Check* nos retorna:
```diff
+ INFO  Inspection.

   • Expected: (List _) 

   • Context: 
   •   x     : _ 
   •   count : Nat 

   Repeat x (Nat.succ count) = ?
                               ┬
                               └Here!
```
Agora basta construir a lista com o valor e chamar a função para o predecessor de count, assim construindo a lista até que chegue a zero.
```rust
Repeat (x: _) (count: Nat) : List _
Repeat x Nat.zero          = List.nil
Repeat x (Nat.succ count)  = List.cons x (Repeat x count)
```

Podemos perceber que, apesar de não definir o tipo de *x*, o *Kind* é poderoso para descobrir o tipo que é nosso x quando usamos o *hole* `_`. Embora seja possível e possa até facilitar construir uma aplicação inteira usando essa notação, não é uma boa prática, já que, a depender do caso, pode ser inferido um tipo diferente do que o desejado. É interessante sempre definir o tipo do nosso elemento, mesmo que seja um tipo polimórico. 

No primeiro caso, quando definimos o tipo `a`, já abarcamos todos os tipos possíveis, não sendo necessário o uso do hole e essa é a mágica do polimorfismo, ele nos permite usar uma mesma função para diversos tipos diferentes.

Para usar uma função polimórfica, nós precisamos passar um ou mais tipos em adição aos outros argumentos. Por exemplo, no caso do *repeat*, nós passamos o tipo `<a: Type>` e que cada elemento da nossa lista é desse tipo. Fizemos o mesmo com o tipo *Pair*, que recebia como argumento dois tipos *a* e *b*. 

Agora fica muito mais fácil compreender os exemplos que usamos no capítulo anterior, quando apresentamos funções como a de *length* e *append*:
```rust
Length <a> (xs: List a) : Nat
Length a (List.nil t)            = Nat.zero
Length a (List.cons t head tail) = (Nat.succ (Length a tail))

App <a: Type> (xs: List a) (ys: List a) : List a
App a (List.nil)                     ys = ys
App a (List.cons head tail)          ys = List.cons a head (App a tail ys)

```
Perceba, há duas notações, uma onde apenas usamos `<a>` e outra onde usamos `<a: Type>`, podemos usar qualquer uma delas, o *Kind* é capaz de compreender as duas formas, será de escolha do desenvolvedor qual ele usará e da complexidade do que será desenvolvido, uma vez que, em códigos muito complexos, talvez seja interessante deixar explícito a outros programadores o que é cada coisa.

Agora é a hora de implementar nossas funçõe com o tipo implícito, usando o `hole` e `sugar syntax`:
```rust
App_implicito (xs: List _) (ys: List _) : List _
App_implicito []                     ys = ys
App_implicito (List.cons head tail)  ys = List.cons head (App_implicito tail ys)
```
Aqui nós aprendemos mais uma coisa, o *sugar syntax* para uma lista vazia e que é apenas `[] `e isso poderia nos induzir a escrever o nosso *List.cons* com `[head, tail]`, mas isso está errado, uma vez que o *head* é um elemento de um tipo e o *tail* é uma lista de elementos desse tipo e, dessa forma, estariamos fazendo uma lista de lista. Ao usar o *sugar syntax* errado, o que o *Kind* esperaria seria:
```bash
- Expected: (List t3_)
- Detected: (List (List t3_))
```

Perceba, ele espera uma lista e recebe uma lista de lista, pois nós declaramos o nosso *tail* como sendo ualgo do tipo t3_ (ignore a escrita do tipo, isso ocorre porque não definimos o tipo e o Kind cria um temporário apenas para retorno da mensagem), o que faz com que ela deixe de ser uma lista, causando essa incongruência no retorno. 

Portanto, é sempre importante saber exatamente o que está sendo feito, ainda mais quando usamos *sugar syntax*, ela serve pra facilitar a nossa vida, mas pode causar alguns problemas quando usada de forma indevida e isso serve igualmente para o *hole* e tipos polimórficos nos auxiliam a excrever um programa mais seguro e, ao mesmo tempo, capaz de servir para inúmeros casos. 

Outra função que podemos reescrever é a de reverse:

```rust
Rev <a> (xs: List a) : List a
Rev a []                    = []
Rev a (List.cons head tail) = App (Rev tail) [head]

Length <a> (xs: List a) : Nat
Length a []                    = 0n
Length a (List.cons head tail) = Nat.succ (Length tail)
```

Feito isso, basta apenas provar que nossas funções são verdadeiras:

```rust
Test_rev1 : Equal (Rev [1,2,3]) [3,2,1]
Test_rev1 = Equal.refl

Test_rev2 : Equal (Rev [Bool.true]) [Bool.true]
Test_rev2 = Equal.refl

Test_length1 : Equal (Length [1,2,3]) 3n
Test_length1 = Equal.refl
```

1.1.3

Aqui estão alguns exercícios simples, assim como os do capítulo Listas, para praticar com polimorfismo. Complete as provas abaixo.

```rust
App_nil_r <a> (xs: List a) : Equal (App xs List.nil) xs
App_nil_r xs = ?

App_assoc <a> (xs: List a) (ys: List a) (zs: List a) : Equal (App xs (App ys zs)) (App (App xs ys) zs)
App_assoc xs ys zs = ?

App_length <a> (xs: List a) (ys: List a) : Equal (Length (App xs ys)) (Plus (Length xs) (Length ys))
App_length xs ys = ?
```

1.1.4

Aqui estão alguns um pouco mais interessantes...

```rust
Rev_app_distr <a> (xs: List a) (ys: List a) : Equal (Rev (App xs ys)) (App (Rev ys) (Rev xs))
Rev_app_distr xs ys = ?

Rev_involutive <a> (xs: List a) : Equal (Rev (Rev xs)) xs
Rev_involutive xs = ?
```

1.2 Pares polimórficos 

Seguindo o mesmo padrão, a definição de tipo
que demos no último capítulo para pares de números podem ser generalizados para polimórficos
pares:

```rust
type Pair <a: Type> <b: Type> {
   new (fst: a) (snd: b)
}  
```

Essa é exatamente a primeira definição de pares que vimos no capítulo anterior e, agora, podemos compreender perfeitamente o que são os tipos `a` e `b` na definição do tipo *Pair*.

Nós podemos refazer as funções de *Pares*, mas agora para tipos polimórficos:

```rust
Fst <a> <b> (pair: Pair a b) : a
Fst (Pair.new fst snd) = fst

Snd <a> <b> (pair: Pair a b) : b
Snd (Pair.new fst snd) = snd
```

A seguinte função recebe duas listas e combina elas numa lista de pares. Nas linguagens funcionais, isso é comumente chamada de *Zip*. 

```rust
Zip <a> <b> (xs: List a) (ys: List b) : (List (Pair a b))
Zip [] ys = []
Zip xs [] = []
Zip (List.cons xs.h xs.t) (List.cons ys.h ys.t) = List.cons (Pair.new xs.h xs.t) (Zip xs.t ys.t)
```

Exercício 1.2.1
Sem rodar o programa, tente responder a seguinte pergunta:
- O que a combinação de [1, 2] e [Bool.true, Bool.false, Bool.false, Bool.true] retornará? 
 
Agora rode o código e veja se acertou. 

Exercício 1.2.2
A função *Split* é o inverso da *Zip*, ela recebe uma lista de pares e retorna um par de listas. Em muitas linguagens funcionais ela é chamada de *Unzip*. 

Preencha a definição de divisão abaixo. Certifique-se de que ela passe no teste unitário fornecido.

```rust
Split <a> <b> (xs: List (Pair a b)) : Pair (List a) (List b)
Split xs = ?

Test_split : Equal (Split [(Pair.new 1 Bool.false), (Pair.new 2 Bool.false)]) (Pair.new ([1, 2]) ([Bool.false, Bool.false]))
Test_split = ?
```

Exercício 1.2.3 
Maybe polimórfico. No capítulo anterior, nós também vimos o tipo *Maybe*, só que para tipos naturais, entretanto, como vimos no capítulo atual, nossas estruturas de dados podem ser polimórficas, o que significa que o tipo *Maybe* também é polimórfico e é isso o que veremos agora.

```rust
Maybe <a: Type> : Type
Maybe.none <a> : (Maybe a)
Maybe.some <a> (value: a) : (Maybe a)
```

Dessa forma, podemos escrever a função do *enésimo* erro para ele ser usado com todos os tipos de listas:

```rust
Nth_error <a> (n: Nat) (xs: List a) : Maybe a
Nth_error a n List.nil              = Maybe.none
Nth_error a n (List.cons head tail) =
  let ind = Nth_error (Pred n) tail
  Bool.if (Eql n 0n) (Maybe.some head) (ind)


Test_nth_error1 : Equal (Nth_error 0n [4n,5n,5n,7n]) (Maybe.some 4n)
Test_nth_error1 = Equal.refl

Test_nth_error2 : Equal (Nth_error 2n [Bool.true]) Maybe.none
Test_nth_error2 = Equal.refl

Test_nth_error3 : Equal (Nth_error 1n [[1n],[2n]]) (Maybe.some [2n])
Test_nth_error3 = Equal.refl

```

1.2.4
Complete a definição de
uma versão polimórfica da função hd_error do último capítulo. Certifique-se de que ele passe nos testes de unitários abaixo.

```rust
Hd_error <a> (xs: Lista a) : Maybe a
Hd_error xs = ?

Test_hd_error1 : Equal (Hd_error [1, 2]) (Maybe.some 1)
Test_hd_error1 = ?

Test_hd_error2 : Equal (Hd_error [[1], [2]]) (Maybe.some [1])
Test_hd_error2 = ?
```


### Funções como dados

Como muitas outras linguagens de programação modernas – incluindo todas as linguagens funcionais (ML, Haskell, Scheme, Scala, Clojure etc.) – o Kind trata as funções como cidadãos de primeira classe, permitindo que sejam passadas como argumentos para outras funções,
retornados como resultados, armazenados em estruturas de dados, etc.

#### 2.1 Funções de Alta Ordem (Higher-Order Functions.)

Funções que manipulam outras funções são frequentemente chamadas de funções de alta ordem (ou ainda de "ordem superior"). Aqui está um exemplo simples:

```rust
Doit3times <x> (f: x -> x) (n: x) : x
Doit3times f x = (f (f (f x)))

Test_doit3times1 : Equal (Doit3times (x => MinusTwo x) 9n) 3n
Test_doit3times1 = Equal.refl

Test_doit3times2 : Equal (Doit3times (x => Notb x) Bool.true) (Bool.false)
Test_doit3times2 = Equal.refl
```

#### 2.2 Filtro
Aqui está uma função de alta ordem mais útil, pegando uma lista de xs e um predicado em x (uma função de x para Bool) e “filtrando” a lista, retornando uma nova lista contendo apenas aqueles elementos para os quais o predicado retorna True.

```rust
Filter <x> (test: x -> Bool) (xs: List x) : List x
Filter test List.nil                      = []
Filter test (List.cons head tail)         =
   Bool.if (test head) (List.cons head (Filter test tail)) (Filter test tail)
```

Por exemplo, se aplicarmos o filtro de "é par" numa lista de números, ela nos retornará uma outra lista apena com os números pares

```rust
Test_filter1 : Equal (Filter (x => Evenb x) [1,2,3,4,5])  [2,4]  
Test_filter1 = Equal.refl

Length_is_one <x> (xs: List x) : Bool
Length_is_one xs               = Eql (Length xs) 1n

Test_filter2 : Equal (Filter (x => Length_is_one x) ([[1],[1,2],[3],[1,2,3],[21]])) ([[1],[3],[21]])
Test_filter2 = Equal.refl
```

Podemos usar filter para fornecer uma versão concisa da função countoddmembers do capítulo Listas.

```rust
CountOddMembers (xs: List Nat) : Nat
CountOddMembers xs             = Length (Filter (x => Oddb x) xs)

Test_CountOddMembers1 : Equal (CountOddMembers  [1n,0n,3n,1n,4n,5n]) 4n
Test_CountOddMembers1 = Equal.refl

Test_CountOddMembers2 : Equal (CountOddMembers  [0n 2n,4n]) 0n
Test_CountOddMembers2 = Equal.refl

Test_CountOddMembers3 : Equal (CountOddMembers []) 0n
Test_CountOddMembers3 = Equal.refl
```


#### 2.3. Funções anônimas. 
É indiscutivelmente um pouco triste, no exemplo acima, ser forçado a definir a função *Length_is_one* e dar-lhe um nome apenas para poder passá-la como um argumento para filtrar, já que provavelmente nunca a usaremos novamente.
Além disso, este não é um exemplo isolado: ao usar funções de ordem superior, muitas vezes queremos passar como argumentos funções “únicas” que nunca mais usaremos; ter que dar um nome a cada uma dessas funções seria tedioso.
Felizmente, existe uma maneira melhor. Podemos construir uma função “on the fly” sem declará-la no nível superior ou dar-lhe um nome.

```rust
Test_anon_fun : Equal (Doit3times (x => (Mult x x)) 2n) 256n
Test_anon_fun = Equal.refl
```

A expressão `x => (Mult x x)` pode ser lida como *a função recebe um número `n` e retorna `n * n`* 

Aqui está o exemplo de *Filter* reescrita pra usar uma função anonima:

```rust
Test_filter2 : Equal (Filter (x => (Length_is_one x)) [[1],[1,2],[2],[1,2,3],[21]]) [[1],[2],[21]]
Test_filter2 = Equal.refl

```

2.3.1. Exercício:(filter_even_gt7). Use *Filter* com funções anônimas (em vez de definição de função) para escrever uma função filter_even_gt7 que recebe uma lista de números naturais como entrada e retorna uma lista apenas daqueles que são pares e maiores que 7.

```rust
Filter_even_gt7 (xs: List Nat) : List Nat
Filter_even_gt7 xs = ?

Test_filter_even_gt7a: Equal (Filter_even_gt7 [1n,2n,3n,4n,5n,7n,8n,9n,10n,11n,12n]) [8n,10n,12n]
Test_filter_even_gt7a = ?

Test_filter_even_gt7b : Equal (Filter_even_gt7 [5n, 2n, 6n, 19n, 129n]) []
Test_filter_even_gt7b = ?
```

Uma pequena observação, o leitor mais atento percebeu que usamos uma nova notação, os *n* após os números, esse é um *sugar syntax* que o *Kind* possui, nós podemos escrever números naturais apenas acrescentando um *n* ao número, entretanto essa é uma sintaxe que pode acabar pesando para Kind. Imagine que o usuário pretende apenas adicionar o número *1* ao *1000000*, é um cálculo simples e que o Kind faz com uma mão nas costas, mas fica um pouco mais pesado quando usado o *sugar syntax* dos números naturais, a soma será um `Plus 1n 1000000n`, mas o *Kind* precisará verificar cada *Nat.succ* até o *um milhão e um*, ou seja, serão *um milhão e um* "Nat.succs" computados de forma desnecessárias. Essa sintaxe é bem útil, mas devemos usar com cuidado, o ideal é que para números grandes seja usado um *U60.to_nat*, que é bem mais leve para o Kind.

2.3.2 Use *Filter* para escrever uma função *Partition* em *Kind*

```rust
Partition <x> (test: x -> Bool) (xs: List x) : Pair (List x) (List x)
Partition test xs = ?
```

Dado um conjunto x, uma função de teste do tipo x -> Bool e uma Lista x, a função Partition deve retornar um par de listas. O primeiro membro do par é a sublista da lista original contendo os elementos que satisfazem o teste, e o segundo é a sublista contendo aqueles que falham no teste. A ordem dos elementos nas duas sublistas deve ser a mesma da lista original.

```rust
Test_partition1 : Equal (Partition (x => Oddb x) [1n,2n,3n,4n,5n]) (Pair.new [1n,3n,5n] [2n,4n])
Test_partition1 = ?

Test_partition2 : Equal (Partition (x => Bool.false) [5n, 9n, 0n]) (Pair.new [] [5n, 9n, 0n])

```

2.4 
Oufra função de alta ordem muito útil é a *Map*

```rust
Map <x> <y> (f: x -> y) (xs: List x)  : List y
Map f List.nil                        = List.nil
Map f (List.cons head tail)           = List.cons (f head) (Map f tail)
```

Ela recebe uma função `f` e uma lista `xs = [n1, n2, n3, ...]` e retorna a lista `[f n1, f n2, f n3, ...]`, onde `f` é aplicado a cada elemento de `xs`. Por exemplo:

```rust
Test_map1 : Equal (Map (x => Nat.add 3n x) [2n, 0n, 2n]) [5n, 3n, 5n]
Test_map1 = Equal.refl
```

Os tipos de elementos da lista de entrada e saída não precisam ser os mesmos, pois *Map* aceita dois argumentos de tipo, `x` e `y`; dessa forma pode ser aplicada uma de números para booleanos para produzir uma lista de booleanos:

```rust

Test_map2 : Equal (Map (x => Nat.is_odd x) [2n, 1n, 2n, 5n]) [Bool.false, Bool.true, Bool.false, Bool.true]
Test_map2 = Equal.refl
```
Pode até ser aplicada a uma lista de números uma função que retorne uma lista de lista de booleanos:

```rust
Test_map3 = Equal (Map (x => [(Nat.is_even x), (Nat.is_odd x)]) [2n, 1n, 2n, 5n]) [[Bool.true, Bool.false], [Bool.false, Bool.true], [Bool.true, Bool.false], [Bool.false, Bool.true]]
Test_map3 = Equal.refl
```

2.4.1
Vamos dificultar um pouco mais as coisas. Mostre a comutatividade de Rev e Map, você pode precisar de uma função auxiliar: 

```rust
Map_rev <x> <y> (f: x -> y) (xs: List x) : Equal (Map f (Rev xs)) (Rev (Map f xs))
Map_rev f xs = ?
```

2.4.2
A função *Map* mapeia uma ``List x`` para uma ``List y`` usando uma função do tipo ``x -> y``. Podemos definir uma função semelhante, `Flat_map`, que mapeia uma Lista x para uma Lista y usando uma função f do tipo ``x -> Lista y``. Sua definição deve funcionar "achatando" os resultados de f, assim:

```rust
Flat_equal : Equal (Flat_map ( x => ([x , (Nat.add x 1n), (Nat.add x 2n)])) [1n, 5n, 10n]) [1n, 2n, 3n, 5n, 6n, 7n, 10n, 11n, 12n]
Flat_equal = Equal.refl

Flat_map <x> <y> (f: x -> List y) (xs: List x) : List y
Flat_map f xs = ?

Test_flat_map1 : Equal (Flat_map (x => [x, x, x]) [1n, 5n, 4n]) [1n, 1n, 1n, 5n, 5n, 5n, 4n, 4n, 4n]
Test_flat_map1 = ?
```

As listas não são o único tipo indutivo para o qual podemos escrever uma função *Map*. Aqui está a definição de mapa para o tipo Maybe:

```rust
Maybe_map <x> <y> (f: x -> y) (a: Maybe x)  : Maybe y
Maybe_map f Maybe.none                      = Maybe.none
Maybe_map f (Maybe.some x)                  = Maybe.some (f x)
```

2.5 Fold
Uma função de ordem superior ainda mais poderosa é chamada *Fold*. Esta função é a inspiração para a operação “reduce” que está no coração da estrutura de programação distribuída map/reduce do Google.

```rust
Fold <x> <y> (f: x -> y -> y) (xs: List x) (a: y)   : y
Fold f List.nil a                                   = a
Fold f (List.cons head tail) a                      = f head (Fold f tail a)

Test_fold1 : Equal (Fold (x => y => (Bool.and x y)) [Bool.true, Bool.true, Bool.false] Bool.false) Bool.false
Test_fold1 = ?

Test_fold2 : Equal (Fold (x => y => (* x y)) [1, 2, 3, 4] 1) 24
Test_fold2 = ?

Test_fold3 : Equal (Fold (x => y => (List.concat x y)) [[1], [], [2, 3], [], [4]] [5, 6, 7]) [1, 2, 3, 4, 5, 6, 7]
Test_fold3 = ?
```

2.5.1
Observe que o tipo *Fold* é parametrizado por duas variáveis de tipo, x e y, e o parâmetro f é um operador binário que recebe um x e um y e retorna um y. Você consegue pensar em uma situação em que seria útil que x e y fossem diferentes?


2.6 Funções que constroem funções. 
A maioria das funções de ordem superior sobre as quais falamos até agora usam funções como argumentos. Vejamos alguns exemplos que envolvem o retorno de funções como resultados de outras funções. Para começar, aqui está uma função que recebe um valor x (extraído de algum tipo x) e retorna uma função de Nat para x que retorna x sempre que é chamada, ignorando seu argumento Nat

```rust
Constfun <y> (x: y) : Nat -> y
Constfun x = y => x

Ftrue : Nat -> Bool
Ftrue = Constfun Bool.true

Constfun_example1 : Equal ((Ftrue) 0n) Bool.true
Constfun_example1 = Equal.refl

Constfun_example2 : Equal ((Constfun 5n) 99n) 5n
Constfun_example2 = Equal.refl
```
<!-- Na verdade, as funções de múltiplos argumentos que já vimos também são exemplos de passagem de funções como dados. Para ver por que, lembre-se do tipo de adição -->
<!-- [>  <] -->
<!-- [> # TODO: <] -->
<!-- [>  <] -->
<!-- ```rust -->
<!-- Plus Nat -> Nat : Nat -->
<!-- ``` -->
<!-- [> Cada "->" nesta expressão é, na verdade, um operador binário em tipos. Este operador é associativo à direita, então o tipo de plus é realmente uma abreviação para Nat -> (Nat -> Nat) – ou seja, pode ser lido como dizendo que “plus é uma função de um argumento que pega um Nat e retorna uma função de um argumento que pega outro Nat e retorna um Nat.” Nos exemplos acima, sempre aplicamos mais a ambos os argumentos ao mesmo tempo, <] -->
<!-- [> mas se quisermos, podemos fornecer apenas o primeiro. Isso é chamado de aplicação parcial <] -->
<!-- [>  <] -->
<!-- [> # fim to todo <] -->
<!--  -->
```rust
Plus3 (n: Nat) : Nat
Plus3 n = Plus 3n n

Test_plus3_1 : Equal (Plus3 4n) 7n
Test_plus3_1 = Equal.refl

Test_plus3_2 : Equal (Doit3times (x => Plus3 x) 0n) 9n
Test_plus3_2 = Equal.refl

Test_plus3_3 : Equal (Doit3times (x => Plus 3n x) 0n) 9n
Test_plus3_3 = Equal.refl
```
### Exercícios adicionais
#### 3.0.1
Muitas funções comuns em listas podem ser implementado em termos de *Fold*. Por exemplo, aqui está uma definição alternativa de comprimento:

```rust
Fold_length <x> (xs: List x) : Nat
Fold_length xs = Fold (x => y Nat.succ y) xs 0n

Test_fold_length1 : Equal (Fold_length [4, 7, 0]) 3n
Test_fold_length1 = Equal.refl
```

Prove a validade de *Fold_length*:
```rust
Fold_length_correct <x> (xs: List x) : Equal (Fold_length xs) (List.length xs)
Fold_length_correct xs = ?
```

#### 3.0.2
Nós talmbém podemos definir *Map* nos termos de *Fold*. Termine a função:
```rust
Fold_map <x> <y> (f: x -> y) (xs: List x) : List y
Fold_map f xs = ?
```
Escreva um teorema fold_map_correct em Kind declarando que *Fold_map* está correto e prove isso:

```rust 
Fold_map_correct : ?
```


<!-- TODO: Traduzir exercícios do Savio -->
<!-- #### 3.0.3 -->



<!-- #### 3.0.4 -->

#### 3.0.3
Nós podemos explorar uma forma alternativa de definir os números naturais, usando os *Numerais de Church*, nomeado em homenagem ao matemático Alonzo Church. Podemos representar um número natural n como uma função que recebe uma função `s` como parâmetro e retorna `s` iterado n vezes.

```rust
Num <x> : Type
Num x   = (x -> x) -> x -> x
```

Vamos ver como escrever alguns números com esta notação. Iterar uma função uma vez deve ser o mesmo que apenas aplicá-la. Desta forma:

```rust
One : Num
One = s => z => s z 
```
Perceba, a função aplica um `s` a um `z`, se lermos o `s` como *sucessor* e o `z` como *zero*, temos que o *One* é igual ao *sucessor de zero*.

Similarmente, o *Two* aplica a função `s` duas vezes ao `z`:

```rust
Two : Num
Two = s => z => s (s z)
```
Definir zero é um pouco mais complicado: como podemos “aplicar uma função zero vezes”? A resposta é realmente simples: basta retornar o argumento intocado.

```rust
Zero : Num
Zero = s => z => z
```

Mais geralmente, um número n pode ser escrito como `s => z => s (s ... (s z) ...)`, com n ocorrências de `s`. Observe em particular como a função doit3times que definimos anteriormente é, na verdade, apenas a representação de Church do 3.

```rust
Three : Num
Three = s => z => Doit3times s z
```

Complete as definições das seguintes funções. Certifique-se de que os testes unitários correspondentes sejam aprovados, provando-os com *Equal.refl*

```rust
Succ (n: Num)   : Num
Succ n          = ?

Succ_1 : Equal (Succ Zero) (One)
Succ_1 = ?

Succ_2 : Equal (Succ One) (Two)
Succ_2 = ?

Succ_3 : Equal (Succ Two) (Three)
Succ_3 = ?
```

# Capítulo 6: 
## Lógica em Kind

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


### 1. Conectivos Lógicos
#### 1.1. Conjunção. 
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

##### 1.1.1 Exercício:
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

#### 1.1.3
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

