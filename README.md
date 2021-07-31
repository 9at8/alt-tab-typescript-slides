---
marp: true
theme: gaia
_class: invert
---

<style>
  section {
    justify-content: start;
    align-items: start;
  }
</style>

# Intro to meta programming in TypeScript

<!--
- Hi
- talk about TypeScript

TypeScript is a programming language that came out in 2013. Instead of compiling to bytecode, it compiles to JavaScript. It's a superset of JavaScript. It adds static typing to it.
-->

`TypeScript` = `static types` + `JavaScript`
`static types` = `TypeScript` - `JavaScript` 🤔

---

<!--
- Adi
- CS program - Graduated this year in Winter 2021
- I have been using TypeScript since 2018 - 2nd year
- Hesitant to use it for the first 2-3 weeks, but I could not let it go later on
-->

```ts
const me = {
  nickname:  "Adi",
  github:    "9at8",
  instagram: "9at8",
  discord:   "9at8#8019",
  twitter:   "9at8_",
}
```

- CS 2021
- Using TypeScript since my second co-op in January 2018
- Also used TypeScript during my interviews 😛

![bg right:33%](./me.jpg)

---

## Types as Sets

<!--
Let's get started off with some basic stuff.

We can think about types as sets. Each type represents a set of values.

- The set of booleans contains two values: true and false
- The set of numbers contain all integers and floats
- The set of strings is an infinite set of all valid strings
- Even custom types can be thought of like sets
-->

```
boolean = {true, false}
number = {-1, 2, -3, 3.14, 42, ...}
string = {"oof", "ooff", "oofff", "ooffff", ...}

interface Person {
  firstName: string
  lastName: string
}

Person  = Forall fname in string ->
            Forall lname in string ->
              ({ firstName: fname, lastName: lname })
```

---

<!--
Let's say we need to represent a box of items. There could be anything in this box. How can we represent that?

--

But once we put something inside a box, the compiler has no way of knowing what its type is going to be later on.
-->

```ts
interface Box {
  id: number
  data: unknown
}

const box: Box = { id: 42, data: "i am a string" }

box.item
//  ^^^^ oh no! the compiler no longer knows that it clearly is a string!
```

[Playground link](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgEIHsAeyDeAoZZYAEwC5kQBXAWwCNoDli4w5zKQBrEdAdxDwBfPHgToQAZzDJaWchmwBeXETLIALACYANExZtkAImDI41U8ilRQAc0PJheWZgB0zVkA)

---

## Generics

<!--
This is where generics kick in.

We can create a generic Box, which has a type argument called Data. This is very similar to how we name function parameters.
-->

```ts
interface Box<Data> {
  id: number
  data: Data
}

const stringBox: Box<string> = { id: 42, data: "i am a string" }
const numberBox: Box<number> = { id: 24, data: 1 }

stringBox.data  // <- the compiler knows that data is a string
numberBox.data  // <- the compiler knows that data is a number
```

[Playground link](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgEIHsAeAeAkpAWwD5kBvAKGWWABMAuZEAVwICNpLka4w4H8IBcgF9y5BOhABnMMhlRQAcwyYGK7PKUkAvGWr1kAFgBMAGi48+yAETBkcAvblgFIRdeSiJ02czbQVNSxsP3YoHT1aBmNDc25eBgBGTzFNNxUAOni4KgB6XORsAFpkMAALFAkCAAdgABtoZABrEHQAdylSsp4LXmpOnLTFclCArCzLPILirsr0GvrGlvbO8p7s-qdRqCA)

---

<!--
What if we only wanted boxes of strings and numbers? We can use the extends keyword along with a type union
-->

```ts
interface Box<Data extends string | number> {
  id: number
  item: Data
}

const stringBox: Box<string> = { id: 42, data: "i am a string" }
const numberBox: Box<number> = { id: 24, data: 1 }
const boolBox: Box<boolean> = { id: -42, data: true }
//                 ^^^^^^^
// Type 'boolean' does not satisfy the constraint 'string | number'.
```

