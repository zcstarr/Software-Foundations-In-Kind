# Instalação

Este guia ensina como baixar e instalar o Kind através do Rust, Com o "Cargo" uma ferramenta usada para gerenciar pacotes. É possível que seja necessário ter uma conexão com a internet para prosseguir com este guia.

Primeiramente, instale o Rust usando este [link](www.rust-lang.org/tools/install)

- *Atualmente, o Cargo é a única maneira de instalar o Kind.*
- *Este guia foi escrito quando o Kind estava na versão em beta. Portanto, é necessário instalar a versão do* Channel [nightly](https://doc.rust-lang.org/book/appendix-07-nightly-rust.html).

### Instalando o Kind no Linux ou MacOS

Use seu gerenciador de pacotes (Cargo) para instalar o Kind. Para isso, abra o terminal e digite o seguinte código:

```
cargo +nightly install kind2
```

### Instalando o Kind no Windows

Para usuários do Windows, é possível usar o Kind através do CMD ou WSL2. Se escolher o WSL o método de instalação está neste [link](https://harsimranmaan.medium.com/install-and-setup-rust-development-environment-on-wsl2-dccb4bf63700).

Use seu gerenciador de pacotes (Cargo) para instalar o Kind. Para isso, abra o Bash(WSL2) ou Terminal(CMD) e digite o seguinte comando:

```
cargo +nightly install kind2
```

### Clonando o Repositório do Kind - Método 1

Clone o repositório do Kind usando o comando do git "git clone", da seguinte forma:

```
git clone https://github.com/HigherOrderCO/Kind
```

Depois do passo de clone, use o seguente comando para a instalação

```
cargo +nightly install --path crates/kind-cli --force
```

### Clonando o Repositório do Kind - Método 2

O cargo permite instalar usando o git, sem a necessidade de clonar algum repositório, da seguinte forma:

```
cargo +nightly install --git https://github.com/HigherOrderCO/kind.git 
```

Ao seguir os passos acima, podemos começar a usar o Kind.