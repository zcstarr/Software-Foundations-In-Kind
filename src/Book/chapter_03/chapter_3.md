# Indução: Prova por Indução
## módulo de indução

Nesse capítulo nós veremos sobre provas por indução, mas antes de prosseguirmos
para a indução em si, podemos analisar casos simples onde apenas a reflexão do
caso já prova o teorema.

```rust
Problems.t0 (n: Nat) : Equal Nat (Plus Nat.zero n) n)
```

Ao verificar verificar o objetivo do teorema, recebemos a seguinte resposta:

![mensagem de erro](../../../Imgs/problemst0.png)

No *Problems.t0* o Kind reduz a soma de "*0 + n*" automaticamente para *n* e
que devemos provar a igualdade entre *n* e *n*. Nesse caso basta escrever "*Equal*.**refl**" e obtemos a resposta de
confirmação: 

"*All terms check.*"

```rust
Problems.t1 (n: Nat) : Equal Nat (Plus n Nat.zero) n)
```

Feito o primeiro problema, o seguinte é muito similar, é a soma de "*n + 0 = n*" e essa similaridade pode nos levar a crer que basta invocar a reflexão. Entretanto, no primeiro caso o Kind reduz automaticamente e nesse nós obtemos o seguinte retorno:

![Problems.t1](../../../Imgs/problemst1.png)

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
• Expected: Equal Nat Nat.zero Nat.zero)
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
- Expected: Equal Nat (Nat.succ (Plus n Nat.zero)) (Nat.succ n))
```

Para trabalhar com a indução nessa recursão, devemos definir uma variável para
o caso original de *n*

```rust
Problems.t1 (n: Nat)       : Equal (Plus n Nat.zero) n)
Problems.t1 Nat.zero       = Equal.refl
Problems.t1 (Nat.succ n)   =
    let ind = Problems.t1 n
    ?
```

Ao dar o *Type Check* temos como retorno a seguinte resposta:

![indução no problems.t1](../../../Imgs/problemst1-ind.png)

Ao analizar nosso objetivo e a indução, percebemos que a única diferença entre
o objetivo e a nossa variável *ind* é o *Nat*.**succ**, basta então
incrementar a variável *ind* com o *Nat*.**succ**, para isso nós criamos uma
nova variável e usamos uma função *lambda*:

```rust
let app = Equal.apply (x => (Nat.succ x)) ind)
```

No caso acima nós chamamos a função *Equal*.**apply** para aplicar a nossa
função *lambda* ao *ind*. A função `x => (Nat.succ x)` serve para adicionar
*Nat*.**succ** a todo elemento recebido na variável. Como nossa variável *ind*
é uma função que recebe uma outra variável *n*, a nossa função *lambda*
incrementa a *n* com *Nat*.**succ**, o que retorna exatamente o nosso
objetivo:

![Aplicação na indução do Problems.t1](../../../Imgs/problemst1-app.png)

Podemos perceber que o *app* é exatamente igual ao *Expected*, que é o nosso
objetivo e basta apenas retornar ele, o app para que o *Type Check* valide
a nossa prova:
*All terms check.*

Há casos em que a indução é ainda mais simples, basta compreender o que está acontecendo. Imagine que você quer provar que um número `n` menos ele mesmo é igual a *zero*, independente de qual seja esse número. Como faríamos?
Primeiro, nós verificamos para o caso dele ser *zero* e é uma igualdade verdadeira, *zero* menos *zero* é igual a *zero*. Depois, nós induzimos o caso para o caso de *zero*, que sabemos ser verdadeiro. Parece complicado? Não é, é absurdamente simples, vamos ver como fica isso em *Kind*:

```rs
Minus_diag (n: Nat)     : Equal Nat (Minus  n n) Nat.zero
Minus_diag Nat.zero     = Equal.refl
Minus_diag (Nat.succ n) = Minus_diag n
```

Perceba, essa é uma indução simples, nós falamos que a prova vale para o número e o antecessor dele e, por usarmos uma recursão, para todos os antecessores até *zero*, que é o caso que verificamos ser verdadeiro.
Ou seja, provamos, em apenas três linhas, que um número natural menos ele mesmo sempre dará *zero*, independente de qual for esse número.

### 3.1 Exercícios 

Prove o seguinte usando indução. Você pode precisar de resultados previamente comprovados.

```rust
Mult_0_r (n: Nat) : Equal Nat (Mult n Nat.zero) Nat.zero
Mult_0_r n = ?

