
What is a generic type? It's any and all types, but specifically, one that we do not know at the time of writing.

Let's try an example:
```typescript
type StringArray = string[];
const myStringArray: StringArray = ['a', 'b', 'c'];
```

Nothing strange here. But what if I told you that we can write this in a much more complicated way? 😮

```typescript
type StringArray = Array<string>;
const myStringArray: StringArray = ['a', 'b', 'c'];
```

These two are identical as far as the compiler is concerned. But, what is this `Array<>` type thing?
If we look it up in `lib.es2023.array.d.ts` we see that it is declared as
```typescript
interface Array<T> {
...
}
```

Ok, so `Array<T>` can become `Array<string>` . 
So `T`, is it a magic letter? Nope. It's just a letter. One that is a stand-in for a type that we don't know when we write the generic type.. 
`T` could really be whatever is on your mind, or whatever suits the context as we'll soon see.

Let's try to create our own type with the help of generics. Let's say a `box`.  A box to put awesome stuff in.

```typescript
interface Box {}
```

And we want to be able to remove an awesome thing from the box, and put it back. We also want to label it. So we put a label on it, saying, this is a box of chocolate.

```typescript
interface ChocolateBox {
	get: () => Chocolate;
	put: (what: Chocolate) => void;
}
```

All good. This forces us to only put Chocolate in the box, and we can only get Chocolate out of the box.

Now, we found a bunch of.. `Hat`s. Let's put them in the box:
```typescript
const aHat: Hat = {};
const myChocolateBox: ChocolateBox = {};
myChocolateBox.put(aHat); // Argument of type 'Hat' is not assignable to parameter of type 'Chocolate'.
```

Ok, we need a separate box for hats. Let's create a `HatBox`

```typescript
interface HatBox {
	get: () => Hat;
	put: (what: Hat) => void;
}
```

This looks like an emerging pattern, yes?

Enter, Generics. 

```typescript
interface Box<Thing> { // Here, Thing is used instead of T.
	get: () => Thing;
	put: (what: Thing) => void;
}
```

This gives us a box that we can put things in. We don't know yet what type of things, but we know that if you put a Thing in, you get a Thing out.

If we try to use this then, it could look like this:

```typescript
const myChocolateBox: Box<Chocolate> = {};
const myHatBox: Box<Hat> = {};
```

With this pattern, we could create as many different boxes that we want.

For more in depth information, here's the relevant section in the [Docs](https://www.typescriptlang.org/docs/handbook/2/generics.html)
