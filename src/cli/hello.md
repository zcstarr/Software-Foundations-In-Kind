# Olá, Mundo

Neste ponto, o Kind já deve estar instalado em sua máquina. Se não, volte para a instalação e siga as instruções.

Neste guia, serão usadas linhas de comando e editores de texto, então certifique-se de que o terminal esteja aberto para prosseguir com os passos.

### Criando os documentos

Primeiro de tudo, crie um diretório para armazenar os arquivos do Kind. É recomendável usar um diretório dedicado para manter todos os exercícios e exemplos, mas sinta-se à vontade para fazer o que quiser.
Os três comandos abaixo criarão um diretório chamado 'KindExamples' e um arquivo chamado 'hello_world.kind' dentro do diretório do projeto. Use-os na ordem:

```diff
mkdir KindExamples
cd    KindExamples
touch hello_world.kind //CMD use "copy nul hello_world.kind > nul"
```

A extensão .kind é o que faz dele um arquivo Kind. Por exemplo, um arquivo que termina com .exe é um executável; .js é um arquivo JavaScript; .rs é um arquivo Rust, etc.
Se os comandos foram usados corretamente, o arquivo hello_world.kind deve estar dentro da pasta KindExamples. Então é hora de se divertir, CODING!

### Hello World

Abra o arquivo hello_world.kind no editor de texto, ele estará vazio, mas não se preocupe.
A partir de agora, haverão alguns conceitos avançados. Tudo fará sentido no futuro, esses conceitos que são pertinentes serão explicados no tempo devido. É recomendado que você digite manualmente os códigos, em vez de copiá-los e colá-los em seu arquivo.

Vamos escrever seu primeiro código no arquivo hello_world.kind:

``` Rust
Main {
  "Hello, Kind!"
}
```

### Type Checking

Com o código pronto, você deve usar a Verificação de Tipo para verificar se tudo está em ordem. O verificador de tipo ainda é desconhecido neste guia, mas será explicado com mais detalhes posteriormente. Por enquanto, basta entendê-lo como um verificador que verifica se o arquivo está corretamente "tipado".

Para verificar o tipo de um arquivo Kind, basta usar o comando `kind2 check nomeDoArquivo.kind`. Para o arquivo hello_world.kind, seria:

```
kind2 check hello_world.kind2
```

A mensagem "All terms check." significa que seu arquivo está pronto!

```
All terms check.
```

A verificação de tipo está correta? Então vamos executar o código.

### Executando o código

Para executar um arquivo no Kind, use o comando `kind2 run nomeDoArquivo.kind`. Deve parecer assim:

```
kind2 run hello_world.kind2
```

E pronto! seu terminal deve imprimir "Hello, Kind!" de volta para você.

#### Lembre-se de todos os passos acima, verifique o tipo e depois execute
