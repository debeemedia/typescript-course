<h2 id= 'modules'>MODULES</h2>

[Back to Index](./_sidebar.md)

A module is a file containing code that can be exported for use in other files. In a large codebase, we can't have all the code in one file; we'd typically have hundreds or thousands of files. Modules allow you to organise and manage your code in smaller pieces.

<h3 id= 'exports'>Exports</h3>
Exporting code from a file where it was written is the first step to using it in other files. To export code, you use the `export` keyword:

```ts
export function greet() {}
```

The code above exports a function `greet`. There is another way which allows exporting of multiple items on one line:

```ts
export { greet, returnSomething };
```

The above examples so far are called "named export" because we are exporting the function name `greet` or `returnSomething`. You use the same name when importing.

We also have "default export":

```ts
function printStatus() {}

export default printStatus;
```

Code that is exported as a default export can be imported with any name. Default exports are usually used when exporting the module's main content. It is convention to put default exports at the end of the file. A module can have only one default export and you cannot export multiple items with default export.

```ts
export default greet, returnSomething // Error
```

```ts
export default greet;
export default returnSomething;
// Error: A module cannot have multiple default exports.
```

Functions are not the only exportable or importable items. Classes, variables, types, interfaces etc. can also be exported:

```ts
export const greetings: string[] = ["hello", "hi"];

export type StringOrNumber = string | number;

export interface Person {}

export class Employee implements Person {}
```

<h3 id= 'imports'>Imports</h3>

[Back to Index](./_sidebar.md) | [Back to Section](#modules)

Importing code is the second step to using it in another file. You use the `import` keyword. To import something that was exported as a named export, you use the name that was used to export it. You also specify the path to the file from where it was exported. Create another file and try to import the exported code in your `practice.ts` file.

```ts
import { greet, returnSomething } from "./practice";

// You can then use the imported members:
greet();
```

The above is a "relative import" because the path is a relative path. "./" means "look in the current directory" for a file called "practice", assuming both files are in the same directory. You also do not need to specify the extension (".ts").

To import from a file that is not in the current directory e.g if you have a folder structure like so:

```
dir/
|__ practice.js
|__ inner_dir/
    |__ test.js
```

If you are in `test` and need to import from `practice`:

```ts
import { greet } from "../practice";
```

"../" means "come out of the current directory and look in the parent".

If you are in `practice` and need to import from `test`:

```ts
import { greet } from "./inner_dir/test";
```

All the above examples are named imports. To import an item that was exported as a default export, you can use any name. Let's say we are in `test` and want to import this default export from `practice`:

```ts
export default class Person {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
}
```

You cam import like so:

```ts
import Thing from "./practice"; // We don't have to use the name it was exported with

const thing = new Thing("Hitler");
console.log(thing.greet()); // Output: Hello, my name is Hitler
```

Unused imports are usually in grey to prompt you to remove if not needed

```ts
// 'returnSomething' is declared but its value is never read
```

You can import default and named exports on one line:

```ts
import Thing, { greet, returnSomething_ } from "./practice";
```

If you are importing two items from different files that have the same name e.g two named exports with the same name, you must alias one of them. Let's say we have a third file `log` that also has an exported function `greet`.

```ts
// practice
export function greet() {
  console.log("Hi from the practice file");
}

// log
export function greet() {
  console.log("Hi from the log file");
}
```

If you import both functions like this, you will get an error:

```ts
import { greet } from "./practice";
import { greet } from "./log";

// Duplicate identifier 'greet'.
```

Instead you alias one of them like so:

```ts
import { greet } from "./practice";
import { greet as logGreeting } from "./log";

greet(); // Output: Hi from the practice file
logGreeting(); // Output: Hi from the log file
```

Note that when you create your `log` file, you must run `tsc` to compile it to a js file else:

```bash
Error: Cannot find module './log'
Require stack:
- C:\Users\akink\Desktop\typescript-tutorial\test.js
```

This is because, you are actually importing the js file not the ts file.

<!-- todo explain this -->

Thankfully, with VSCode autosuggestion shortcut when importing (Ctrl + space), you do not need to manually type out paths.

Not all imports are from relative paths. It could be an absolute import or a path (aliased path) import which is a shorthand.

<!-- todo: more on this -->

<!-- todo: declaration files d.ts -->
