<h2 id= 'type-system-essentials'>TYPE-SYSTEM ESSENTIALS</h2>

[Back to Index](_sidebar.md)

<h3 id= 'primitive-types'>Primitive Types</h3>

These are the simplest, most basic types. In JavaScript, as well as TypeScript, they include `number`, `string`, `boolean`, `null`, and `undefined`.

```ts
let x: number = 2;
let y: string = "hello"; // TypeScript, like JavaScript, allows single and double quotes for strings
let z: boolean = true;
```

The examples above are "explicit typing" because you tell TypeScript exactly what type the variable should be.

But you could also write:

```ts
let x = 2;
```

TypeScript will infer from the assigned value, 2, that it is a number. Hover over the variable x and confirm. This is "implicit typing".

You can use an explicit type when you're not assigning a value to the variable initially e.g.

```ts
let x: number;
x = 2;
```

So because of TypeScript's static typing, you cannot do this:

```ts
let string: string;
string = 2; // Error: Type 'number' is not assignable to type 'string'.
```

- SideNote: Avoid using the following:

```ts
let x: Number;
let y: String;
let z: Boolean;
```

These are not TypeScript primitive types but are JavaScript object wrappers around the corresponding primitive types and are actually object types. Observe this:

```ts
let x: number = 4;
let y: Number = new Number(4);
console.log(typeof x === typeof y);
// Output: false
```

Using the object wrappers is not recommended as it can lead to unexpected behaviour.

<b> Difference between `null` and `undefined`</b>

Think of `null` as an object placeholder. Historically, JavaScript wanted `null` to represent an "empty object reference". It signals the intentional absence of any value or object.
`undefined` means a variable has been declared but not assigned any value yet. It represents an uninitialized or missing state.

- Use `null` when you want to explicitly indicate that something is empty.
- Use `undefined` when the absence of a value happens naturally e.g. missing object properties, or, as stated, default for unassigned variables.

```ts
let result = null;
```

If you hover over `result`, you will notice that it is implicitly assigned the type "any" (discussed later), which means you can later do this:

```ts
result = 2;
result = "hello";
```

The same applies to `undefined`:

```ts
let result = undefined;
```

However, this is not the case with `const` declarations:

```ts
const result = null;
result = 3; // Error: Cannot assign to 'result' because it is a constant.
```

You cannot reassign a value to a constant variable and TypeScript enforces this.

<h3 id= 'arrays-tuples'>Arrays and Tuples</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

An array is a list or collection of items.
In TypeScript, you can specify the type of elements in an array e.g an array of strings, numbers, objects, or even arrays. You can also define arrays that allow multiple types, though it's recommended to maintain one type to simplify type narrowing later on.

```ts
const greetings: string[] = ["hello", "hi"];
```

Here, `greetings` is typed as an array of strings. If you try to push a value of the wrong type, say, number, you will get an error:

```ts
greetings.push(9); // Error: Argument of type 'number' is not assignable to parameter of type 'string'
```

You can define an array of arrays like this:

```ts
const multi: string[][] = [
  ["hello", "hi"],
  ["see you", "bye"],
];
```

When you define an empty array, it is best to provide an explicit type, otherwise, TypeScript will infer its type as `any[]`, and you should be clocking by now that we always try to avoid the `any` type.

```ts
const ids: number[] = []; // Recommended
const ids = []; // Implicitly any[]
```

A tuple is like a stricter array. It has a fixed length and you define the type for each position in the array.

```ts
const fruits: [string, string, string] = ["apple", "banana", "orange"];
```

You specified that there will be three elements in the `fruits` array, and all of them will be of "string" type.

You can also mix types in tuples:

```ts
const fruitsAndPrice: [string, number, string, number] = [
  "apple",
  20,
  "banana",
  10,
];
```

Let's say you assign an item in the tuple to a variable:

```ts
const fruit = fruitsAndPrice[0]; // Inferred as string
```

When you hover over `fruit`, you will notice that TypeScript correctly infers its type as string.
You can't have this with a regular array:

```ts
const fruitsAndPrice = ["apple", 20, "banana", 10];
const fruit = fruitsAndPrice[0]; // Inferred as string | number
```

Since `fruitsAndPrice` array can hold both strings and numbers, TypeScript infers its type as `(string | number)[]`, and any item you access will be of type `string | number`, which is less precise.

So when should you use a tuple? Most likely when the element types and order matter, and the structure is fixed. If the elements are of the same type and the length can vary, you can use an array.

<h3 id= 'literal-types'>Literal Types</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

A literal is a precise representation of a primitive type. We can have a number literal, boolean literal or string literal.
String literals allow you to assign a value only from a specific set of string values to a variable:

```ts
let size: "small" | "medium" | "large";
size = "extra-large"; // Error: Type '"extra-large"' is not assignable to type '"small" | "medium" | "large"'.
```

