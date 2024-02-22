
TypeScript has a bunch of utility types available to us, which can all be found here: https://www.typescriptlang.org/docs/handbook/utility-types.htm

So, today we'll try to deconstruct and understand some of them a little better.

Let's start with [Pick](https://www.typescriptlang.org/docs/handbook/utility-types.html#picktype-keys)

It's defined in the documentation as:

> `Pick<Type, Keys>`
> Released:  
> [2.1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html#partial-readonly-record-and-pick)
> Constructs a type by picking the set of properties `Keys` (string literal or union of string literals) from `Type`

We'll start by understanding the description.

`Pick<Type, Keys>` - This tells us that Pick is a generic type, with two type arguments.

`Constructs a type` - So, we'll get a new type returned from it.

`Keys` - The second argument, should be a `string literal` or a `union of string literals` that exist on `Type`

`string literal` - Is a literal string. That is for example `'ImAString'`, and not a primitive like `string`.

`union of string literals` - Would then be a `Union` of literal strings, like `'first' | 'second'`

`Union` - Could probably be described as `|`, which is used for joining types together.
> A union type is a type formed from two or more other types, representing values that may be _any one_ of those types. We refer to each of these types as the union’s _members_.

`Type` - Any type with properties.

Ok, so we now know what `Pick` wants from us, and what to expect. Let's try it out:

```typescript
type OurObject = { a: string, b: number; c: boolean};
type BandC = Pick<OurObject, 'b' | 'c'>
//   ^? type BandC = { b: number; c: boolean; }
```

We've 'picked' `b` and `c` from `OurObject` and end up with a new type, that's a copy of `OurObject` but without the property `a`.

How does this work under the hood?

Pick is written as `Pick<T, K extends keyof T> = { [P in K]: T[P]; }`
So, we'll make our own, and try to understand the parts of this.

```typescript
type OurObject = { a: string, b: number; c: boolean};

type OurPick<T, K extends keyof T> = { [P in K]: T[P]; }
type BandC1 = OurPick<OurObject, 'b' | 'c'>;
//   ^? type BandC1 = { b: number; c: boolean; }
```

We can see that the result of `OurPick` is the same as the utility type `Pick`.

We see that `T` is what was defined as `Type` earlier, and that `K` is what was defined as `Keys`. We also se that `K` extends `keyof T`. This means that what we provide as a literal string, or a union of literal strings, must also be existing properties on the type `T`.

If we were to write this out in a non generic way:
```typescript
type OurObject = { a: string, b: number; c: boolean};

type NonGenericPick = { [Property in 'b' | 'c']: OurObject[Property]; }
type BandC2 = NonGenericPick;
//   ^? type BandC2 = { b: number; c: boolean; }
```

We iterate over the provided `K` keys (b, c), and for each iteration we assign the current value to `Property`. With `Property` we lookup the corresponding value in `OurType`. 

This type of Type, that iterates, is actually called a [Mapped Type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html). And it is defined in the documentation as:
> A mapped type is a generic type which uses a union of `PropertyKey`s (frequently created [via a `keyof`](https://www.typescriptlang.org/docs/handbook/2/indexed-access-types.html)) to iterate through keys to create a type:

And an implementation could look like this:
```typescript
type OptionsFlags<Type> = {
  [Property in keyof Type]: boolean;
};
```

But diving deeper in to that is for another time. Let's just continue, and remove the mapping as well:
```typescript
type OurObject = { a: string, b: number; c: boolean};

type NonGenericNonIterativePick = 
{ b: OurObject['b']; } &
{ c: OurObject['c']; }

type BandC3 = NonGenericNonIterativePick;
//   ^? type BandC3 = { b: OurObject['b']; } & { c: OurObject['c']; }
```

Type hints break down a bit here. But the result is the same as before.

So, we've gone through a bunch of concepts in this writeup. And I don't really know how to end this. I guess a key takeaway is that complex types are, from what we've seen, built on other concepts and built in types.

Ok, let's end with a naïve implementation in code rather than types:
```typescript
function Pick(object: Record<string,any>, properties: string[]) {
    const newType: Record<string, any> = {};
    for (const property in object) {
        if (properties.includes(property)) {
            newType[property] = object[property]
        }
    }
    return newType;
}

const picked = Pick({a: 'string', b: 'string', c: 'string'}, ['a', 'b']);
console.log(picked)
```

Link to [Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBA8grgJxgIwFYQMbCgXigbygEMAuKAZ2AQEsA7AcwBopkza4BbZCBAbigxlkAe2EAbCEVoBfXgFgAUItCQoAISkATAMK4oABWoYA1gB54SNJmDMA5MltQAPlFsZbAPkUB6b1H8AegD8isrg0BaGJqYAKswA0lAQAB7AELSa5FDGECDCAGZQMR56hADa+lB0UPEAumQxFbX80mGqGhnaAIx6kUZmFijoWHYOzq7uHvIKvv5QwaEKKtAAcsK0AOLpPEZRxqVQFQjCkAigVbSuYy5utvWwiEPWRyc8oM1QrUvh6lraAEx6NabbY0DB7aazQIhJTfVTAra0HYYYEASTSCCIwGoADcIHtcIpCKwHpZhsAyvY7i0oAAyIkCMiDKxYSnuD5fNrQDo6ADMQPWiORaIxWNx+P6kL80MWGHWlGIJB5ujwxJIXWYgigVDg0C+RAAdMgfH4FgpDRgTfMYYaiFazYp8nBaFhqOsDP0ABTCFnAMgAJUwwgQmlMlBoDEYUhAHmYYGOp2xEHIZHDdHoZVqAEoCIo5gJ5dgkQB3GLhANBkNhqjp5jRkqq2R5ub5YNQT1y2gK+OvM4gC5QH3knP4Zv5-zUQqenuJ6jJg10DBiOCaZPThNvEBZkdj8f5ktlyBlGeb2p6IfPE992q7-NfO+7hAQYCIS4H8LTTkKTvd-oQTR6Hsnr4KQrhpgwtjMCStjgfQkGMmBNYQdIzCUkQ8FUtm0w-uIEAGmIwj0NOf6aFmQA)
