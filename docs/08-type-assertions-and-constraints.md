<h2 id= 'type-assertions-and-constraints'>TYPE ASSERTIONS AND CONSTRAINTS</h2>

[Back to Index](_sidebar.md)

<!-- todo: brief intro -->

<h3 id= 'type-assertions-with-as'>Type Assertions with `as`</h3>

Type Assertion or Type Casting in TypeScript is a way to assert that a value is of a particular type. It basically silences any type checking from TypeScript. You're telling the compiler: "Trust me, the value is of this type", so you better be sure! Use as a last resort, only when you know more about the type than the compiler can infer. It doesn't change runtime behaviour, so if you assert incorrectly, you risk runtime errors.

The `as` keyword is commonly used for type assertions. Let's look at an example. Suppose you fetch some data from an API. Its type is `any` but you KNOW what shape the data should have:

Example:

```ts
type User = {
  id: number;
  name: string;
};

const data = '{"id": 1, "name": "Debs"}';

// parsedData is of `any` type
const parsedData = JSON.parse(data);

// We can assert it as `User`
const user = parsedData as User;

// TypeScript now knows that `user.name` is a string
console.log(user.name.toUpperCase()); // Output: DEBS
```

But what if we were wrong and `name` is actually a number:

```ts
const data = '{"id": 1, "name": 79}';

const user = JSON.parse(data) as User;

console.log(user.name.toUpperCase());
```

We have asserted a particular type and TypeScript trusts our assertion. But when we run the code as before, we will get a runtime error this time:

```bash
console.log(user.name.toUpperCase());
C:\Users\akink\Desktop\typescript-tutorial\practice.js:758
console.log(user.name.toUpperCase());
                      ^

TypeError: user.name.toUpperCase is not a function
```

<h3 id= 'literal-assertions-with-as-const'>Literal Assertions with `as const`</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-assertions-and-constraints)

`as const` is a special form of type assertion that freezes a value to its literal type. Whether it's a primitive value or an object or an array, it makes it deeply `readonly`. "deeply" means that for arrays and objects, nested elements are also `readonly`. "readonly" means that the value cannot be reassigned after initialization.

```ts
// `as const` with a primitive (string)
let course = "TypeScript" as const;
course = "JavaScript"; // Error: Type '"JavaScript"' is not assignable to type '"TypeScript"'
```

In the example above, it is suddenly as if we declared `course` with `const`:

```ts
const course = "TypeScript";
```

`as const` freezes the variable to its narrowest possible type which is the literal type `TypeScript`. The type is no longer inferred as `string`.

```ts
// `as const` with a two-dimensional array
const names = ["deBee", "Debs", "Emmanuel", [6, 23, 28]] as const;

names[0] = "Mike"; // Error: Cannot assign to '0' because it is a read-only property.
names[3][1] = 17; // Error: Cannot assign to '1' because it is a read-only property.
```

In the example above, the `names` array is now "readonly". Its elements are recursively frozen such that, no matter the dimension of nesting, they cannot be reassigned and they remain "readonly" too. TypeScript infers the type of each element as its literal type:

```ts
const name = names[1];
```

TypeScript infers the type of `name` as `Debs` rather than `string`

```ts
// `as const` with a nested object
const person = {
  id: 1,
  name: "Debs",
  contact: { email: "hello@nonexistent.com", phone: "000-00-0000" },
} as const;

person.name = "Mike"; // Error: Cannot assign to 'name' because it is a read-only property.
person.contact.email = "bye@nonexistent.com"; // Error: Cannot assign to 'email' because it is a read-only property.
```

The same applies to objects. Each property in the object is now inferred as its literal type, and none of them can be reassigned, including nested properties.

<h3 id= 'safer-constraints-with-satisfies'>Safer Constraints with `satisfies`</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-assertions-and-constraints)

The `satisfies` keyword is a type constraint operator. It checks that a value conforms to a given type. If it doesn't, TypeScript raises an error; if it does, the value still keeps its inferred type. It is a way of telling the compiler: "Make sure this value is valid for this type".

