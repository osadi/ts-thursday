Let's talk about `in`. 

What would be the difference between:
```typescript
'thursday' in week
weekday in Week
```

In JavaScript land, we use it as:
```javascript
const obj = { id: '123' };
'id' in obj; // true
```

Where it checks if the prop (supplied as a string or a symbol) is defined on the object, or the objects prototype chain.
Note that the property can have the value `undefined` and the check would still return `true`.

One thing that might not be obvious is that we can use `in` on arrays as well:
```javascript
const myArr = ['one', 'two', 'three'];
0 in myArr // true
3 in myArr // false
'one' in myArr // false
```

There's more to `in` in JS than what we covered here but that's enough for now.

Let's look at `in` from a TypeScript perspective then. Important to remember is that it's still valid in all places shown above, but also in a type declaration. And there it behaves quite different.

We'll touch on subjects such as `union`, `mapped types` , `keyof` and probably more, to be able to talk about this little word. But we'll gloss over a lot of details.

We use `in` when we build a new type with the help of a  [Mapped Type](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)

```typescript
type MappedType = {
	[Prop in Union]: AnyType
}
```

When `in` is used in this place, it's an iterator that works on each member of a union. So if we try to break this down:
```typescript
type Weekdays = 'monday' | 'tuesday' | 'wednesday' | 'thursday' | 'friday';

type MappedType = {
	[Weekday in Weekdays]: string;
}
// Produces
// { monday: string; tuesday: string; wednesday: string; ...etc }
```

Here `tsc` would iterate over `Weekdays`, assign each member to `Weekday`, and produce the final type. Something like:
```typescript
[Weekday in Weekdays]
[Weekday = 'monday']
[monday]: string
monday: string
[Weekday = 'tuesday']
[tuesday]: string
tuesday: string
...
```

And a `union` can be created by typing it explicitly by us, or it can be produced by something like `keyof`.

```typescript
type MyItem = {
	id: string;
	name: string;
	active: boolean;
}

const myItem: MyItem = {
	id: '123',
	name: 'thing',
	active: true
}

type NewUnion = keyof MyItem;
type NewUnion2 = keyof typeof myItem;

```

So the gist of it:
`in obj` checks if a property is defined.
`in Union` iterates over a union.

