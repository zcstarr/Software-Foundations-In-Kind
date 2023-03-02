# Basic Type Examples

Then, in this section you may find Basic type examples with only Nullary Constructors.

## Type Single

```Rust,ignore
// A type with only one constructor that doesn't have a stored value.
type Single { 
  lonely       // :(
}

// Single concrete values: 
  // Single.lonely
```

## Type Bool

```Rust,ignore
// A type with two constructors where both doesn't have a stored value.

type Bool { 
  true
  false
}

// Bool concrete values:
  // Bool.true
  // Bool.false
```

## Type Cardinal

```Rust,ignore
Type Cardinal
// A type with four constructors and neither of them have a stored value.

type Cardinal { 
  north
  south
  west
  east
}

// Cardinal concrete values: 
  // Cardinal.north
  // Cardinal.south
  // Cardinal.west
  // Cardinal.east
```