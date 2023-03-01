# Currying

Spicing things up

## A Different method to represent a function

We learned in [Basic Functions](../Basic/Functions.md) the standard method to build a function but here things are about to get spicy.

## Currying

Currying is the transformation of a function with multiple arguments into a sequence of single-argument functions. That means converting a function like this f(a) (b) (c). The variables, a, b and c must have a type in Kind, lets name them a,b and c Polymorphic Types, since they can be of any type, and represent them using an Arrow:

```rs,ignore
f: a -> b -> c
```

In this example the function f has an argument of the type a, then one of the type b and return something of the type c, remember, a, b and c are only names for the Polymorphic Types, they can have the same type:
```rs,ignore
f (a: Bool) (b:Bool) : Bool         // Bool.eql, Bool.or, Bool.and, ...
/////////////////////////
f:    Bool ->  Bool -> Bool        // Bool.eql, Bool.or, Bool.and, ...
/////////////////////////
f:    Bool ->  Bool -> Bool
|      |        |       |
v      v        v       v
f (a: Bool) (b:Bool) : Bool
```
This method works for representing Generic Functions, since our function f can be any Bool function that receives two arguments, we don't clearly know which function we are referring to, that means we abstracted the specific function to a generic one.
We are going to use this syntax in some functions and to understand better the [type Nat](./Numbers.md).
