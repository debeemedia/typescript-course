<h2 id= 'generics'>GENERICS</h2>

[Back to Index](../_sidebar.md)

In the section on [type aliases](#type-aliases), we demonstrated with an example how type aliases can help us reduce repetition in our code. Generics offer us that and even more. Generics allow reusability and flexibility when defining types, without sacrificing TypeScript's static typing.

<h3 id= 'generic-functions'>Generic Functions</h3>

```ts
function returnSomething<T>(param: T): T {
  return param;
}
```

The function above is a generic function that takes in a parameter of type `T` (this is purely convention; you can use any letter) and returns a value of the same type `T`. "T" is a type parameter that is a placeholder for any type such as string, number, boolean, object, array, unknown, any etc. That is what type parameters are: placeholders for the actual type that is passed in when the generic component is used. In our example, whatever argument type is passed into the function must be the return type of the function.

The function is called by passing in the type parameter `T` and ensuring that the argument is of type `T`. The function can be called in the following ways, amongst several others:

```ts
const returnedString = returnSomething<string>("Hi"); // `T` is a string
const returnedNumber = returnSomething<number>(1); // // `T` is a number
const returnedObject = returnSomething<object>({ name: "Debs" }); // `T` is an object
const returnedStringArray = returnSomething<string[]>(["Debs"]); // `T` is an array
```

If you pass in an argument with its type different from the type parameter, you will get an error:

```ts
const returnedString = returnSomething<number>("Hi");
// Error: Argument of type 'string' is not assignable to parameter of type 'number'.
```

<h3 id= 'generic-interfaces-and-type-aliases'>Generic Interfaces and Type Aliases</h3>

Generics can also be used with interfaces e.g.

```ts
interface Person<T, U> {
  name: T;
  age: U;
}
const p: Person<string, number> = {
  name: "Debs",
  age: 2,
};
```

The generic interface above takes in two type parameters, `T` and `U`.

Generics can be used with type aliases:

```ts
type Statuses<T> = T[];

const statusCodes: Statuses<number> = [200, 201, 400, 401, 500];

const statusMessages: Statuses<string> = [
  "OK",
  "Created",
  "Bad Request",
  "Unauthorized",
  "Internal Server Error",
];
```

> Sidenote:
> With generic interfaces and type aliases, TypeScript enforces that you provide the type parameter:

```ts
type Statuses<T> = T[];
const statusCodes: Statuses<> = [200, "Created", 400, 401, 500];

// Error: Generic type 'Statuses' requires 1 type argument(s).
```

> This makes sense because `Statuses<>` is an alias, and when you use it, you're telling TypeScript to resolve it immediately. If you omit the type, TypeScript doesn't know what to do.

> This is not the case with generic functions and classes. With a generic function for instance, TypeScript will try to infer the type as the type of the argument passed in:

```ts
function returnArray<T>(param: T): T[] {
  return [param];
}
returnArray("hi");
```

> If you hover over the function call, you will notice that TypeScript has inferred the type like so:

```ts
function returnArray<string>(param: string): string[];
```

<h3 id= 'generic-classes'>Generic Classes</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#generics)

Let's look at a practical example of generics with classes to demonstrate its utility. Suppose we have this class that does not employ the use of generics. The class is used to manage staff members:

```ts
type Employee = { id: number; name: string; role: string };

class Staff {
  private staff: Employee[] = [];

  getStaffMember(index: number): Employee {
    return this.staff[index];
  }

  getAllStaffMembers(): Employee[] {
    return this.staff;
  }

  addStaffMember(staffMember: Employee): void {
    this.staff.push(staffMember);
  }

  removeStaffMember(index: number): void {
    this.staff.splice(index, 1);
  }
}

const employees = new Staff();

employees.addStaffMember({ id: 1, name: "Debs", role: "Backend developer" });

console.log(employees.getAllStaffMembers());
// Output: [ { id: 1, name: 'Debs', role: 'Backend developer' } ]
```

The class works with an array of employees. But what if we also want to work with managers or interns. We'd have to duplicate the class and change the type from `Employee` to, say, `Manager`. This is not ideal. Let's see how we can use generics to solve it:

```ts
class Staff<T> {
  private staff: T[] = [];

  getStaffMember(index: number): T {
    return this.staff[index];
  }

  getAllStaffMembers(): T[] {
    return this.staff;
  }

  addStaffMember(staffMember: T): void {
    this.staff.push(staffMember);
  }

  removeStaffMember(index: number): void {
    this.staff.splice(index, 1);
  }
}
```

We have redefined the class to be a generic class that works with a type `T`. Now we can reuse the class across different types and not just for `Employee`, like so:

```ts
type Employee = { id: number; name: string; role: string };
type Manager = { id: number; name: string; mgtLevel: number };
type Intern = { name: string };

const employees = new Staff<Employee>();
employees.addStaffMember({ id: 1, name: "Debs", role: "Backend developer" });

const managers = new Staff<Manager>();
managers.addStaffMember({ id: 2, name: "deBee", mgtLevel: 14 });

const interns = new Staff<Intern>();
interns.addStaffMember({ name: "Deborah" });
```

When instantiating the class, a type parameter is passed in, and it is that type that the methods of the class require in their implementation.

<h3 id= 'generic-constraints-with-extends-keyword'>Generic Constraints with `extends` Keyword</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#generics)

Suppose we want to constrain the types that the generic class can use. Maybe a method in the class uses a particular property; if a type does not have that property, it could lead to runtime errors. To prevent that from happening, we restrict the allowed types with the `extends` keyword:

```ts
type Employee = { id: number; name: string; role: string };
type Manager = { id: number; name: string; mgtLevel: number };
type Intern = { name: string };

class Staff<T extends Employee | Manager> {
  private staff: T[] = [];

  printStaffMemberId(index: number): void {
    console.log(this.getStaffMember(index).id);
  }
}
```

The generic class is now restricted to the `Employee` and `Manager` types. This is so that the `printStaffMemberId` method can access the `id` property which is present in those types.

```ts
const employees = new Staff<Employee>();
employees.addStaffMember({ id: 1, name: "Debs", role: "Backend developer" });

console.log(employees.printStaffMemberId(0)); // Output: 1

const interns = new Staff<Intern>();
// Error: Type 'Intern' does not satisfy the constraint 'Employee | Manager'.
```

Here, `Intern` does not conform to the allowed types and so TypeScript raises an error. `Intern` does not have the `id` property used in the `printStaffMemberId` method so if we bypass TypeScript, we will get undefined at runtime.

<h3 id= 'generic-default-type-parameter'>Generic Default Type Parameter</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#generics)

Generics can have default types e.g.

```ts
class Staff<T extends Employee | Manager = Employee> {
  private staff: T[] = [];

  // ...

  addStaffMember(staffMember: T): void {
    this.staff.push(staffMember);
  }
}

const s = new Staff();

s.addStaffMember({ id: 1, name: "Debs", role: "Backend developer" });
```

In the example above, `Employee` is the default type. So when we instantiate the class without providing any type parameter, TypeScript expects an object of `Employee` type.

This allows you to avoid repeating the generic type in common scenarios, while still giving you the flexibility to override it when needed.

<h3 id= 'generics-with-keyof-operator'>Generics with `keyof` Operator</h3>

[Back to Index](../_sidebar.md) | [Back to Section](#generics)

Let's look at a common trick with generics, object types and `keyof`:

```ts
function returnName<T, K extends keyof T>(person: T, property: K): T[K] {
  return person[property];
}

type Person = {
  id: number;
  name: string;
};
const person: Person = { id: 1, name: "Debs" };

console.log(returnName(person, "name")); // Output: Debs
```

In the example above, `T` represents the object type while `K` represents a key (property). `K extends keyof T` ensures that the key is a valid property of (i.e. key of) the object type `T`. The function returns the value of the property.
