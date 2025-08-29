# Lesson 15: Advanced TypeScript Features

## Conditional Types

Conditional types TypeScript ki ek advanced feature hai jo types ko conditionally define karne ki capability provide karti hai, similar to how ternary operators work in JavaScript.

```typescript
// Basic conditional type
type IsString<T> = T extends string ? true : false;

// Usage
type Result1 = IsString<string>;  // type Result1 = true
type Result2 = IsString<number>;  // type Result2 = false
type Result3 = IsString<"hello">; // type Result3 = true (literal type is also a string)
```

### Nested Conditional Types

Conditional types ko nest bhi kiya ja sakta hai for more complex scenarios:

```typescript
type TypeName<T> = 
  T extends string ? "string" :
  T extends number ? "number" :
  T extends boolean ? "boolean" :
  T extends undefined ? "undefined" :
  T extends null ? "null" :
  T extends Function ? "function" :
  T extends any[] ? "array" :
  "object";

// Usage
type T1 = TypeName<string>;          // "string"
type T2 = TypeName<number[]>;        // "array"
type T3 = TypeName<() => void>;      // "function"
type T4 = TypeName<{name: string}>;  // "object"
```

## Mapped Types

Mapped types existing type ko transform karke new type create karne ki capability provide karte hain. Ye specially useful hote hain jab aap existing type se derived type banana chahte hain.

```typescript
// Basic mapped type
type Optional<T> = {
  [K in keyof T]?: T[K]
};

interface User {
  id: number;
  name: string;
  email: string;
}

// Create new type with all properties optional
type OptionalUser = Optional<User>;
// Equivalent to:
// {
//   id?: number;
//   name?: string;
//   email?: string;
// }

// Another example: making all properties readonly
type Readonly<T> = {
  readonly [K in keyof T]: T[K]
};

type ReadonlyUser = Readonly<User>;
// Equivalent to:
// {
//   readonly id: number;
//   readonly name: string;
//   readonly email: string;
// }
```

### Modifier Flags in Mapped Types

Mapped types mein property modifiers (+/- for readonly and ?) ko add or remove karne ke liye flags use kar sakte hain:

```typescript
// Remove readonly modifier
type Mutable<T> = {
  -readonly [K in keyof T]: T[K]
};

// Remove optional modifier (make all properties required)
type Required<T> = {
  [K in keyof T]-?: T[K]
};

// Example
interface Config {
  readonly endpoint: string;
  readonly timeout?: number;
}

type MutableConfig = Mutable<Config>;
// Equivalent to:
// {
//   endpoint: string;
//   timeout?: number;
// }

type RequiredConfig = Required<Config>;
// Equivalent to:
// {
//   readonly endpoint: string;
//   readonly timeout: number;
// }
```

## Template Literal Types with Mapping

Template literal types ko mapped types ke saath combine kar sakte hain for powerful transformations:

```typescript
// Create getters for object properties
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};

interface Person {
  name: string;
  age: number;
}

type PersonGetters = Getters<Person>;
// Equivalent to:
// {
//   getName: () => string;
//   getAge: () => number;
// }

// Another example: Create boolean flags from union type
type Flags<T extends string> = {
  [K in T as `is${Capitalize<K>}`]: boolean
};

type Features = "dark" | "mobile" | "accessible";
type FeatureFlags = Flags<Features>;
// Equivalent to:
// {
//   isDark: boolean;
//   isMobile: boolean;
//   isAccessible: boolean;
// }
```

## Recursive Types

TypeScript mein recursive types allow karte hain ki aap self-referential structures jaise tree nodes ya nested comments ko represent kar sakein:

```typescript
// Recursive type for tree structure
type TreeNode<T> = {
  value: T;
  children?: TreeNode<T>[];
};

// Usage
const tree: TreeNode<string> = {
  value: "root",
  children: [
    { value: "child1" },
    { 
      value: "child2",
      children: [
        { value: "grandchild1" },
        { value: "grandchild2" }
      ]
    }
  ]
};

// Recursive type for JSON values
type JSONValue = 
  | string
  | number
  | boolean
  | null
  | JSONValue[]
  | { [key: string]: JSONValue };

// Valid JSON object
const json: JSONValue = {
  name: "Product",
  price: 29.99,
  inStock: true,
  tags: ["electronics", "gadget"],
  details: {
    manufacturer: "TechCorp",
    specifications: null
  }
};
```

