<h2 id= 'type-narrowing-and-type-guards'>TYPE NARROWING AND TYPE GUARDS</h2>

[Back to Index](_sidebar.md)

Type Narrowing is a way of checking and reducing the possible types of a value in a conditional block. When the particular type of a variable has been narrowed, operators, properties and methods specific to that type will be available. You can confidently perform safe and accurate operations without mindless type assertions that can lead to unexpected values or even runtime errors.

Some type guards you can use when type narrowing include `typeof`, `Array.isArray` `instanceof`, and `in`. These are all JavaScript runtime checks that are leveraged by TypeScript for type narrowing at compile time. TypeScript also has its own exclusive form of type guard: the `is` keyword, which can be used to define custom type guards.

<h3 id= 'typeof-type-guard'>Type Guard: typeof</h3>

The `typeof` operator is used to check the primitive type of a value at runtime. It returns "string", "number", "bigint", "boolean", "undefined", "object", "function", or "symbol".

> SideNote: `typeof` doesn't work for arrays because in JavaScript, arrays are technically objects. For example, `typeof [1, "hi"]` will return "object". To check for arrays, use "Array.isArray".

> `typeof` also doesn't work for nulls because "null" is of object type (the classic JavaScript bug). `typeof null` will return "object". To check for null, use the strict equality operator (===).

<!-- todo: at the juncture here, talk about strictNullChecks and strict in tsconfig? -->

Let's look at an example using `typeof`:

```ts
function returnSomething(param: string | number): string {
  let result: string;

  if (typeof param === "string") {
    result = param.trim().toUpperCase();
  } else {
    result = param.toExponential();
  }
  return result;
}

console.log(returnSomething("halo")); // Output: HALO
console.log(returnSomething(36100)); // Output: 3.61e+4
```

Here, we have a function that accepts a parameter that could possibly be of type string or number. Before performing any operation with the parameter, we check its type.

Notice that because TypeScript narrows the type of `param` to "string" in the first conditional block, string methods like "trim", "toUpperCase", "toLowerCase", "charAt", "endsWith" etc. are available. These methods are not available in the else-conditional block where TypeScript narrows the type of `param` to "number". Instead we have methods for performing operations on numbers there.

Imagine if we do not do type narrowing and we call the function like so:

```ts
function returnSomething(param: string | number): string {
  return param.trim().toUpperCase();
}

console.log(returnSomething("halo"));
// Error:
// Property 'trim' does not exist on type 'string | number'.
// Property 'trim' does not exist on type 'number'.
```

TypeScript will throw a compile-time error, which, if ignored (with "// @ts-ignore") <!-- todo: link here; discuss typescript ignore and expect error comments -->, will not cause any issue because the argument passed in is a string and the methods `trim()` and `toUpperCase()` are valid string methods.

If we however pass in a number and ignore TypeScript's warning, we will get a runtime error:

```ts
console.log(returnSomething(36100));
```

```bash
C:\Users\akink\Desktop\typescript-tutorial\practice.js:690
    return param.trim().toUpperCase();
                 ^

TypeError: param.trim is not a function
```

<h3 id= 'array-is-array-type-guard'>Type Guard: Array.isArray</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-narrowing-and-type-guards)

`Array.isArray` is a JavaScript built-in method that returns a boolean "true" if the value is an array and "false" otherwise. For example:

```ts
console.log(Array.isArray([1, "hi"])); // Output: true
console.log(Array.isArray(new Array())); // Output: true
console.log(Array.isArray("hi")); // Output: false
console.log(Array.isArray(1)); // Output: false
```

Let's use it as a type guard to narrow down the value in this example:

```ts
function returnSomething(param: string | string[]): string {
  if (Array.isArray(param)) {
    return param.join(", ");
  } else {
    return param;
  }
}

console.log(returnSomething(["halo", "bye"])); // Output: halo, bye
console.log(returnSomething("halo")); // Output: halo
```

We use `Array.isArray` to check if `param` is an array. When the type is narrowed to an array, TypeScript knows we can safely call array methods like `join`, `map`, `filter` etc. We use the `join` method on the array to return a string. In the "else" block, when narrowed to string, only string methods like `trim`, `toUpperCase` etc. are available. We return the string as it is.

<h3 id= 'instance-of-type-guard'>Type Guard: instanceof</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-narrowing-and-type-guards)

The `instanceof` operator is used to check if an object is an instance of a particular constructor function or class at runtime:

```ts
class Employee {}
class Manager {}

const employee = new Employee();
console.log(employee instanceof Employee); // Output: true
console.log(employee instanceof Manager); // Output: false
```

`instanceof` leverages JavaScript's object prototype chaining and checks whether the `prototype` property of the class constructor exists anywhere in the object's prototype chain.

To loosely explain, `instanceof` checks:

- Is `employee.__proto__` equal to `Employee.prototype`? Yes. Return true:

```ts
console.log(Object.getPrototypeOf(employee) === Employee.prototype); // Output: true

console.log(Object.getPrototypeOf(employee) === Manager.prototype); // Output: false
```

