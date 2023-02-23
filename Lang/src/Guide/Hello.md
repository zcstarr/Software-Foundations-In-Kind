# Hello Kind

This is definitely not another "Hello World!" guide. Or is it?

By this point, Kind should already be installed in your machine. If not, please go back to [Installation](./Installation.md) and move along with the instructions.

You are not sure Kind is correctly installed? Try proceeding with this guide, if it doesn't work, well... it's not installed. If it worked, then you're ready to learn how to become a ~~PRO GAMER~~ Kind programmer!~

In this guide, command lines and text editors will be used, so make sure the terminal is open to proceed through the steps. Don't know what is a Terminal and Text Editor? So go back to [Terminal](./Terminal.md) guide and [Text Editors](./IDE.md).

## Creating the documents

First of all, create a directory to store the Kind files. It is recommended to use a dedicated directory to keep all the exercises and examples, but feel free to do as you want.

The three commands below will create a directory called 'KindExamples' and a file called 'hello_world.kind2' inside the project directory. Use them in order:

```
//Making a directory
mkdir KindExamples
```

```
//Moving through directories
cd    KindExamples
```

```
//Making a file
touch hello_world.kind2  ////CMD use "copy nul hello_world.kind2 > nul"
```

The .kind2 extension is what makes it a Kind file. For example, a file that ends with .exe is an Executable; .js is a JavaScript file; .rs is a Rust file, etc.

If the commands were used correctly, the file hello_world.kind2 should be inside the folder KindExamples. Then it's time for fun, CODING!

## Hello World

Open the hello_world.kind2 file on the [Text Editors](./IDE.md), it is going to be empty, but no worries, remember our little story in [About Kind](./Guide/About.md) about exploring a Universe? This is just your first exploration.

- *Starting now, there are going to be some advanced concepts. But don't be scared, everything will make sense in the future, ethoses concepts that are pertinent will be explained in it's given time.*
- *It is recommended that you manually type the codes, instead of copy pasting them in your file.*

Let's write your first code into the hello_world.kind2 file:

```
//hello_world.kind2
Main {
  "Hello, Kind!"
}
```

And that's it! That's how a Kind "Hello World!" is written. A print function in four lines.

## Type Checking

With the code ready, you should use the Type Check to see if everything is in order. The Type Checker is unknown until now in this guide, but will be explained further. Right now, simply understand it as a verifier that checks if the file is correctly typed and "typed" (haha!).

To Type Check a Kind file, just use the command Kind fileName.kind2. For the hello_world.kind2 file it would be:

```
//Terminal
kind2 check hello_world.kind2
```

The message "All terms check." means your file is ready!

```
//Terminal
All terms check.
```

The Type Check is ok? Then let's run the code.

## Running the code

To Run a file in Kind, use the command ``kind2 run nomeDoArquivo.kind``. Yes, the fileName goes without the extension in this case. It should look like this:

```
//Terminal
kind2 run hello_world.kind2
```

And voila! your terminal should print Hello, Kind! back to you.

<!-- To print any other messages, re-open the file and change the Parameters insideIO.print function. An example is to type IO.print("I am amazing!")  and run the file, try it! -->

Just remember every steps above, Type Check it then run!

```
//Terminal
"Hello, Kind!"
```

With that you've finished your first exploration and may call yourself a Kind Astronaut, congratulations! Welcome to the Kind programming world!

In this first mission you explored your first ecosystem, discovered.

You just explored your first ecosystem and discovered your first function. The next section will go deeper into the [Kind Basics](../Basic/Basics.md).
