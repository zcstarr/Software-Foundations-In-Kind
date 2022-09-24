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

```bash
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


```bash
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

### Outrio caso

Vamos verificar se a a igualdade "n +(*m* + 1) = 1 + (*n* + *m*)" é verdadeira

Primeiro, o nosso problema:
``````rust
Problems.t2 (n: Nat) (m: Nat)  : (Equal Nat (Nat.add n (Nat.succ m)) (Nat.succ(Nat.add n m))) 
``````
Verificamos o primeiro caso, quando *n* é zero:
```rust
Problems.t2 Nat.zero m         = Equal.refl
``````
e partimos para o caso seguinte
```rust
Problems.t16 (Nat.succ n) m     = ?
``````

e o nosso objetivo atual vira:
```bash
Goal: (Equal Nat (Nat.succ (Nat.add n (Nat.succ m))) (Nat.succ (Nat.succ (Nat.add n m))))
`````````

Traduzindo, o sucessor da adição de *n* e o sucessor de *m* é igual ao
sucessor do sucessor da adição de *n* e *m*. Para resolver esse problema,
invocaremos a indução:

```rust
let ind = Problems.t16 n m
`````````
e o nosso objetivo atual é provar que:
```rust
Goal: (Equal Nat (Nat.succ (Nat.add n (Nat.succ m))) (Nat.succ (Nat.succ (Nat.add n m))))
`````````
Traduzindo novamente, que o *sucessor* da adição de *n* e o *sucessor* de *m* é igual ao *sucessor* do *sucessor* da adição de *n* e *m*. 

mas agora nós temos uma ferramenta muito útil, a nossa variável ind que é:
```rust
(Equal Nat (Nat.add n (Nat.succ m)) (Nat.succ (Nat.add n m)))
``````
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
Problems.t3 (n: Nat) (m: Nat)      : (Equal Nat (Nat.add n  m) (Nat.add m n))```

No primeiro caso, para *n* e *m* igual a zero nós temos uma reflexão:
```rust
Problems.t3 Nat.zero Nat.zero      = Equal.refl
```
Então partimos para o próximo caso:
```rust
Problems.t3 (Nat.succ n) m         = ?
```

E aqui parece que temos um novo problema: 
``````rust
Goal: (Equal Nat (Nat.succ (Nat.add n m)) (Nat.add m (Nat.succ n)))

```
Ao analisar o problema, percebemos que dentro dele há um teorema já provado, de
que o *sucessor* da adição de dois números é igual a adição de um número com o
*sucessor* dele, então podemos usar isso ao nosso
favor.

Começaremos aplicando um *Nat*.**succ** no nosso problema original:
```rust
 let inda    = (Equal.apply (x => (Nat.succ x)) (Problems.t3 n m ))```


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
let indc    = Equal.chain indb (Equal.mirror inda)```


E o indc nos retorna um valor similar ao desejado:
```rust
Goal: (Equal Nat (Nat.succ (Nat.add n m)) (Nat.add m (Nat.succ n)))
indc : (Equal Nat (Nat.add m (Nat.succ n)) (Nat.succ (Nat.add n m)))
```

Podemos perceber que um é o outro espelhado, para torná-los iguais, usaremos o
*Equal*.**mirror** novamente:
```rust
let app     = Equal.mirror indc```


Ao chamar o *app* o *Type Check* nos retorna a mensagem *All terms check* e
desta forma provamos, por meio da indução e usando uma outra prova, a comutação
da adição, ou seja, que a soma de *n* e *m* é igual a soma de *m* e *n*.