In JavaScript, any object instantiated from a class will have it's hidden `[[Prototype]]` or `__proto__` pointing to that class's prototype object. It is this chain that `instanceof` checks and returns true if it finds the constructor's prototype at any level and false if otherwise.

We use `instanceof` for type narrowing like so:

```ts
class Employee {
  resign() {
    console.log("I quit!");
  }
}
class Manager {
  fire() {
    console.log("You're fired!");
  }
}

function takeAction(person: Employee | Manager) {
  if (person instanceof Employee) {
    person.resign();
  } else {
    person.fire();
  }
}

const employee = new Employee();
const manager = new Manager();

takeAction(employee); // Output: I quit!
takeAction(manager); // Output: You're fired!
```

Here, TypeScript uses the `instanceof` type guard to narrow the union type. In the "if" block, the `person` parameter is treated as an instance of the `Employee` class, so the method `resign` is available; conversely, in the "else" block, `person` is treated as an instance of `Manager`, and so the method `fire` is available.

Again, because of prototype chaining, `instanceof` also checks a class that inherits from another class. If `Manager` extends `Employee`, then a `Manager` instance is considered both an `Employee` and a `Manager`:

```ts
class Employee {
  resign() {
    console.log("I quit!");
  }
}
class Manager extends Employee {
  fire() {
    console.log("You're fired!");
  }
}

function takeAction(person: Employee) {
  if (person instanceof Manager) {
    person.fire();
    person.resign();
  } else {
    person.resign();
  }
}

const employee = new Employee();
const manager = new Manager();

// Output: I quit!
takeAction(employee);

// Output:
// You're fired!
// I quit!
takeAction(manager);
```

In the "if" block where `person` is treated as an instance of `Manager`, both `fire` and `resign` methods are available because `Manager` extends `Employee` and as such, `manager` is an instance of both `Manager` and `Employee`.

To loosely explain, `instanceof` checks the prototype chain:

- Is `manager.__proto__` equal to `Employee.prototype`? No. It's actually equal to `Manager.prototype`. Go up the chain:
- Is `manager.__proto__.__proto__` equal to `Employee.prototype`? Yes. Return `true`

```ts
console.log(Object.getPrototypeOf(manager) === Employee.prototype); // Output: false

console.log(
  Object.getPrototypeOf(Object.getPrototypeOf(manager)) === Employee.prototype
); // Output: true
```

<h4 id= 'interfaces-and-instanceof'>Interfaces and instanceof</h4>

Unlike classes, interfaces do not exist at runtime. They exist only at compile-time for type checking and are stripped away after compilation. Remember we said that `instanceof` works by checking the object's prototype chain at runtime? This means that `instanceof` cannot work with interfaces:

```ts
interface Person {
  speak(): void;
}

class Employee implements Person {
  speak() {
    console.log("Hi there!");
  }
}

const employee = new Employee();

console.log(employee instanceof Person);
// Error: 'Person' only refers to a type, but is being used as a value here.
```

TypeScript complains because `Person` disappears after compilation. There is nothing to check against at runtime.

<h4 id= 'structural-duck-typing'>Structural (Duck) Typing</h4>

Unlike runtime type guards such as `instanceof` that depend on prototypes, TypeScript's type-system depends on what is called Structural Typing or Duck Typing at compile time. It means that type compatibility is determined by the structure of the object rather than its class or constructor. Duck typing is based on the saying: "If it looks like a duck, swims like a duck, and quacks like a duck, then it probably is a duck."

So, as demonstrated in the following example, if an object has the required properties and methods, then it is considered compatible with the interface, regardless of what class created it:

```ts
interface Person {
  speak(): void;
}

class Employee implements Person {
  speak() {
    console.log("I am an employee");
  }
}

class AI {
  speak() {
    console.log("I am artificial intelligence!");
  }
}

function saySomething(person: Person) {
  person.speak();
}

const employee = new Employee();
const aI = new AI();

saySomething(employee); // Output: I am an employee
saySomething(aI); // Output: I am artificial intelligence!
```

Both `Employee` and `AI` are accepted as `Person`, even though `AI` does not implement `Person`, because they have the required `speak` method. This is duck typing: "if it quacks like a duck, it is a duck."

Note:

- JavaScript itself does not have a type system. It only checks if the properties and methods are available at runtime and throws an error if not. So it behaves duck-like.
- TypeScript formally uses duck-typing at compile time, so you get the error earlier if an object does not match the required shape.

<h3 id= 'in-type-guard'>Type Guard: in</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-narrowing-and-type-guards)

Another runtime tool is the `in` operator which simply checks if a property exists in an object. This reflects JavaScript's duck-type behaviour: if it exists, fine; if not, throw an error. TypeScript then enforces this duck-typing at compile time, ensuring you get the error earlier.

We use the `in` operator for type narrowing like so:

```ts
interface Person {
  speak(): void;
}
interface AI {
  generate(): void;
}

function act(entity: Person | AI) {
  if ("speak" in entity) {
    // TypeScript knows it's a Person
    entity.speak();
  } else {
    // TypeScript knows it's an AI
    entity.generate();

    // entity.speak(); // Error: Property 'speak' does not exist on type 'AI'.
  }
}
```