## Generic Type Constraints with keyof

`keyof` operator TypeScript mein type ke all property names ko union type ke form mein extract karta hai. Is ko generic constraints ke saath use karke type-safe operations perform kar sakte hain:

```typescript
// Basic keyof example
interface User {
  id: number;
  name: string;
  email: string;
}

type UserKeys = keyof User; // "id" | "name" | "email"

// Generic function with keyof constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = {
  id: 123,
  name: "Ali",
  email: "ali@example.com"
};

// Type-safe property access
const userName = getProperty(user, "name");  // Returns string
const userId = getProperty(user, "id");      // Returns number
// const invalid = getProperty(user, "age");  // Error: "age" is not a key of User
```

## Discriminated Union Types with Type Predicates

Discriminated unions ke saath type predicates ko use karke powerful type-narrowing achieve kar sakte hain:

```typescript
// Discriminated union types
interface Square {
  kind: "square";
  size: number;
}

interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
}

interface Circle {
  kind: "circle";
  radius: number;
}

type Shape = Square | Rectangle | Circle;

// Type predicate functions
function isSquare(shape: Shape): shape is Square {
  return shape.kind === "square";
}

function isRectangle(shape: Shape): shape is Rectangle {
  return shape.kind === "rectangle";
}

function isCircle(shape: Shape): shape is Circle {
  return shape.kind === "circle";
}

// Using type predicates for narrowing
function calculateArea(shape: Shape): number {
  if (isSquare(shape)) {
    // TypeScript knows shape is Square here
    return shape.size * shape.size;
  } else if (isRectangle(shape)) {
    // TypeScript knows shape is Rectangle here
    return shape.width * shape.height;
  } else if (isCircle(shape)) {
    // TypeScript knows shape is Circle here
    return Math.PI * shape.radius * shape.radius;
  }
  
  // This will never execute if all shapes are handled above
  // Using never type for exhaustiveness check
  const _exhaustiveCheck: never = shape;
  return 0;
}

// Using the function
const shapes: Shape[] = [
  { kind: "square", size: 5 },
  { kind: "rectangle", width: 4, height: 6 },
  { kind: "circle", radius: 3 }
];

shapes.forEach(shape => {
  console.log(`Area of ${shape.kind}: ${calculateArea(shape)}`);
});
```

## infer Keyword in Conditional Types

TypeScript mein `infer` keyword conditional types ke andar type variables ko introduce aur extract karne ke liye use hota hai:

```typescript
// Extract the return type of a function type
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// Usage
function getMessage() {
  return "Hello, TypeScript!";
}

type Message = ReturnType<typeof getMessage>; // string

// Extract array element type
type ArrayElementType<T> = T extends Array<infer E> ? E : never;

type NumberArray = number[];
type NumberType = ArrayElementType<NumberArray>; // number

// Extract Promise type
type UnwrapPromise<T> = T extends Promise<infer U> ? U : T;

type PromiseString = Promise<string>;
type ResolvedType = UnwrapPromise<PromiseString>; // string
```

## Intersection Types aur Type Merging

Intersection types (`&`) multiple types ko combine karke ek new type banate hain jisme sab involved types ki properties hoti hain:

```typescript
interface Person {
  name: string;
  age: number;
}

interface Employee {
  employeeId: string;
  department: string;
}

// Intersection type combines both interfaces
type EmployeePerson = Person & Employee;

const worker: EmployeePerson = {
  name: "Sara",
  age: 28,
  employeeId: "E123",
  department: "Engineering"
};

// Complex example with discriminated unions
type Success<T> = {
  status: "success";
  data: T;
};

type Error = {
  status: "error";
  error: {
    message: string;
    code: number;
  };
};

type Response<T> = Success<T> | Error;

// Add metadata to all responses
type MetaData = {
  timestamp: number;
  requestId: string;
};

// Combine with intersection type
type ResponseWithMetadata<T> = Response<T> & MetaData;

// Example usage
const successResponse: ResponseWithMetadata<string[]> = {
  status: "success",
  data: ["item1", "item2"],
  timestamp: Date.now(),
  requestId: "req-123"
};

const errorResponse: ResponseWithMetadata<never> = {
  status: "error",
  error: {
    message: "Not found",
    code: 404
  },
  timestamp: Date.now(),
  requestId: "req-456"
};
```

