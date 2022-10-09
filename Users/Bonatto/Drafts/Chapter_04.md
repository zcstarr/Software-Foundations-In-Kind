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

Já que o Kind espera 
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

1.1
```rust
Snd_fst_is_swap (p: Pair Nat Nat )            : (Equal (Pair Nat Nat) (Pair.swap Nat Nat (Pair.swap Nat Nat p)) p)
Snd_fst_is_swap (Pair.new Nat Nat fst snd)    = ? 
```

1.2
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
