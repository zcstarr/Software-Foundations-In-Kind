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
   }  ```


A forma de construir um par é a seguinte:
```rust
Pair.new <a: Type> <b: Type> (fst: a) (snd: b) : (Pair a b)
```
Aplicando dois `*Nat*` ao nosso tipo `*Pair*`, onde ``**a**``  e `**b**` são dois números naturais, podemos contruir da seguinte forma:

```rust
Pair.new a b  : (Pair a b)```

Aqui estão duas funções simples para extrair o primeiro e o segundo componentes de um par. As definições também ilustram como fazer a correspondência de padrões em dois argumentos construtores.

Exemplo 1: (Pair.fst Nat (List Nat) (Pair 2 [1,2,3])) ->  2
```rust
Pair.fst <a> <b> (pair: Pair a b) : a
Pair.fst a b (Pair.new p.a p.b fst snd) = fst```

Exemplo 2: (Pair.snd Nat (List Nat) (Pair 2 [1,2,3])) -> [1,2,3]
```rust
Pair.snd <a> <b> (pair: Pair a b) : b
Pair.snd a b (Pair.new p.a p.b fst snd) = snd```

### Algumas provas

Vamos tentar provar alguns fatos simples sobre pares. Se declararmos as coisas de uma maneira particular (e ligeiramente peculiar), podemos completar provas com apenas reflexividade:

```rust```
Surjective_pairing (p: Pair Nat Nat) : (Equal p (Pair.new (Pair.fst p) (Pair.snd p)))

```

resolver