TypeScript will not infer `size` as a string type, but as a stricter `"small" | "medium" | "large"` union type and therefore will not allow you to assign a value outside of these four values to the `size` variable.

Another interesting thing to note is that when you initialize a variable, as below:

```ts
let hiWithLet = "hi";
const hiWithConst = "hi";
```

TypeScript infers `hiWithConst` as the stricter literal "hi" and infers `hiWithLet` as a broader "string" because the value of `hiWithLet` can change but the value of `hiWithConst` cannot change because it is a constant.

Number literals allow you to assign a value only from a specific set of numeric values to a variable:

```ts
let responseSuccessCodes: 200 | 201 = 200;
responseSuccessCodes = 400; // Error: Type '400' is not assignable to type '200 | 201'.
```

From the examples so far, you can see that literal types are typically combined with union types (discussed later).

Boolean literals allow you to assign a value only from the boolean values true or false to a variable. `true` and `false` are the only boolean literals in TypeScript.

```ts
let isActive: true;
isActive = false; // Error: Type 'false' is not assignable to type 'true'.
```

Let's spice up the examples a little bit:

```ts
function alwaysTrue(): true {
  return true;
  // return false; // Error: Type 'false' is not assignable to type 'true'.
}
```

The function return type (discussed later) is literally `true`, not just `boolean`; so returning anything else will cause a type error.

<h3 id= 'type-aliases'>Type Aliases</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

Before we discuss union types, let's quickly go over type aliases. Type aliases provide a way for you to create custom names for complex types, improving code readability and reusability.

You define a type alias with the keyword `type`, followed by the alias and then setting it equal to the proposed type or union of types. Type aliases conventionally use the PascalCase. This helps avoid confusion between types and variables.

```ts
type Name = string;
const userName: Name = "deBee";
```

`Name` is now an alias for the string type. So if you do this, you will get an error:

```ts
const userName: Name = true; // Error: Type 'boolean' is not assignable to type 'string'.
```

Type aliases reduce repetition in your code. Suppose you have this:

Type aliases reduce repetition in your code. Suppose you have this:

```ts
function returnSomething(
  param1: string | number,
  param2: string | number
): string | number {
  if (typeof param1 === "number" && typeof param2 === "number") {
    return param1 + param2;
  } else {
    return `${param1}, ${param2}`;
  }
}
```

We haven't discussed function types yet, but what we're basically doing above is defining a function that takes in two parameters of type string or number and, with some type narrowing (this will be discussed later), returns a value of type string or number.

Now, you can see that there is so much repetition going on there. So we can simply define a type alias and reuse it in the function:

```ts
type StringOrNumber = string | number;

function returnSomething(
  param1: StringOrNumber,
  param2: StringOrNumber
): StringOrNumber {
  if (typeof param1 === "number" && typeof param2 === "number") {
    return param1 + param2;
  } else {
    return `${param1}, ${param2}`;
  }
}
```

Type aliases can also be defined for object types, array types and basically any type you want. More type aliases will show up in code samples in subsequent sections.

Suppose we have a type `Person`:

```ts
type Person = {
  id: number;
  name: string;
  gender: "Male" | "Female";
};
```

And we define an object of that type:

```ts
const person = {
  id: 1,
  name: "deBee",
};
```

Let's say we stop there. When you hover over "person", you will hear TypeScript crying out: Property 'gender' is missing in type '{ id: number; name: string; }' but required in type 'Person'. So let's make TypeScript happy:

```ts
const person = {
  id: 1,
  name: "deBee",
  gender: "Female",
};
```

Notice how we used a literal type to define the `gender` property. As soon as you type "gender" while creating the "person" object, TypeScript suggests "Female" or "Male" to choose from. Auto-suggestion/completion is another understated benefit of using TypeScript.

<!-- todo: clarify this. is it actually typescript that suggests or vscode -->

Note that while it is completely valid to use a type for an object, interfaces are typically used with objects and classes. This will be discussed later.

<h3 id= 'union-intersection'>Union and Intersection Types</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

Union and Intersection types allow us to combine types together. Let's revisit one of our examples under Type Aliases:

```ts
type StringOrNumber = string | number;
```

The above example defines an alias for a union type. A union type is denoted by the pipe symbol "|". Think of it as meaning "or". So type `StringOrNumber` defines a type of string or number. This means that any variable initialised with that type must be assigned a value that is either a string or a number, otherwise, you will get an error.

We also have the intersection type as denoted by the ampersand symbol "&". Think of it as meaning "and". This defines a combination of multiple types. For example:

