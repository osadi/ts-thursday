
We'll just try to establish the words used within the TS framework.

**Superset**
All valid JS is valid TS. What TS adds, is type information. So TS will not argue about syntax errors, but it will argue about how values are used.

**Erased Types**
No type information is available in the runtime JS code. Which means, that when the TS compiler is done, what you're left with is just JS. 
- There are exceptions, for example Enums
```typescript
enum NotErased {
    'FIRST',
    'SECOND'
}
console.log(NotErased.FIRST)

// Compiles down to
"use strict";
var NotErased;
(function (NotErased) {
    NotErased[NotErased["FIRST"] = 0] = "FIRST";
    NotErased[NotErased["SECOND"] = 1] = "SECOND";
})(NotErased || (NotErased = {}));
console.log(NotErased.FIRST);
```

The compiler does not change the behavior of your code.

**Union**
This is a type formed from two other types, representing a new type that could be _either_ of those two.

For example `string | number`

Typescript will then only allow operations on the value that's available for every member. 

For instance: 
```typescript
declare const stringOrNumber: string | number;

stringOrNumber.toUpperCase();
/**
Property 'toUpperCase' does not exist on type 'string | number'.  
Property 'toUpperCase' does not exist on type 'number'.
*/
```

**Narrowing**
The solution to the above 'issue' is to _narrow_ the type. That is, to write code so that TS can remove types on the Union, until it is satisfied that we have a specific type to work with.

This can be done with:
```typescript
if (typeof numberOrString === 'string) {
	// here, numberOrString is a string
	numberOrString.toUpperCase(); // works
} else {
	// TS can also deduce that this is of type number, since we've removed
	// all other possibilites in the above statement.
	// This works, bc right now we only have two types in our union.
	numberOrString + 100; // works
}
```

**Template Union**
Similar to a union, but it doesn't have to be a union of types, but of _litreal strings_.
Example:
```typescript
type Languages = 'en' | 'sv' | 'de';
// Or with template string
type LanguageWithId = `${Languages}_id`;
//   ^? type LanguageWithId = "en_id" | "sv_id" | "de_id"
```

**Type Alias**
With an alias, you can give a type a specific name.
Say that we want the above to represent an ID.
```typescript
type ID = string | number
declare const a: ID = 'abc';
declare const b: ID = 123;
```

**Conditional Types**
Similar to how you would write a ternary expression in JS:
`condition ? trueExpression : falseExpression
 but with types instead:
`SomeType extends OtherType ? TrueType : FalseType;`

**Extends** on an interface
Is used when you want to share type information between types.
```typescript
interface Base {
	name: string;
}

interface Place extends Base {
	location: string;
	// name: string; Will have the 'name' property inherited from Base
}
```

**Extends** on a generic
Adds a constraint to the generic type.
```typescript
interface Named {
	name: string;
}

function logName<Type extends Named>(arg: Type) { console.log(arg.name) };
// Inside of logName TS knows that the argument has a property 'name'.
```


**Intersection Types**
Is a type merged from two different types
```typescript
interface A { a: string };
interface B { b: number };
type C = A & B;
// type C = { a: string; b: number }
```

**Naked Type Parameter**
The type parameter is present without being wrapped in another type.
```typescript
type NakedUsage<T> = T extends boolean ? "YES" : "NO"
type WrappedUsage<T> = [T] extends [boolean] ? "YES" : "NO"; // wrapped in a tuple
```
This matters when we're talking about distribution and unions.
```typescript
type Distributed = NakedUsage<number | boolean > // = NakedUsage<number> | NakedUsage<boolean> =  "NO" | "YES" 
type NotDistributed = WrappedUsage<number | boolean > // "NO"    
type NotDistributed2 = WrappedUsage<boolean > // "YES"
```
See [Distributed Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html#distributive-conditional-types)

**Generic**

**Discriminated Union**

**Distributed Union**