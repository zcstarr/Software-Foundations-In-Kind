# Accessing Constructor Values

In case you need to access, case.

## A New Discovery

In the Basic Function section we learned that Case is the desconstrutor, analyzer, bond breaker, and now we are going to understand why it is the desconstrutor. Constructors they Hold the value, and the case gives us access to that value, when we are pattern matching a variable of a type with a constructor that is storing some information.

I am going to write a wrapper function for the Boolean true, that function is going to store the concrete value of Bool.false in a maybe type. Let's make a file named WeLoveMaybe.... ah, okay, we can make a Maybes.kind to test our functions.

## Accessing Values in Constructors using Maybe as example

```Rust,ignore
Maybe.wrapper (b: Bool) : Maybe Bool
Maybe.wrapper Bool.true = Maybe.some Bool.true
Maybe.wrapper Bool.false = Maybe.none Bool
```

This function isn't very useful, but it works as an example, lets run it and see the results for each input, Bool.true and Bool.false

--Img--

The Maybe.some  saves anything we want to, in this case we decided to save a concrete value of Bool.false, and if we input a Bool.false value, it returns none, we are going to have better usage for the Maybe type in the future.
Now let us do the opposite, an unwrapper, a function that receives a Maybe and returns to us a Bool

```Rust,ignore
Maybe.unwrapper (b: (Maybe Bool)) : Bool
Maybe.unwrapper Maybe.none = ?a
Maybe.unwrapper (Maybe.some value) = ?b
```

This is a very important part that we want you to pay attention, i am going to explain how data saved by constructors can be used, using branches to make it visible and clearer, also within the goals and cases for more visualization, lets proceed.

If we type check this code, Kind is going to give us two paths for us to follow, the none and some, the type checker would look somewhat like that:

```diff
+   INFO  Inspection.

  • Expected: Bool 

  Maybe.unwrapper Maybe.none = ?a
                               ┬─
                               └Here!
```

```diff
+ INFO  Inspection.

  • Expected: Bool 

  • Context: 
    •   b.value : Type 
    •   b.value = Bool 
    •   v       : b.value 

  Maybe.unwrapper (Maybe.some v) = ?b
                                   ┬─
                                   └Here!
```

This is how it would look like with the branches

```
    case b
    /   \
 none   some (value)
  |      |
  ?a     ?b
```

The some Constructor carries the saved value with him, and the way it is represented in kind is  constructor.data,

but this value is ONLY ACCESSIBLE BY THAT BRANCH, the none has NO access to concrete value, remember the bag example? the none branch means YOU HAVE NO BAG, but the some means YOU HAVE A BAG and the value is inside that bag.

As you can see, our b.value is something of the type Bool, which is our concrete value, the thing inside the bag, whatever the bag is holding, any value in the Maybe.some constructor is our thing.value whenever we access that variable.

But, what do we return when it is none? well, this is why the type Maybe exists, so our "unwrapper" function isn't very useful, other than to show us how this type works, but don't worry, at the end of this explanation there are going to be some real examples and usage for the type Maybe, lets proceed with the example thou. Our unwrapper version should return to us false if there it is nothing and if there it is something, it should return the value of that boolean saved. In the wrapper we saved False if the boolean given is true.

```rs,ignore
Maybes.unwrapper (b: Maybe<Bool>): Bool
  case b {
    none: Bool.false
    some: b.value
  }
     case b
    /       \
  none     some (b.value)
   |           |
Bool.false   b.value
```

For the Maybe type, this is how we get access to the value stored by it, if you want to see real functions uses, go here for examples or here to practice with the type Maybe. These examples were only to show you how to use the type Maybe and how to access a value stored by it, but this is how every type that has a constructor with arguments works, these are the first steps we take towards some very complex matter. Time for the Pair

Accessing values in constructors using Pairs.

Pairs are incredible and yet a very simple Type, they can see intimidating because it has two Polymorphic Types but they are indeed very simple.

Since Pairs has only one constructor, it branch always carries their values with them, we saw how a branch carries value for the Maybe type in the Maybe.some branch, lets write two functions, one that gets the First element of a pair and one that gets the Second element of a Pair.

Lets write both functions in a file named Pairs.kind, and name the functions first and second, both of the functions won't be Polymorphic at first, but then we write them to be Polymorphic, just for the simplicity of the example. The functions are going to have as argument a variable named pair of the type ``Pair Bool Bool ``and the return is just a Bool.

```Rust,ignore
//this function returns the first value of the pair
Pairs.first (pair: (Pair Bool Bool)) : Bool {
  ?a
}

//this function returns the second value of the pair
Pairs.second (pair: (Pair Bool Bool)) : Bool {
  ?b
  }
```

If you want you can try to write the answer by yourself, below this line there will be the answer.
Okay, first things first (hehe) as everything in case, we open a Kind! i mean, we open a Case like we always gonna do in functions.

```Rust,ignore
Pairs.first (pair: (Pair Bool Bool)) : Bool {
  match Pair pair {
    new => ?a
  }
}
```

The Pair has only one constructor named new, then it is the only thing inside our case, looks cute doesn't it?

```diff
+ INFO  Inspection.

  • Expected: Bool 

  • Context:
    •   pair     : (Pair Bool Bool) 
    •   pair.fst : Bool 
    •   pair.snd : Bool 

  match Pair pair {
    new => ?
           ┬
           └Here!
  }
```

When we opened the case, we gained access to both values, fst and snd, this is how it would look like in the branch.

```
  match pair
         |
        new pair.fst pair.snd
         |
         ?a
```

A very simple branch, as said above, pairs are very simple, since we want the first value of that pair, all we need to do is return the pair.fst value.

```Rust,ignore
Pairs.first (pair: (Pair Bool Bool)) : Bool {
  match Pair pair {
    new => pair.fst
  }
}
```

The second you can try to do by yourself, the answer is in the Function Examples you can find later on, i strongly recommend you to try to do them by yourself.

Well, this function only works for Booleans, imagine if we had to do one for each type in Kind, lucky for us we know Polymorphic Types, we can use them to make functions that accepts every sort of type, and we call that Abstraction, when you can abstract your function to work for every single type in Kind. Some functions won't really work, but a lot of them will, and if you can think how to Abstract them, that is very good, abstraction is one of the keys to be a good Kind Developer, YET it is a very hard concept and thing to get used to, give yourself some time, it is okay.

If you want to practice some pair functions can be found here, and the answers here.

Okay Buddy, now that we have met the Polymorphic Types and Constructor that saves data and how to access that data, lets learn how to work with that information with, learn more about pattern match is going to be very useful for us, let's see [More about Functions](./Functions.md).
