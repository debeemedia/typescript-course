<h3 id= 'tsconfig'>About the "tsconfig.json" File</h3>

[Back to Index](_sidebar.md)

The `tsconfig.json` file specifies the root files and the compiler options required to compile the project. It's what tells the TypeScript compiler how to behave. Below is a snippet:

```json
{
  "compilerOptions": {
    "target": "ES6",
    "module": "commonjs",
    "strict": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "sourceMap": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

There are other configurations and settings and luckily, the file comes with helpful comments on each.

Briefly:

`compilerOptions` specifies settings for how the compiler should output code.

`include` specifies files or folders to include in compilation.

`exclude` specifies files or folders to ignore.

Under `compilerOptions`:

`strict` enables all strict type-checking options. When this is set to `true`, TypeScript will complain if you, for instance, write a function without typing (providing types for) the parameters - "Parameter implicitly has an any type".

`rootDir` specifies where the source files are located.

`outDir` specifies where to place compiled JavaScript files.

`target` specifies the version of JavaScript that TypeScript will compile to to ensure compatibility with older environments e.g old browsers.

`sourceMap` specifies whether to generate source map files (`.js.map`). These are non-human-readable files that map the original `ts` files to the compiled `js` files. They help debugging tools show you the exact line in your TypeScript file where an error occurred, otherwise you would have to sift through obfuscated JavaScript files.
