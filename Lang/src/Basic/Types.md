# Basic Types

What you need to know about Types in Kind.

Types, which is short for "Data Types", have an important role in Kind's environment. They provide Kind the information about which type of data it's going to work with. That's why the word "Type" appears a lot throughout this guide.

So, it is extremely important that you get as comfortable as possible using it. Please, read this guide as many times as necessary to fell like an expert in this subject.

## What are Types?

A Type is how a group of values is seted. Every value within that group is in the same category. Bellow, types system will be represented as boxes in a visual example that makes it more understandable

---Img---

The easiest way to think about Types is to see them as a group that defines other things. Or, in a way that Astronauts can understand, they are like planets in this Kind Universe.

---Img---

This is how it looks when you define a type in Kind using Bool as an example:

```
//Bool.kind2

type Bool {  
  true
  false
}
```

There are basically two things that forms a Type, a name and its Constructors. Let's understand better this syntax:

---Img---

In the first line write ``type`` to say it is defining a type, followed by the name of the type. The first letter of this name is always in upper case, unlike ``type``, which is always written in lower case.

---Img---

The curly brackets determines where type definition starts and ends. Everything inside the curly brackets are the constructors of that type, and constructor names must be in lower case as well.

---Img---

It is important to notice that constructos do not have a value unless they are in a function. In other words, inside the Type definition, they don't have it.  For example, constructors like "true" and "false", that are from the Bool type, do not hold any meaning by themselves, they are just a name. Only when inside functions it is assigned values to them that defines what is true and what is false.

Note that a Type can hod an uncountable number of constructors:

```
//example.kind2

type Typename { 
  constructor1
  constructor2
  constructor3
  constructor4
  constr...
}
// "..." meaning continuation
```

It is very important to name constructors based on what value they will have inside the functions, it improves the readability of the code.

Now that you know Types a little bit better, it's time to dig deeper into what constitutes a type. Let's proceed to [Constructors](./Constructors.md).
