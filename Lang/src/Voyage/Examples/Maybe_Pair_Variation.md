# Maybe and Pair

Maybe type and Pair type

## Types

### Maybe

```Rust,ignore
type Maybe (a: Type) { 
  none 
  some (value: Bool)
 }
  
// Maybe concrete values:
  // Maybe.none
  // Maybe.some a value // value can be anything of the type a

//Examples:
  // Maybe.some Bool.true
  // Maybe.some (Pair.new Bool.true Bool.true)
```

### Pair

```Rust,ignore
type Pair (a: Type) (b: Type) {
  new (fst: a) (snd: b)
}

// Pair concrete values:
  // Pair.new a b fst snd // fst and snd can be anything of the type a and a
  
//Examples:
  // Pair.new Bool.true Bool.false
  // Pair.new (Maybe.none Bool) Bool.true
```
