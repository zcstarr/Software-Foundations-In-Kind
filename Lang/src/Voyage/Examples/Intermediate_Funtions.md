# Intermediate Functions

## MaybeBool Examples

### Function Create

```Rust,ignore
Create (a:Bool) : MaybeBool{
 MaybeBool.some a
}
  
```

The create function gets the input "a" of the Type Bool and creates a Type MaybeBool where its value is "a". Therefore, if "a" is Bool.true, the result will be ``MaybeBool.some Bool.true``, note that the value of "a" is now stored inside the constructor "some" of the MaybeBool type.

### Function Or

```Rust,ignore
Or (a: MaybeBool) (b: MaybeBool) : MaybeBool {
  match MaybeBool a {
    none => b
    some value => MaybeBool.some value
  }
}
```

The function "or" returns the first input that is a Maybe.some

## PairBool Examples

### Function neg

```Rust,ignore
Neg (riap: PairBool) : PairBool {
  match PairBool riap {
    new fst snd => 
      match Bool fst {
        true => 
          match Bool snd {
            true => PairBool.new Bool.false Bool.false
            false => PairBool.new Bool.false Bool.true
          }
        false => 
          match Bool snd {
            true => PairBool.new Bool.true Bool.false
            false => PairBool.new Bool.true Bool.true
          }
      }
  }
}
```

This function receives a PairBool and returns a new PairBool with the negation of its elements. If you look closely, there is a smarter way to do this function.

## Function swap

```Rust,ignore
swap (a: PairBool) : PairBool {
   match PairBool a {
    new: PairBool.new a.snd a.fst
  }
}
```

The swap functions simply swaps the first with the second element of the pair. Basically it creates a new pair where the second element is the first and vice versa.
