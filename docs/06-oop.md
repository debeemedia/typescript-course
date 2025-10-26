<h2 id= 'oop'>OOP</h2>

[Back to Index](../_sidebar.md)

<h3 id= 'classes-access-modifiers'>Classes and Access Modifiers</h3>

Classes are not limited to TypeScript. They are a JavaScript feature:

<!-- todo: they are in fact a feature of any programming language! -->

```js
class Person {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const person1 = new Person("Debs");
console.log(person1.name); // Output: Debs
```

For a class like the above `Person` class in TypeScript, by default, the properties and methods are "public". In TypeScript, we have the following class access modifiers:

- Public: Any property or method with this modifier can be accessed anywhere.
- Private: Any property or method with this modifier can only be accessed within the class.
- Protected: Any property or method with this modifier can only be accessed within the class or a subclass.

With "private" properties, we cannot do this:

```ts
class Person {
  private name: string;
  // ...
}

const person1 = new Person("Debs");
console.log(person1.name); // Error: Property 'name' is private and only accessible within class 'Person'
```

You will notice that VsCode does not suggest the private property when typing:

```ts
console.log(person1.name);
```

A workaround to access "name" is to use something like a getter:

```ts
class Person {
  private name: string;

  constructor(name: string) {
    this.name = name;
  }

  getName() {
    return this.name;
  }
}

const person1 = new Person("Debs");
console.log(person1.getName()); // Output: Debs
```

Modifiers are also applicable to methods:

```ts
class Person {
  // ...
  private greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
  // ...
}

const person1 = new Person("Debs");
console.log(person1.name); // Error: Property 'greet' is private and only accessible within class 'Person'
```

Protected properties or methods can ony be accessed inside the class itself or another class that inherits from the class:

```ts
class Person {
  protected name: string;

  constructor(name: string) {
    this.name = name;
  }

  protected greet() {
    console.log(`Hello, my name is ${this.name}`);
  }
}

const person1 = new Person("Debs");

person1.name; // Error: Property 'name' is protected and only accessible within class 'Person' and its subclasses.
person1.greet(); // Error: Property 'greet' is protected and only accessible within class 'Person' and its subclasses.ts
```

Let's create a subclass:

```ts
class Employee extends Person {
  callMe() {
    console.log("Hi " + this.name);
  }
}

const employee = new Employee("Debs");

employee.callMe(); // Output: Hi Debs
```

It is recommended practice to always use the strictest access modifier. If the property or method does not need to be public, make it protected; if it does not need to be protected, make it private.

Why make a property or method private? <!-- todo -->

<h4 id= 'difference-between-private-and-hash-prefix'>Difference between `private` and `#`</h4>

"private" access modifier is a TypeScript-only feature. It exists only at compile-time, meaning its restriction is only enforced when type checking. It does not exist in the emitted JavaScript code, as such, it can be bypassed by ignoring TypeScript (TypeScript comments will be discussed later).

"#" is a real JavaScript feature available since ES2022. It is actually enforced at runtime by the JavaScript engine, as such, any class property or method prefixed with "#" is truly private and can never be accessed outside of the class.

Let's illustrate the difference with this example:

```ts
class CheckingPrivate {
  private prop1: string;
  #prop2: string;

  constructor(prop1: string, prop2: string) {
    this.prop1 = prop1;
    this.#prop2 = prop2;
  }
}
```

Let's try to access the property with TypeScript's `private` modifier:

```ts
const checkingPrivate = new CheckingPrivate("property 1", "property 2");

console.log(checkingPrivate.prop1);
//Property 'prop1' is private and only accessible within class 'CheckingPrivate'.
```

Of course, TypeScript throws a compile-time error. But when you run:

```bash
tsc
```

And check the compiled code in your `practice.js` file, you will see the corresponding line where you accessed `prop1`

```js
console.log(checkingPrivate.prop1);
```

And when you run the file:

```bash
node practice.js
```

"property 1" is logged to the console.

Let's try to access the property with JavaScript's "#" modifier (notice that the property name is always prefixed with "#" even when accessing):

```ts
const checkingPrivate = new CheckingPrivate("property 1", "property 2");

console.log(checkingPrivate.#prop2);
// Property '#prop2' is not accessible outside class 'CheckingPrivate' because it has a private identifier.
```

Now, when you run:

```bash
tsc
```

And check the compiled code in your `practice.js` file, this is what the corresponding line where you attempted to access the property looks like:

```js
console.log(checkingPrivate.);
```

It is completely invisible and raises a syntax error: "Identifier expected."

Attempt to run the file again:

```bash
node practice.js
```

