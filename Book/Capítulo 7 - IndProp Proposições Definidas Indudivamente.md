# IndProp:  Proposições Definidas Indutivamente

## Proposições Definidas Indutivamente

No capítulo de Lógica, examinamos várias maneiras de escrever proposições, incluindo conjunção, disjunção e quantificadores. Neste capítulo, adicionamos uma nova ferramenta à mistura: definições indutivas.

Lembre-se de que vimos duas maneiras de afirmar que um número *n* é par: podemos dizer (1) ``Evenb n = Bool.true`` ou (2) `(k => Equal n (Nat.double k))`. Outra possibilidade é dizer que n é par se pudermos estabelecer sua paridade a partir das seguintes regras:

  - Regra *Ev_0*: O número 0 é par. 
  - Regra *Ev_SS*: Se *n* é par, então *Nat.succ (Nat.succ n)* é par.

Para ilustrar como essa definição de paridade funciona, vamos imaginar usá-la para mostrar que 4 é par. Pela regra *Ev_SS*, basta mostrar que 2 é par. Isso, por sua vez, é garantido novamente pela regra *Ev_SS*, desde que possamos mostrar que 0 é par. Mas esse último fato segue diretamente da regra *Ev_0*.

Veremos muitas definições como esta durante o restante do curso. Para fins de discussões informais, é útil ter uma notação leve que facilite a leitura e a escrita. Regras de inferência são uma dessas notações:

<br> 

$$ 
\frac{  }{Ev\ \ 0} \ \ ev\_0
$$

<br>

$$ 
\frac{\ \ \ \  ev\ \  \ \ \  }{Ev\ \ (Nat.succ \ (Nat.succ \ n))} \ \ ev\_SS
$$

<br>


Cada uma das regras textuais acima é reformatada aqui como uma regra de inferência; a leitura pretendida é que, se as premissas acima da linha forem todas válidas, então a conclusão abaixo da linha segue. Por exemplo, a regra *ev_SS* diz que, se *n* satisfaz *ev*, então `Nat.succ (Nat.succ n)` também satisfaz. Se uma regra não tiver premissas acima da linha, então sua conclusão é válida incondicionalmente.

Podemos representar uma prova usando essas regras combinando as aplicações de regras em uma árvore de prova. Aqui está como poderíamos transcrever a prova acima de que 4 é par:

$$
\frac{  }{Ev\ \ 0} \ \ ev\_0 \\
\ \ \ \frac{  }{Ev\ \ 2} \ \ ev\_SS \\
\ \ \ \frac{  }{Ev\ \ 4} \ \ ev\_SS \\
\ \ \ \frac{  }{Ev\ \ 6} \ \ ev\_SS \\
$$

<br>
Por que chamar isso de "árvore" (em vez de "pilha", por exemplo)? Porque, em geral, as regras de inferência podem ter várias premissas. Veremos exemplos disso abaixo.

<br>
Juntando tudo isso, podemos traduzir a definição de paridade em uma definição formal do Kind usando uma declaração de dados, em que cada construtor corresponde a uma regra de inferência:

```rust
type Ev ~ (n: Nat){
  ev_0 : Ev Nat.zero
  ev_ss <n : Nat> (pred: Ev n) : Ev (Nat.succ (Nat.succ n))
}
```

Essa definição é diferente em um aspecto crucial em relação aos usos anteriores de *dados*: seu resultado não é um *Tipo*, mas sim uma função de *Nat* para *Tipo* - isto é, uma propriedade dos números. Note que já vimos outras definições indutivas que resultam em funções, como *List*, cujo tipo é *Type -> Type*. O que é novo aqui é que, como o argumento *Nat* de *Ev* não tem nome e aparece à direita dos dois pontos, é permitido que ele tome valores diferentes nos tipos de diferentes construtores: `Nat.zero` no tipo de *ev_z* e `Nat.succ (Nat.succ n)` no tipo de *ev_ss*.

Por outro lado, a definição de*List* nomeia o parâmetro *x* globalmente, forçando o resultado de *Nil* e *Cons* a ser o mesmo `(List x)`. Se tivéssemos tentado omitir o tipo `n : Nat` ao definir *ev_ss*, teríamos visto um erro:

```rust
type Wrong_ev ~ (n: Nat){
  wrong_ev_0 : Ev Nat.zero
  wrong_ev_ss (pred: Ev n) : Ev (Nat.succ (Nat.succ n))
}
```
Com o seguinte retorno:

```diff
   - ERROR  Cannot find the definition 'n'.

      ┌──[ev.kind2:9:25]
      │
    8 │      wrong_ev_z : Ev Nat.zero
    9 │      wrong_ev_ss (pred: Ev n) : Ev (Nat.succ (Nat.succ n))
      │                            ┬                           ┬
      │                            │                           └Here!
      │                            └Here!
   10 │    }

```
("Parâmetro" aqui é jargão do Kind para um argumento à esquerda dos dois pontos em uma definição indutiva; "índice" é usado para se referir a argumentos à direita dos dois pontos.)

