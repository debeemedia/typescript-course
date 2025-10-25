<h2 id= 'introduction'>INTRODUCTION TO TYPESCRIPT</h2>

([Back to Index](../_sidebar.md))

TypeScript is a superset of JavaScript. This means it was built on top of JavaScript's syntax and functionality. TypeScript is a statically-typed language, as opposed to JavaScript which is dynamically-typed. What does this mean?

In a dynamically-typed language e.g JavaScript, the type of the variables can change at anytime, and the type is checked only at runtime. In a statically-typed language e.g Go, the type of the variables cannot change once declared, and the type is checked at compile time. Why is this important and how does runtime differ from compile time?

Runtime is the phase in code execution where the program is actually executed by the computer. Compile time is the phase where the source code (the high-level language code you've written) is analysed and converted by a compiler into another format, usually machine-readable code (code that the computer can understand) or bytecode, before the program is executed by the computer. Again, statically-typed languages check types at compile time, while dynamically-typed languages check types at runtime.

The implication of this is that, with statically-typed languages, the errors are detected during compilation, before the program runs. This lets us catch type errors early, and very importantly, allows for finding of errors that could be hidden in edge cases. Generally, it lets us write more reliable code, with fewer errors. Dynamically-typed languages on the other hand, though flexible, could cause unexpected behaviour since type errors are not caught until runtime.

TypeScript gives us the static typing ability when using JavaScript. Note that it is eventually compiled down to JavaScript that can run in the browser and Node.js environments, and the types are stripped off; type checking happens during this TypeScript-to-JavaScript compilation step, not at runtime when JavaScript is executed. So think of it as a developer-tool that allows us enjoy the benefits of static typing, without leaving the JavaScript ecosystem.