And you will get a runtime error:

```bash
C:\Users\me\Desktop\typescript-tutorial\practice.js:537
console.log(checkingPrivate.);
                            ^

SyntaxError: Unexpected token ')'
```

In short, use "private" if you only care about TypeScript-level protection and don't mind that it is still accessible in plain JavaScript. Use "#" when you need true runtime privacy e.g. to hide implementation details.

<h3 id= 'constructor-parameter-properties'>Constructor Parameter Properties</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#oop)

TypeScript gives us a shorthand for declaring and initializing class properties directly from the constructor. Instead of declaring the property first and then assigning it in the constructor, you can prefix the constructor parameter with an access modifier: `public`, `private` or `protected`.
Example without shorthand:

```ts
class Person {
  public name: string;

  constructor(name: string) {
    this.name = name;
  }
}
```

Example using parameter properties:

```ts
class Person {
  constructor(public name: string) {}
}
```

Both versions achieve the same thing. With the shorthand, TypeScript automatically declares the `name` property and initializes it with the constructor argument. You can also do same with the other access modifiers.

Constructor parameter properties are great for simple classes; but for classes with many parameters or complex logic, explicit property declaration may be better to avoid cluttering the constructor.

<h3 id= 'abstract-classes'>Abstract Classes</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#oop)

An abstract class is a restricted class whose sole purpose is to act as a base class. It cannot be instantiated, that is, it cannot be used to create objects. It is meant to be extended by a subclass.
If you try to instantiate it, TypeScript will throw an error:

```ts
abstract class Person {}

const person1 = new Person(); // Error: Cannot create an instance of an abstract class
```

An abstract class can have abstract properties and methods; these abstract properties and methods must be implemented by any subclass of the abstract class.

```ts
abstract class Person {
  // Abstract property
  abstract name: string;

  // Abstract method
  abstract introduce(): string;

  // Concrete method
  greet(): string {
    return `Hello there!. ${this.introduce()}`;
  }
}

class Employee extends Person {}
// Error: Non-abstract class 'Employee' is missing implementations for the following members of 'Person': 'name', 'introduce'.
```

Notice that the abstract property "name" and the abstract method "introduce" are not given any value or implemented at all, as opposed to the concrete method "greet". If you try to access an abstract property or method in the constructor, you will get an error:

```ts
abstract class Person {
  abstract name: string;

  abstract introduce(): string;

  constructor() {
    console.log(this.name); // Error: Abstract property 'name' in class 'Person' cannot be accessed in the constructor.
  }

  greet(): string {
    return `Hello there!. ${this.introduce()}`;
  }
}
```

By declaring an abstract property/method, you are essentially saying: "Any subclass must provide this property/method, but the abstract/base class itself must not and cannot implement it". So, inside the base class constructor, that property/method does not exist yet. It is only guaranteed to exist once a concrete subclass is instantiated. This way, TypeScript prevents you from accessing an uninitialised property.

However, you can safely use abstract properties/methods inside other concrete methods of the abstract class. This is because, by the time those methods are called, the subclass will have provided the actual implementation. This is why we confidently called `this.introduce()` in the concrete method, `greet`, because we are sure that it will be implemented by the subclass.

Let's create a subclass:

```ts
class Employee extends Person {
  name = "Debs";

  introduce(): string {
    return `I am ${this.name} and I am an employee.`;
  }
}

const employee = new Employee();

console.log(employee.greet()); // Output: Hello there!. I am Debs and I am an employee.

console.log(employee.introduce()); // Output: I am Debs and I am an employee.
```

We can have another class that inherits from the abstract class:

```ts
class Manager extends Person {
  name = "Debs";

  introduce(): string {
    return `I am ${this.name} and I am the manager.`;
  }
}

const manager = new Manager();

console.log(manager.greet()); // Output: Hello there!. I am Debs and I am the manager.

console.log(manager.introduce()); // Output: I am Debs and I am the manager.
```

So in practice, the abstract method is usually utilized in a concrete method in the abstract class, allowing for common behaviour to be shared while the subclasses fill in the details.

<!-- todo: override keyword in classes -->

<h3 id= 'classes-implementing-interfaces'>Classes Implementing Interfaces</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#oop)

Classes can extend other classes. Also, classes, like objects, can implement interfaces. <!-- todo: link to previous -->
The interface provides a structure that the class must adhere to. Just like abstract properties and methods on abstract classes, every property and method on an interface is non-implementable. It is the duty of the class to implement them. All non-optional members (properties and methods) must be implemented:

