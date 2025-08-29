# Lesson 14: Type Inference aur Literal Types

## Type Inference Kya Hai?

Type inference TypeScript ka ek powerful feature hai jo automatically variables aur expressions ke types determine karta hai, bina explicit type annotations ke. Ye TypeScript ko "smart" banata hai aur typing ko less verbose banata hai.

## Basic Type Inference

TypeScript initialization ke time variables ke types infer karta hai:

```ts
// TypeScript automatically infers these types
let name = "Ali";          // type: string
let age = 25;              // type: number
let isActive = true;       // type: boolean
let scores = [85, 90, 95]; // type: number[]
```

Agar initialization value nahi di gayi hai, to TypeScript default type `any` infer karta hai:

```ts
// Without initialization, TypeScript can't infer a specific type
let username;         // type: any
let email: string;    // type explicitly declared as string but no value
```

## Context-based Type Inference

TypeScript aapke code ke context ke based par bhi types infer karta hai:

```ts
// Context-based type inference
function getNames() {
  return ["Ali", "Fatima", "Usman"]; // Returns string[]
}

// TypeScript infers that names is string[] based on the return type of getNames()
const names = getNames();

// TypeScript knows that name is a string because it comes from a string array
names.forEach(name => {
  console.log(name.toUpperCase()); // TypeScript knows name is a string
});
```

## Return Type Inference

Functions ke return types bhi automatically infer hotay hain:

```ts
// Return type is inferred as number
function addNumbers(a: number, b: number) {
  return a + b;
}

// Return type is inferred as string
function greeting(name: string) {
  return `Hello, ${name}!`;
}

// Return type is inferred as boolean
function isAdult(age: number) {
  return age >= 18;
}
```

## Literal Types

Literal types specific values ke types hain, na ke general types jaise `string` ya `number`. Literal types exact values ko represent karte hain.

### String Literal Types

```ts
// String literal type
let direction: "north" | "south" | "east" | "west";

direction = "north"; // Valid
direction = "south"; // Valid
direction = "up";    // Error: Type '"up"' is not assignable to type '"north" | "south" | "east" | "west"'
```

### Number Literal Types

```ts
// Number literal type
let diceRoll: 1 | 2 | 3 | 4 | 5 | 6;

diceRoll = 1;  // Valid
diceRoll = 6;  // Valid
diceRoll = 7;  // Error: Type '7' is not assignable to type '1 | 2 | 3 | 4 | 5 | 6'
diceRoll = 0;  // Error: Type '0' is not assignable to type '1 | 2 | 3 | 4 | 5 | 6'
```

### Boolean Literal Types

Boolean literals bhi exist karte hain, lekin unka use less common hai kyunki sirf do possible values hain:

```ts
// Boolean literal type
let isEnabled: true;  // Can only be true
let isDisabled: false; // Can only be false

isEnabled = true;   // Valid
isEnabled = false;  // Error: Type 'false' is not assignable to type 'true'
```

### Object Literal Types

Objects mein bhi literal properties use kar sakte hain:

```ts
// Object with literal properties
type Button = {
  label: string;
  size: "small" | "medium" | "large";
  variant: "primary" | "secondary" | "outline";
};

const submitButton: Button = {
  label: "Submit",
  size: "medium",      // Must be one of the literal types
  variant: "primary"   // Must be one of the literal types
};

// Error: Type '"extra-large"' is not assignable to type '"small" | "medium" | "large"'
const errorButton: Button = {
  label: "Error",
  size: "extra-large", // Error: Not a valid literal
  variant: "primary"
};
```

## Type Inference with Literal Types

TypeScript literal types ke saath type inference bhi apply karta hai:

```ts
// TypeScript infers the literal type
const direction = "north"; // Type is "north" (not string)
const value = 42;          // Type is 42 (not number)
const active = true;       // Type is true (not boolean)

// This won't work because TypeScript inferred a literal type
direction = "south";       // Error: Type '"south"' is not assignable to type '"north"'
```

### `const` vs `let` with Literals

`const` aur `let` keywords ka type inference par effect:

```ts
// With 'const', TypeScript infers a literal type
const direction = "north"; // Type is literal "north"

// With 'let', TypeScript infers a wider type
let direction2 = "north";  // Type is string, not the literal "north"

// These both work with 'let'
direction2 = "north";
direction2 = "south";
```

## `as const` Assertion

TypeScript mein `as const` assertion ka use karke object aur array literals ko read-only aur narrow literal types mein convert kar sakte hain:

