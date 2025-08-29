# Lesson 7: Enums, Generics aur Type Narrowing

## Enums in TypeScript

### Enum Kya Hai?
Enum (Enumeration) ek special type hota hai jo related constants ko ek group mein define karne ki permission deta hai. Ye code ko readable aur maintainable banate hain.

```typescript
// Basic enum
enum Direction {
  Up,
  Down,
  Left,
  Right
}

// Usage
let move: Direction = Direction.Left;
console.log(move); // Output: 2 (by default, values start from 0)
```

### Numeric Enums
By default, enum values 0 se start hote hain, lekin custom values bhi de sakte hain:

```typescript
enum StatusCode {
  OK = 200,
  NotFound = 404,
  InternalServerError = 500
}

const status: StatusCode = StatusCode.NotFound;
console.log(status); // Output: 404
```

### String Enums
String values bhi enums mein use kar sakte hain:

```typescript
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT"
}

console.log(Direction.Up); // Output: "UP"
```

### Heterogeneous Enums
Mixed types (strings and numbers) bhi use kar sakte hain:

```typescript
enum BooleanLikeHeterogeneousEnum {
  No = 0,
  Yes = "YES",
}
```

### Reverse Mapping
Numeric enums mein value se name bhi access kar sakte hain:

```typescript
enum Direction {
  Up,
  Down,
  Left,
  Right
}

let dirName: string = Direction[2]; // "Left"
console.log(dirName);
```

### Const Enums
Performance optimize karne ke liye `const enum` use kar sakte hain:

```typescript
const enum HttpStatus {
  OK = 200,
  NotFound = 404,
  InternalServerError = 500
}

// Usage 
const status = HttpStatus.OK;
// Compiles to: const status = 200; (direct value)
```

## Generics in TypeScript

### Generics Kya Hain?
Generics ek tarah ke templates hote hain jo different types ke saath kaam kar sakte hain while maintaining type safety. Ye code ko reusable banate hain.

```typescript
// Generic function
function identity<T>(arg: T): T {
  return arg;
}

// Usage
let output1 = identity<string>("Hello"); // type: string
let output2 = identity<number>(100);    // type: number
```

### Generic Function Examples
Generics ki power ko samjhne ke liye aur examples:

```typescript
// Generic function that works with any array type
function getFirstElement<T>(arr: T[]): T | undefined {
  return arr.length > 0 ? arr[0] : undefined;
}

// Usage
const first1 = getFirstElement<number>([1, 2, 3]);          // type: number | undefined
const first2 = getFirstElement<string>(["apple", "banana"]); // type: string | undefined
```

### Multiple Type Parameters
Ek se zyada type parameters bhi use kar sakte hain:

```typescript
// Function with multiple type parameters
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

// Usage
const result = pair<string, number>("hello", 42); // type: [string, number]
```

### Generic Constraints
Type parameters ko constraint (limit) kar sakte hain:

```typescript
// Interface for constraint
interface HasLength {
  length: number;
}

// Generic function with constraint
function getLength<T extends HasLength>(arg: T): number {
  return arg.length; // Safe because we know T has a length property
}

// Usage
getLength("Hello");          // Works - string has length
getLength([1, 2, 3]);        // Works - array has length
// getLength(123);           // Error - number doesn't have length
```

### Generic Classes
Classes bhi generic ho sakti hain:

```typescript
// Generic class
class Box<T> {
  private value: T;

  constructor(value: T) {
    this.value = value;
  }

  getValue(): T {
    return this.value;
  }
}

// Usage
const stringBox = new Box<string>("Hello TypeScript");
const numberBox = new Box<number>(42);

console.log(stringBox.getValue()); // "Hello TypeScript"
```

### Generic Interfaces
Interfaces bhi generic ho sakti hain:

```typescript
// Generic interface
interface Pair<T, U> {
  first: T;
  second: U;
}

// Usage
const pair: Pair<string, number> = {
  first: "Hello",
  second: 42
};
```

### Default Type Parameters
Generic type parameters ko default values bhi de sakte hain:

```typescript
// Default type parameter
interface ApiResponse<T = any> {
  data: T;
  status: number;
  message: string;
}

// With explicit type
const userResponse: ApiResponse<User> = {
  data: { id: 1, name: "Ahmed" },
  status: 200,
  message: "Success"
};

// With default type
const generalResponse: ApiResponse = {
  data: "Some data",
  status: 200,
  message: "Success"
};
```

## Type Narrowing

### Type Narrowing Kya Hai?
Type narrowing TypeScript ko ye batane ka process hai ke kisi variable ka type kya hai, especially jab wo union type ho.

