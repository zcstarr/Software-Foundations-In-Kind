# Basic Function Examples

And finally, some Basic Function Examples using the Boolean Type.

## Function Identity

```Rust,ignore
Identity (b: Bool): Bool 
Identity b = b
```

The ``Identity`` function always returns the input value itself. Therefore, if "b" value is "True" then the output is also "True". The same would happen if the value of "b" is "False".

## Function Negation

```Rust,ignore
Negation (a: Bool) : Bool 
Negation Bool.true = Bool.false
Negation Bool.false = Bool.true  
```

The function negation always returns the opposite of the user input. If the input is the Boolean "True", it returns "False". The contrary is also correct.

## Function And

```Rust,ignore

Negation (a: Bool) : Bool 
Negation Bool.true = Bool.false
Negation Bool.false = Bool.true 
```

This function basically returns True if both inputs are "True". Taking close attention to the "True" branch inside "case b", you may realize it does the same as function identity, returns the value of the input itself. Also, you don't really need to examine the "False" branch, because every other input will return False.
So, let's take a look on how to better write this function.

## Function And with Pattern Matching

```Rust,ignore
And (a: Bool) (b: Bool) : Bool
And Bool.true Bool.true = Bool.true
And Bool.true Bool.false = Bool.false
And Bool.false Bool.true = Bool.false
And Bool.false Bool.false = Bool.false
```
One possible way to write the function is opening the "case a b", in other worlds, opening both cases at the same time. But, in this situation, there are a lot of Bool.false outcomes. To avoid this repetition you can set a default answer and make the cases that matches this to be implicit. Just like below:

```Rust,ignore
And (a: Bool) (b: Bool) : Bool
And Bool.true  b = b
And Bool.false b = Bool.false
```
## Function Or

```Rust,ignore
Or (a: Bool) (b: Bool) : Bool
Or Bool.true Bool.true = Bool.true
Or Bool.true Bool.false = Bool.true
Or Bool.false Bool.true = Bool.true
Or Bool.false Bool.false = Bool.false
```

According to the Or logics, if any input to this function is "True", then the outcome will also be "True". That's why the "True" branch on "case a" does not depend on the "b" value.
On the other hand, for the "False" branch inside the same case, the outcome depends purely on the "b" value. If it is "True" the function returns "True", and if it is "False" the return will also be "False".

Let's see how it is possible to would work using Pattern Matching.

## Function Or with Pattern Matching

```Rust,ignore
Or (a: Bool) (b: Bool) : Bool {
 match Bool a {
    true => Bool.true
    false => b
  }
}
```

It basically happens the same as the function AND example, there is a repetition of the same outcome, in this case the "Bool.true". So it is possible to do the sabe as we did before, set it as the default answer and implicit some of the pattern match cases. It would look like this:

```Rust,ignore
Or (a: Bool) (b: Bool) : Bool
Or Bool.true  b = Bool.true
Or Bool.false b = b
```

## Function Equal with composition

```Rust,ignore
Eql (a: Bool) (b: Bool) : Bool
Eql Bool.true   Bool.true  = Bool.true
Eql Bool.false  Bool.false = Bool.true
Eql a           b          = Bool.false
```
You can use functions inside functions as well, that is, hacking the game! We are going to learn about it later, but as you are already here, know that you can compose functions.