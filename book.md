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