Podemos pensar na definição de Ev como definindo uma propriedade do Kind `Ev: Nat -> Type`, juntamente com os teoremas `ev_z: Ev 0` e `ev_ss <n : Nat> (pred: Ev n) : Ev (Nat.succ (Nat.succ n))`. Tais "teoremas construtores" têm o mesmo status que teoremas provados. Em particular, podemos aplicar nomes de regras como funções umas às outras para provar Ev para números específicos...

```rust
Ev_4 : Ev 4n
Ev_4 = Ev.ev_ss 2n (Ev.ev_ss 0n Ev.ev_z)
```

Também podemos provar teoremas que têm hipóteses envolvendo Ev.

```rust
Ev_plus5 (n: Nat) : Ev n -> Ev (Nat.add 4n n)
Ev_plus5 n = x => Ev.ev_ss (Ev.ev_ss x)
```

Mais geralmente, podemos mostrar que qualquer número multiplicado por 2 é par:


#### Ev_double

```rust
Ev_double (n: Nat) : Ev (Nat.double n)
Ev_double n = ?
```

## Usando evidências em provas

 Além de construir evidências de que os números são pares, também podemos raciocinar sobre tais evidências. Introduzir *Ev* com uma declaração de dados diz ao *Kind* não apenas que os construtores `ev_z` e `ev_ss` são formas válidas de construir evidências de que algum número é par, mas também que esses dois construtores são as únicas maneiras de construir evidências de que os números são pares (no sentido de *Ev*).

Em outras palavras, se alguém nos dá uma evidência e para a afirmação Ev n, então sabemos que e deve ter uma das duas formas:

 - `e` é `ev_z` (e *n* é Nat.zero), ou
 - `e` é `ev_ss` aplicando a indução com `n`  e ele é igual ao sucessor do sucessor do `n`\*.     

###### * Nota: Já usamos essa estratégia antes, relembre do exercício *Problems.t3* do capítulo de indução, aqui a diferença é que há apenas um "Nat.succ" a mais na nossa indução.

Isso sugere que deve ser possível analisar uma hipótese da forma `Ev n` da mesma forma que fazemos com estruturas de dados definidas de forma indutiva; em particular, deve ser possível argumentar por *indução* e *análise de casos* sobre essa evidência. Vamos ver alguns exemplos para ver o que isso significa na prática.

### Pattern Matching  nas evidências

Suponha que estamos provando algum fato envolvendo um número *n* e nos é dada a hipótese `Ev n`. Já sabemos como realizar a *análise de casos* em *n* usando a tática de inversão, gerando submetas separadas para o caso em que `n = Nat.zero` e o caso em que `n = Nat.succ n` para algum *n*. Mas para algumas provas, podemos querer analisar diretamente a evidência de que `Ev n` é verdadeiro.

Pela definição de *Ev*, existem dois casos a considerar:
  - Se a evidência for da forma `ev_z`, sabemos que `n = Nat.zero`.
  - Caso contrário, a evidência deve ter a forma `ev_ss n e`, onde `n = Nat.succ (Nat.succ n)` e `e` é a evidência para `Ev n`.

Podemos realizar esse tipo de raciocínio em Kind, novamente usando o *pattern matching*. Além de permitir que raciocinemos sobre igualdades envolvendo construtores, a inversão fornece um princípio de análise de casos para proposições definidas de forma indutiva. <!-- Quando usada dessa forma, sua sintaxe é semelhante à da função destruct: passamos a ela uma lista de identificadores separados por caracteres | para nomear os argumentos de cada um dos possíveis construtores.-->

```rust ignore
Ev_minus2 (n: Nat) (e: Ev n) : Ev (Nat.pred (Nat.pred n))
Ev_minus2 Nat.zero e = e
Ev_minus2 (Nat.succ Nat.zero) e = Ev.ev_z
Ev_minus2 (Nat.succ (Nat.succ n)) (Ev.ev_ss e) = e
```

Em palavras, aqui está como o raciocínio de *pattern matching* funciona nesta prova:
  -  Se a evidência for da forma `ev_z`, sabemos que `n = Nat.zero`. Portanto, é suficiente
mostrar que `Ev (Nat.pred (Nat.pred Nat.zero))` é válido. Pela definição de `Nat.pred`, isso é
equivalente a mostrar que `Ev Z` é válido, o que segue diretamente de `ev_0`.
  -  Caso contrário, a evidência deve ter a forma `ev_ss n e`, onde
`n = Nat.succ (Nat.succ n)` e `e` é a evidência para `Ev n`. Devemos então mostrar que
`Ev (Nat.pred (Nat.pred (Nat.succ (Nat.succ n))))` é válido, o que, após simplificação, segue
diretamente de `e`.

Suponha que quiséssemos provar a seguinte variação de *Ev_minus2*:

```rust ignore
Evss_ev (n: Nat) (e: Ev (Nat.succ (Nat.succ n))) : Ev n
```

Intuitivamente, sabemos que a evidência para a hipótese não pode consistir apenas do construtor `ev_z`, uma vez que `Nat.zero` e `Nat.succ` são construtores diferentes do tipo *Nat*; portanto, `ev_ss` é o único caso que se aplica. Infelizmente, a função 
# destruct não é inteligente o suficiente para perceber isso e ainda gera duas submetas. Ainda pior, ao fazê-lo, mantém a meta final inalterada, deixando de fornecer qualquer informação útil para completar a prova.