# Type Checker

Expected description, found joke.

The Type Checker is one of Kind's strongest features, and throughout this book it will be used very often. Remember back in [Hello Kind!](../Guide/Hello.md) where we "type checked" a file? Now is the time to understand what really happened there.

## What is a Type Checker

A type exists with its rules, and those rules place limits on its constructors. The Type Checker is an algorithm that inspect if the limits of a type are being respected. The action of verifying types is called **type checking**.

The Type checker is a very important tool, because the bigger a code is, the harder it will be to make changes, even small ones, and not compromise something along the way. It guides us in where it is needed to make changes. This, by itself, drastically reduces the chances of bugs in our programs while also helping to keep our codes more concise and readable.

As big as a code can be, the Type Checker will be your best friend when working with it. The bigger the code, the more rewarding it feels to use it.

Type Checkers can be separated in two categories:

- Static Type Checker
- Dynamic Type Checker

And this guide addresses the one that Kind uses, the Static Type Checker.

### What it does

The Type Checker does two sorts of verifications:

- Syntax Verification
- Types Verification

In the first step, the Type Checker checks the syntax of the code. Syntax is the language used in Kind, just like all the other languages in the word. Portuguese, deutsch, english and so on, every one of these has its own rules and methods, and it's used to communicate. Kind syntax works in the same way, and the Type Checker will verify if there it is no mistakes in your writing. If it finds any mistake, Kind's Type Checker will point to you where and what it is.

```diff
- ERROR  Unexpected token 'true'.

type Bool  
 true
 ┬───
 └Here!
 false
}
```

Above, the Type Checker found an error in the code. See how it says how it was expected a curly bracket, and instead found the letter "true". That's why this code is missing a "{" to open the constructors' box.
If no syntax error is found in the code, the Type Checker will proceed to its next function, to verify if the Types are correct. Kind is strongly Typed, therefore it's always necessary to explicit the types that are in the code.

Imagine you are reading a code that uses the Bool Type, and in some point you verify that a function returns a Nat type (the type of the natural numbers). The Type Checker mission is to find and point this mismatch. That way, this code can be corrected and will be more safe, reliable and clearer. The Type Checker is like your best friend when writing.

It is called  Static Type Checker because it checks everything before running the program. iIf both syntax and type are ok, the console will show the message: All terms check. This means that the file Syntax and Types are right and ready to run!

```
//Terminal
All terms check.
```

It is strongly recommended to Type Check a file every time before running it, to avoid potential time waste. Because, if the types are wrong, there will be a compilation error message, and it will be necessary to type check it all over again, and that's double of your time wasted!
To type check a file, simply use this command:

```
//Terminal
kind2 check hello_world.kind2
```

You must replace the fileName for the name of the file you're using, and don't forget the extension .kind!
Knowing how to use this powerful tool, let's learn more about what gives life to Kind, the functions.

Thats all of the Kind Basics! if have come all this way congrats! now you can write basic functions in Kind, now, we can proceed to Kind Intermediate! Let's go Rookie, time for us go to a Voyage!