## Branded/Nominal Types

TypeScript structurally typed hai (duck typing), lekin kabhi kabhi aapko nominal typing ki zarurat ho sakti hai. Branded types (phantom types bhi kehte hain) ek technique hai jisse aap nominal typing emulate kar sakte hain:

```typescript
// Create branded types for primitive values
type UserId = number & { readonly __brand: unique symbol };
type OrderId = number & { readonly __brand: unique symbol };

// Type-safe creation function
function createUserId(id: number): UserId {
  return id as UserId;
}

function createOrderId(id: number): OrderId {
  return id as OrderId;
}

// Functions that accept only specific branded types
function processUser(userId: UserId) {
  console.log(`Processing user ${userId}`);
}

function processOrder(orderId: OrderId) {
  console.log(`Processing order ${orderId}`);
}

// Usage
const userId = createUserId(123);
const orderId = createOrderId(456);

processUser(userId);
// processUser(orderId); // Error: OrderId is not assignable to UserId
// processUser(123);     // Error: number is not assignable to UserId

processOrder(orderId);
// processOrder(userId); // Error: UserId is not assignable to OrderId
```

## Polymorphic this Type

Class methods mein `this` type method ko flexible banata hai, jisse derived classes mein proper typing maintain hoti hai:

```typescript
class BasicCalculator {
  public value: number;

  constructor(value: number = 0) {
    this.value = value;
  }

  // Methods return 'this' type
  add(operand: number): this {
    this.value += operand;
    return this;
  }

  multiply(operand: number): this {
    this.value *= operand;
    return this;
  }

  // Other methods
  current(): number {
    return this.value;
  }
}

// Extended calculator with more operations
class ScientificCalculator extends BasicCalculator {
  // Add more methods
  sin(): this {
    this.value = Math.sin(this.value);
    return this;
  }

  cos(): this {
    this.value = Math.cos(this.value);
    return this;
  }
}

// Method chaining works properly with types
const calc = new ScientificCalculator(2)
  .multiply(5)      // Returns ScientificCalculator
  .sin()            // Works because multiply returns ScientificCalculator instance
  .add(1)
  .current();       // Returns number: 1.909297...

console.log(calc);
```

## Index Types aur Mapped Types ka Advanced Use

Index types aur mapped types ko combine karke complex type transformations achieve kar sakte hain:

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  category: string;
  stock: number;
}

// Pick certain keys based on their value type
type PickByValueType<T, ValueType> = {
  [K in keyof T as T[K] extends ValueType ? K : never]: T[K]
};

// Usage
type NumericProductProps = PickByValueType<Product, number>;
// Equivalent to: { id: number; price: number; stock: number; }

// Omit certain keys based on their value type
type OmitByValueType<T, ValueType> = {
  [K in keyof T as T[K] extends ValueType ? never : K]: T[K]
};

// Usage
type NonNumericProductProps = OmitByValueType<Product, number>;
// Equivalent to: { name: string; category: string; }

// Transform object structure
type Flattened<T> = {
  [K in keyof T as `${string & K}`]: T[K] extends object ? Flattened<T[K]> : T[K]
};

// Convert object to record with path keys
type NestedPaths<T, Prefix extends string = ""> = {
  [K in keyof T]: T[K] extends object
    ? NestedPaths<T[K], `${Prefix}${string & K}.`>
    : `${Prefix}${string & K}`
}[keyof T];

// Example
interface Person {
  name: string;
  address: {
    street: string;
    city: string;
    location: {
      lat: number;
      lng: number;
    }
  }
}

type PersonPaths = NestedPaths<Person>;
// "name" | "address.street" | "address.city" | "address.location.lat" | "address.location.lng"
```

## Decorators

TypeScript mein decorators ek experimental feature hai jo classes, methods, properties aur parameters ko annotate ya modify karne ki capability provide karta hai. Angular, NestJS aur TypeORM jaise frameworks mein decorators bahut use hote hain:

```typescript
// Method decorator
function log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;

  descriptor.value = function(...args: any[]) {
    console.log(`Calling ${propertyKey} with arguments: ${JSON.stringify(args)}`);
    const result = originalMethod.apply(this, args);
    console.log(`Method ${propertyKey} returned: ${JSON.stringify(result)}`);
    return result;
  };

  return descriptor;
}

