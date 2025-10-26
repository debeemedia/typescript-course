<h2 id= 'utility-types'>UTILITY TYPES</h2>

[Back to Index](_sidebar.md)

Utility Types essentially are built-in generic types used to manipulate or create new types based on existing types.
Here are some commonly used utility types:

<h3 id= 'partial'>Partial</h3>

Partial Utility Type creates a type where all the properties of `Type` in `Partial<Type>` are optional:

```ts
interface Person {
  id: number;
  name: string;
  email: string;
}

function updatePerson(person: Partial<Person>) {
  person.email = "noreply@person.com";
}
```

In the example above, `Partial` takes in a type parameter `Person` as its generic parameter. It then returns a new type where all the properties of `Person` are optional.

If you hover over `person.email` in the function, you will see that TypeScript infers its type as `string | undefined`. All the properties are now optional.

<h3 id= 'required'>Required</h3>

[Back to Index](_sidebar.md) | [Back to Section](#utility-types)

Required Utility Type is sort of the opposite of Partial Utility Type. It makes all the properties of `Type` required:

```ts
type Person = {
  id: number;
  name?: string;
  email?: string;
};

// Error: Type '{ id: number; }' is missing the following properties from type 'Required<Person>': name, email
const person: Required<Person> = {
  id: 1,
};
```

It doesn't matter that `name` and `email` are optional on the existing type. We must provide them when using `Required`:

```ts
const person: Person = {
  id: 1,
  name: "Debs",
  email: "hello@nonexistent.com",
};
```

<h3 id= 'readonly'>Readonly</h3>

[Back to Index](_sidebar.md) | [Back to Section](#utility-types)

Readonly Utility Type creates a type where all the properties of `Type` are `readonly`, meaning they cannot be modified once assigned a value:

```ts
interface Person {
  id: number;
  name: string;
  email: string;
}

const person: Readonly<Person> = {
  id: 1,
  name: "Debs",
  email: "hello@nonexistent.com",
};

person.email = "noreply@person.com";
// Error: Cannot assign to 'email' because it is a read-only property.
```

Of course you can access the properties; you just cannot reassign them. But this only applies to the top-level properties. Unlike [`as const`](#literal-assertions-with-as-const) which can recursively make an object's properties readonly, `Readonly` utility type has a shallow effect:

```ts
interface Person {
  id: number;
  name: string;
  contact: {
    email: string;
    phone: string;
  };
}

const person: Readonly<Person> = {
  id: 1,
  name: "Debs",
  contact: {
    email: "hello@nonexistent.com",
    phone: "000-00-0000",
  },
};

person.contact.email = "bye@nonexistent.com";
// No error
```

We are still able to reassign another value to the deeply nested property `person.contact.email`.

And since we're on the subject of differences between `Readonly` and `as const`, if you hover over the `email` property in `person.contact`, you will notice that TypeScript still infers its type as `string`. With `as const`, it would be the literal type `hello@nonexistent.com`.

<h3 id= 'pick'>Pick</h3>

[Back to Index](_sidebar.md) | [Back to Section](#utility-types)

Pick Utility Type is used to create a new type by picking a set of properties from an existing type:

```ts
type Person = {
  id: number;
  name: string;
  email: string;
};

type Manager = Pick<Person, "id" | "email">;

const manager: Manager = {
  email: "manager@nonexistent.com",
  id: 1,
};
```

In the example above, the Pick Utility Type takes in two generic parameters: `Person` (the original type) and `"id" | "email"` (a union of keys from the `Person` type to be picked). It returns a new type containing only the specified keys from `Person`.

It is commonly used with the intersection (&) operator to combine types:

```ts
type Manager = Pick<Person, "id" | "email"> & {
  role: string;
  mgtLevel: number;
};

const manager: Manager = {
  email: "manager@nonexistent.com",
  id: 1,
  role: "Manager",
  mgtLevel: 14,
};
```

<h3 id= 'omit'>Omit</h3>

[Back to Index](_sidebar.md) | [Back to Section](#utility-types)

The Omit Utility Type is sort of the opposite of Pick Utility Type. It constructs a new type by picking all properties from an existing type but excluding a set of specified keys. It is useful when you want to include almost all of the properties on the existing type, so rather than listing all out, you just list the ones you don't want.

```ts
type Person = {
  id: number;
  name: string;
  email: string;
};

type PersonWithoutId = Omit<Person, "id">;
```

You can also combine with other properties:

```ts
type PersonWithoutId = Omit<Person, "id"> & { phone: string };

const personWithoutId: PersonWithoutId = {
  email: "hello@nonexistent.com",
  name: "Debs",
  phone: "000-00-0000",
};
```

<h3 id= 'extract'>Extract</h3>

[Back to Index](_sidebar.md) | [Back to Section](#utility-types)

You can think of the Extract Utility Type as somewhat similar to the Pick Utility Type, but in this case, we're picking members of a union type, not keys of an object type:

```ts
type StatusCodes = 200 | 201 | 401 | 404 | 410 | 500 | 502;

type SuccessCodes = Extract<StatusCodes, 200 | 201>; // Type is 200 | 201
```

You cannot use "Pick" for something like that:

```ts
type SuccessCodes = Pick<StatusCodes, 200 | 201>;
// Error: Type '200 | 201' does not satisfy the constraint '"toString" | "toFixed" | "toExponential" | "toPrecision" | "valueOf" | "toLocaleString"'.
```

<!-- todo: explain what the above error actually means -->

And you also cannot use "Extract" in place of "Pick".

<h3 id= 'exclude'>Exclude</h3>

[Back to Index](_sidebar.md) | [Back to Section](#utility-types)

You can think of the Exclude Utility Type as somewhat similar to Omit, and the direct opposite of Extract. It is used to select all the members of a union type, except the ones you specify:

```ts
type StatusCodes = 200 | 201 | 401 | 404 | 410 | 500 | 502;

type ErrorCodes = Exclude<StatusCodes, 200 | 201>; // Type is 401 | 404 | 410 | 500 | 502
```

And like Pick and Extract, you cannot mix up Omit and Exclude.

<h3 id= 'record'>Record</h3>

Record Utility Type (`Record<Key, Type>`) is used to define an object type with keys of type `Key` and values of type `Type`. For example:

```ts
const person: Record<string, string | number> = {
  id: 1,
  name: "Debs",
  email: "hello@nonexistent.com",
};
```

In the example above, person is a "Record", an object type with string keys and values that can be string or number. This also makes the `person` variable extensible. You can add properties with values that conform to `string | number`:

```ts
person.phone = "000-00-0000";
```

You can't do that if you just have an object literal without a specific type:

```ts
const person = {
  id: 1,
  name: "Debs",
  email: "hello@nonexistent.com",
};

person.phone = "000-00-0000";
// Error: Property 'phone' does not exist on type '{ id: number; name: string; email: string; }'.
```

With the Record utility type, you could tell TypeScript: "This object can have string keys, and values that can be string or number".

However, in the example above, no type was declared, so TypeScript inferred the most specific type from the object literal: an object with exactly three properties (id, name and email). It's not extensible because the inferred type is strict and literal, not open to additional keys.

Let's look at another example of the Record Utility Type with number keys:

```ts
type Person = {
  id: number;
  name: string;
  email: string;
};

type Employee = Person & { employeeId: number };

type StaffInfo = Record<number, Employee>;

const staffInfo: StaffInfo = {
  1: { id: 1, employeeId: 5, email: "hello@nonexistent.com", name: "Debs" },
  2: { id: 4, employeeId: 12, email: "bye@nonexistent.com", name: "Emmanuel" },
};
```

In the example above, `staffInfo` is a `Record<number, Employee>`, i.e an object where:

- each key is a `number` (e.g. 1, 2)
- each value is an `Employee` (a combination of `Person` and `employeeId`)

<!-- todo Lowercase utility type... and others-->

<!-- todo: after utility types, Modules (Import/Export) -->

<!-- typescript comments -->
