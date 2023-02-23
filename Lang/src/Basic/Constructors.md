# Constructors - Basic

What are they building?

Constructor is a short name for "data constructor". Every one of them belongs to a Type and stores a value. It can be used to store values together, but can also store none. In that last case it is called a Nullary Constructor, meaning that they are constants.

Here is an example of Constructors from the Bool Type:

```rs, ignore
 //Bool.kind2

type Bool { // type's declaration + type name
  true      // first  constructor, named true
  false     // second constructor, named false
}           // end of declaration
```

In the Boolean type we have two Nullary constructors, true and false, for the sake of simplicity, all constructors examples in the Kind Basics section are going to be Nullary, further along, examples of other types of constructors will be shown.

## Boxes for constructors

A Type is basically a box to keep the Constructors. The Bool Type, for example, is a box that stores the ``true`` and ``false`` constructors. But remember, when inside that box, they don't have any value, just a name, in order to have values, they need to be inside functions.

- *Reminder: The box is just a container, and so, it is used as an example. A type could be represented by anything with the capability to store things.*

Constructors represents the concrete values stored by a Type, and are called by using it's type name + constructor name:

```rs, ignore
// Boolean type
type Bool {
    true
    false
}

// representation of the value
    Bool.true
    //or
    Bool.false
```
The ``Bool.true`` and ``Bool.false`` are the correct way to call those constructors outside their Type. In this case, the Bool Type.