// Property decorator
function required(target: any, propertyKey: string) {
  let value: any;
  
  const getter = function() {
    return value;
  };
  
  const setter = function(newVal: any) {
    if (newVal === undefined || newVal === null) {
      throw new Error(`Property ${propertyKey} is required`);
    }
    value = newVal;
  };
  
  Object.defineProperty(target, propertyKey, {
    get: getter,
    set: setter
  });
}

// Class using decorators
class UserService {
  @required
  private name: string;
  
  constructor(name: string) {
    this.name = name;
  }
  
  @log
  getUser(id: number) {
    return { id, name: this.name };
  }
}

// Usage
const userService = new UserService("Test User");
userService.getUser(123);
// Console output:
// Calling getUser with arguments: [123]
// Method getUser returned: {"id":123,"name":"Test User"}
```

## tsconfig.json Advanced Options

`tsconfig.json` file mein advanced compiler options TypeScript code ki stricter type checking aur better error detection provide karti hain:

```json
{
  "compilerOptions": {
    // Strict type checking options
    "strict": true,                           
    "noImplicitAny": true,                    
    "strictNullChecks": true,                
    "strictFunctionTypes": true,             
    "strictBindCallApply": true,            
    "strictPropertyInitialization": true,    
    "noImplicitThis": true,                  
    "alwaysStrict": true,                    
    
    // Additional checks
    "noUnusedLocals": true,                  
    "noUnusedParameters": true,              
    "noImplicitReturns": true,               
    "noFallthroughCasesInSwitch": true,      
    "noUncheckedIndexedAccess": true,        
    
    // Module resolution
    "baseUrl": "./",                         
    "paths": {
      "@app/*": ["src/app/*"],              
      "@shared/*": ["src/shared/*"]         
    },
    
    // Advanced output options
    "declaration": true,                     
    "declarationMap": true,                  
    "sourceMap": true,                       
    "removeComments": false,                 
    "outDir": "./dist",                      
    
    // Library options
    "lib": ["es2020", "dom"],               
    "target": "es2019",                      
    "module": "esnext",                      
    "moduleResolution": "node",              
    
    // TypeScript features
    "experimentalDecorators": true,          
    "emitDecoratorMetadata": true           
  },
  "include": ["src/**/*.ts", "src/**/*.tsx"],
  "exclude": ["node_modules", "**/*.spec.ts"]
}
```

## Type Assertions with unknown Type

`unknown` is the type-safe alternative to `any`. Jab aapko type initially pata nahi hai lekin baad mein type assertion ya type narrowing karke access karna hai, tab `unknown` use karein:

```typescript
// Parse JSON with type safety
function parseJSON<T>(json: string): T {
  // Parse returns any, but we cast to unknown first for safety
  const parsed: unknown = JSON.parse(json);
  return parsed as T;
}

interface User {
  id: number;
  name: string;
  isActive: boolean;
}

// Using type parameter for proper typing
const user = parseJSON<User>('{"id": 1, "name": "Ahmed", "isActive": true}');
console.log(user.name); // "Ahmed"

// Type guard function for runtime type validation
function isUser(value: unknown): value is User {
  if (typeof value !== 'object' || value === null) return false;
  
  const obj = value as any;
  return (
    typeof obj.id === 'number' &&
    typeof obj.name === 'string' &&
    typeof obj.isActive === 'boolean'
  );
}

function processUserData(data: unknown) {
  if (isUser(data)) {
    // TypeScript knows data is User here
    console.log(`User ${data.name} with ID ${data.id} is ${data.isActive ? 'active' : 'inactive'}`);
  } else {
    console.log('Invalid user data');
  }
}
```

## Type-Level Programming with TypeScript

TypeScript mein type-level programming complex type transformations perform karne ki capability provide karta hai:

```typescript
// Tuple to Union conversion
type TupleToUnion<T extends any[]> = T[number];

type Colors = ["red", "green", "blue"];
type Color = TupleToUnion<Colors>; // "red" | "green" | "blue"

// Union to Intersection conversion
type UnionToIntersection<U> = 
  (U extends any ? (k: U) => void : never) extends ((k: infer I) => void) ? I : never;

