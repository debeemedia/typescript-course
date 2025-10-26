<h2 id= 'modules'>MODULES</h2>

[Back to Index](_sidebar.md)

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

[Back to Index](_sidebar.md) | [Back to Section](#modules)

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

<h3 id= 'type-declaration-files'>Type Declaration Files</h3>

[Back to Index](_sidebar.md) | [Back to Section](#modules)

A Type Declaration File is a TypeScript file that contains only type definitions, no actual code logic. It can be identified by its `.d.ts` extension. It gives the compiler information about existing JavaScript code. This is usually useful when importing a library that was written in plain JavaScript into a TypeScript codebase. The library author will usually provide a type declaration file so that TypeScript can know about the types of function arguments, their return values and the structure of objects within the library.

Let's illustrate how it works with this mini setup. We'll be creating a simple library that sums all the numbers in an array.

<b>Example 1. Declaring Types for a JavaScript Library</b>

- Inside a new practice folder, create a folder called `lib`. Inside the library, create the JavaScript file called `sum.js` and a corresponding type declaration file for it `sum.d.ts`. Note that the files must have the same name "sum".

- Export a function that sums all the numbers in an array from the `sum.js` file:

```js
exports.sumArray = function (arr) {
  return arr.reduce((prev, curr) => prev + curr, 0);
};
```

- Define types for the function in the `sum.d.ts` file:

```ts
export function sumArray(arr: number[]): num;
```

The function takes in an array of numbers and returns a number.

- Now, let's mimic a consumer of the library. Create a `src` folder (on the same level as the `lib` folder) with an `index.ts` file.

- Import and consume the library in the `index.ts` file:

```ts
import { sumArray } from "../lib/sum";

console.log(sumArray([1, 2, 3]));
```

You wil observe that we are already getting type safety. For instance, try to provide a string argument to the function:

```ts
console.log(sumArray("hi"));
// Error: Argument of type 'string' is not assignable to parameter of type 'number[]'.
```

This is thanks to the type we declared in the `sum.d.ts` file.

- Create a tsconfig.json file with minimal config options:

```json
{
  "compilerOptions": {
    "target": "ES2019",
    "module": "CommonJS",
    "outDir": "dist",
    "rootDir": ".",
    "esModuleInterop": true,
    "allowJs": true,
    "moduleResolution": "node",
    "strict": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*", "lib/**/*"]
}
```

- Install TypeScript as a project dependency:

```bash
npm install --save-dev typescript
```

Compile:

```bash
npx tsc
```

> SideNote: In case you're wondering how `npx tsc` differs from `tsc`: when you run `tsc`, you're running the global TypeScript compiler (the version installed with `npm install -g typescript`) instead of the installation that matches your project's version. See [setup](03-setup.md?id=setup).

You will notice that the files are compiled to a `dist` folder (what we specified in the `outDir` in the tsconfig)

- Run the compiled JavaScript:

```bash
node dist/src/index.js

# Output: 6
```

<b>Example 2. Letting TypeScript Generate `.d.ts` Files Automatically</b>

We could also have a setup where our code is written in TypeScript and we just want to generate a `.d.ts` file for consumers. Instead of writing it manually, we can have the compiler generate it for us:

- Inside a new practice folder, create a `src` folder that contains a TypeScript file `sum.ts` with the function:

```ts
export function sumArray(arr: number[]): number {
  return arr.reduce((prev, curr) => prev + curr, 0);
}
```

- Create a tsconfig.json file to emit declarations:

```json
{
  "compilerOptions": {
    "target": "ES2019",
    "module": "CommonJS",
    "declaration": true,
    "declarationDir": "dist/types",
    "outDir": "dist",
    "rootDir": "src",
    "esModuleInterop": true,
    "moduleResolution": "node",
    "strict": true
  },
  "include": ["src/**/*"]
}
```

Key lines:

(a) `"declaration": true` will generate the `.d.ts` files.

(b) `"declarationDir": "dist/types"` is optional. It will put the declarations in a separate `types` folder. If omitted, TypeScript will place the `.d.ts` next to the `.js` in `dist`.

(c) `"outDir": "dist"` will compile the JS files to the `dist` folder.

- Run the commands:

```
npm install --save-dev typescript

npx tsc
```

After `tsc` runs, you'll have:

```
dist/
|__ sum.js
|__ types/
    |__ sum.d.ts
```

(If you used `declarationDir: dist/types`)

The `sum.d.ts` contains the declaration:

```ts
export declare function sumArray(arr: number[]): number;
```

Now when someone installs your package in a TypeScript project, the `.d.ts` file will provide types.

If you’re publishing your package, ensure your `package.json` includes the types or typings field:

```json
{
  "name": "my-sum-lib",
  "main": "dist/sum.js",
  "types": "dist/types/sum.d.ts"
}
```

This tells TypeScript consumers exactly where your type definitions live.
