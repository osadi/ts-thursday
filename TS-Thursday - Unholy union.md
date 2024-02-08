We'll start this of by creating a couple of interfaces, with a property `type` that we can discriminate against:

```typescript
interface Disciple {
  type: 'disciple';
  name: string;
}

interface Angel {
  type: 'angel'
  name: string;
  wings: boolean;
}

interface Wolf {
  type: 'animal';
  name: string;
  legs: number;
}
```

And we'll add them to a union like so:
```typescript
type UnholyUnion = Disciple | Angel | Wolf;
```

So now to the problem; How can we create a new union from this, that's a subtype of our `UnholyUnion`?

We would probably reach for `Pick` to help us out here. It's documented as `From T, pick a set of properties whose keys are in the union K`. Sounds about right.
  
```typescript
type PickedFromUnion = Pick<UnholyUnion, 'type' | 'name'>
```

What we want:
```typescript
type PickedFromUnion = 
| {type: 'disciple'; name: string; }
| {type: 'angel'; name: string;}
| {type: 'animal'; name: string;}
```

What do we get? Well, not what we were hoping for..
```typescript
type PickedFromUnion = {
  type: 'disciple' | 'angel' | 'animal';
  name: string
}
```
  
Why is that?
Here's a discussion on [GitHub](https://github.com/microsoft/TypeScript/issues/28339) where they explain that: 
> Pick<T, K>/Omit<T, K> are **not** distributive over union types

Ok, so how do we get what we want?

Here's one way:
```typescript
type DistributedPick<Type, Keys extends keyof Type> = Type extends unknown
  ? Pick<Type, Keys>
  : never
```

 Running our type through this gives us:
```typescript
type DistributedPickFromUnion = DistributedPick<UnholyUnion, 'type' | 'name'>
/**
| {type: 'disciple'; name: string; }
| {type: 'angel'; name: string;}
| {type: 'animal'; name: string;}
*/
```  

How does this work?

To understand this, we first need to understand what `unknown` is. Taken from the TS [documentation on unknown](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-0.html#new-unknown-top-type):
> `unknown` is the type-safe counterpart of `any`. Anything is assignable to `unknown`, but `unknown` isn’t assignable to anything but itself and `any` without a type assertion or a control flow based narrowing. Likewise, no operations are permitted on an `unknown` without first asserting or narrowing to a more specific type.

We can test this with
```typescript
type Test = Wolf extends unknown ? true : false;
// Test = true, because anything is assignable to unknown.
```

So, if the type we supply on `Type` extends `unkown`, (which we now know that all types do), we from `Type` `Pick` all supplied `Keys`, ending up with a new union.

Mission accomplished!