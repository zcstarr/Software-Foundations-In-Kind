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


## Usando outros teoremas

Em Kind, como na matemática informal, grandes provas são frequentemente divididas em uma sequência de
teoremas, com provas posteriores referindo-se a teoremas anteriores. Mas às vezes uma prova
exigirá algum fato variado que é muito trivial e de muito pouco geral
interesse em dar-lhe o seu próprio nome de nível superior. Nesses casos, é conveniente
ser capaz de simplesmente enunciar e provar o “sub-teorema” necessário exatamente no ponto
onde é usado.

Analisemos o seguinte teorema: 

```rust
Problems.t2 (n: Nat)  (m: Nat):     (Equal Nat (Nat.add (Nat.add n Nat.zero) m) (Nat.add n m))
```

Ao analisar o problema, percebemos que dentro dele há um teorema já provado, de
que a adição entre *n* e *zero* é igual a *n*, então podemos usar isso ao nosso
favor
```
