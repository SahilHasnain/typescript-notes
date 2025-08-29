# Lesson 8: Type Assertions, Special Types & Compilation

## Type Assertions in TypeScript

### Type Assertion Kya Hai?
Type assertion ek way hai TypeScript ko batane ka ke aap ek variable ke type ke bare mein compiler se zyada jaante hain. Ye type conversion nahi hai, sirf compiler ko hint deta hai.

```typescript
// Two syntax forms for type assertion

// Syntax 1: Using angle brackets (old style)
let someValue: any = "Hello TypeScript";
let strLength1: number = (<string>someValue).length;

// Syntax 2: Using 'as' keyword (preferred)
let someValue2: any = "Hello TypeScript";
let strLength2: number = (someValue2 as string).length;
```

### Type Assertion vs Type Casting
Type assertion TypeScript mein compiler ko batane ke liye hota hai, runtime mein koi effect nahi hota:

```typescript
// Type assertion does NOT change the runtime value
let value: any = "123";
let numValue = value as number; // Still a string at runtime
console.log(typeof numValue); // "string"

// To actually convert, use conversion functions
let actualNumValue = Number(value);
console.log(typeof actualNumValue); // "number"
```

### Common Type Assertion Use Cases

#### 1. DOM Elements
DOM elements ko access karte waqt type assertions useful hote hain:

```typescript
// Without type assertion
const myElement = document.getElementById("my-element");
// myElement.value; // Error: Property 'value' does not exist on type 'HTMLElement'

// With type assertion
const inputElement = document.getElementById("my-input") as HTMLInputElement;
inputElement.value = "New value"; // Works fine
```

#### 2. Response Data Typing
API calls se response data ko type karne ke liye:

```typescript
interface User {
  id: number;
  name: string;
}

async function fetchUser() {
  const response = await fetch("/api/users/1");
  const data = await response.json();
  
  // Type assertion for the API response
  const user = data as User;
  return user;
}
```

#### 3. Narrowing Union Types
Union types ko narrow karne ke liye:

```typescript
interface Cat {
  name: string;
  purr(): void;
}

interface Dog {
  name: string;
  bark(): void;
}

function makeNoise(animal: Cat | Dog) {
  if ("purr" in animal) {
    (animal as Cat).purr();
  } else {
    (animal as Dog).bark();
  }
}
```

### Double Type Assertion
Kabhi kabhi double assertion ki zarurat hoti hai, lekin ye use carefully karna chahiye:

```typescript
// Double assertion - use with extreme caution
function handleValue(value: number) {
  // Very dangerous!
  const element = value as unknown as HTMLElement;
}
```

### Type Assertion with Non-null Assertion
Non-null assertion operator (`!`) Type ka ek special case hai:

```typescript
// Non-null assertion
function process(value: string | null | undefined) {
  // Tell TypeScript value won't be null/undefined
  const strLength = value!.length;
}
```

## Special Types in TypeScript

### 1. any Type
`any` type TypeScript ko skip karne deta hai type checking:

```typescript
// 'any' type
let value: any = "Hello";
value = 123;
value = true;
value = { x: 10 };
value.foo(); // No error, even though this might fail at runtime
```

### 2. unknown Type
`unknown` type `any` se safer version hai:

```typescript
// 'unknown' type - safer than 'any'
let value: unknown = "Hello";
value = 123;
value = true;

// Using unknown requires type checking
if (typeof value === "string") {
  console.log(value.toUpperCase()); // Works because type is checked
}

// Won't work without type checking
// value.toUpperCase(); // Error: Object is of type 'unknown'
```

### 3. never Type
`never` type aisa value represent karta hai jo kabhi occur nahi hota:

```typescript
// Function that never returns (throws error)
function throwError(message: string): never {
  throw new Error(message);
}

// Function with unreachable end point
function infiniteLoop(): never {
  while (true) {
    // do something
  }
}

// Use in exhaustive checking
type Shape = Circle | Square;

function assertNever(x: never): never {
  throw new Error("Unexpected object: " + x);
}

function getArea(shape: Shape) {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "square":
      return shape.sideLength ** 2;
    default:
      // If new shapes are added without handling them,
      // this code will catch the error at compile time
      return assertNever(shape as never);
  }
}
```

### 4. void Type
Function ke return type ke liye jab kuch return nahi karna:

```typescript
// void type
function logMessage(message: string): void {
  console.log(message);
  // No return statement needed
}

// void vs undefined
function returnUndefined(): undefined {
  return undefined; // Must explicitly return undefined
}
```

### 5. object Type
Non-primitive types ke liye:

```typescript
// 'object' type
function acceptObject(obj: object): void {
  console.log(obj);
}

acceptObject({ name: "Ali" }); // Works
acceptObject([1, 2, 3]);       // Works (arrays are objects)
// acceptObject(42);           // Error: Argument of type 'number' is not assignable
// acceptObject("string");     // Error: Argument of type 'string' is not assignable
```

## TypeScript Compilation Process

### TypeScript ko JavaScript mein Compile Karna
TypeScript se JavaScript mein code compile karne ke liye, TypeScript compiler (tsc) use kiya jata hai:

1. TypeScript file (.ts) create karein
2. tsc command se JavaScript (.js) mein compile karein
3. Generated JavaScript ko run karein

### Setup Steps

#### 1. TypeScript Compiler Installation
```bash
# Install TypeScript globally
npm install -g typescript

# Check installation
tsc --version
```

#### 2. Manual Compilation
```bash
# Compile a single file
tsc filename.ts

# Watch mode (auto-recompile on changes)
tsc filename.ts --watch
```

#### 3. Project Setup with tsconfig.json
TypeScript config file project ke root mein bana sakte hain:

```bash
# Generate tsconfig.json
tsc --init
```

Example tsconfig.json:
```json
{
  "compilerOptions": {
    "target": "es2016",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

Important compiler options:
- `target`: JavaScript version to compile to
- `module`: Module system (commonjs, es6, etc.)
- `outDir`: Output directory for compiled files
- `rootDir`: Root directory of source files
- `strict`: Enable all strict type checking options

#### 4. Project Compilation
```bash
# Compile entire project based on tsconfig.json
tsc

# Compile and watch for changes
tsc --watch
```

### TypeScript with Node.js
Node.js ke saath TypeScript use karne ke liye options:

#### Option 1: Compile then Run
```bash
# Compile
tsc

# Run compiled JavaScript
node dist/index.js
```

#### Option 2: ts-node (Directly Run TypeScript)
```bash
# Install ts-node
npm install -g ts-node

# Run TypeScript directly
ts-node src/index.ts
```

### TypeScript with webpack
Modern web applications mein webpack ke saath TypeScript use karna:

```javascript
// webpack.config.js example
module.exports = {
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/
      }
    ]
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js']
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

## Debugging TypeScript

### Source Maps
Source maps TypeScript files ko browser mein directly debug karne ki permission dete hain:

```json
// tsconfig.json
{
  "compilerOptions": {
    "sourceMap": true,
    // other options...
  }
}
```

### VS Code Debugging
VS Code mein TypeScript debugging setup:

1. launch.json file create karein:
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Program",
      "program": "${workspaceFolder}/src/index.ts",
      "preLaunchTask": "tsc: build - tsconfig.json",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```

2. Debug view mein "Launch Program" select karein aur debug start karein.

## TypeScript Configuration in Depth

### Key tsconfig.json Options
Important options aur unka kya effect hota hai:

```json
{
  "compilerOptions": {
    // JavaScript Language Level
    "target": "es2020",           // Modern browsers
    
    // Module System 
    "module": "esnext",           // Modern ESM modules
    
    // Type Checking Strictness
    "strict": true,               // Enable all strict checks
    "noImplicitAny": true,        // Error on implied 'any' types
    "strictNullChecks": true,     // More rigorous null checking
    
    // Module Resolution
    "moduleResolution": "node",   // How to resolve imports
    "baseUrl": "./",              // Base directory to resolve non-relative imports
    "paths": {                    // Path mapping for imports
      "@/*": ["src/*"]
    },
    
    // Output Options
    "outDir": "./dist",           // Output directory
    "sourceMap": true,            // Generate sourcemaps for debugging
    
    // Interop Options
    "esModuleInterop": true,      // Better interop with CommonJS
    
    // Advanced Options
    "skipLibCheck": true,         // Skip type checking of declaration files
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],        // Files to include
  "exclude": ["node_modules"]     // Files to exclude
}
```

## Next Steps
[Classes, Inheritance, Access Modifiers](09_classes_inheritance.md) ko padhein. 