# Installation

This guide teaches you how to download and install Kind through Rust, using "Cargo", a tool used to manage packages. It may be necessary to have an internet connection to proceed with this guide.

Firstly, install Rust using this [link](www.rust-lang.org/tools/install)

- *Currently, Cargo is the only way to install Kind.*
- *This guide was written when Kind was in beta version. Therefore, it is necessary to install the* [nightly version](https://doc.rust-lang.org/book/appendix-07-nightly-rust.html).

### Installing Kind on Linux or MacOS

Use your package manager (Cargo) to install Kind. To do this, open the terminal and type the following code:
```
cargo +nightly install kind2
```

### Installing Kind on Windows

For Windows users, it is possible to use Kind through CMD or WSL2. If you choose WSL, the installation method is in this [link](https://harsimranmaan.medium.com/install-and-setup-rust-development-environment-on-wsl2-dccb4bf63700).

Use your package manager (Cargo) to install Kind. To do this, open Bash(WSL2) or Terminal(CMD) and type the following command:

```
cargo +nightly install kind2
```

### Cloning the Kind Repository - Method 1

Clone the Kind repository using the git command "git clone", as follows:

```
git clone https://github.com/HigherOrderCO/Kind
```

After the cloning step, use the following command for installation:

```
cargo +nightly install --path crates/kind-cli --force
```

### Cloning the Kind Repository - Method 2

Cargo allows you to install using git, without the need to clone any repository, as follows:

```
cargo +nightly install --git https://github.com/HigherOrderCO/kind.git 
```

By following the above steps, we can start using Kind.