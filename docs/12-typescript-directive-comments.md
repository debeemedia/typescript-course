<h2 id= 'typescript-directive-comments'>TYPESCRIPT DIRECTIVE COMMENTS</h2>

[Back to Index](_sidebar.md)

TypeScript Directive Comments are not ordinary comments. They tell the compiler how to go about type-checking.

@ts-Directives include `@ts-expect-error`, `@ts-ignore`, `@ts-nocheck` and `@ts-check`.

<h3 id= 'ts-expect-error'>@ts-expect-error</h3>

This tells the compiler to expect that an error will occur on the next line and ignore it, i.e. "do not raise an error". Suppose we have this code snippet:

```ts
let str: number = "hello";
```

Here, we create a variable `str` and annotate its type as `number`, but we assign a string to it. This causes the compiler to raise an error:

```ts
let str: number = "hello";
// Error: Type 'string' is not assignable to type 'number'.
```

We can actually prevent TypeScript from raising the error with the @ts-expect-error directive:

```ts
// @ts-expect-error
let str: number = "hello";
```

This suppresses the compiler.

If however, the code should ordinarily not raise an error and we use the directive, TypeScript will raise another error. For instance if we have this:

```ts
// @ts-expect-error
let str: string = "hello";
```

Here we annotate the type of `str` as `string` and assign an actual string "hello" to the variable. This is correct. So, using the `@ts-expect-error` directive forces the compiler to raise an error on the directive itself:

```ts
// @ts-expect-error
let str: string = "hello";
// Error: Unused '@ts-expect-error' directive.
```

TypeScript warns you that you've added the directive but no error actually exists.

So, what is the point of suppressing errors? Isn't that why we are using TypeScript?

Well, there could be situations where you KNOW something TypeScript doesn't. You could be interacting with an external library with incorrect type definitions, or consuming data from an API, or just running a piece of code that is safe at runtime but impossible to fully type.

It's also useful when you are intentionally testing failing scenarios in a test file; you expect certain lines to produce type errors so you can appropriately simulate your test scenario.

If you are gradually migrating a codebase from JavaScript to TypeScript and you need to temporarily suppress type errors so that the build is not blocked, the directive can also come in handy.

<h3 id= 'ts-ignore'>@ts-ignore</h3>

[Back to Index](_sidebar.md) | [Back to Section](#typescript-directive-comments)

This tells the compiler to ignore any error on the next line:

```ts
// @ts-ignore
let str: number = "hello";
// No error is raised
```

Unlike "@ts-expect-error", if the line ordinarily does not raise an error, "@ts-ignore" will not raise an error:

```ts
// @ts-ignore
let str: string = "hello";
// No "unused directive" error is raised
```

You should prefer `@ts-expect-error` to `@ts-ignore`.

You could maybe use `@ts-ignore` when you are working with a library with inconsistent types and you need to suppress an error; but perhaps the library fixes the types next month and you don't want TypeScript to raise an unused directive error. Essentially you are telling TypeScript: "This line will error. Shut up now and forever, even if the error is later fixed."

<h3 id= 'ts-nocheck'>@ts-nocheck</h3>

[Back to Index](_sidebar.md) | [Back to Section](#typescript-directive-comments)

This directive is placed at the top of a file to disable type checking for the entire file

```ts
// @ts-nocheck
// Place the line above at the top of the file
```

As with the aforementioned directives, `@ts-nocheck` can be useful when migrating a codebase from JavaScript to TypeScript.

<h3 id= 'ts-check'>@ts-check</h3>

[Back to Index](_sidebar.md) | [Back to Section](#typescript-directive-comments)

This directive is placed at the top of a JavaScript file (a file with `.js` extension) to enable type checking as though it were a TypeScript file. You would typically use it with JSDoc comments to provide type information. For example:

```js
// @ts-check

/**
 * This function returns a greeting with the provided name.
 *
 * @param {string} name
 * @returns {string}
 */
function greet(name) {
  return `Hello. My name is ${name}`;
}

greet(1); // Error: Argument of type 'number' is not assignable to parameter of type 'string'.
```

A JSDoc is a special multi-line comment in JavaScript that starts with:

```js
/**
 * ...
 */
```

It lets you provide type information, description, parameter details, return values and other metadata for different JavaScript constructs like variables, objects, classes, functions (as shown above in `greet`) etc.

TypeScript understands JSDoc and uses it to infer types in `.js` files.

In the example, we defined a function that accepts a string argument, but we invoked it with a number. Because of the `@ts-check` directive, an error is raised, even though the file is plain JavaScript.

With this directive, you can benefit from type safety in a `.js` file. This makes it useful for gradually migrating a JavaScript codebase to TypeScript:
JS -> Add `@ts-check` and JSDoc -> Convert to `.ts` later.
