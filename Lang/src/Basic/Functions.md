# Basic Functions

The life inside Kind Universe.

## What are Functions

Functions gives types and constructors a meaning. Types, by themselves, are simply a categorization of constructors, but, when applied to functions, that's when the "magic" happens!

Just like the Astronaut from About Kind story, let's explore the Boolean planet to "discover" and document  the functions  "**AND**", "**NOT**" and "**OR**"  (Don't it just look like some sort of time travel? Are you the Astronaut from the story?).

The Bool still has no concrete use, since it still has no function. True and false is just names for the constructors, but even so, they got to have some logic behind them. Let's start with Not Function, the **Boolean Negation**.

## Defining a Function

Functions in Kind are like mathematical functions, you tell them how to work, give them something to work with and it will return an answer. Let's see how it works in the example bellow:

```rust,ignore
// selfReturningFunction.kind2

Identity (b: Bool) : Bool
Identity Bool.true = Bool.true
Identity Bool.false = Bool.false
```

This is a function that returns the input itself, it is called Identity function. And it is a simple example of the functions' syntax in Kind. In the first line there are a lot of information about the function for us and the machine to interpret it. And so here is a  "translation" of this first line:

- *"Hmrm ``clears throat`` identity is a function that receives a variable named b , which is of the type Bool, and the function returns something of the type Bool."*

## Function Anatomy

--Img--

[Yes, all of that is written up there, functions in Kind always start with their name, the name not necessarily needs to start with a lower case letter, but its my personal preference, you could name it ItSelf or Itself.After you name the function we open brackets and we write the arguments followed to the type of that function (b: Bool) (c: Bool) (d: Bool) and then we close brackets. Once we are done listing all the arguments our function has we add a cólon :  to tell Kind we have named our function and listing the arguments and we proceed to the return, after the Cólon it means that our function is going to give us a result of something of that type, in our example we decided to use Bool as the type of return, but it could be any existing type in Kind, even a type you create yourself. [It is kind erroneous calling it a variable since they are immutable, they are values of that type, but we call them variable]]

## Return

Lets use our box again, when we pattern matched our variable and we got either true or false, now we must specify what to return for those cases, on our example we returned Bool.true when true and Bool.false for false, that means we covered every possibility in the ``type Bool`` and we gave a Bool return to the function, which is what it expected.

for the box, it would look like this:

--Img--

You always gotta fill the answer with something of the type you specified in first place, it won't work if you return something of a different type from what you specified, this would be an example;

```rust,ignore
Wrong (a: Bool) : Bool
Wrong Bool.true = Bool.true
Wrong Bool.false = Nat.zero
```

Nat.zero is something of the type Nat, but we specified the return as a Bool, therefore the type checker would return an error of type in this situation.

```diff
// Type mismatch in the function named wrong.

-   ERROR  Type mismatch

  • Got      : Nat 
  • Expected : Bool 

  Wrong Bool.true = Bool.true
  Wrong Bool.false = Nat.zero
                     ┬───────
                     └Here!
```

As we have seen in Type Checker, this is how a code would return with the wrong type

## Pattern Matching - match

All a data constructor does is holding values together. When we want to use them we need to deconstruct in order to specify what to do based on which constructor is being used. This is done with pattern matching. Type is the creator, holder, container, match is the deconstructed, analyzer, bond breaker.

Pattern matching is the act of verify possible matches of a given value, this is done sequentially.

On our functions in Kind we call it case and we give the value our variable, like in the second line of our example, we verify within b the possible matches, in that case it can only be true or false, since the Bool type  has only two possible matches.

Let's go back to our boxes, here is the boolean box:

--Img--

~~These are the two possible cases of a value of the type Bool, it's either true or false, because its how we defined in our type Bool , so when we open a match of something from the type Bool,  its like if we were opening the box, looking at every possibility and giving them an answer to what to do when the pattern matches for every of the single cases. ~~

To visualize the options we can use branches to see clearer the possible paths.

```rust,ignore
  match variable
   /       \ 
 true      false
  |          |     
  ?a         ?b 

```

- ?a is the return for the function ``MATCH`` the variable we input is true.

- ?b is the return for the function ``MATCH`` the variable we input is false.

in our example itself we returned True case b is true and False case b is false.

```rust,ignore
    match b
   /      \ 
 true     false
  |         |     
 true     false
```

The returns are the tips of the branches.

## Basic Function Example

First things first, create a new file ``namedNegation.kind2``.

In our ``Negation.kind2`` file we start with the first line (You can try doing it by yourself, click [here](../Examples/Function.md) for the answer)

```rust,ignore
Negation.function (b: Bool) : Bool 
Negation.function b = ?help
```

## Kind Goals

A goal can be written as ? followed by a name. For example, ``?help`` is a goal named ``help``. Goals are extremely useful when developing algorithms and proofs, as they allow you to keep a part of your program incomplete while you work on the rest. They also allow you to inspect the context and expected type on that part. For example, our ``help`` goal in negation will display:

```diff
+  INFO  Inspection.

    • Expected: Bool 

    • Context: 
    •   b : Bool 

    Negation.function (b: Bool) : Bool 
    Negation.function b = ?help
                          ┬────
                          └Here!
```