[Playground link](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgEIHsAeAeAInMOZCTSEAEwGdlKwpQBzZAH2RAFcBbAI2gD5kAbwBQyZMHIAuNl15RRycgTjT8hYQF9hwhOhC0adRhkzST2WvRAMBAXiHipyACwAmADSLl0gETBkcJwBhlYMPshauvpgMjzQJmZY2BxxUHYOEtKuzp5KhNIAjBE6egbc6OgANgloSeVVEHAg6YKO0gC0brneyHTsKBpAA)

---

<!--
Lets say our boxes have more boxes inside them. We want to able to look at the type of the item in the innermost box easily. We can use the infer keyword along with recursive types for this
-->

```ts
type Unbox<T> = T extends Box<infer Data>
  ? Data extends Box<any>
    ? Unbox<Data>
    : Data
  : never

// Unbox<Box<Box<string>>> = Unbox<Box<Data>>, OR
// Data = Box<string>

const strBox = { id: 42, data: "i am a string" }
const strBoxBox = { id: 24, data: strBox }

type InnerType1 = Unbox<typeof strBox>
//   ^^^^^^^^^^ string
type InnerType2 = Unbox<typeof strBoxBox>
//   ^^^^^^^^^^ string
```

[Playground link](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgEIHsAeAeAInMOAPmQG8AoZZYAEwC5kQBXAWwCNpLkaC4H9C5AL7lyCdCADOYZNKigA5hkwNl2OYpIBeMtXrIALACYANN14MARMGRwWt2WHkgFl5CPFSZzdtGWqsbB8OKG1dWgYjAzMeQgYARndRMABPAAcUAFUQNkCAFTC85AhMSBAaSTRA0BhoZAFiLgB+et5i0ohyyrU4EBSiLioW7NycBoGqKn5eLgYQCAA3TnIAehXkEcC1NQ0XIn3kHU2cNXGiMwB5ACVV9YbDqpxdhQGxCWlHKGUH0j0GYxiFmQ1ls9jgn0Ubg87xkcmU3x0vwiyCigLin2+InIqQyyAAkiB5lA8ukIIkjjlAjiIOgYBisAM1pMAHqstnsiEubGk-GE6AkjJGB7HbDU2n0zDKRnrKjsuXMzkKIA)

---

## What is meta programming?

Wikipedia:
> Metaprogramming is a programming technique in which computer programs have the ability to treat other programs as their data.

**Metaprogram**: A program that read, generate, analyze or transform other programs, and even modify itself while running.

<!--
- move computation from runtime to compile time.
-->

---

## `meta-typing` package

