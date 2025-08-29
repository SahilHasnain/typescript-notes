# Lesson 4: TypeScript Mein Variables

## Overview
Is lesson mein hum dekhenge ke TypeScript mein different types ke variables kaise declare kiye jate hain.

## Basic Data Types

### 1. Primitive Types
TypeScript mein basic primitive types ye hain:

```typescript
// Number
let age: number = 25;
let price: number = 99.99;
let negative: number = -10;

// String
let name: string = "Ahmed";
let greeting: string = 'Hello!';
let template: string = `My name is ${name}`;

// Boolean
let isStudent: boolean = true;
let isCompleted: boolean = false;

// null and undefined
let empty: null = null;
let notDefined: undefined = undefined;
```

### 2. Arrays
Arrays ko TypeScript mein do tarike se declare kiya ja sakta hai:

```typescript
// Method 1: Using square brackets
let numbers: number[] = [1, 2, 3, 4, 5];
let names: string[] = ["Ali", "Sara", "Usman"];

// Method 2: Using generic Array type
let numbers: Array<number> = [1, 2, 3, 4, 5];
let names: Array<string> = ["Ali", "Sara", "Usman"];
```

### 3. Tuples
Tuple ek fixed length array hai jahan har position ka type pre-defined hota hai:

```typescript
// Tuple: first element string, second number
let person: [string, number] = ["Ali", 25];

// Tuple with more elements
let employee: [number, string, boolean] = [1, "Ahmed", true];

// Accessing tuple elements
console.log(person[0]); // "Ali"
console.log(person[1]); // 25
```

### 4. Any Type
Jab aap kisi variable ka type nahi jaante, tab `any` use kar sakte hain:

```typescript
let variable: any = 10;
variable = "now I'm a string";
variable = true;
variable = [1, 2, 3];
```

> ⚠️ Warning: `any` use karne se TypeScript ka benefit kam ho jata hai. Jahan tak ho sake, specific types use karein.

### 5. Object Type
Objects ko TypeScript mein type karein:

```typescript
// Object type with defined properties
let person: { name: string; age: number } = {
  name: "Ali",
  age: 30
};

// Access properties
console.log(person.name); // "Ali"
```

## Type Inference with Variables
TypeScript automatically types ko infer kar leta hai:

```typescript
// TypeScript automatically infers types
let name = "Ali";       // string type
let age = 25;           // number type
let isActive = true;    // boolean type
let numbers = [1, 2, 3]; // number[] type
```

## Type Compatibility
TypeScript mein type compatibility ka concept important hai:

```typescript
let x: number = 10;
let y: number = 20;

x = y; // Valid: same type

let a: any = "hello";
let b: string = a; // Valid: any can be assigned to anything

let n: number = 10;
// n = "hello"; // Error: Type 'string' is not assignable to type 'number'
```

## Common Types Table

| Type | Description | Example |
|------|-------------|---------|
| `number` | Numeric values | `let age: number = 25;` |
| `string` | Text values | `let name: string = "Ali";` |
| `boolean` | True/false values | `let isActive: boolean = true;` |
| `any` | Any type (avoid when possible) | `let data: any = fetchData();` |
| `unknown` | Safer version of any | `let userInput: unknown;` |
| `void` | No return value | `function log(): void {...}` |
| `null` | Intentional absence of value | `let empty: null = null;` |
| `undefined` | Uninitialized value | `let notSet: undefined;` |
| `never` | Never occurs (unreachable) | `function error(): never {...}` |

## Next Steps
[Functions, Arrays aur Objects](05_functions_arrays_objects.md) ke baare mein aur detail se padhein. 