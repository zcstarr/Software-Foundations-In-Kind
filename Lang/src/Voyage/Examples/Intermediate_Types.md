# Intermediate Types

## Types

### type Relationship

```Rust,ignore
//a type with three constructors, where two of them has no value stored to it, but
//the constructor other has a value named single of the type Single

type Relationship {
  couple
  marriage
  other (single: Single)
}

// Relationship concrete values:
	// Relationship.couple
	// Relationship.marriage
	// Relationship.other (Single.lonely)
```

## type Multiple

```Rust,ignore
//a type with two Polymorphic Types named a and b and four constructors,
//
//threethings has 
//          x is a value of the type a
//          y is a value of the type b
//          z is a value of the type Multiple with two Polymorphic types a and b
//
//onething has
//          x is a value of the type b
//
//twothings has
//          x is a value of the type a
//          y is a value of the type b
//
//emtpy has no value store to it 

type Multiple (a: Type) (b: Type) {
  threethings (x: a) (y: b) (z: Multiple a b)
  onething (x: b)
  twothings (x: a) (y: b)
  empty
}

//Since a and b are variables, some of the possibilites as example:
// a = Duo            | b = Duo
// a = Multiple a b   | b = Bool
// a = Bool           | b = Nat
//They can be of any Type, a and b can have the same Type as well.

// Multiple Concrete values:
	// Multiple.threethings Bool.true Bool.false (Multiple.empty)
	// Multiple.onething Bool.false
	// Multiple.twothings Bool.true Bool.false
	// Multiple.empty
```