```ts
type Person = {
  id: number;
  name: string;
};

const person = {
  id: 1,
  name: "Debs",
} satisfies Person;
```

If you leave out any property, TypeScript raises an error:

```ts
const person = {
  id: 1,
} satisfies Person;
// Error: Property 'name' is missing in type '{ id: number; }' but required in type 'Person'
```

If you add an extra property, TypeScript also raises an error:

```ts
const person = {
  id: 1,
  name: "Debs",
  password: "password",
} satisfies Person;
// Error: Object literal may only specify known properties, and 'password' does not exist in type 'Person'.
```

It's a bit different when working with non-literals, that is, pre-declared variables:

```ts
type Person = {
  id: number;
  name: string;
};

const personData = {
  id: 1,
  name: "Debs",
  email: "hello@nonexistent.com", // extra property
};

const person = personData satisfies Person;
// No error
```

In the example above, `satisfies` only checks that `personData` is assignable to `Person`. Since, `personData` has at least `{id: number, name: string}`, it satisfies `Person`. Extra properties are allowed when the object comes from a variable. TypeScript only applies the "excess property checking" rule to inline object literals (as in the previous example), not to pre-declared variables.

This is especially useful in scenarios where you need to validate that a config object satisfies a given interface. The config object may have more fields than the interface.

Now, if you remember explicit typing, you might be wondering what difference `satisfies` makes since we get the same behaviour explained in all the above scenarios when we explicitly type the variable (with the colon ":") like this:

```ts
const person: Person = {
  id: 1,
  name: "Debs",
};
```

The thing with `satisfies` is that it preserves as much as possible the narrowest type inferred from a value. Let's use an example to contrast explicit typing with `satisfies` and see how `satisfies`shines.

With explicit typing:

```ts
const greetings: Record<string, string> = {
  hello: "Hello",
  goodbye: "Goodbye",
};

console.log(greetings.halo);
// No Error, but undefined at runtime!
```

In the example, we used the [Record Utility Type](#record) to define an object type with string keys and string values. Because TypeScript expects that the keys are `string` (a wider type), we do not get autocomplete and it doesn't matter that we tried to access with a string property that does not exist in the object. Any string key is "valid". But `halo` does not exist on `greetings` and when we run the code, we get `undefined`. This is dangerous.

With `satisfies`:

```ts
const greetings = {
  hello: "Hello",
  goodbye: "Goodbye",
} satisfies Record<string, string>;

console.log(greetings.halo);
// Error: Property 'halo' does not exist on type '{ hello: string; goodbye: string; }'
```

We get safe autocomplete and no typos. This is because with `satisfies`, the compiler infers the literal type from the value. The object shape "{ hello: string; goodbye: string; }" is preserved, even though we're checking it against "Record<string, string>".

`satisfies` is considered a safer constraint to `as` type assertion, because it does not change the inferred type of the value and it does not silence the compiler (as we've seen in examples using `as`). Think of it as a type constraint without type casting.

<h3 id= 'double-assertions-with-as-unknown-as'>Double Assertions with `as unknown as`</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-assertions-and-constraints)

If `as` silences the compiler, `as unknown as` gaslights the compiler. It is a double assertion or double cast. It forcefully converts a value from one type to another via the intermediate `unknown` type. Sometimes, TypeScript does not allow a direct type assertion (using `as`), usually between two types that diverge too much or unrelated types that do not have a common structure.

```ts
const whoAmI = "I am a string" as number;
// Error: Conversion of type 'string' to type 'number' may be a mistake because neither type sufficiently overlaps with the other. If this was intentional, convert the expression to 'unknown' first.
```

This, of course, is for safety reasons. But when we use `as unknown as`, we bypass this restriction:

```ts
const whoAmI = "I am a string" as unknown as number;
// No Error. This is of course wrong.
```

We are telling TypeScript to treat `whoAmI` as type `unknown` first, then treat that `unknown` as `number`. That essentially is overriding the compiler's safety checks.

Use `as unknown as` with extreme caution, only when you know more than the compiler. Wrongful use may cause runtime errors. Below is an example of a legitimate use case:

<!-- todo -->

```ts

```
