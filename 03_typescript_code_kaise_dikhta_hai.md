# Lesson 3: TypeScript Code Kaise Dikhta Hai?

## Overview
Is lesson mein hum dekhenge ke TypeScript code JavaScript se kis tarah different dikhta hai aur kaise type annotations use hoti hain.

## Basic Syntax Comparison

### JavaScript vs TypeScript Comparison
JavaScript aur TypeScript mein main farq type annotations ka hota hai:

```javascript
// JavaScript
let name = "Ali";
let age = 25;
let isStudent = true;

function greet(person) {
  return "Hello, " + person;
}
```

```typescript
// TypeScript
let name: string = "Ali";
let age: number = 25;
let isStudent: boolean = true;

function greet(person: string): string {
  return "Hello, " + person;
}
```

## Type Annotations

### Variables Par Type Annotations
```typescript
let name: string = "Ali";
let age: number = 25;
let isStudent: boolean = true;
let hobbies: string[] = ["reading", "coding"];
let tuple: [string, number] = ["position", 1];
```

### Functions Par Type Annotations
```typescript
// Parameters aur return value dono par type annotations
function add(a: number, b: number): number {
  return a + b;
}

// Void return type (kuch return nahi karta)
function logMessage(message: string): void {
  console.log(message);
}
```

## Type Safety Examples

### JavaScript Mein Type Safety Nahi Hoti
```javascript
// JavaScript mein ye allowed hai (lekin bug create kar sakta hai)
let name = "Ali";
name = 5;  // No error in JavaScript
```

### TypeScript Mein Type Safety Hoti Hai
```typescript
// TypeScript mein ye error dega
let name: string = "Ali";
name = 5;  // Error: Type 'number' is not assignable to type 'string'
```

## Type Inference
TypeScript automatically types ko infer bhi kar leta hai:

```typescript
// Type annotation ki zarurat nahi - TypeScript samajh jata hai
let name = "Ali";       // Automatically string type infer kar lega
let age = 25;           // Automatically number type infer kar lega
let isStudent = true;   // Automatically boolean type infer kar lega
```

## Explicit vs Implicit Typing
```typescript
// Explicit typing (clearly defined)
let name: string = "Ali";

// Implicit typing (inferred by TypeScript)
let name = "Ali";  // Still typed as string, but TypeScript guesses it
```

## Next Steps
[TypeScript Mein Variables](04_typescript_mein_variables.md) ko padhein. 