Plus_n_sm (n: Nat) (m: Nat) : Equal Nat (Nat.succ (Plus n m)) (Plus n (Nat.succ m))
Plus_n_sm n m = ?

Plus_comm (n: Nat) (m: Nat) : Equal Nat (Plus n m) (Plus m n)
Plus_comm n m = ?

Add_0_r (n: Nat) : Equal Nat (Plus n Nat.zero) n
Add_0_r n = ?

Plus_assoc (n: Nat) (m: Nat) (p: Nat) : Equal Nat (Plus n (Plus m p)) (Plus (Plus n m) p)
Plus_assoc n m p = ?
```

Considere a seguinte função que dobra o argumento recebido
```rust
Double (n: Nat)     : Nat
Double Nat.zero     = Nat.zero
Double (Nat.succ n) = Nat.succ (Nat.succ (Double n))
```

Use indução para provar esses seguintes teoremas sobre *Double*:
```rust
Double_plus (n: Nat) : Equal Nat (Double n) (Plus n n)
Double_plus n = ?
```

Alguns teoremas é necessário analisar a melhor forma de se provar, por exemplo, para provar que um numero é par, poderiamos provar pelo sucessor dele, mas isso nos faria ter que provar para o sucessor do succesor dele, isso faz com a que a prova de *evenb* ser mais difícil por indução, então é importante perceber quando é necessário e quando não é.
```rust
Evenb_s (n: Nat) : Equal Bool (Evenb (Nat.succ n)) (Notb  (Evenb n))
Evenb_s n = ?
```

## Outro caso

Vamos verificar se a a igualdade "n +(*m* + 1) = 1 + (*n* + *m*)" é verdadeira

Primeiro, o nosso problema:
```rust
Problems.t2 (n: Nat) (m: Nat) : Equal Nat (Plus n (Nat.succ m)) (Nat.succ(Plus n m))) 
```

Verificamos o primeiro caso, quando *n* é zero:
```rust
Problems.t2 Nat.zero m = Equal.refl
```

e partimos para o caso seguinte
```rust
Problems.t2 (Nat.succ n) m = ?
```

e o nosso objetivo atual vira:
```rust
• Expected: Equal Nat (Nat.succ (Plus n (Nat.succ m))) (Nat.succ (Nat.succ (Plus n m))))
```

Traduzindo, o sucessor da adição de *n* e o sucessor de *m* é igual ao
sucessor do sucessor da adição de *n* e *m*. Para resolver esse problema,
invocaremos a indução:
```rust
let ind = Problems.t2 n m
```

e o nosso objetivo atual é provar que:
```rust
• Expected: Equal Nat (Nat.succ (Plus n (Nat.succ m))) (Nat.succ (Nat.succ (Plus n m))))
```

Traduzindo novamente, que o *sucessor* da adição de *n* e o *sucessor* de *m* é igual ao *sucessor* do *sucessor* da adição de *n* e *m*. 

mas agora nós temos uma ferramenta muito útil, a nossa variável ind que é:
```rust
Equal Nat (Plus n (Nat.succ m)) (Nat.succ (Plus n m)))
```

Ora, analisando o nosso objetivo e a nossa variável ind, podemos perceber que
basta dar um *Nat*.**succ** em ambos os lados da indução e ela ficará
exatamente igual ao nosso objetivo, para isso usaremos uma função
*lambda*:
```rust
let app = Equal.apply (x => (Nat.succ x)) ind)
```

E a nossa variável *app* retornará o nosso objetivo:
```rust
Equal Nat (Nat.succ (Plus n (Nat.succ m))) (Nat.succ (Nat.succ (Plus n m))))
```

Bastando apenas retornar o *app* para e o Kind nos retornará o tão almejado
*All terms check*.


# Usando outros teoremas

Em Kind, como na matemática informal, grandes provas são frequentemente divididas em uma sequência de
teoremas, com provas posteriores referindo-se a teoremas anteriores. Mas às vezes uma prova
exigirá algum fato variado que é muito trivial e de muito pouco geral
interesse em dar-lhe o seu próprio nome de nível superior. Nesses casos, é conveniente
ser capaz de simplesmente enunciar e provar o “sub-teorema” necessário exatamente no ponto
onde é usado.

Analisemos o seguinte teorema da comutação da adição: 
```rust
Problems.t3 (n: Nat) (m: Nat) : Equal Nat (Plus n  m) (Plus m n))
```
No primeiro caso, para *n* e *m* igual a zero nós temos uma reflexão:
```rust
Problems.t3 Nat.zero Nat.zero = Equal.refl
```
Então partimos para o próximo caso:
```rust
Problems.t3 (Nat.succ n) m = ?
```

E aqui parece que temos um novo problema: 
```rust
Expected: Equal Nat (Nat.succ (Plus n m)) (Plus m (Nat.succ n)))
```
Ao analisar o problema, percebemos que dentro dele há um teorema já provado, de
que o *sucessor* da adição de dois números é igual a adição de um número com o
*sucessor* dele, então podemos usar isso ao nosso
favor.

Começaremos aplicando um *Nat*.**succ** no nosso problema original:
```rust
let ind_a = Equal.apply (x => (Nat.succ x)) (Problems.t3 n m ))
```

Depois invocaremos nosso problema já resolvido, o *Problems*.**t2**:
```rust 
let ind_b = Problems.t2 m n
```

Ao dar o *Type Check*, o terminal nos retorna:

![Problems.t3](../../../Imgs/problemst3.png)

Agora podemos perceber que a primeira parte da *ind_a* é igual ao inverso da
primeira parte do nosso
objetivo e a primeira parte da *ind_b* é igual a segunda do objetivo, basta
apenas organizar e juntar as partes necessárias. Para isso usaremos a
*Equal*.**mirror** e a *Equal*.**chain**.
```rust
let ind_c = Equal.chain ind_b Equal.mirror ind_a)
```

E o ind_c nos retorna um valor similar ao desejado:
```rust
• Expected: Equal Nat (Nat.succ (Plus n m)) (Plus m (Nat.succ n)))
•   ind_c : Equal Nat (Plus m (Nat.succ n)) (Nat.succ (Plus n m)))
```

Podemos perceber que um é o outro espelhado, para torná-los iguais, usaremos o
*Equal*.**mirror** novamente:
```rust
let app = Equal.mirror ind_c
```

Ao chamar o *app* o *Type Check* nos retorna a mensagem *All terms checked* e
desta forma provamos, por meio da indução e usando uma outra prova, a comutação
da adição, ou seja, que a soma de *n* e *m* é igual a soma de *m* e *n*.
 
## 3.2 Mais exercícios

Você pode usar a *rewrite* ou *chain* nessa prova, escolha o que achar mais fácil
```rust
Plus_swap (n: Nat) (m: Nat) (p: Nat) : Equal Nat (Plus n (Plus m p)) (Plus m (Plus n p))
Plus_swap n m p = ?
```

Agora prove a comutatividade da multiplicação. (Você provavelmente precisará definir e provar um teorema auxiliar separado para ser usado na prova deste. Você pode descobrir que *Plus_swap* é útil.

```rust
Mult_comm (n: Nat) (m: Nat) : Equal Nat (Mult n m) (Mult m n)
Mult_comm n m = ?
```

Pegue um pedaço de papel. Para cada um dos teoremas a seguir, primeiro pense se (a) pode ser provado usando apenas simplificação e reescrita, (b) também requer análise de caso (destruição) ou (c) também requer indução. Anote sua previsão.
Em seguida, preencha a prova. (Não há necessidade de entregar seu pedaço de papel; isso é apenas para incentivá-lo a refletir antes de hackear!)

```rust
Lte_refl (n: Nat) : Equal Bool Bool.true (Lte n n)
Lte_refl n = ?

Zero_nbeq_s (n: Nat) : Equal Bool (Eql (Nat.zero) (Nat.succ n)) Bool.false
Zero_nbeq_s n = ?

And_false_r (b: Bool) : Equal Bool (Andb b Bool.false) Bool.false
And_false b = ?

S_nbeq_0 (n: Nat) : Equal Bool (Eql (Nat.succ n) Nat.zero) Bool.false

Mult_1_l (n: Nat) : Equal Nat (Mult (Nat.succ Nat.zero) n) n
Mult_1_l n = ?

All3_spec (b: Bool) (c: Bool) : Equal Bool (Orb (Orb (Andb b c) (Notb  b)) (Notb  c)) Bool.true
All3_spec b c = ?

Mult_plus_distr_r (n: Nat) (m: Nat) (p: Nat) : Equal Nat (Mult (Plus n m) p) (Plus (Mult n p) (Mult m p))
Mult_plus_distr_r n m p = ?

Mult_assoc (n: Nat) (m: Nat) (p: Nat) : Equal Nat (Mult (Mult m p)) (Mult (Mult n m) p)
Mult_assoc n m p = ?
```