[https://github.com/ronami/meta-typing](https://github.com/ronami/meta-typing)

<!--
The rest of the talk will be focused on code from the meta-typing package. This package provides a bunch of utilities for TypeScript that can be used during compile time. All the code in the slides has been more or less directly copied from that package.
-->

---

## Incrementing and decrementing numbers

<!--
TypeScript type system does not have arithmetic in it
-->

```ts
type IncTable = { 0: 1; 1: 2; 2: 3; 3: 4; 4: 5; 5: 6; 6: 7; 7: 8; 8: 9; 9: 10 };

type DecTable = { 10: 9; 9: 8; 8: 7; 7: 6; 6: 5; 5: 4; 4: 3; 3: 2; 2: 1; 1: 0 };

type Inc<T extends number> = T extends keyof IncTable
  ? IncTable[T]
  : never;

type Dec<T extends number> = T extends keyof DecTable
  ? DecTable[T]
  : never;

type Two = Inc<1>
type One = Dec<Inc<1>>
```

---

## Less than or equal to

```ts
// `true` if `A` is smaller than or equals to `B`, `false` otherwise
type Lte<A extends number, B extends number> =
  IsNever<A> extends true
  ? IsNever<B> extends true
    ? true
    : false
  : Lte<Inc<A>, Inc<B>>;

/*
  Lte<8, 9> = Lte<9, 10> = Lte<10, never> = Lte<never, never> = true
  Lte<9, 8> = Lte<10, 9> = Lte<never, 10> = false
  */
```

---

## Head and tail of a list

```ts
type Head<T extends Array<any>> =
  T extends [any, ...Array<any>]
    ? T['0']
    : never;

type Tail<T extends Array<any>> =
  T extends [any, ...infer Rest]
    ? Rest
    : never;
```


---

# THE BIG REVEAL

---

## Insertion sort

```ts
type Insert<
  N extends number,
  R extends number[]
> = R extends []
  ? [N]
  : Lte<N, Head<R>> extends true
    ? [N, ...R]
    : [
        Head<R>,
        ...Insert<N, Cast<Tail<R>, number[]>>
      ]
```

---

```ts
type InsertionSort<
  T extends number[],
  R extends number[] = []
> = T extends []
  ? R
  : InsertionSort<
      Cast<Tail<T>, number[]>,
      Insert<Head<T>, R>
    >
```

[Playground Link](https://www.typescriptlang.org/play?#code/C4TwDgpgBAkgdgYwCoEMBGAbaBeKBvKABgC4oBGAbnNICYqbSBmKx0gFirdIFYrvSAbFQGkA7FVGkAHFSmkAnFXmkyhKAF8KAKC2hIUACIRk6LFFwFVCpdNliJg4Tz7tOTFrXoqqZUms06etDwCAA8SFAQAB7AEHAAJgDOUHAArgC2aBAATgB85lAR0bEJyQDWECAA9gBmsIiomBBaUFAA-PUmTQDaSAC6LVCkcBAAbjnauuDQRmFFMXFJKRlZeQXzJUsV1XWzjViDHXumEL0DrcNjE4HTUAASECjx4ZELpVAocCDdffnYgxtFslup8QAAaKAAOmhoJ+51a7UK3QA5IRkfCLikrtlJkFCigAJYYF7FIEfL4-P4A16bYGgiHQyEEuA1HJQABKEESwAxiM53MGmJG4xxN30MESADlseE-lAzjSyd1hTk+ojgNlUtBSDUUBhEhBcbcADKxUIAQUV7zSmRyEIAQlaljbVlTWhLpSKLflSe8NVrDrApTL7T63kt-c0EerNVGETq9QbBqRTRBQiFvRCM6Hckb9ABhFDci0OuWW31LR0dS2ke156AAZTIBXgBuywAJVTgDaq7dC3QEEPkENEELIEMIELYEMYv3r9TbHa7Pb71IryRdOR+YMG7KdG5WW7VuB+WjlgPep9aHXZyYXOSX3d7wFCgoRheLqCJsohm+ylJ3aN3TgRdQgeJ4fw5XI32gqZ9AbGgWxAh9QkYCFunHKAaCnCFuF+MVgmQ1dWklfdlltbJAI5Mi-1POU93XeV4Q6bpJXhFMzUlCFwOedlcjDWkoEjN8WK4qFoXZXlSG6N9ox40I+KooDxMhVsULEj8Xy-YlFPI1ZKWg5SBiAA)

---

- Sorting
  - Quick-sort
  - Merge-sort
  - Insertion-sort

- Puzzles
  - N-Queens
  - Maze-solving
  - Binary trees
  - Square Matrix Rotation
  - Towers of Hanoi

---

# Questions?

(Probably a good idea to not use this stuff during interviews)

[Slides (source)](https://github.com/alt-tab-typescript-slides)
[Slides (html)](https://9at8.github.io/alt-tab-typescript-slides/README.html)
[Slides (pdf)](https://9at8.github.io/alt-tab-typescript-slides/README.pdf)