```ts
interface Person {
  name: string;
  contact?: {
    email_address: string;
    phone_number: string;
  };
  greet(): string;
}

class Employee implements Person {}
// Error: Class 'Employee' incorrectly implements interface 'Person'.
// Type 'Employee' is missing the following properties from type 'Person': name, greet
```

> SideNote: When something is not implemented e.g `greet(): string` on the `Person` interface, we mean it has no block "{}". It is just a declaration of a contract.

When defining the class that will implement the interface, we can have other members that are not on the interface, as long as we have what is on the interface:

```ts
class Employee implements Person {
  name: string;
  employeeId?: number;

  constructor(name: string) {
    this.name = name;
  }

  greet() {
    return `Hello. I am ${this.name}`;
  }

  sayBye() {
    return `Goodbye!`;
  }
}

const employee = new Employee("Debs");

console.log(employee.greet()); // Output: Hello. I am Debs
console.log(employee.sayBye()); // Output: Goodbye!
```

> SideNote: Notice that we made `employeeId` optional on the `Employee` class. Because we do not initialize it in the constructor, if it were required, TypeScript will raise an error: "Property 'employeeId' has no initializer and is not definitely assigned in the constructor."

Let's create another class that implements the interface:

```ts
class Manager implements Person {
  name: string;
  mgtLevel?: number;

  constructor(name: string) {
    this.name = name;
  }

  greet() {
    return `Hello. I am ${this.name}`;
  }
}

const manager = new Manager("deBee");
console.log(manager.greet()); // Output: Hello. I am deBee
```

To demonstrate how interfaces help us with abstraction, supposing we were to instantiate `employee` like this:

```ts
const employee: Person = new Employee("Debs");

console.log(employee.greet()); // Output: Hello. I am Debs
console.log(employee.sayBye()); // Error: Property 'sayBye' does not exist on type 'Person'.
```

We got a compile-time error when we tried to call the `sayBye` method. Because we are now strictly viewing `employee` through the lens of the `Person` interface, TypeScript will not assume that any member that is not defined on the interface is available, even if it actually is. Note that if you run the code, no runtime error will be thrown and you will still get "Goodbye!" logged to your console. It is only TypeScript's way of checking you so you can be extra sure.

Suppose we define a function that takes in a parameter `person` of the `Person` interface:

```ts
function introduceSelf(person: Person) {
  console.log(person.greet());
}
```

In the function, the only members available on `person` are those available on the interface. We can confidently call `greet` because we are sure it will be available on whatever "person" is.

If we had done this instead, TypeScript would raise a warning:

```ts
function introduceSelf(person: Person) {
  console.log(person.sayBye()); // Error: Property 'sayBye' does not exist on type 'Person'.
}
```

If we ignore TypeScript's warning and go ahead to call the function and pass in `employee`:

```ts
introduceSelf(employee);
```

We get "Goodbye!" logged to the console when we run the code. No problem there because `sayBye` actually exists on `employee`. But what if we pass in `manager`:

```ts
introduceSelf(manager);
```

We get an actual runtime error because that method does not exist on `manager`:

```bash
C:\Users\akink\Desktop\typescript-tutorial\practice.js:452
    console.log(person.sayBye());
                       ^

TypeError: person.sayBye is not a function
```

So this is how TypeScript helps safeguard your code.

The idea of interfaces is that we are viewing an object through a particular lens. We treat objects that implement an interface similarly and hide any complexity we don't need. This makes our program flexible.

<!-- todo -->

A class can implement multiple interfaces...

<!-- todo: this section on classes implementing interfaces needs serious polishing -->

<!-- todo -->

When to use interface versus abstract class?
...

<h3 id= 'static-properties-methods'>Static Properties and Methods</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#oop)

In JavaScript, `static` is used to define a property or method that belongs to the class, rather than an instance of the class. It is especially useful for shared values that are not dependent on the class instances. Nothing changes in TypeScript here, except the added type safety and access modifiers.

Example:

```ts
class Person {
  // Static property
  static instanceCount: number = 0;

  // Instance property
  name: string;

  constructor(name: string) {
    // this.instanceCount++
    // Error: Property 'instanceCount' does not exist on type 'Person'. Did you mean to access the static member 'Person.instanceCount' instead?

    Person.instanceCount++;

    // "this" is used to reference an instance of the class
    this.name = name;

    console.log(`Inside constructor: I am ${this.name}`);
  }

  // Static method
  static greet() {
    console.log(`Inside static method "greet": I am ${this.name}`);
  }
}

const employee = new Person("Debs");
// This is how to access an instance property:
console.log(employee.name);
// Output:
// Inside constructor: I am Debs
// Debs

const manager = new Person("deBee");
console.log(manager.name);
// Output:
// Inside constructor: I am deBee
// deBee

// This is how to access a static property:
console.log(Person.instanceCount);
// Output: 2

// This is how to access a static method:
Person.greet();
// Output:
// Inside static method "greet": I am Person
```