```ts
type Person = {
  id: number;
  name: string;
  gender: "Male" | "Female";
};

type Contact = {
  email: string;
  phone_number: string;
};

type User = Person & Contact;

const user: User = {
  id: 1,
  name: "deBee",
  gender: "Female",
  email: "okekedeborah@gmail.com",
  phone_number: "+X-obviously-invalid-number",
};
```

So what we have just done is use type aliases to define a `Person` type, a `Contact` type and a `User` type which is an intersection of the `Person` and `Contact` types. We then initialize a `user` object which is of type `User`. This means that the "user" object must contain ALL the properties in both the `Person` and `Contact` types.

<h3 id= 'enums'>Enums</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

Enum stands for enumeration. It is a set of named constants:

```ts
enum ArticleStatus {
  Draft = "Draft",
  Completed = "Completed",
  Published = "Published",
  Archived = "Archived",
}
```

Enums enhance code readability and maintainability. If you change the value of `ArticleStatus.Completed` which is "Completed" to "Finished", it won't mess up your code because you renamed a variable.

The example above is a string enum. There are also numeric enums and their values are mapped to auto-incrementing integers

```ts
enum ArticleStatus {
  Draft,
  Completed,
  Published,
  Archived,
}
console.log(ArticleStatus.Draft); // Output: 0
console.log(ArticleStatus.Completed); // Output: 1
console.log(ArticleStatus.Published); // Output: 2
console.log(ArticleStatus.Archived); // Output: 3
```

By default, the first value is 0 and subsequent values increment by 1, however, you can start with a different value and TypeScript takes it from there:

```ts
enum ArticleStatus {
  Draft = 2,
  Completed,
  Published,
  Archived,
}
console.log(ArticleStatus.Published); // Output: 4
```

Reverse mapping is also possible where you use the value to get the name:

```ts
enum ArticleStatus {
  Draft,
  Completed,
  Published,
  Archived,
}
console.log(ArticleStatus[2]); // Output: Published
```

> SideNote:
> Numeric enums are rare in modern TypeScript codebases. String enums and union types are much cleaner and more practical. You'll probably never need numeric enums unless you're doing very specific work, so don't dwell too much on it.

String enums, on the other hand, do not auto-increment and have no reverse mapping. Once you begin to define a string enum, you must assign a value to each member:

```ts
enum ArticleStatus {
  Draft = "Draft",
  Completed = "Completed",
  Published = "Published",
  Archived, // Error: Enum member must have initializer.
}
```

One thing to note about enums is that they are real objects at runtime. Unlike union types, for instance, that are stripped off after compilation, enums are compiled down to real JavaScript code, except you prefix the enum with `const` as below:

```ts
const enum ArticleStatus {
  Draft,
  Completed,
  Published,
  Archived,
}

const status = ArticleStatus.Published;
```

Run `tsc` and check the compiled `practice.js` file. What you will see is

```js
const status = 2;
```

So if you're concerned about emitting extra JavaScript, perhaps in a frontend project where bundle size matters, you can use `const enum`, union types or `as const` objects. However, with `const enum`, reverse mapping is not possible:

```ts
const enum ArticleStatus {
  Draft,
  Completed,
  Published,
  Archived,
}
console.log(ArticleStatus[2]); // Error: A const enum member can only be accessed using a string literal.
```

<!-- todo: touch up String Literal Unions from Objects and String Literal Unions from Arrays. The sections seem a bit disjointed -->

<h3 id= 'string-literal-unions-objects'>String Literal Unions from Objects</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

I want to demonstrate a lighter (lighter, in terms of not generating JavaScript code), and still type-sound, alternative to enums, but first, we will need to discuss `as const`, `typeof` and `keyof`, then see how we can combine all three.

<h4 id = 'as-const'>"as const"</h4>

Suppose we have an object:

```ts
const ArticleStatus = {
  D: "Draft",
  C: "Completed",
  P: "Published",
  A: "Archived",
};
```

Right now, TypeScript infers the type of the object `ArticleStatus` to be:

```ts
{
  D: string;
  C: string;
  P: string;
  A: string;
}
```

When you add `as const` to the object:

```ts
const ArticleStatus = {
  D: "Draft",
  C: "Completed",
  P: "Published",
  A: "Archived",
} as const;
```

TypeScript will now infer the type as:

```ts
{
    readonly D: "Draft";
    readonly C: "Completed";
    readonly P: "Published";
    readonly A: "Archived";
}
```

