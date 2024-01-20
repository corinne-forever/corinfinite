---
share: true
---

I really want to use Rust on the frontend, but the maturity of the React ecosystem is so much better and is better known by AI tools.

This article seeks to document things I wish Typescript had and ways to go about getting them.

# Algebraic Data Types
There may be a few ways to to ADTs. I haven't looked in detail.

https://github.com/pfgray/ts-adt


```ts
type DiscriminatedUnion<K extends PropertyKey, T extends object> = {
  [P in keyof T]: ({ [Q in K]: P } & T[P]) extends infer U ? { [Q in keyof U]: U[Q] } : never
}[keyof T]

type MyUnion = DiscriminatedUnion<"kind", {
  circle: { radius: number },
  square: { length: number }
}>

// Full example: http://www.typescriptlang.org/play/#code/C4TwDgpgBAIglgZwMYCc4Fs4DsCGwIAmAqlnAPZYA8A0lBAB75YEJQAKKZkKo1EIAGigAVOowjNWZAEYArCEmAA+KAF4oAbwBQUKAG02UbFADW-MgDMRAXQBcUABQb9ARSNYo1O+ygBfKABkIgbWAJRiTCzuFhAoUERQAPyaru6m5lZE3kR6LtZ+UPZYEABusVq+emYgljZaWqCQUACyICTkHurwyGiYuPjEpBSUAEQm2AQjQtq6SHAoSAA2EPbOKDgEcACuCEVb6NKxfgI6UAgAjls4KCspy1gA5sAAFnsHR74VSjpaAPQAVFBGtBWu0KGpNKddONmPYRnMFssRgBuKFQdabHZvQ4oVH+AA+kN00ImcIuVxuKLR9yerygWH2OLxUH+vyAA

// Source: https://github.com/microsoft/TypeScript/issues/36336
```


# Functional programming via fp-ts
Several functional programming concepts are supported by [fp-ts](https://github.com/gcanti/fp-ts) which contains many [useful modules](https://gcanti.github.io/fp-ts/modules/).

Some useful items are:
- NonEmptyArray
- Either (similar-ish to Rust's Result type)
- Option

See also: https://www.purescript.org/

# Serialization
Rust's serde is a great example of the kind of workflow one would like for taking input from external systems and turning into a validated strong type.

In TypeScript is is very tempting to cast an any you got to the type you think it should be (e.g. the JSON payload to a REST endpoint) and plug away, maybe with a little validation. That approach is bug prone as it's easy to miss verifying a field when new ones are added and can result in an error when the object is later used. 

Last I looked the solutions for this were generally lacking, but it's possible I missed something or there are new solutions that are better.

For example: 
- https://github.com/gcanti/io-ts
- https://github.com/colinhacks/zod
- typia
- class-validator
- class-transformer

# nominal typing
There is an outstanding issue to add first class support to nominal typing: https://github.com/microsoft/TypeScript/issues/202

There are many ways to address this. For example [this page](https://basarat.gitbook.io/typescript/main-1/nominaltyping)  discusses some solutions, but none of them I think are particularly elegant.

This is the one I have used previously for builtin types:
```ts
// For builtin types like strings and numbers:
type UserId = number & { readonly __brand: unique symbol };

// TODO: check if this disallows comparisons between UserIds and numbers

function makeUserId(n: number) {
    return n as UserId;
}

// For classes:
class Foo {
	private __nominal: void
	constructor(public name: string) {}
}
```

Ultimately I think my preferred way would look like what I like to do in in C++:
```ts
// For single value
class Foo {
	private __nominal: void
	constructor(public value: string) {}
}

Foo f;
string s = f.value;
// value can be made private or readonly for further encapsulation if desired

// For multiple values
class Bar {
	private __nominal: void
	constructor(public name: string, public date: string) {}
}
```

This may also be possible to achieve with https://github.com/gcanti/newtype-ts

Another proposed mechanism:
```
declare const symbolB : unique symbol; 
interface B {
    x: any;
    symbol: typeof symbolB;
};
declare const b:B;

const symbolA = Symbol("a");
interface A {
    x: any;
    symbol: typeof symbolA;
};
let a:A = b; // error
//  ~
// Type 'B' is not assignable to type 'A'.
//   Types of property 'symbol' are incompatible.
//     Type 'typeof symbolB' is not assignable to type 'typeof symbolA'.(2322)

const c = Math.random() ? a : b;
if (c.symbol===symbolB){
    c; // const c: B
}
```