type Union = { a: string } | { b: number } | { c: boolean };
type Intersection = UnionToIntersection<Union>;
// { a: string } & { b: number } & { c: boolean }

// String manipulation types
type RemoveSpaces<S extends string> = 
  S extends `${infer L} ${infer R}` ? `${L}${RemoveSpaces<R>}` : S;

type Greeting = "Hello   TypeScript   World";
type CompactGreeting = RemoveSpaces<Greeting>; // "HelloTypeScriptWorld"

// Recursive type transformations
type DeepReadonly<T> = {
  readonly [K in keyof T]: T[K] extends object 
    ? T[K] extends Function 
      ? T[K] 
      : DeepReadonly<T[K]> 
    : T[K]
};

interface NestedObject {
  name: string;
  settings: {
    theme: string;
    notifications: {
      email: boolean;
      sms: boolean;
    }
  }
}

// Makes everything readonly, including nested objects
type ReadonlyNestedObject = DeepReadonly<NestedObject>;
```

## Practical Tips for Advanced TypeScript

1. **Type-Safe Event Emitters**: Custom event system with proper type checking:

```typescript
type EventMap = {
  'user:login': { userId: string; timestamp: number };
  'user:logout': { userId: string; timestamp: number };
  'notification:new': { message: string; type: 'info' | 'error' | 'success' };
};

class TypedEventEmitter<Events extends Record<string, any>> {
  private listeners: {
    [E in keyof Events]?: Array<(data: Events[E]) => void>
  } = {};

  on<E extends keyof Events>(event: E, listener: (data: Events[E]) => void) {
    if (!this.listeners[event]) {
      this.listeners[event] = [];
    }
    this.listeners[event]!.push(listener);
    return this;
  }

  emit<E extends keyof Events>(event: E, data: Events[E]) {
    if (!this.listeners[event]) return false;
    this.listeners[event]!.forEach(listener => listener(data));
    return true;
  }

  off<E extends keyof Events>(event: E, listener: (data: Events[E]) => void) {
    if (!this.listeners[event]) return this;
    this.listeners[event] = this.listeners[event]!.filter(l => l !== listener);
    return this;
  }
}

// Usage
const emitter = new TypedEventEmitter<EventMap>();

emitter.on('user:login', ({ userId, timestamp }) => {
  console.log(`User ${userId} logged in at ${new Date(timestamp).toISOString()}`);
});

// Type-safe event emission
emitter.emit('user:login', { 
  userId: 'user123', 
  timestamp: Date.now() 
});

// Type error: 
// emitter.emit('user:login', { userId: 'user123' }); // Missing timestamp
// emitter.emit('user:logout', { userId: 'user123', extraProp: true }); // Extra property
```

2. **Immutable Data Structures**: TypeScript ke saath immutable data structures ka implementation:

```typescript
type Immutable<T> = 
  T extends Function | Date | RegExp ? T :
  T extends Array<infer U> ? ReadonlyArray<Immutable<U>> :
  T extends Map<infer K, infer V> ? ReadonlyMap<Immutable<K>, Immutable<V>> :
  T extends Set<infer U> ? ReadonlySet<Immutable<U>> :
  T extends object ? { readonly [K in keyof T]: Immutable<T[K]> } :
  T;

interface Todo {
  id: number;
  title: string;
  completed: boolean;
  tags: string[];
  author: {
    id: number;
    name: string;
  };
}

// Create an immutable version of the Todo type
type ImmutableTodo = Immutable<Todo>;

function createTodo(data: Todo): ImmutableTodo {
  // Cast to immutable type after creation
  return data as ImmutableTodo;
}

const todo = createTodo({
  id: 1,
  title: "Learn TypeScript",
  completed: false,
  tags: ["typescript", "programming"],
  author: {
    id: 123,
    name: "Ahmed"
  }
});

// Type errors:
// todo.completed = true;           // Error: Cannot assign to 'completed' because it is a read-only property
// todo.tags.push("advanced");      // Error: Property 'push' does not exist on type 'readonly string[]'
// todo.author.name = "New Name";   // Error: Cannot assign to 'name' because it is a read-only property
```

## Next Steps
[Advanced TypeScript Patterns & Error Handling](16_advanced_patterns_error_handling.md) ko padhein. 