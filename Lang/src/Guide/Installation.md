# Installation

Prepare your spaceship carefully.

This guide teaches how to download and install Kind through Rust, using ``Cargo``, a tool used for managing packages. It may be necessary to have an internet connection to proceed with this guide.

First, install Rust using this [link](https://www.rust-lang.org/tools/install).

- *Currently, ``Cargo`` is the only way to install Kind.*
- *This guide was written when Kind was in the beta version, so it is necessary to install the [nightly](https://doc.rust-lang.org/book/appendix-07-nightly-rust.html) channel version.*

### Linux or MacOS

Use your package manager (Cargo) to install Kind. To do so, open the terminal and type the following code:

```
cargo +nightly install kind2
```

### Windows

For Windows users, it is possible to use Kind through CMD or WSL2. If you choose WSL, the installation method is in this [link](https://harsimranmaan.medium.com/install-and-setup-rust-development-environment-on-wsl2-dccb4bf63700).

Use your package manager (Cargo) to install Kind. To do so, open Bash (WSL2) or Terminal (CMD) and type the following command:

```
cargo +nightly install kind2
```

### Cloning the Kind Repository - Method 1

Clone the Kind repository using the "git clone" command, as follows:

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
cargo +nightly install --git https://github.com/HigherOrderCO/Kind.git
```

By following the above steps, we can start using Kind.

Now, let's proceed to [Text Editors](./IDE.md) and finish preparing our spaceship for exploration.
