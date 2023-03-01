# More about Functions

Functions Part 2

## Another Research

Whenever we talk about Pattern Matching, a lot of doubt can occur, in here i plan to get rid of most of this doubt about Polymorphic Type functions, so we can proceed to the next section of this Book, lets work in some Case usage, examples and exercises.

## Case and Values

As said in [Basic Functions](../Basic/Functions.md#pattern-matching---match), "Pattern matching is the act of verify possible matches of a given value, this is done sequentially." and in here i want to focus in values, now that we know how to access values held by constructors.

We are going to build some functions that work particularly in situations where we need to analyze values, since we know the types Bool, Maybe and Pair, we are going to mostly use them to practice.
Here are some examples of functions using values that we access from the type Maybe.

## Maybe First Example

This first function example can be seen as a function to verify if something you saved is the same as you input, we are going to use this function in a future example with different parameters.

```Rust,ignore
is_equal (input: Bool) (saved: (Maybe Bool)) : Bool {
	match Maybe saved {
    none => Bool.false
    some => ?b
  }
}
```

If we Type Check that function we get the following result:

```diff
+ INFO  Inspection.

  • Expected: Bool 

  • Context: 
    •   input       : Bool 
    •   saved       : (Maybe Bool) 
    •   saved.value : Bool 

  none => Bool.false
  some => ?b
          ┬─
          └Here!
      
```

We have an user input that is a Bool and the saved.value that is also a Bool, now it is time for us to open a case for the saved.value, it is a value of the type Bool, then you have either true or false.

```Rust,ignore
is_equal (input: Bool) (saved: (Maybe Bool)) : Bool {
  match Maybe saved {
    none => Bool.false
    some => match Bool saved.value {
      true => ?a
      false => ?b
    }
  }
}
```

Now we just need to verify each branch, this function idea is to show you that we can open a case in everything that is a concrete value, since saved.value is a concrete value of the type Bool, we can pattern match it.

The answer of this function can be found here, and let's proceed to the next function, in here we are going to build a value inside a function and pattern match it.

## Maybe Second Example

This one is purely meant to show you that we can build values inside function and pattern match it, i am going to write here the same function we just did but version 2.

```Rust,ignore
Is_equal_2 (input: Bool) (saved: (Maybe Bool)) : Bool {
  match Maybe saved {
    none => Bool.false
    some => (
      let eql = Bool.equal Bool.true saved.value
      ?a
    )
  }
}
```

Now, to the type check:

```
+ INFO  Inspection.

  • Expected: Bool 

  • Context: 
    •   input       : Bool 
    •   saved       : (Maybe Bool) 
    •   saved.value : Bool 
    •   eql         : Bool 
    •   eql         = (Bool.equal Bool.true saved.value)

  let eql = Bool.equal Bool.true saved.value
  ?a
  ┬─
  └Here!
```

First i need to explain you the let .   We use let to build some value inside a function and assign it to a name, that sometimes makes the code clearer and have better readability,  but we are going to  go without it, it is just for showing purposes. In this case we built something of the type Bool using the function Bool.eql, the result is either true or false, then it is a value of the type Bool
Lets move the Bool.eql out of the let and just pattern match it.

```Rust,ignore
Is_equal_2(input: Bool) (saved: (Maybe Bool)) : Bool {
  match Maybe saved {
    none => Bool.false
    some => match Bool (Bool.eql Bool.true saved.value) {
      true => ?a
      false => ?b
    }
  }
}

//If you wanted to use case on a value declared on the let you could
Is_equal_2 (input: Bool) (saved: (Maybe Bool)) : Bool {
  match Maybe saved {
    none => Bool.false
    some => (
      let eql = Bool.equal Bool.true saved.value
      match Bool eql {
        true => ?a
        false => ?b
      }
    )
  }
}
  
  //They're the same
```

That is completely legal and works, you are saying the following:  "If the saved value is true then ?a . else ?b " and ah, that is literally the known if else on Kind.

## Pair First Example

We are going to dive a little bit here, for us to practice Polymorphic Types, first a function that verifies if any of values of the pair are true.

```Rust,ignore
//If we open the pair first
Snd(pair: (Pair Bool Bool)) : Bool {
  match Pair pair {
    new fst snd => snd
  }
}

//If we open the pair afterwards
Snd(pair: (Pair Bool Bool)) : Bool {
  match Pair pair {
    new => pair.snd
  }
}
```

This function gets the second value of the Pair, that in this case is a Bool, but what if we wanted other Types? not only Bool?

```Rust,ignore
//If we open the pair first
Snd <a> (pair: (Pair a a)) : a {
  match Pair pair {
    new fst snd => snd
  }
}

//If we open the pair afterwards
Snd <a> (pair: (Pair a a)) : a {
  match Pair pair {
    new => pair.snd
  }
}
```

But the problem is, we limited the Types, the pairs can only have the same Type in this case, what if we wanted a Pair of two different values?

## Pair Second Example

```Rust,ignore
//If we open the pair first
Snd <a> <b> (pair: (Pair a b)) : b {
  match Pair pair {
    new fst snd => snd
  }
}

//If we open the pair afterwards
Snd <a> <b> (pair: (Pair a b)) : b {
  match Pair pair {
    new => pair.snd
  }
}
```

ps: A and B can be of the same Type, they are just variables.

We literally just changed the header of the function, everything else stays the same. This is a complicated step to take, but don't worry, only with practice and time this will clarify in your mind, i wanted to show you Abstraction for you to know that it is possible and have it growing on your mind, you are going to learn with time, don't worry!

You can look for more practicing exercises here, or look for [Variation Examples](./Examples/Maybe_Pair_Variation.md) or the official [Pair/Maybe example](./Examples/Maybe_Pair.md).
This is the end of our Voyage and the start of our [Discovery](../Discovery/Discovery.md)!
