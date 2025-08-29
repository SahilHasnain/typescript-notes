# Lesson 5: Functions, Arrays aur Objects TypeScript mein

## Functions in TypeScript

### Basic Function Typing
TypeScript mein functions ke parameters aur return values ko type assign kiya jata hai:

```typescript
// Function with typed parameters and return type
function add(a: number, b: number): number {
  return a + b;
}
```

### Function Expressions
Function expressions bhi same tarike se type kiye jate hain:

```typescript
// Function expression with types
const multiply = function(x: number, y: number): number {
  return x * y;
};

// Arrow function with types
const divide = (a: number, b: number): number => a / b;
```

### Optional Parameters
Question mark `?` se parameter ko optional bana sakte hain:

```typescript
function greet(name: string, title?: string): string {
  if (title) {
    return `Hello ${title} ${name}`;
  }
  return `Hello ${name}`;
}

// Dono tarike se call kar sakte hain
greet("Ahmed");        // "Hello Ahmed"
greet("Ahmed", "Mr."); // "Hello Mr. Ahmed"
```

### Default Parameters
Default values bhi assign kar sakte hain:

```typescript
function greetWithDefault(name: string, greeting: string = "Hello"): string {
  return `${greeting}, ${name}!`;
}

greetWithDefault("Usman");             // "Hello, Usman!"
greetWithDefault("Sara", "Welcome");   // "Welcome, Sara!"
```

### Rest Parameters
Rest parameters se variable number of arguments accept kar sakte hain:

```typescript
function sum(...numbers: number[]): number {
  return numbers.reduce((total, num) => total + num, 0);
}

sum(1, 2);          // 3
sum(1, 2, 3, 4, 5); // 15
```

### Function Overloads
Multiple function signatures define kar sakte hain:

```typescript
// Overload signatures
function getInfo(id: number): { id: number, name: string };
function getInfo(name: string): { id: number, name: string };

// Implementation
function getInfo(idOrName: number | string): { id: number, name: string } {
  if (typeof idOrName === "number") {
    return { id: idOrName, name: "Default" };
  } else {
    return { id: 0, name: idOrName };
  }
}
```

## Arrays in TypeScript

### Array Types
TypeScript mein arrays ke type do tarike se define kiye jate hain:

```typescript
// Method 1: Type followed by []
let numbers: number[] = [1, 2, 3, 4, 5];
let names: string[] = ["Ali", "Sara", "Usman"];

// Method 2: Generic Array<Type>
let numbers: Array<number> = [1, 2, 3, 4, 5];
let names: Array<string> = ["Ali", "Sara", "Usman"];
```

### Multi-type Arrays (Union Type)
Union type se multiple types ke elements rakh sakte hain:

```typescript
// Array can contain numbers or strings
let mixed: (number | string)[] = [1, "two", 3, "four"];
```

### Readonly Arrays
Arrays ko immutable banane ke liye readonly keyword:

```typescript
// Readonly array - can't be modified after creation
const fixedNumbers: readonly number[] = [1, 2, 3];
// fixedNumbers.push(4); // Error: Property 'push' does not exist on type 'readonly number[]'
```

### Array Methods with TypeScript
TypeScript array methods ke saath type safety provide karta hai:

```typescript
const numbers: number[] = [1, 2, 3, 4, 5];

// TypeScript knows map returns a new array of the same type
const doubled: number[] = numbers.map(n => n * 2);

// TypeScript knows filter maintains the element type
const evenNumbers: number[] = numbers.filter(n => n % 2 === 0);

// TypeScript infers correct return type
const sum: number = numbers.reduce((total, n) => total + n, 0);
```

## Objects in TypeScript

### Basic Object Types
Objects ke liye types inline define kar sakte hain:

```typescript
// Inline object type
let person: { name: string; age: number } = {
  name: "Ali",
  age: 30
};
```

### Optional Properties
Objects mein bhi optional properties use kar sakte hain:

```typescript
// Object with optional property
let user: { 
  id: number; 
  name: string; 
  email?: string; // Optional property
} = {
  id: 1,
  name: "Ahmed"
  // email is optional, so it can be omitted
};
```

### Index Signatures
Jab object keys dynamic hon:

```typescript
// Object with index signature
let scores: { [key: string]: number } = {
  math: 95,
  science: 90,
  history: 85
};

// Can add properties dynamically
scores.english = 88;
```

### Nested Objects
Complex nested objects bhi TypeScript mein type kar sakte hain:

```typescript
// Nested object types
let employee: {
  id: number;
  name: string;
  contact: {
    email: string;
    phone?: string;
    address: {
      city: string;
      country: string;
    }
  }
} = {
  id: 1,
  name: "Sara",
  contact: {
    email: "sara@example.com",
    address: {
      city: "Karachi",
      country: "Pakistan"
    }
  }
};
```

## Practice Exercise

```typescript
// Function to calculate area of different shapes
function calculateArea(shape: "circle", radius: number): number;
function calculateArea(shape: "rectangle", width: number, height: number): number;
function calculateArea(shape: "square", side: number): number;
function calculateArea(shape: string, ...args: number[]): number {
  switch(shape) {
    case "circle":
      return Math.PI * args[0] * args[0];
    case "rectangle":
      return args[0] * args[1];
    case "square":
      return args[0] * args[0];
    default:
      throw new Error("Unsupported shape");
  }
}

// Examples of use:
const circleArea = calculateArea("circle", 5);
const rectangleArea = calculateArea("rectangle", 4, 6);
const squareArea = calculateArea("square", 4);
```

## Next Steps
[Interface, Union Types aur Type Aliases](06_interface_union_types_aliases.md) ko padhein. 