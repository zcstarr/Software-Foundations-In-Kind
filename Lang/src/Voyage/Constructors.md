# Constructors with Value

Constructors Part 2

## Atmospheric Entry

In this Voyage we are going to take larger steps, a lot of abstract concepts are going to be seen here, but we are going to start with a simpler approach and as we proceed, we are going to go deeper on everything we learn in here.
Before we leave on our next trip towards the Pair and Maybe planets, i want to show you some simpler concepts before we fully see their potential, then lets proceed.

## Beyond Nullary Constructors

Constructors are made to hold values together, we just learned about the Nullary constructors, or constants, and here we are going to learn about constructors with values.
if it sound confusing don't worry, we are going to explore this concept in order for us to understand all about it, first we proceed with a variation of the type Maybe, then we go for the Pairs.

### Maybe variation and Constructors with Values

The type Maybe in kind is very powerful, but in here we are going to use a variation of that type to explain the concept of constructors with value.
Constructors are functions, Nullary constructors has no arguments, therefore they return nothing but their own value, but constructors with arguments their value can vary, and this is how the constructor with arguments would look:

```Rust,ignore
type MaybeBool {
  none
  some (value: Bool)
}

```

As you can see, the ``some`` constructor is a function that has an argument ``value`` of the type Bool.

If we think of the type Bool it is a type with two possible values True or False, in this Maybe version it is very similar, but instead, it has a function that stores a value of the type Bool. Therefore, we can notice that this MaybeBool is basically the same as the Boolean type, but instead of only giving us two options, it also stores a value of that type.  In the case of our constructor to be none, it has no value, but if it is some, it has something stored inside the constructor.
Using a bag as example, either you have No bag (none)  or a bag with something inside (some).

### Building a Maybe Variation Concrete value using Constructors with arguments

To build concrete values we are going to do the same as we learned in [Basic Types](../Basic/Types.md), we write typename.constructor:

```Rust,ignore
MaybeBool.none // Right
MaybeBool.some // Wrong

```

But that wouldn't work, since MaybeBool is a function, we need to give him something of the type Bool, since the variable value is of the type Bool

```Rust,ignore
MaybeBool.some (Bool.true)  // Right
MaybeBool.some (Bool.false) // Right
```

Either of the options works, since we fulfilled with what Kind asked us, if it doesn't seem very organic to you, we are going to revise this concept multiple time throughout the book.
Then all you gotta do is, typename.constructor(arguments of that type) and you build a concrete value of that type, let us see another example, now with Pairs.

### Pair variation and Constructors with Values

Same as the Maybe type, the pair also has a constructor with value, but as the name suggest it has TWO values, and instead of being either One or Another, it is always BOTH, can you figure out how? If not thats ok, lets write a variation of the Pair type, a Pair of Bool.

```Rust,ignore
type PairBool {
  new (fst: Bool) (snd: Bool)
}
```

This is how a pair of bool would look like, you have only one constructor because it is ALWAYS that one, you cannot have an either option here! i choose the name new for the constructor.  fst and snd for the arguments just to make it clearer, you could choose any name you want!
Let us build now concrete pair values using the Bool type.

### Building a Pair Variation Concrete value using Constructors with arguments

```Rust,ignore
Pair.new // Wrong
```

The new constructor receives two values as arguments, the fst and snd, both being of the type Bool, therefore

```Rust,ignore
Pair.new Bool.true Bool.true
Pair.new Bool.true Bool.false
```

There are two other possibilities for this pair, if you want to try to build yourself using the Bool.false, you are more than welcome to, for practice purposes.

Pair is a good type to store two things together, imagine you want to have a name linked to a number? Like:

```Rust,ignore
1 - Anna
2 - Bruce
3 - Jeorge
4 - Myrna
...
```

Or maybe you want to link other two things together, like a list to a boolean.

```Rust,ignore
 []      - Bool.true
 [1]     - Bool.false
 [1,2]   - Bool.true
 [1,2,3] - Bool.false
 . . .
 ```

That is a lot of different types, we've seen Numbers, Text, List, Booleans (Nat, String, List, Bool) only in this example, there are infinite types out there, imagine creating a Pair type for each and every of the possibilities, or making a Maybe Type for each of the possible types, that would take forever right? Yes, it would take forever, BUT types are very smart and they asked themselves, what if we become a VARIABLE?

Yes, types can be variables, in [Polymorphic Types](./Polymorphic.md) we are going to learn how to use this powerful Tool to include every possible type.

Let's go partner, it is time for me to take you to the real Pair and Maybe planets, off we go to the [Polymorphic Types](./Polymorphic.md) planets, FIRST ONE, Pairs!!!!
