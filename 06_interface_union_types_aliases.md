# Lesson 6: Interface, Union Types aur Type Aliases

## Interfaces in TypeScript

### Interface Kya Hai?
Interface ek tarah ka blueprint hota hai jo object ke structure ko define karta hai. Ye batata hai ke object mein kaun kaun si properties honi chahiye aur un properties ka type kya hona chahiye.

```typescript
// Basic interface
interface Person {
  name: string;
  age: number;
}

// Interface ka use
let user: Person = {
  name: "Ahmed",
  age: 25
};
```

### Optional Properties
Interface mein optional properties ko question mark `?` se define kiya jata hai:

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  description?: string; // Optional property
}

// Valid hai - description optional hai
const phone: Product = {
  id: 1,
  name: "Smartphone",
  price: 499.99
};
```

### Readonly Properties
Properties ko readonly bana sakte hain jisse unhe modify na kiya ja sake:

```typescript
interface User {
  readonly id: number;
  name: string;
  email: string;
}

const user: User = {
  id: 101,
  name: "Ali",
  email: "ali@example.com"
};

// user.id = 102; // Error: Cannot assign to 'id' because it is a read-only property
```

### Function Types in Interfaces
Interface mein methods ko define karna:

```typescript
interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
}

const basicCalc: Calculator = {
  add: (a, b) => a + b,
  subtract: (a, b) => a - b
};
```

### Extending Interfaces
Ek interface dusre se inherit kar sakti hai:

```typescript
interface Person {
  name: string;
  age: number;
}

interface Employee extends Person {
  employeeId: number;
  department: string;
}

const employee: Employee = {
  name: "Sara",
  age: 28,
  employeeId: 123,
  department: "IT"
};
```

### Interface vs Type Alias
Interface aur Type dono object types define karte hain, lekin kuch differences hain:

1. Interface sirf object structure define karta hai
2. Interfaces extend ho sakti hain
3. Interfaces merged ho sakti hain (declaration merging)

## Union Types

### Union Types Kya Hain?
Union types multiple types ko combine karte hain, jisse variable mein kisi bhi type ki value store ho sakti hai:

```typescript
// Union type: string ya number
let id: string | number;
id = 101;     // Valid hai
id = "ID101"; // Valid hai
// id = true; // Error: Type 'boolean' is not assignable
```

### Union with Different Types
Complex union types:

```typescript
// Function parameters as union types
function displayID(id: string | number) {
  console.log(`ID: ${id}`);
}

// Union of arrays
let data: number[] | string;
data = [1, 2, 3]; // Valid - number array
data = "hello";   // Valid - string
// data = [1, "two"]; // Error - Neither a string nor number array
```

### Type Narrowing with Union Types
Union types ke saath type narrowing zaruri hoti hai:

```typescript
function processValue(value: string | number) {
  // Type narrowing with typeof
  if (typeof value === "string") {
    // TypeScript knows value is a string here
    return value.toLowerCase();
  } else {
    // TypeScript knows value is a number here
    return value.toFixed(2);
  }
}
```

### Discriminated Unions
Objects mein union types ko manage karne ka ek pattern:

```typescript
interface Circle {
  kind: "circle"; // Literal type as discriminant
  radius: number;
}

interface Square {
  kind: "square"; // Literal type as discriminant
  side: number;
}

type Shape = Circle | Square;

function calculateArea(shape: Shape): number {
  // Using the discriminant property for type narrowing
  switch (shape.kind) {
    case "circle":
      // TypeScript knows shape is Circle here
      return Math.PI * shape.radius * shape.radius;
    case "square":
      // TypeScript knows shape is Square here
      return shape.side * shape.side;
  }
}
```

## Type Aliases

### Type Alias Kya Hai?
Type alias ek tarah ka shorthand ya nickname hota hai existing types ke liye:

```typescript
// Simple type alias
type UserID = string | number;

// Usage
let userId: UserID = 123;
userId = "user_123"; // Valid hai
```

### Complex Type Aliases
Type aliases complex structures ke liye bhi use kiye ja sakte hain:

```typescript
// Object type alias
type Person = {
  name: string;
  age: number;
};

// Function type alias
type GreetFunction = (name: string) => string;

// Function using the type alias
const sayHello: GreetFunction = (name) => `Hello, ${name}!`;
```

### Union and Intersection with Type Aliases
Type aliases complex type combinations ke liye perfect hain:

```typescript
// Union type alias
type Status = "pending" | "completed" | "failed";

// Intersection type alias (combining types)
type Employee = Person & { 
  employeeId: number; 
  department: string;
};

// Usage
let taskStatus: Status = "pending";
// taskStatus = "started"; // Error: Type '"started"' is not assignable

const employee: Employee = {
  name: "Usman",
  age: 30,
  employeeId: 123,
  department: "Engineering"
};
```

### Recursive Type Aliases
Type aliases recursive bhi ho sakte hain (self-referential):

```typescript
// Recursive type alias for tree structure
type TreeNode = {
  value: string;
  children?: TreeNode[];
};

// Usage
const tree: TreeNode = {
  value: "root",
  children: [
    { value: "child1" },
    { 
      value: "child2",
      children: [{ value: "grandchild" }]
    }
  ]
};
```

## Practical Example: Combining Interfaces, Unions, and Type Aliases

```typescript
// Various status types
type TaskStatus = "todo" | "in-progress" | "done" | "cancelled";

// Base interface
interface BaseTask {
  id: number;
  title: string;
  status: TaskStatus;
}

// Extended interfaces
interface RegularTask extends BaseTask {
  type: "regular";
  dueDate: Date;
}

interface RecurringTask extends BaseTask {
  type: "recurring";
  frequency: "daily" | "weekly" | "monthly";
}

// Union type of all task types
type Task = RegularTask | RecurringTask;

// Function using the types
function processTask(task: Task) {
  console.log(`Processing task: ${task.title}`);
  
  // Type narrowing using discriminant property
  if (task.type === "regular") {
    console.log(`Due on: ${task.dueDate.toDateString()}`);
  } else {
    console.log(`Repeats: ${task.frequency}`);
  }
}

// Usage
const dailyStandupTask: Task = {
  id: 1,
  title: "Daily Standup",
  status: "todo",
  type: "recurring",
  frequency: "daily"
};

processTask(dailyStandupTask);
```

## Key Differences: Interface vs Type Alias

| Feature | Interface | Type Alias |
|---------|-----------|------------|
| Declaration Merging | ✅ Supported | ❌ Not Supported |
| Extends/Implements | ✅ Can extend or be implemented | ✅ Can use intersection (&) |
| Primitive Types | ❌ Cannot represent primitives | ✅ Can represent any type |
| Union Types | ❌ Cannot represent unions directly | ✅ Can represent unions |
| Use Case | Perfect for object shapes and APIs | Flexible for any type structure |

## Next Steps
[Enums, Generics aur Type Narrowing](07_enums_generics_type_narrowing.md) ko padhein. 