So instead of inferring the values as just strings, TypeScript infers them as [literal types](#literal-types) i.e string literals. (`as const` is explained [here](#literal-assertions-with-as-const))

`readonly` means that the properties are read-only, so you cannot accidentally change them:

```ts
const ArticleStatus = {
  D: "Draft",
  C: "Completed",
  P: "Published",
  A: "Archived",
} as const;

ArticleStatus.C = "Finished"; // Error: Cannot assign to 'C' because it is a read-only property.
```

<h4 id = 'typeof'>"typeof"</h4>

`typeof` (discussed in more detail [here](#typeof-type-guard)) is used to get the type of a value. It's as simple as this:

```ts
const ArticleStatus = {
  D: "Draft",
  C: "Completed",
  P: "Published",
  A: "Archived",
} as const;

type ArticleStatusType = typeof ArticleStatus;
```

TypeScript infers the type of `ArticleStatusType` as:

```ts
type ArticleStatusType = {
  readonly D: "Draft";
  readonly C: "Completed";
  readonly P: "Published";
  readonly A: "Archived";
};
```

<h4 id = 'keyof'>"keyof"</h4>

`keyof` is used to extract the keys of an object as a union:

```ts
const ArticleStatus = {
  D: "Draft",
  C: "Completed",
  P: "Published",
  A: "Archived",
} as const;

type ArticleStatusKeys = keyof typeof ArticleStatus;
```

TypeScript infers the type of `ArticleStatusKeys` as:

```ts
type ArticleStatusKeys = "D" | "C" | "P" | "A";
```

So `keyof` turns the keys of the object to a union of string literals. It can be useful when you want to constrain a variable to use only the keys of an object.

Final Step:
To get the union of the values, we do:

```ts
const ArticleStatus = {
  D: "Draft",
  C: "Completed",
  P: "Published",
  A: "Archived",
} as const;

type ArticleStatusValues = (typeof ArticleStatus)[keyof typeof ArticleStatus];
```

TypeScript infers `ArticleStatusValues` as:

```ts
type ArticleStatusValues = "Draft" | "Completed" | "Published" | "Archived";
```

We indexed into the object's type with the keys to get the union of the values. So we have successfully created a "string literal union type", similar to what string enums give us, but with no JavaScript output.

<!-- todo: emphasize this with example: -->

This of course does not invalidate the use of enums. Enums are still very useful because the values are actual runtime variables...

<h3 id= 'string-enums-vs-as-const-keyof-typeof'>String Enums Versus `as const` + `keyof typeof`</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

Let's compare two examples, one with string enum and the other with `as const` + `keyof typeof`

`as const` + `keyof typeof`:

```ts
const ArticleStatus = {
  Draft: "Draft",
  Completed: "Completed",
  Published: "Published",
  Archived: "Archived",
} as const;

type ArticleStatus = (typeof ArticleStatus)[keyof typeof ArticleStatus];

function printStatus(status: ArticleStatus) {
  switch (status) {
    case "Draft":
      console.log("This article is in progress");
      break;

    case "Completed":
      console.log("This article is finished");
      break;

    case "Published":
      console.log("This article has been published");
      break;

    case "Archived":
      console.log("This article is in the archives");
      break;

    default:
      throw new Error("Invalid status");
  }
}

printStatus("Draft"); // Output: This article is in progress
```

You enjoy auto-completion when typing out the case statements in the function. And if we pass in a wrong argument into the function, we get an immediate compile-time error:

```ts
printStatus("Drafts"); // Argument of type '"Drafts"' is not assignable to parameter of type 'ArticleStatus'.
```

Enums:

```ts
enum ArticleStatus_ {
  Draft = "Draft",
  Completed = "Completed",
  Published = "Published",
  Archived = "Archived",
}

function printStatus_(status: string) {
  switch (status) {
    // No type safety in the case. NO auto-completion
    case ArticleStatus_.Draft:
      console.log("This article is in progress");
      break;

    case ArticleStatus_.Completed:
      console.log("This article is finished");
      break;

    case ArticleStatus_.Published:
      console.log("This article has been published");
      break;

    case ArticleStatus_.Archived:
      console.log("This article is in the archives");
      break;

    default:
      throw new Error("Invalid status");
  }
}

printStatus_("Draft"); // Output: This article is in progress
```

No auto-completion because our function parameter accepts a string. The error from our default case statement is only thrown at runtime if we pass in a wrong argument:

```ts
printStatus_("Drafts"); // No error until runtime
```

Runtime error:

```bash
Error: Invalid status
```

But here's a little trick when working with string enums:

```ts
type ArticleStatusLiteral_ = `${ArticleStatus_}`;

function printStatus_(status: ArticleStatusLiteral_) {
  // Now there's type safety
}

printStatus_("Drafts"); // Argument of type '"Drafts"' is not assignable to parameter of type '"Draft" | "Completed" | "Published" | "Archived"'
```

It's a TypeScript trick using template literal types to extract the enum's string values into a string literal union. And just like that, we have auto-completion and compile-time warning.

<!-- todo: a fitting conclusion?? -->

<h3 id= 'string-literal-unions-arrays'>String Literal Unions from Arrays</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

String literal union types can also be gotten from arrays. Let's look at two examples:

Example 1: Simple string array

```ts
const validStatuses = ["Draft", "Completed", "Published", "Archived"];
```

Right now the type of `validStatuses` is:

```ts
string[]
```

Let's add `as const`:

```ts
const validStatuses = ["Draft", "Completed", "Published", "Archived"] as const;
```

`as const` freezes the array such that its type is no longer an array of strings; its type now becomes:

```ts
readonly[("Draft", "Completed", "Published", "Archived")];
```

Final step:

```ts
const validStatuses = ["Draft", "Completed", "Published", "Archived"] as const;

type ValidStatus = (typeof validStatuses)[number];
```

`(typeof validStatuses)[number]` indexes into that array's type at any index (`number`) so it extracts the union and the type of `ValidStatus` becomes:

```ts
type ValidStatus = "Completed" | "Published" | "Archived" | "Draft";
```

We have successfully extracted a string union from an array.

> SideNote: For arrays and tuples, "number" is the index type. You can also index specific positions e.g:

```ts
const validStatuses = ["Draft", "Completed", "Published", "Archived"] as const;

type ValidStatus = (typeof validStatuses)[0];
// type ValidStatus = "Draft"
```

Example 2: Array of objects

```ts
const articleStatusesData = [
  {
    name: "TypeScript",
    status: "Published",
  },
  {
    name: "Push Notifications",
    status: "Completed",
  },
  {
    name: "Post Feed System",
    status: "Archived",
  },
  {
    name: "Search System",
    status: "Draft",
  },
] as const;

type ArticleStatus = (typeof articleStatusesData)[number]["status"];
// "Completed" | "Published" | "Archived" | "Draft"

type ArticleName = (typeof articleStatusesData)[number]["name"];
// "TypeScript" | "Push Notifications" | "Post Feed System" | "Search System"
```

The extra step here is an additional index access for the field of interest i.e `name` or `status`.

<h3 id= 'optional-chaining-non-null'>Optional Chaining and Non-null Assertion</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

Optional chaining is denoted by the question mark symbol (?.), while Non-null assertion is denoted by the exclamation mark (!). These operators allow us to deal with null or undefined values in TypeScript. Optional chaining is actually available in both JavaScript and TypeScript but non-null assertion is available only in TypeScript.

Optional chaining allows you to access properties on an object that may be null or undefined without throwing a runtime error.

```ts
type Person = {
  id: number;
  name: string;
  contact?: {
    email_address: string;
    phone_number: string;
  };
};

const person: Person = {
  id: 1,
  name: "deBee",
};

const phoneNumber = person.contact.phone_number; // Error: 'person.contact' is possibly 'undefined'.
console.log(phoneNumber);
```

In the above example, the `contact` property of the `Person` type is optional, and so when we try to access `person.contact.phone_number` when initializing the variable `phoneNumber`, TypeScript immediately warns us that the `contact` property may not be available. If you go ahead and run the code:

```bash
tsc practice.ts
node practice.js
```

You will get a runtime error:

```bash
C:\Users\me\Desktop\typescript-tutorial\practice.js:245
const phoneNumber = person.contact.phone_number;
                          ^

TypeError: Cannot read properties of undefined (reading 'phone_number')
```

This error is thrown by Node.js at runtime because it tried to access `phone_number` on `person.contact` which is undefined. Even though TypeScript warns us here, it doesn't prevent runtime errors:

```ts
// 'person.contact' is possibly 'undefined'. ts(18048)
```

So how can we safely access potentially undefined properties without crashing our code? This is where optional chaining comes in:

```ts
const phoneNumber = person.contact?.phone_number;
console.log(phoneNumber);
```

- SideNote: If you were typing out the entire code in VSCode editor, its intellisense would automatically insert the optional chaining operator right before the object access dot notation.

With optional chaining, what we're basically telling the TypeScript compiler is this: "First, check if `contact` is undefined or null; if it is, stop and return `undefined` instead of accessing the `phone_number` property; if it's not, continue and return the value of `contact.phone_number`.

When you run the code now, instead of the error that crashes your program, you will get a graceful "undefined".

TypeScript will also now infer the type of the variable `phoneNumber` as `string | undefined`.

- SideNote: A more verbose approach would be:

```ts
if (person.contact) {
  const phoneNumber = person.contact.phone_number;
}
```

Non-null assertion, on the other hand, tells the compiler to ignore the possibility of the property being undefined or null.

You basically use it to bypass TypeScript's null-check for that expression. It removes the warning at compile-time but does not affect runtime behaviour. If the value is actually null or undefined at runtime, your code will still crash. We already discussed the difference between runtime and compile-time [here](#introduction).

```ts
const phoneNumber = person.contact!.phone_number;
console.log(phoneNumber);
```

Use "!" with caution and only when you are completely certain, based on your logic or some guarantee, that a value will neither be null nor undefined.

<h3 id= 'function-types'>Function Types</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

<h4 id= 'basic-function-types'>Basic Function Types</h4>

Function Types in TypeScript involve specifying the types of a function's parameters, as well as the type of the return value (even if it returns "nothing", which is represented by `void`). You must explicitly type function parameters (even as "any" (still not recommended!) or "unknown"), otherwise you'll get an error if `noImplicitAny` is enabled in your tsconfig file (it is enabled by default in `strict` mode):

```ts
function greet(name) {
  console.log(`Hello. My name is ${name}.`);
  // Error: Parameter 'name' implicitly has an 'any' type
}
```

The return type, however, can be inferred by TypeScript, but it is good practice to explicitly type it, for clarity and future maintenance.

Below, we specify the type of the function's parameters, as well as the return type:

```ts
function greet(name: string): void {
  console.log(`Hello. My name is ${name}.`);
  // Error: Parameter 'name' implicitly has an 'any' type
}
```

The function above accepts a string and returns nothing.

- `name: string` enforces that the argument must be a string
- `: void` indicates that the function returns nothing.

You can see that even though the function returns nothing, we provide a "void" type as the return type. If you omit the return type, TypeScript will also infer it as "void" in this case.
The function can be called like this:

```ts
greet("deBee"); // Output: 'Hello, My name is deBee.
```

Passing a value of the wrong type when calling the function will result in an error:

```ts
greet(14); // Error: Argument of type 'number' is not assignable to parameter of type 'string'.
```

You can make a parameter optional using the question mark (?) symbol:

```ts
function greetings(name: string, position?: string) {
  console.log(
    `Hello. My name is ${name}. ${position ? `I am a ${position}.` : ""}`
  );
}

greetings("deBee"); // Output: Hello. My name is deBee.
greetings("deBee", "backend developer"); // Output: Hello. My name is deBee. I am a backend developer.
```

In the above code sample, we checked if `position`, which is an optional parameter, is available, before consuming it. TypeScript infers the type of the optional `position` parameter as `string | undefined`.

You can also provide default values for parameters. Any parameter with a default is automatically optional:

```ts
function greetings(name: string, position: string = "developer") {
  console.log(`Hello. My name is ${name}. I am a ${position}.`);
}

greetings("deBee"); // Output: Hello. My name is deBee. I am a developer.
greetings("deBee", "backend developer"); // Output: Hello. My name is deBee. I am a backend developer.
```

As a rule of thumb, place the optional or default parameters at the end of the parameter list, after all the required parameters. This eliminates any ambiguity and TypeScript can correctly match arguments to parameters when the function is called.

```ts
function greetings(position?: string, name: string) {} // Error: A required parameter cannot follow an optional parameter.
```

In the code sample above, we placed the optional parameter before the required parameter and TypeScript immediately threw a compile-time error. This is because, once a parameter is marked optional, all parameters that come after it must be optional too.

```ts
function greetings(position: string = "developer", name: string) {}

greetings("deBee");
// Error: Expected 2 arguments, but got 1.
// Error> An argument for 'name' was not provided.
```

In the code sample above, we placed a default parameter (which is optional in practice) before the required parameter and TypeScript erroneously assumed our "deBee" argument is intended for "position" and that we didn't pass anything for "name". This caused a compile-time error.

<b>Typing Destructured Function Parameters</b>

You can provide types when using object destructuring in function parameters e.g

```ts
function greetings({
  name,
  position = "developer",
}: {
  name: string;
  position?: string;
}) {
  console.log(`Hello. My name is ${name}. I am a ${position}.`);
}

greetings({ name: "deBee" }); // Output: Hello. My name is deBee. I am a developer.
```

The function parameters are destructured, and their types are defined inline. Although "position" has a default value, you still need to mark it as optional using "?". This is because, when using inline object type annotations, TypeScript does not automatically infer that a default value means the property is optional. It treats type declarations and default values as separate concerns.

Here's a more verbose, but clearer, syntax:

```ts
function greetings(options: { name: string; position?: string }) {
  const { name, position = "developer" } = options;

  console.log(`Hello. My name is ${name}. I am a ${position}.`);
}

greetings({ name: "deBee" }); // Output: Hello. My name is deBee. I am a developer.
```

Instead of destructuring directly in the parameter list, you accept the object as a whole and destructure it inside the function body. The position property is still marked optional in the type and you provide a default during destructuring.
Using "options" as the parameter name is a common convention when a function takes a single object containing multiple named arguments. You can call it anything.

The `greetings` function in our example so far returns "void" which is a special type (discussed later) in TypeScript that is used to indicate that nothing is returned from a function. You can similarly provide a return type for a function that returns something concrete:

```ts
function sum(param1: number, param2: number): number {
  return param1 + param2;
}
```

The `sum` function above takes two arguments of the number type and returns a number value.

<h4 id= 'asynchronous-functions'>Asynchronous Functions</h4>

An asynchronous function is a function that returns a promise. In TypeScript, you specify this using the Promise<Type> syntax. "Type" represents the type of the value that the promise will eventually resolve to.

To declare an asynchronous function, use the async keyword, then specify the return type as Promise<Type> e.g

```ts
async function fetchSomething(): Promise<string> {}
```

TypeScript can often automatically infer the return type without explicitly typing it, however, as mentioned before, it is recommended practice to add the return type.

<h4 id= 'rest-parameters'>Rest Parameters</h4>

Rest parameters allow a function to accept an indefinite number of arguments as an array. This is useful when you don't know in advance how many arguments will be passed. Rest parameters use the spread syntax (...) in the parameter list.

```ts
function sumNumbers(...numbers: number[]) {
  return numbers;
}
```

The `...numbers` syntax gathers all the arguments into an array.

The type of the rest parameters must be an array, otherwise, you'll get an error:

```ts
function sumNumbers(...numbers: number) {
  return numbers;
}
// Error: A rest parameter must be of an array type.
```

The function can also take other arguments:

```ts
function sumNumbers(str: string, ...numbers: number[]) {
  return numbers;
}
```

However, the rest parameters must come last:

```ts
function sumNumbers(...numbers: number[], str: string) {
  return numbers;
}
// Error: A rest parameter must be last in a parameter list.
```

Also, no trailing comma after the rest parameters, which isn't really an issue in regular parameter lists:

```ts
function sumNumbers(str: string, ...numbers: number[]) {
  return numbers;
}
// Error: A rest parameter or binding pattern may not have a trailing comma.
```

Now let's improve the function and see how it is called:

```ts
function sumNumbers(...numbers: number[]) {
  let sum = 0;
  numbers.forEach((num) => (sum += num));
  return sum;
}

const result = sumNumbers(1, 2, 3);
console.log(result); // Output: 6
```

Rest Parameters are a JavaScript feature (ES6), not exclusive to TypeScript. TypeScript just adds type annotations to rest parameters.

<!-- todo: rest parameters; overloading -->

<h3 id= 'special-types'>Special Types</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

TypeScript special types include `any`, `unknown`, `void` and `never` types.

<h4 id= 'any'>`any`</h4>

The `any` type is a special type that allows you to be flexible in TypeScript, but it's as good as disabling TypeScript's static typing. As the name implies, it allows any type for a variable and basically disables type checking since you will not get any error if a variable's type changes; so basically, you're back to where you were with vanilla JavaScript.

```ts
let str: any = "hi";
str = 3; // No error
```

Use the "any' type sparingly: only when you are in a situation where you are not able to predict what the type of a variable is going to be, or when you could possibly receive many types. However, alternatives such as [union types](#union-intersection) or the "unknown" type are preferable.

<h4 id= 'unknown'>`unknown`</h4>

The `unknown` type is a special type that is similar to the "any" type in that it can hold any value, but is more type-safe than the "any" type. This is because, rather than ignoring type checking, it forces you to perform what is known as type narrowing which involves checking the type before performing an operation. You could do this type narrowing with `typeof` or `instanceof` [type guards](#type-narrowing-and-type-guards), for example:

```ts
let myVar: unknown = 1;

if (typeof myVar === "number") {
  const result = myVar + 1;
} else if (typeof myVar === "string") {
  const result = myVar.length;
}
```

You will notice, for instance, that in the conditional block checking if `myVar` is a string, properties available to string types like "length" become available. This is not available in the conditional block checking for number type. So TypeScript is still at work here. Also, if you try to do something like this without type narrowing, you will get an error:

```ts
const result = myVar + 1; // Error: 'myVar' is of type 'unknown'
```

You will not get that error if you use "any" type, and this is why the "unknown" type is a safer choice.

<h4 id= 'void'>`void`</h4>

The `void` and `never` special types are primarily used when typing functions to indicate that nothing is returned from a function.

The `void` type is specifically used to indicate that a function completes execution but nothing concrete is returned from it e.g a function that performs some side effect operation like logging to the console or updating a database.

```ts
function greet(name: string): void {
  console.log(`Hello. My name is ${name}.`);
}
```

This is in contrast to a function that returns something concrete:

```ts
function returnGreeting(name: string): string {
  return `Hello. My name is ${name}.`;
}
```

A "void" function can return `undefined`. Let's modify our `greet` function a little:

```ts
function greet(name: string): void {
  console.log(`Hello. My name is ${name}.`);
  return undefined;
}
```

You cannot do this with the `returnGreeting` function:

```ts
function returnGreeting(name: string): string {
  console.log(`Hello. My name is ${name}.`);
  return undefined; // Error: 'Type "undefined" is not assignable to type "string"'
}
```

Note that, you cannot return `null` from a "void" function. Let's modify the `greet` function again:

```ts
function greet(name: string): void {
  console.log(`Hello. My name is ${name}.`);
  return null; // Error: 'Type "null" is not assignable to type "void"'.
}
```

<h4 id= 'never'>`never`</h4>

The `never` type is specifically used to indicate that a function will never return e.g when the function throws an error, or when it enters an infinite loop.
Unlike a "void" function, a "never" function cannot return `undefined` or any other value because the function execution is never completed.

```ts
function throwError(message: string): never {
  throw new Error(message);
}
```

Note that TypeScript may be conservative when inferring "never" functions and may infer them as "void", therefore it is recommended to explicitly type a function as "never" if that is your intention, just as we did with the `throwError` function.

Let's look at a practical application of the "never" type.

Suppose we have a function that takes a primary colour and returns a concise description of the colour:

```ts
type PrimaryColour = "Red" | "Yellow" | "Blue";

function handleColour(colour: PrimaryColour) {
  switch (colour) {
    case "Red":
      return `Red is a primary colour. A tomato is red.`;
    case "Yellow":
      return `Yellow is a primary colour. A butterfly is yellow.`;
    case "Blue":
      return `Blue is a primary colour. The sky is blue.`;
    default:
      return assertNever(colour);
  }
}

function assertNever(param: never): never {
  throw new Error(`Unexpected colour: ${JSON.stringify(param)}`);
}
```

For now, we do not want to accept any colour that is not a primary colour in our `handleColour` function, hence the `assertNever`.

If someone later adds colour "Green" to the `PrimaryColour` type and the case of "Green" is not handled in the switch statement, TypeScript will throw an error at `assertNever(colour)`:

```ts
type PrimaryColour = "Red" | "Yellow" | "Blue" | "Green";

function handleColour(colour: PrimaryColour) {
  switch (colour) {
    case "Red":
      return `Red is a primary colour. A tomato is red.`;
    case "Yellow":
      return `Yellow is a primary colour. A butterfly is yellow.`;
    case "Blue":
      return `Blue is a primary colour. The sky is blue.`;
    default:
      return assertNever(colour); // Error: Argument of type '"Green"' is not assignable to parameter of type 'never'.
  }
}
```

So, the default case is now a compile-time safeguard. If we had just written:

```ts
// ...
default:
  return `${colour} is a primary colour.`
```

We would get no warning, and a silent bug would be introduced.

<h3 id= 'interfaces'>Interfaces</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-system-essentials)

Interfaces allow you to enforce certain properties on objects or classes. They define the "shape" of an object and determine what properties or methods the object should have.
To create an interface, use the keyword `interface`, followed by the name of the interface, then the block defining the shape the object should take:

```ts
interface Person {
  id: number;
  name: string;
  contact?: {
    email_address: string;
    phone_number: string;
  };
  hello(): string;
}
```

You declare an object with an interface type like so:

```ts
const person: Person = {
  id: 1,
  name: "Debs",
  contact: {
    email_address: "",
    phone_number: "",
  },
  hello() {
    return this.name + " says hello";
  },
};
```

Just as with [type aliases](#type-aliases), the object must implement all non-optional properties and methods on the interface, otherwise, TypeScript will throw an error:

```ts
const person: Person = {
  id: 1,
  // Error: Property 'name' is missing in type '{ id: number; }' but required in type 'Person'.
};
```

An interface can "extend" another interface and the child interface inherits the properties and methods of the parent interface:

```ts
interface Employee extends Person {
  employeeId: number;
}

const employee: Employee = {
  id: 1,
  name: "Debs",
  contact: {
    email_address: "",
    phone_number: "",
  },
  hello() {
    return this.name + " says hello";
  },
  employeeId: 5,
};
```

An interface can extend multiple interfaces:

```ts
interface Manager extends Person, Employee {
  mgtLevel: number;
}

const manager: Manager = {
  id: 1,
  name: "Debs",
  contact: {
    email_address: "",
    phone_number: "",
  },
  hello() {
    return this.name + " says hello";
  },
  employeeId: 5,
  mgtLevel: 14,
};
```

You can use interfaces in functions:

```ts
function getManagerDetails(manager: Manager): string {
  return `${manager.name} is on the management team at level ${manager.mgtLevel} with id of ${manager.employeeId}`;
}
```

Classes also implement interfaces. No surprise there, since classes are objects. This will be discussed in more detail under OOP. <!-- todo: link here -->

What is the difference between `type alias` and `interface`?
Both type aliases and interfaces can be used to define the structure of an object. Type aliases also define the structure of primitive types, which interfaces cannot do. ... <!-- todo: write more -->...