```ts
// Without 'as const' - types are widened
const directions = ["north", "south", "east", "west"]; // Type: string[]

// With 'as const' - literal types are preserved
const directionsLiteral = ["north", "south", "east", "west"] as const; 
// Type: readonly ["north", "south", "east", "west"]

// Try to modify the array with 'as const'
directionsLiteral[0] = "north"; // Error: Cannot assign to '0' because it is a read-only property
directionsLiteral.push("up");   // Error: Property 'push' does not exist on type 'readonly ["north", "south", "east", "west"]'

// With objects
const settings = {
  theme: "dark",
  fontSize: 16,
  animation: true
}; // Regular object with widened types

const settingsLiteral = {
  theme: "dark",
  fontSize: 16,
  animation: true
} as const; // All properties become readonly literal types

// This works with regular object
settings.theme = "light";

// This fails with 'as const' object
settingsLiteral.theme = "light"; // Error: Cannot assign to 'theme' because it is a read-only property
```

## Template Literal Types

TypeScript 4.1+ mein template literal types feature hai jo string literals ko dynamically combine karne ki capability provide karta hai:

```ts
// Basic template literal type
type Greeting = `Hello, ${string}!`;

let validGreeting: Greeting = "Hello, TypeScript!"; // Valid
let invalidGreeting: Greeting = "Hi there!";       // Error: Type '"Hi there!"' is not assignable to type '`Hello, ${string}!`'

// Combining literal types with template literals
type Color = "red" | "green" | "blue";
type Size = "small" | "medium" | "large";

// Creates types like: "small-red", "medium-blue", etc.
type ColoredSize = `${Size}-${Color}`;

let myColoredSize: ColoredSize = "small-red";   // Valid
let invalid: ColoredSize = "red-small";         // Error: Wrong order
let alsoInvalid: ColoredSize = "tiny-red";      // Error: "tiny" is not a valid Size
```

## Practical Uses of Literal Types

### Function Parameters with Specific Values

```ts
// Function that only accepts specific values
function setAlignment(align: "left" | "center" | "right"): void {
  console.log(`Setting alignment to ${align}`);
}

setAlignment("left");    // Valid
setAlignment("center");  // Valid
setAlignment("top");     // Error: Argument of type '"top"' is not assignable
```

### Discriminated Unions

Literal types discriminated unions ke saath powerful combinations banate hain:

```ts
// Shapes using discriminated union with literal type
type Circle = {
  kind: "circle";  // Literal type acts as discriminator
  radius: number;
};

type Rectangle = {
  kind: "rectangle";  // Literal type acts as discriminator
  width: number;
  height: number;
};

type Shape = Circle | Rectangle;

function calculateArea(shape: Shape): number {
  // TypeScript can narrow down the type based on the literal
  if (shape.kind === "circle") {
    // TypeScript knows it's a Circle here
    return Math.PI * shape.radius * shape.radius;
  } else {
    // TypeScript knows it's a Rectangle here
    return shape.width * shape.height;
  }
}

const myCircle: Shape = {
  kind: "circle",
  radius: 5
};

console.log(calculateArea(myCircle)); // 78.53981633974483
```

### API Status Responses

Literal types API responses ko type karne mein helpful hain:

```ts
// API response types using literal types
type ApiSuccess<T> = {
  status: "success";
  data: T;
};

type ApiError = {
  status: "error";
  errorCode: number;
  message: string;
};

type ApiResponse<T> = ApiSuccess<T> | ApiError;

// Function that handles API response
function handleResponse<T>(response: ApiResponse<T>): T | null {
  if (response.status === "success") {
    return response.data;
  } else {
    console.error(`Error ${response.errorCode}: ${response.message}`);
    return null;
  }
}

// Example usage
const successResponse: ApiResponse<number[]> = {
  status: "success",
  data: [1, 2, 3, 4, 5]
};

const errorResponse: ApiResponse<number[]> = {
  status: "error",
  errorCode: 404,
  message: "Data not found"
};

const data = handleResponse(successResponse); // Type: number[] | null
```

## Type Widening and Narrowing with Literals

### Type Widening

TypeScript mein "type widening" tab hota hai jab TypeScript ek narrow type ko wider type mein convert karta hai, typically variable re-assignment ke time:

```ts
// Type widening examples
let id = "user_123";    // Inferred as string, not "user_123"
let count = 42;         // Inferred as number, not 42
let flag = true;        // Inferred as boolean, not true

// Using literals with let
let direction = "north" as const; // Type is "north"
direction = "south";              // Error: Type '"south"' is not assignable to type '"north"'
```

### Type Narrowing

TypeScript mein "type narrowing" tab hota hai jab TypeScript runtime checks ke through types ko more specific banata hai:

```ts
// Type narrowing with literals and type guards
function handleValue(value: string | number) {
  // Type is string | number here
  
  if (typeof value === "string") {
    // Type is narrowed to string here
    console.log(value.toUpperCase());
  } else {
    // Type is narrowed to number here
    console.log(value.toFixed(2));
  }
  
  // Type is string | number again here
}

// Type narrowing with literal type checks
type Direction = "north" | "south" | "east" | "west";

function navigate(direction: Direction) {
  // Type is Direction here
  
  if (direction === "north") {
    // Type is narrowed to "north" here
    console.log("Heading north!");
  } else if (direction === "south") {
    // Type is narrowed to "south" here
    console.log("Heading south!");
  } else {
    // Type is narrowed to "east" | "west" here
    console.log(`Heading ${direction}!`);
  }
}
```

## Common Patterns with Type Inference and Literal Types

### Config Objects with Literal Properties

```ts
// Config object with literal properties
type ThemeConfig = {
  mode: "light" | "dark" | "system";
  contrast: "normal" | "high";
  fontSize: "small" | "medium" | "large";
  animations: boolean;
};

// Default configuration
const defaultConfig: ThemeConfig = {
  mode: "system",
  contrast: "normal",
  fontSize: "medium",
  animations: true
};

// User configuration with partial override
function createUserConfig(overrides: Partial<ThemeConfig>): ThemeConfig {
  return { ...defaultConfig, ...overrides };
}

// Valid config
const userConfig = createUserConfig({
  mode: "dark",
  fontSize: "large"
});

// Invalid config would cause error at compile time
const invalidConfig = createUserConfig({
  mode: "blue" // Error: Type '"blue"' is not assignable to type '"light" | "dark" | "system"'
});
```

### Function Overloads with Literal Return Types

```ts
// Function overloads with literal return types
function convert(value: number, to: "string"): string;
function convert(value: string, to: "number"): number;
function convert(value: number | string, to: "string" | "number"): string | number {
  if (to === "string") {
    return String(value);
  } else {
    return Number(value);
  }
}

const stringResult = convert(42, "string");     // Type: string
const numberResult = convert("42", "number");   // Type: number
```

### State Management with Literals

```ts
// State management with literal types for actions
type State = {
  count: number;
  loading: boolean;
  error: string | null;
};

type Action =
  | { type: "INCREMENT"; payload: number }
  | { type: "DECREMENT"; payload: number }
  | { type: "SET_LOADING"; payload: boolean }
  | { type: "SET_ERROR"; payload: string | null };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + action.payload };
    case "DECREMENT":
      return { ...state, count: state.count - action.payload };
    case "SET_LOADING":
      return { ...state, loading: action.payload };
    case "SET_ERROR":
      return { ...state, error: action.payload };
    default:
      // Using TypeScript's never type to ensure exhaustive handling
      const _exhaustiveCheck: never = action;
      return state;
  }
}

// Initial state
const initialState: State = {
  count: 0,
  loading: false,
  error: null
};

// Example usage
const newState = reducer(initialState, { type: "INCREMENT", payload: 5 });
```

## Best Practices for Type Inference and Literal Types

1. **Let TypeScript Infer When Obvious**: Jahan type obvious ho, wahan explicit type annotations se bachain.

2. **Be Explicit When Necessary**: Complex types ya function parameters ke liye explicit types use karein.

3. **Use `as const` for Fixed Values**: Fixed values ke liye `as const` assertion use karein jahan aap chahte hain ke literal types preserve rahen.

4. **Prefer Literal Union Types Over Enums**: Enums ki jagah literal union types use karein for better type safety.

5. **Leverage Discriminated Unions**: Complex object hierarchies ko manage karne ke liye literal types ke saath discriminated unions use karein.

6. **Don't Over-constrain**: Har cheez ko literal type na banayein - sirf wahan use karein jahan specific values ki restriction important hai.

7. **Use Template Literal Types for Patterns**: String patterns ko enforce karne ke liye template literal types use karein.

## Type Inference vs. Explicit Types

| Scenario | Type Inference | Explicit Types |
|----------|----------------|----------------|
| Simple variables | ✅ Better readability | ❌ Unnecessary verbosity |
| Complex objects | ❌ Can be unclear | ✅ Documents structure |
| Function parameters | ❌ Usually not possible | ✅ Required for clarity |
| Function returns | ✅ Often clear from implementation | ✅ Good for documentation |
| API contracts | ❌ Not recommended | ✅ Essential for stability |
| Literal values | ✅ Works well with `as const` | ✅ Helps restrict values |

## Next Steps

[Advanced TypeScript Features](15_advanced_typescript_features.md) ko padhein. 