We'll end with a  [Playground](https://www.typescriptlang.org/play?#code/FAehAIDkHsBdwM7XAVwQUwGYoDYDphYBPAB3XAFkiBBAJ1oEMjwBeRWWgSwDsBzAbQC6AbkKlyFBiTIATACriAYrWgBbOo2ZsA3sHDgw4AHoB+PeH4BrdMx7hrRaJko16TQQC52XPsAC+wKAQcgAWnAjgtOgAxlEMsOgR0Ci0LgDyAEYAVuDEZHjg1DhI4NxwiMhoWLgEhgBCKPCwIeQO4DjQ0JYoJOAtUeDRDNzgGeRVMuCY0Klw-blhfAgANOAMEQDu6ADkODiI6OgEeRJEmTk6U5y0CLBetz68qxjR0NwyXtwoqmO0fqInShSWQKMjKNTnVjgXT6QymcxWGzgOwOJzpbKedFZRFEET+QKGULhcAbTh7SIxOIJcDJVJUSEnVYZRprckkFRkWiwTiJNYDMobUrfX5BQrRWAoBh7ZhVbD4MRkIHSdDyJQqVTnABMUJhBgg8P0OORI1RznpGM+wvQtFEAVFRM2ZP2UVi6Hi5FpWNy4iZLKl+wePF4EQF4FUaHg6HexvA2zwDC2SFUOwVEmBKtB6HBGuyAGYdeY4WZDW0UTY0easpiAAYAEm0lZxgj88cTanQ1dtwFe3FuYfTMlzXkkytVYPV53zOnMmGuty820DfG2q0MAB4ALSlcoJxLt8BR5K8ELmF5vD6xgCMAAZW3vk9t8aKABLW8hbQZU8jDGnZGKwVYNn6cg3WiEJ2k4BJGH2TB1QWcZuE4N5kQiBh7CRZDPW4dBBWgP9xVqCAihKdl0FgYhUAwOVjnEFwAFVEOQthtlnG5YG2cAAB9YzPd4OO47ZmmuGR+NjaYUmabYAVokcQTVNQGKQkZp1hfViwsUsRioRS3kxJdeC7UUAHVyCGEZXh+Hhv1GFBuDAmlnGaRJyAed1eB5BBUyVWQAHEyKgtc5AAPgLVTjHU-gAAUOWtCi7G0xiRnWcBq14Mj6wAYSkSCpU4AAvdA12i6BOWIIK-GrTE5EMwxhkmKIqng1RwC82SVUgK1aD88jrShNqZG6gKvh+a0gtEQx9HhYzTJ-WAGGsNZ2k6Sw1iaFo1gANwYMkGAyHBxm5HBIOYQFq0ihguU4KVq1WZp4hQxbmTJeA7FUAdvXyUUQnIkgEA8MANkBvATgQWJOBIWAcGGXg8BmXgQBkaBogQEAQjqjJlpARoyWOjcQbwb7VBwABiEgLu5KUTlFABJF6IhkLArMmdYPFAAAqLzzsuqVApCy4opjU1wDkQQTC8OQosELjUHeRnsJkYRwACNmQECQEuYpnBKyhDWrpwNdKzG0VJrMAkIAAGTIiIOBOyoMEWt7Rw+8hplSJMyMWXhKOqeVDFfAZq1p9BVGrB6sgjNZuBOn1wAAaSRcM+zGRa2jRIPVEI8AAFEAA8GFUEh9vAdOdWRC99KV1Z4+YRP4GT6tOBkatgAZ6IoYGbA7O5ZC0tgABldshL4Nd06rpF0BzhJ3giIX06CgAKQtgjCCIECYa20fgD8NmGeAfzwrJ-2hAIwtJZpwHZErYo8hZ7on8IXpGavVm2RuV0jyYnPALacBQchiXTvwV+IlBAEH0A4Lw2gjR2GrpiQBDhmyrHMNwfO6B7gcCDMAAAlF4dOgR7Qr37NYCIqgZguSjPbTgzgPxow2t+EYB8j5n3AqQgYl9SoeWADwKCmAGDRFOCXXUjd0GPFEPobkyZbj5xIJaEaNpzCWB4BebYLQZBpRCNAXgUlkGoJEUGMRF8uDREsDgIgXgMbQH2sMQyPY+xRAiGwXuA9kxD14AbIgo9YxvwXtoMuC59IcT8C-FBD4sGiFsfAex2pHFkWcR7IM7jPHAO2D4+wSj-EYOXJXWMISdhhL1ClRR7xQ7EjKFvTeJIXLbUmNvXeKpuxvDsYkKc4AnGD09okhIqgX5v2ltsdknBjGmJSfPXxwieKZK0dk7YuTtj5MMCZbYAxVDhAQEGFKAyhlEGbhEikCAAAsUI2kuI6VQJJvSBKbJMUQEZYzlEBNWFc0x-j2lBkCcE1BczFaGGrE87ZD0v4bBUHwZ2DTeyRMSAAViObE15w8zldJ6SJPpfzbl+ImY8d+fyvAcD-tM2Z8yICLIGLwTozNeDbW4Pgv2b5KmshKBgcgX8G7cBKUlcUSkCi03ADIB+XBmQJCSHQ1IaFbJKWeMgZhi0xWYWcMklFRjrnbFZiADm4CbCQOgU-GwcCulNiCS1fQYwLKJAURq6E6L9KPMVc80YnQrEjGVqrM24ATKDGSDgZmxRJWMF6J6VKsKTlBlDnVIh-94AIDINEKhgzM5xNcRBBaQkEAFDdTvbga0Zp7F6rAZAkaYgxqGNKRAPRC7MB-NWbQ4z9IVTWBvF2c54Bk0YC4nNyAv6dw5W8FuMR24u1sl2kYxz4l8DqB4mQI8ukHknlGGQEQq3l0mX4BeeBV3NtQVBP64AuYbutAgNcJw0TDtcZO4OSKUlBRweAAASmRFI3BMwHvEEewNI63HnJEkFIKgRuHWl4fwlw6cYDU0mLqCRiQ5oF1kb8AxRTlGqPUZo7R+hcl6L4AY7FdrLFum4F2XZ9iABsML+5wt4GOkDnTg7AZkKk8Zi5JnvJyZ8wlwt1oJtqRmsMEdaH0JSo3UO7DYpEDBU0hAAB2Yj8bPbkYnQi4OtGZD3IY-i5j3yiXfm9VxvsCBS2mMjr+Q+4oSSQXAhW-jhir5cmE8AIAA) with a few examples and random type manipulation.