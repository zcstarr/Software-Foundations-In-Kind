# Polymorphic Types

Types Part 2

## First Encounter

Welcome to this new planet, the Pair planet, in here we are going to learn about a new sort of Type.

In the Pair Planet, every single life form is tied to another, two things walking hands held to each other, we can also notice, all life forms in the Bool Planet they have no specific shape, their shape is NOT YET DEFINED, but let me show you a thing, the portal shaper.

Whenever the pairs pass through the portal shaper, they actually get their shape defined, and once its defined they can't go back, therefore, before the portal shaper they have no defined shape, after they pass through they get their shape defined.

The Pair shape, each one get an individual shape, they can be the same, they can be different, no matter, they can be anything, as long as they pass through the Shaper, this sort of life form is known as Polymorphic.

## Polymorphism

Whenever i ask you to think of a Pair, i am pretty sure your brain will think of a Pair of somethings am i right? even if you think of a pair of "nothings", this nothing is a something, on Kind it is the same. Something is abstract and can be anything, this something can be Fruits, can be Cars, can be Number, can be anything of a group, and groups are Types.

The portal shaper is the return of the function, and before the pairs pass through they are variables, and once they pass, the variable becomes a specific Type.

In Kind we name these as Polymorphic Types, they are variables that can assume any type, some types needs these variables in order to encompass multiple things, same for Lists, whenever you think of a list, you think of a list of something, imagine having to define types for every single thing in existence? The way we do it, is having a variable that we can assign it to some specific type we want.

## Pairs

As we have seen in [Constructors with Value](./Constructors.md), the Pair is a type with a single constructor that has two arguments to it, the First (fst) value of the pair and the Second (snd) value of that pair, but now we are going to modify a thing, our Pair was a Pair of Booleans, now we are going to make the type Pair that includes all types in kind using Variables as Polymorphic Types, in this case it would look like this:

```Rust,ignore
type Pair (a: Type) (b: Type) { 
  new (fst: a) (snd: b)
}
```

Well, let us read that right now:

Hmrm *clears throat* the type ``Pair``  is a type that has **TWO POLYMORPHIC TYPES A and B**, it has one constructor named *new*,  and this constructor has two (arguments, values, variables. Either of these options work) values, fst is of type A and snd is of the type B.

This would be the anatomy of this type.

--Img--

A and B are type variables, we call these variables Polymorphic Types, to differ them of value variables, to make the code more readable, most of the Type variables are written in Upper case Letter, as the constructor variables usually start in lower case, to make it clearer to the reader.

The Pair type has two variables because you can have a Pair of two distinct things, like we saw earlier, but A and B can be of the same type, that is not a problem, as long as their values belong to the Types you input, Pairs accept them all, even other pairs (Let this part for later! Too deep down!!!).

That changes mainly the way we build our concrete values with Polymorphic Types, as we specify what is the type of each of the values inside the constructor before we fill them, here are some examples of Pairs:

```Rust,ignore
// Pair of Bools 
Pair.new Bool Bool Bool.true  Bool.true 
Pair.new Bool Bool Bool.false Bool.false

//Pair of Number and Text 
Pair.new Nat String 1n "Anna"
Pair.new Nat String 2n "Bruce"

//Pair of a Numeric List and a Boolean
Pair.new (List Nat) Bool [] Bool.true
Pair.new (List Nat) Bool [1n] Bool.false
```

As said above, you gotta be very precise with specifying a type, if you invert A with B you would get errors.

```Rust,ignore
// Here we said the First type is a Number and we wrote a String (Text)
// We also said the Second type is a String and we wrote a Nat (Number)

Pair.new Nat String "Anna" 1n 
Pair.new Nat String "Bruce" 2n
```

Next, let's look for the other type we learned earlier the Maybe type, but now, lets make it work with all Types, having a Polymorphic Type variable.

## Maybe

We are going to use Maybe a lot, imagine we need to write a function that get the first element of a list, if this list is empty, what would we return? the number 0? a text saying "nothing"? we have the Maybe.none for that, and if that list has something, we take the first element and save him inside our bag, the Maybe.some. Bless the Maybe!!

Like the Pair and many other types are going to work in the future, the Maybe type has a Polymorphic Type variable attached to it, since we can save everything on it, isn't it lovely?(Is it clear my favoritism to the type maybe? no? i gotta make it clear here!) This is how it should look:

```Rust,ignore
type Maybe (a: Type) {
  none 
  some (value: a)
}
```

Well, let us read that:

Hmrm *clears throat* the type ``Maybe`` is a type that has  **ONE POLYMORPHIC TYPE A**, it has two constructors, none and some, and the some constructor has one (argument, value, variable. Either of these options work) variable, named value that is something of the type A.

In this case we only need one Polymorphic Type variable, since it only stores one value of a single Type
Lets build some concrete value on the Maybe type:

```Rust,ignore
//None examples
Maybe.none Bool
Maybe.none Nat
Maybe.none String
Maybe.none (List Nat)

//Some examples
Maybe.some Bool Bool.true
Maybe.some Bool Bool.false
Maybe.some String "Maria"
Maybe.some String "Anna"
```

I like to say that the some constructor "hugs" the value and takes care of him, the Maybe is very lovely with their values!

Since the type Pair stores two values and the type Maybe stores one value, if we need to access them somehow in a function, we are going to do within cases as well, pretty much everything in kind is done with case, let us learn how to in the next section, [Accessing Constructor Values](./Accessing.md).
