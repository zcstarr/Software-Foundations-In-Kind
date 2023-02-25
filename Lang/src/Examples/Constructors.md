# Basic Constructors Examples

Here are examples of nullary constructors using different Types.

## Single Constructors

```Rust,ignore
// A type with only one constructor without a stored value.

type Single {
  lonely       // :(
}

// Single constructor: 
  // lonely
```

## Bool Constructors

```Rust,ignore
// A type with two constructors where both do not have a stored value.

type Bool { 
  true
  false 
}

// Bool constructors:
  // true
  // false
```

## Cardinal Constructors

```Rust,ignore
// A type with four constructors and neither of them have a stored value.

type Cardinal { 
  north  
  south  
  west   
  east     
}

// Cardinal constructors: 
  // north
  // south
  // west
  // east
```