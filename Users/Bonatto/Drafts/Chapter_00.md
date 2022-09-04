# Capítulo 00
#### Uma breve introdução ao Kind

Neste estudo, abordaremos o uso de provas formais para validar nossos
programas, para tanto, devemos compreender algumas coisas sobre o que
usaremos, isto é, as ferramentas que temos acesso para o nosso objetivo.

Logo de cara percebemos que temos um elemento que pode ser novo para muitos
de nós, o kind e até para quem já possui uma noção de provas formais, pode
ficar a pergunta no ar, **o que é o kind**?

- ####  O kind
Kind é uma linguagem de programação funcional, mínima, eficiente, pratica e um assistente de provas.

Essa poderosa linguagem nos permite o uso de provas formais de forma eficiente e veloz, o que nos dá segurança a um custo computacional baixíssimo. 

No kind, por ser uma linguagem de programação funcional, tudo é uma
função, desde a propria função em si a até mesmo os construtores e tipos.

Falando em tipos, vamos usar um como exemplo, o tipo *Booleano*:

```Bhaskelrust 
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

```
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

```
Suco : Type
Suco.laranja  : Suco
Suco.caju     : Suco
```

O suco de laranja possui dois construtores, o Suco.**laranja** e o
Suco.**caju**. Esse tipo é fictício, ele não existia até então, mas agora
podemos usar ele como um tipo e isso nos mostra que, graças ao design do Kind,
podemos criar uma infinidade de tipos, pois todo tipo é uma função.

Já vimos dois tipos e ainda nem começamos a programar de fato, vejam como o
Kind é amigável conosco, mas isso é porque ainda nem começamos a ver as provas
formais, lá nós veremos que ele é muito mais amigável do que só na sintaxe
dele. 

Mas falando em provas formais, o que é uma prova formal?

- #### Provas formais.
<!-- TODO: continuar aqui -->