Notice how it shows the type it expects on each hole, as well as the context available there.

Our function named ``function``has only one variable for us to work with, now if we had more variables and needed to look even further in our branches of possibilities, we could open a case inside a case (pattern match a value inside another branch). We are going to look further in the following examples.

## Intermediate Function Example

After we defined the "**NOT**" function, we still have the "**AND**" and "**OR**" functions, but those functions have two variables as arguments, and we need to verify deeper down inside each case, lets investigate together.

Now that we are done with the ``Negation.kind2`` file, we can save it and open a new one named ``Logics.kind2``

The logical **AND** operator returns *true* if both operands are **true** and returns *false* otherwise, and the result is of type ``Bool``. This is how the function header would look like:

```rust,ignore
Logics.and (a: Bool) (b: Bool) : Bool
Logics.and a b = ?ab
```

If you want, you can proceed by yourself. After this line, it is the solution and explanation.

```rust,ignore
Logics.and (a: Bool) (b: Bool) : Bool
Logics.and Bool.true b = ?a
Logics.and Bool.false b = ?b
```

We are pattern matching our variable b , in the first branch, we are working with the possibility of b being true, in that case we must analyze a  because the answer depends on that, since its only true if both of our values are true.

```rust,ignore
      case b
     /       \ 
-> true      false
    |          |     
   ?a         ?b 

```

We can open a case in the variable a  when we are analyzing the case of b  being true. It would look like that

```rust,ignore
Logics.and (a: Bool) (b: Bool) : Bool
Logics.and Bool.true  Bool.true = ?a
Logics.and Bool.false Bool.true = ?b
Logics.and a          Bool.false = ?c
```

Now we are working with both cases:

- ``Case b is true and case a is true`` = Goal a (?a)
- ``Case b is true and case a is false`` = Goal b (?b)

```rust,ignore
       case b
      /      \ 
    true     false
     |         |     
-> case a      ?c
   /    \
 ?a      ?b 
```

Following our logic, when A and B are true, the answer is true, therefore the goal a (?a) is Bool.true, and when B is true but A is false, we have Bool.false as answer:

```rust,ignore
Logics.and (a: Bool) (b: Bool) : Bool
Logics.and Bool.true  Bool.true = Bool.true
Logics.and Bool.false Bool.true = Bool.false
Logics.and a          Bool.false = ?c
```

```rust,ignore
       case b
      /      \ 
    true     false
     |         |     
-> case a      ?c
   /    \
true    false
 |        |
true     false
```

We have now finished our left branch, or, the  branch with value true. Time for us to go to the false branch.

We do the same as we did before, but we know both values MUST be true in order for the function to return true, therefore if b is already false, it is impossible for it to return true, we would get the function looking like that:

```rust,ignore
Logics.and (a: Bool) (b: Bool) : Bool
Logics.and Bool.true  Bool.true = Bool.true
Logics.and Bool.false Bool.true = Bool.false
Logics.and a          Bool.false = Bool.false
```

```rust,ignore
       case b
      /      \ 
    true     false
     |         |     
   case a    false  <-
   /    \
true    false
 |        |
true     false  
```

And this is how the And function would look like in Kind .  .  . BUT! you can improve it even further, i wont give you the answer here, but you are more than welcome to do it yourself, don't worry if you cant find the answer now, you can come back later and redo it with way more knowledge! ahh!! the smarter version is in [Functions](../Examples/Function.md) if you want to look for it.

## Running a Function

Now that we have learned all about how to build a function, it is time for us to test our logics and if the function is working as intended, if we wrote everything right and all type checked, it means all types and syntaxes are right, and in here, we are going to learn how to run a function.

First we are going to add the name of the file as a function in the code, without arguments just the name and a return, since our file is named ``Logics.kind2``we are going to add it to the bottom of the file, with the return of the function we are going to try out. We are testing the ``Logics.and`` function, therefore the return is of the Bool type, we would add a line with the following code:

```rust,ignore
Main : Bool {
  ...
}
```

``Main: type of Return``

Bellow that line we call our function to run, our ``Logics.and``  function receives two booleans as argument of the variables ``a``  and ``b`` , so we are going to test it with two booleans:

```rust,ignore
Logics.and (a: Bool) (b: Bool) : Bool
Logics.and Bool.true  Bool.true = Bool.true
Logics.and Bool.false Bool.true = Bool.false
Logics.and a          Bool.false = Bool.false

Main : Bool {
  Logics.and Bool.true Bool.false 
}
```

If you want to try other options, you can change the two arguments as the following examples:

```rust,ignore
//Examples of function call:
// Logics.and (Bool.true)  (Bool.true)
// Logics.and (Bool.false) (Bool.true)
// Logics.and (Bool.false) (Bool.false)
```

Note: Kind can only run one function at time, you can have as many functions in a file as you want, but you can only run one at a time.

With all this knowledge, you can do the "**OR**" logics, and you can also find more basic Bool [exercises in our github](https://github.com/HigherOrderCO/class/blob/main/Kind/Exercises/ex_00.kind2). The "**OR**" logic answer can be found [here](../Examples/Function.md#function-or-with-syntax-sugar).

Before proceeding to learn about functions, and how to use constructors inside them, it is important to understand about an important tool in Kind's environment, the Type Checker!