<h3 id= 'is-type-guard'>User-defined (Custom) Type Guard with: is</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-narrowing-and-type-guards)

Unlike `typeof` or `instanceof` which are built into JavaScript, the `is` keyword is a TypeScript-only feature that lets you create user-defined or custom type guards. Here's how it works:

```ts
function isString(param: unknown): param is string {
  return typeof param === "string";
}

function test(param: string | number) {
  if (isString(param)) {
    // TypeScript narrows the type of param to "string". String methods are safe here.
    return param.toUpperCase();
  } else {
    // TypeScript narrows the type of param to "number". Number methods are available here.
    return param.toExponential();
  }
}
```

The first function, `isString`, is a user-defined type guard function. The function's return type (the `param is string` part) is called a "type predicate". In TypeScript, a type predicate is a special form of return type used in functions to tell the compiler that a parameter has a specific type if the function returns `true`. A type predicate has the form `param is Type` and tells the compiler: "If this function returns `true`, then treat `param` as `Type` within this scope."

A user-defined type guard like our `isString` must satisfy the following conditions:

- The parameter in the type predicate must refer to one of the function's parameters e.g. the `param` in `param is string` is the function parameter.
- The function must return a boolean value e.g. `typeof param === "string"`will return `true` if `param` is a string and `false` otherwise.

The function as a whole helps us define custom logic that TypeScript can use for type narrowing. In our example, we are essentially telling TypeScript: "If this function returns `true`, then `param` is a string, therefore, treat `param` as a string in whatever conditional branch it is used". This is why TypeScript is able to narrow the type of the parameter `param` in the `test` function. We could safely access properties and methods (e.g `toUpperCase`) specific to the narrowed type.

The essence of the custom type guard function `isString` is to be used in another function like `test` to help with type narrowing. The `is` keyword in the return type tells the compiler: "If this function returns true, then assume the type of the variable is what I say it is". If you only return a plain boolean (without using a type predicate with `is`), TypeScript won't be able to narrow the type:

```ts
function isString(param: unknown): boolean {
  return typeof param === "string";
}

function test(param: string | number) {
  if (isString(param)) {
    return param.toUpperCase();
    // Error: Property 'toUpperCase' does not exist on type 'number'
  } else {
    return param.toExponential();
    // Error: Property 'toExponential' does not exist on type 'string'.
  }
}
```

Let's look at another example where we combine the `in` operator with a custom type guard:

```ts
interface Person {
  id: number;
  name: string;
}
interface Employee extends Person {
  employeeId: number;
}
interface Manager extends Employee {
  mgtLevel: number;
}

function isManager(person: Person | Employee | Manager): person is Manager {
  return "mgtLevel" in person;
}

function returnMgtLevel(person: Person | Employee | Manager) {
  if (isManager(person)) {
    console.log(`Manager on level ${person.mgtLevel}`);
  } else {
    console.log("Not a manager");
  }
}

const employee: Employee = { id: 1, employeeId: 5, name: "Jack" };
const manager: Manager = { id: 1, employeeId: 5, name: "Jill", mgtLevel: 14 };

returnMgtLevel(employee); // Output: Not a manager
returnMgtLevel(manager); // Output: Manager on level 14
```

The example above is a good use-case for custom type guards. Remember we can't use `instanceof` because at runtime, JavaScript does not see interfaces, just plain objects. That's where our custom type guard comes in. It helps us avoid unsafe type assertions (`as Manager`).

There is another alternative with "discriminated unions"; sometimes people prefer it to writing custom type guards.

<h3 id= 'discriminated-unions'>Discriminated Unions</h3>

[Back to Index](_sidebar.md) | [Back to Section](#type-narrowing-and-type-guards)

Discriminated Unions are also known as tagged unions. They provide another way to do type narrowing without writing custom type guards.

Suppose we have these three interfaces:

```ts
interface Wolf {
  howl(): void;
  species: "Canis lupus";
}

interface Person {
  speak(): void;
  species: "Homo sapiens";
}

interface AI {
  generate(): void;
  species: "Homo deus";
}
```

We have a property `species` that is common to all three interfaces but holds a different value for each interface. It is the "discriminant" or "tag" property. By checking the value of the discriminant property in a control flow statement like an "if" or a "switch" statement, TypeScript can narrow the type of the variable, allowing you to access properties and methods applicable to that variable without mindless type assertions. For example:

```ts
type Entity = Wolf | Person | AI;

function doSomething(entity: Entity) {
  switch (entity.species) {
    case "Canis lupus":
      // `howl` is available here
      entity.howl();
      break;

    case "Homo sapiens":
      // `speak` is available here
      entity.speak();
      break;

    case "Homo deus":
      // `generate` is available here
      entity.generate();
      break;

    default:
      throw new Error("Invalid entity");
  }
}
```

The discriminated union in our example is the union type `Entity` which is a union of `Wolf | Person | AI`. All three members share the discriminant property `species` with different literal values. Based on the discriminant, TypeScript can automatically narrow the type, eliminating the need for custom `is` functions.
