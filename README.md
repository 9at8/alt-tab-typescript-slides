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

# Intro to "Advanced" TypeScript

<!--
- TypeScript is a programming language which compiles to JavaScript that came out in 2013.
- TypeScript is a superset of JavaScript.
-->

---

```ts
const me = {
  nickname:  "Adi",
  github:    "9at8",
  instagram: "9at8",
  discord:   "9at8#8019",
  twitter:   "9at8_",
}
```

---

## Intro to "advanced" TypeScript? 🤔🤔🤔

`TypeScript` = `static types` + `JavaScript`
`static types` = `TypeScript` - `JavaScript` ????

<!--
Sometimes, we want to express certain ideas in a language, but it gets pretty hard to exactly represent those ideas. This talk goes over some of those ideas that can be represented in TypeScript, but it uses techniques that you dont use everyday.
-->

---

## Basics

<!--
Lets quickly go over some basic concepts.

- Primitive types
- Type aliases
-->

- Primitive types - `string`, `number`, `boolean`
- Type aliases

  ```ts
  type Name = string
  const me: Name = "Aditya Thakral"
  ```

---

- Interfaces

```ts
  interface Person {
    name: string
  }

  function sendMessage(person: Person) { ... }

  const me = {
    name: "Aditya Thakral",
    program: "CS",
  }

  sendMessage(me)
```

<!--
- Interfaces
- TypeScript is structurally typed
-->

---

## Types as Sets

<!--
We can think about types as sets. Each type represents a set of values.

- The set of booleans contains two values: true and false
- The set of numbers contain all integers and floats
- The set of strings is an infinite set of all valid strings
- Even custom types can be thought of like sets
-->

```
boolean = {true, false}
integers = {-1, 2, -3, 3.14, 42, ...}
string = {"oof", "ooff", "oofff", "ooffff", ...}

interface Person {
  name: string
}

Person = Forall s in string -> ({ name: s })
```

<!--
Now that we know that types are like sets, what can we do with this information?
-->

---

## Unions

<!--
We have two types: the `Circle` type and the `Rectangle` type.
-->

  ```ts
  interface Circle {
    type: 'circle'
    radius: number
  }

  interface Rectangle {
    type: 'rectangle'
    length: number
    width: number
  }
  ```

---

<!--
We can now create a union of these two types.
-->

  ```ts
  type Shape = Circle | Rectangle

  function getArea(shape: Shape) {
    switch (shape.type) {
    case 'circle': return Math.PI * shape.radius ** 2
    case 'rectangle': return shape.length * shape.width
    }
  }
  ```

---

<!--
We have two sets - Cat and Dog

Let's say dogs like `number`s and cats like `string`s.

We want to create one map, that maps `string`s to `Cat`, and `number`s to `Dog`.
-->

  ```ts
  interface Cat {  // I <3 strings
    type: 'cat'
    name: string
    meow: () => void
  }

  interface Dog {  // I <3 numbers
    type: 'dog'
    name: string
    woof: () => void
  }
  ```

---

In JavaScript, this is trivial (but you don't get static typing):

  ```js
  const map = new Map()
  map.set('i am a string', cat)
  map.set(42, dog)

  // But nothing is stopping us from accidentally doing this! 😰
  map.set(24, cat)
  ```

---

How do we do this in TypeScript?

```ts
  type DogOrCatMap = Map<number, Dog> & Map<string, Cat>

  const foo: DogOrCatMap = new Map()

  const fooDog = foo.get(34)
  const fooCat = foo.get('hello')

  // ---- can we make a generic type?

  // Copied from https://github.com/ronami/meta-typing/blob/master/src/tail/index.d.ts
  export type Tail<T extends Array<any>> =
    ((...t: T) => void) extends (h: any, ...rest: infer R) => void ? R : never;

  type NewMap<TArr extends [unknown, unknown][]> =
    TArr extends [[infer TKey, infer TValue], ...any[]]
      ? Pick<Map<TKey, TValue>, 'get' | 'set' | 'delete'> & NewMap<Tail<TArr>>
      : unknown

  // ----- test

  const newMap: NewMap<[
    [string, Cat],
    [number, Dog],
  ]> = new Map()

  const aDog = newMap.get(12)
  const aCat = newMap.get('12')
  ```
