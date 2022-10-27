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
ListBool : Type
ListBool.nil : (List Bool)
ListBool.cons (head: Bool) (tail: ListBool Bool) : (ListBool Bool)
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

Esse tipo já exite no Kind e podemos perceber que ele é idententico ao ListBool, mas com um tipo `a`, esse *tipo* recebe qualquer outro tipo, seja *Nat*, *Bool*, *Maybe* e etc.
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
List.repeat <a: Type> (xs: a) (count: Nat)  : List a
List.repeat xs Nat.zero                     = List.nil
List.repeat xs (Nat.succ count)             = List.cons xs (List.repeat xs count)
 ```

Tal como acontece com Nil e Contras, podemos usar repeat aplicando-o primeiro a um tipo e depois a
seu argumento de lista:

```rust
Test_repeat1 : (Equal (Repeat 4 (U60.to_nat 2))(List.cons 4 (List.cons 4 List.nil)))
Test_repeat1 = Equal.refl
```

Para usar repeat para construir outros tipos de listas, simplesmente instanciamos com um parâmetro de tipo apropriado:
```rust
Test_repeat2 : (Equal (Repeat Bool.false (U60.to_nat 1)) (List.cons Bool.false List.nil))
Test_repeat2 = Equal.refl
```


