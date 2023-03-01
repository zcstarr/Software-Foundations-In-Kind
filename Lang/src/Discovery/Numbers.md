# Natural Numbers

## A New Voyage

Welcome to the new planet, i hope you aren't dizzy from the travel! Let's go partner, I have many things I want to show you, and ah, welcome to the  Natural Number Planet.
Then let me ask you, when i said Natural Numbers, and thinking of types, can you imagine us doing an infinite type, since the natural numbers can go from zero to positive infinite? Well, you could go with:

```Rust,ignore
type Nat { 
  zero
  one
  two
  three
  ....
}
```

And that would take ages (forever i would say), that's what the natural numbers thought as well, then they had to figure out how to have all numbers in a much smaller structure, it had to fit in a Planet. After years, the Nat (We are going to affectionately call them Nat, the type Nat) found out how:

```
type Nat {
  zero
  succ (pred: Nat)
}
```

Within these 2 constructors, the type Nat managed to approach all positive infinite numbers starting with zero, if you can't see how yet, by the end of this page, we are going to be able to!
Also, just to make it clear, succ is a short name for Successor and pred is for Predecessor.

```
type Nat {
  zero                // Nullary constructor
  succ (pred: Nat)    // constructor with value
}
```

As we can see, zero is a constant, a Nullary constructor (which is true), now succ has a variable named pred of the type Nat.

Constructors with value

Lets remember two things here:

- To declare concrete values, we write typename.constructor, using Bool as example we would have either Bool.true or Bool.false as valid concrete data to Bool type.
- Functions have a name, followed to its arguments, the type, then the return.
Now that we know the Nat type, if we were to declare its concrete values we would have:

```Rust,ignore
Nat.zero
Nat.succ
```

Lets make a new file, named Numbers.kind and type check both of values, remembering, we can only do it one by one in Kind.

We add a line with Numbers: Nat  to our file and lets test first the Nat.zero:

```Rust,ignore
Numbers: Nat

All terms check.
```

Time for us to do the same with the Nat.succ:

```
Type mismatch.
- Expected: Nat
- Detected: (pred:Nat) -> Nat
```

The type checker acuses a Type mismatch, since it expected something of the type Nat, and found a function that has an argument of the type Nat, and then this function is something of the type Nat.

- In kind we write a function the way we learned in [Basic Functions](../Basic/Functions.md), but we can represent it with arrows ->  as explained in [Currying](./Currying.md), you can find more examples about it [here](../Voyage/Examples/Intermediate_Funtions.md).

## Succs and Preds

TODO