Notice how `this.name` in the static method `greet` resolves to the class name "Person" because in static methods, "this" points to the class itself, not an instance. Conversely, inside the constructor, "this" refers to the specific instance ("Debs" or "deBee") created with the `new` keyword.

If you already know JavaScript, all this is not news to you. So what does TypeScript do?

TypeScript makes it possible to use the access modifiers we spoke about: `public` (default in JavaScript), `private` and `protected` with `static`:

```ts
class Person {
  // private static: only accessible inside this class
  private static count: number = 0;

  // protected static: accessible inside this class and subclasses
  protected static lastId: number = 0;

  public id: number;

  constructor() {
    // Allowed. Inside the class, we can access private static
    Person.count++;

    Person.lastId++;

    this.id = Person.lastId;
  }

  public static getCount() {
    return Person.count;
  }
}
const person1 = new Person();
const person2 = new Person();

// Output: 2
console.log(Person.getCount());

// Error: Property 'count' is private and only accessible within class 'Person'.
console.log(Person.count);

// Error: Property 'lastId' is protected and only accessible within class 'Person' and its subclasses.
console.log(Person.lastId);

class SubPerson extends Person {
  constructor() {
    super();
    // Allowed:
    console.log(SubPerson.getCount());

    // Allowed. Subclasses can access protected static
    console.log(SubPerson.lastId);

    // Error: Property 'count' is private and only accessible within class 'Person'.
    console.log(SubPerson.count);
  }
}
```

- `private static`: accessible only inside the class itself.
- `protected static`: accessible inside the class and any subclasses.
- `public static` (default in JavaScript): accessible anywhere.

This shows how TypeScript enforces visibility for static members, something JavaScript alone cannot do.

<h3 id= 'super-in-subclasses'>Super in Subclasses</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#oop)

When working with class inheritance, you will often see "super" in constructors. Again, it is a JavaScript feature, not specific to TypeScript alone. However, TypeScript offers you the added advantage of type safety.
`super` calls the constructor of the base/parent class. In the subclass, you must call `super` in the constructor, otherwise TypeScript will throw a bunch of errors:

> Constructors for derived classes must contain a 'super' call.

> 'super' must be called before accessing 'this' in the constructor of a derived class.

If the base class constructor takes parameters, you must pass them into `super`, otherwise you can just call `super()` with nothing.
Example:

```ts
class Person {
  constructor(public name: string) {}
}

class Employee extends Person {
  constructor(name: string) {
    super(name);
  }
}

const dev = new Employee("Debs");
console.log(dev.name); // Output: Debs
```

This is standard JavaScript class behaviour. TypeScript just enforces it with type safety and compile-time checks. Without TypeScript, if you don't call `super()`, you get a runtime error:

```bash
ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
```

And if you don't pass in "name" to `super`, you get "undefined" when you log `dev.name`:

```ts
class Employee extends Person {
  constructor(name: string) {
    super(undefined as any); // Or just super()
  }
}

const dev = new Employee("Debs");
console.log(dev.name); // Output: undefined
```

<h3 id= 'override-modifier'>Override Modifier</h3>

The `override` keyword in TypeScript is used in subclasses to redefine (override) a property or method in a base class. It is a TypeScript-only, compile-time check.

```ts
class Person {
  speak() {
    console.log("I am a person");
  }
}

class Employee extends Person {
  override speak() {
    console.log("I am an employee");
  }
}

const employee = new Employee();
employee.speak(); // Output: I am an employee
```

In plain JavaScript (without `override`), this is the default behaviour. When you define a method in a subclass with the same name as that on the parent class, it overrides (silently replaces) it.

But with `override`, you're telling the TypeScript compiler that you intend to override something that exists in the parent class. If the parent does not have that method or property, TypeScript will raise an error. This prevents bugs from typos and from changes in the base class:

```ts
class Employee extends Person {
  override spek() {
    console.log("I am an employee");
  }
}

// Error: This member cannot have an 'override' modifier because it is not declared in the base class 'Person'.Did you mean 'speak' ?
```

Without the `override` keyword, JavaScript (and TypeScript) will happily define an unintentional extra method when you think you are overriding.

The same protection applies if the method actually existed in the parent class but was later removed or renamed. `override` will flag your subclass implementation and force you to make a decision.
