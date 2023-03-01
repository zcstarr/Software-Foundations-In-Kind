# Maybe and Pair Basics

MaybeBool type and PairBool type

## Types

### MaybeBool

```Rust,ignore
type MaybeBool { 
  none 
  some (value: Bool)
 }
  
// MaybeBool concrete values:
  // MaybeBool.none
  // MaybeBool.some Bool.true 
  // MaybeBool.some Bool.false
```

### PairBool

```Rust,ignore
type PairBool {
  new (fst: Bool) (snd: Bool)
}

// PairBool concrete values:
  // PairBool.new Bool.true  Bool.true 
  // PairBool.new Bool.true  Bool.false
  // PairBool.new Bool.false Bool.true 
  // PairBool.new Bool.false Bool.false
```