```typescript
// Without type narrowing
function process(value: string | number) {
  // Error: Property 'toUpperCase' does not exist on type 'string | number'
  // value.toUpperCase(); 
}

// With type narrowing
function processCorrect(value: string | number) {
  if (typeof value === "string") {
    // TypeScript now knows value is a string
    return value.toUpperCase();
  } else {
    // TypeScript now knows value is a number
    return value.toFixed(2);
  }
}
```

### Type Guards
Type narrowing ke liye different type guards (checks) use kar sakte hain:

#### 1. typeof Type Guard
Primitive types ke liye:

```typescript
function printValue(value: string | number | boolean) {
  if (typeof value === "string") {
    console.log("String:", value.toUpperCase());
  } else if (typeof value === "number") {
    console.log("Number:", value.toFixed(2));
  } else {
    console.log("Boolean:", value);
  }
}
```

#### 2. instanceof Type Guard
Classes ke liye:

```typescript
class Dog {
  bark() { return "Woof!"; }
}

class Cat {
  meow() { return "Meow!"; }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    // TypeScript knows animal is Dog
    return animal.bark();
  } else {
    // TypeScript knows animal is Cat
    return animal.meow();
  }
}
```

#### 3. in Operator Type Guard
Properties check karne ke liye:

```typescript
interface Fish {
  swim(): void;
}

interface Bird {
  fly(): void;
}

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    // TypeScript knows animal is Fish
    return animal.swim();
  } else {
    // TypeScript knows animal is Bird
    return animal.fly();
  }
}
```

#### 4. Custom Type Guards
Custom functions bhi bana sakte hain:

```typescript
// Custom type guard function
function isFish(animal: Fish | Bird): animal is Fish {
  return (animal as Fish).swim !== undefined;
}

function move(animal: Fish | Bird) {
  if (isFish(animal)) {
    // TypeScript knows animal is Fish
    return animal.swim();
  } else {
    // TypeScript knows animal is Bird
    return animal.fly();
  }
}
```

### Discriminated Unions
Type narrowing ke liye ek common pattern:

```typescript
interface Circle {
  kind: "circle";
  radius: number;
}

interface Square {
  kind: "square";
  sideLength: number;
}

type Shape = Circle | Square;

function calculateArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      // TypeScript knows shape is Circle
      return Math.PI * shape.radius ** 2;
    case "square":
      // TypeScript knows shape is Square
      return shape.sideLength ** 2;
  }
}
```

## Practical Examples

### Example 1: Generic Repository
```typescript
// Entity interface with an ID field
interface Entity {
  id: number | string;
}

// Generic repository for any entity type
class Repository<T extends Entity> {
  private items: T[] = [];

  add(item: T): void {
    this.items.push(item);
  }

  get(id: string | number): T | undefined {
    return this.items.find(item => item.id === id);
  }

  getAll(): T[] {
    return this.items;
  }

  delete(id: string | number): void {
    const index = this.items.findIndex(item => item.id === id);
    if (index !== -1) {
      this.items.splice(index, 1);
    }
  }
}

// Usage
interface User extends Entity {
  id: number;
  name: string;
  email: string;
}

const userRepository = new Repository<User>();
userRepository.add({ id: 1, name: "Ahmed", email: "ahmed@example.com" });
const user = userRepository.get(1);
```

### Example 2: State Handling with Enums and Type Narrowing
```typescript
enum RequestState {
  Idle = "idle",
  Loading = "loading",
  Success = "success",
  Error = "error"
}

interface RequestIdle {
  state: RequestState.Idle;
}

interface RequestLoading {
  state: RequestState.Loading;
}

interface RequestSuccess<T> {
  state: RequestState.Success;
  data: T;
}

interface RequestError {
  state: RequestState.Error;
  error: string;
}

type Request<T> = 
  | RequestIdle
  | RequestLoading
  | RequestSuccess<T>
  | RequestError;

// Function to handle the request state
function handleRequest<T>(request: Request<T>) {
  switch (request.state) {
    case RequestState.Idle:
      return "Request not started";
    case RequestState.Loading:
      return "Loading...";
    case RequestState.Success:
      // TypeScript knows request has data property
      return `Success! Data: ${JSON.stringify(request.data)}`;
    case RequestState.Error:
      // TypeScript knows request has error property
      return `Error: ${request.error}`;
  }
}

// Usage
type User = { id: number; name: string };
const successRequest: Request<User> = {
  state: RequestState.Success,
  data: { id: 1, name: "Ali" }
};

console.log(handleRequest(successRequest)); // Success! Data: {"id":1,"name":"Ali"}
```

## Next Steps
[Type Assertions, Special Types & Compilation](08_type_assertions_compilation.md) ko padhein. 