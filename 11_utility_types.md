# Lesson 11: Utility Types (Partial, Required, etc.)

## Utility Types Kya Hain?

TypeScript mein utility types pre-defined generic types hain jo existing types ko transform karne ke liye use hote hain. Ye types aapko existing types se naye types create karne mein help karte hain, without manually redefining them.

## Common Utility Types

### 1. Partial\<T>

`Partial<T>` ek utility type hai jo ek type `T` ke saare properties ko optional bana deta hai.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  age: number;
}

// Partial<User> means all properties are optional
function updateUser(userId: number, updates: Partial<User>) {
  // can update any subset of User properties
}

// Using the function
updateUser(1, { name: "Ahmed" }); // Valid - only updating name
updateUser(2, { age: 30, email: "ahmed@example.com" }); // Valid
// No need to provide all properties of User
```

### 2. Required\<T>

`Required<T>` ek utility type hai jo ek type `T` ke saare optional properties ko required bana deta hai.

```typescript
interface BlogPost {
  title: string;
  content: string;
  author?: string;
  tags?: string[];
  publishedAt?: Date;
}

// Required<BlogPost> means all properties are required, even the optional ones
const completeBlogPost: Required<BlogPost> = {
  title: "TypeScript Utility Types",
  content: "This is a blog post about TypeScript utility types...",
  author: "Ali", // Now required
  tags: ["typescript", "programming"], // Now required
  publishedAt: new Date() // Now required
};
```

### 3. Readonly\<T>

`Readonly<T>` ek utility type hai jo ek type `T` ke saare properties ko readonly bana deta hai (unhe modify nahi kiya ja sakta).

```typescript
interface Config {
  apiUrl: string;
  timeout: number;
  retries: number;
}

// Readonly<Config> means all properties cannot be modified after creation
const appConfig: Readonly<Config> = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  retries: 3
};

// Trying to modify will result in an error
// appConfig.timeout = 10000; // Error: Cannot assign to 'timeout' because it is a read-only property
```

### 4. Record\<K, T>

`Record<K, T>` ek utility type hai jo key type `K` aur value type `T` ke saath ek object type create karta hai.

```typescript
// Record<string, number> means an object with string keys and number values
const scores: Record<string, number> = {
  math: 95,
  science: 90,
  history: 85
};

// With literal types
type Subject = "math" | "science" | "history" | "english";
type Grades = Record<Subject, number>;

const studentGrades: Grades = {
  math: 95,
  science: 90,
  history: 85,
  english: 88
};
```

### 5. Pick\<T, K>

`Pick<T, K>` ek utility type hai jo existing type `T` se specific properties `K` ko pick karke ek new type banata hai.

```typescript
interface User {
  id: number;
  name: string;
  email: string;
  address: string;
  phoneNumber: string;
}

// Pick only name and email from User
type UserCredentials = Pick<User, "name" | "email">;

const credentials: UserCredentials = {
  name: "Sara",
  email: "sara@example.com"
  // No other User properties allowed here
};
```

### 6. Omit\<T, K>

`Omit<T, K>` ek utility type hai jo existing type `T` se specific properties `K` ko exclude karke ek new type banata hai.

```typescript
interface Product {
  id: number;
  name: string;
  price: number;
  category: string;
  stock: number;
  description: string;
}

// Omit id from Product when creating a new one (server will generate it)
type CreateProductDTO = Omit<Product, "id">;

const newProduct: CreateProductDTO = {
  name: "Smartphone",
  price: 599,
  category: "Electronics",
  stock: 100,
  description: "A new smartphone with great features"
};
```

### 7. Exclude\<T, U>

`Exclude<T, U>` ek utility type hai jo union type `T` se union type `U` ke members ko exclude karta hai.

```typescript
// Union type of string literals
type Status = "pending" | "completed" | "failed" | "cancelled" | "in-progress";

// Exclude some states to create a new type
type ActiveStatus = Exclude<Status, "completed" | "failed" | "cancelled">;
// ActiveStatus = "pending" | "in-progress"

const status: ActiveStatus = "pending"; // Valid
// const status2: ActiveStatus = "completed"; // Error: Type '"completed"' is not assignable
```

### 8. Extract\<T, U>

`Extract<T, U>` ek utility type hai jo union type `T` se sirf woh members extract karta hai jo union type `U` mein assignable hain.

```typescript
type Status = "pending" | "completed" | "failed" | "cancelled" | "in-progress";
type PositiveOutcome = "completed" | "in-progress" | "success";

// Extract common members from both unions
type PositiveStatus = Extract<Status, PositiveOutcome>;
// PositiveStatus = "completed" | "in-progress"

const status: PositiveStatus = "completed"; // Valid
// const status2: PositiveStatus = "pending"; // Error: Type '"pending"' is not assignable
```

### 9. NonNullable\<T>

`NonNullable<T>` ek utility type hai jo type `T` se `null` aur `undefined` ko remove karta hai.

```typescript
type MaybeString = string | null | undefined;

// Remove null and undefined
type DefinitelyString = NonNullable<MaybeString>;
// DefinitelyString = string

function processValue(value: NonNullable<string | null>) {
  // We can safely use string methods here
  return value.toUpperCase();
}
```

### 10. Parameters\<T>

`Parameters<T>` ek utility type hai jo function type `T` ke parameters ka tuple type extract karta hai.

```typescript
function createUser(name: string, age: number, isAdmin: boolean): User {
  // implementation
  return { id: 1, name, age, isAdmin };
}

// Extract parameter types as a tuple
type CreateUserParams = Parameters<typeof createUser>;
// CreateUserParams = [string, number, boolean]

// Can be used to type function parameters consistently
function validateUserParams(...args: Parameters<typeof createUser>) {
  const [name, age, isAdmin] = args;
  // validation logic
}
```

### 11. ReturnType\<T>

`ReturnType<T>` ek utility type hai jo function type `T` ke return type ko extract karta hai.

```typescript
function fetchUser() {
  return {
    id: 1,
    name: "Ahmed",
    isLoggedIn: true
  };
}

// Extract the return type of fetchUser
type User = ReturnType<typeof fetchUser>;
// User = { id: number; name: string; isLoggedIn: boolean; }

// Now we can use this type elsewhere
function processUser(user: ReturnType<typeof fetchUser>) {
  console.log(user.name);
}
```

## Custom Utility Types Banana

Khud ke utility types bhi TypeScript mein bana sakte hain:

```typescript
// Custom utility type that makes all properties nullable
type Nullable<T> = { [P in keyof T]: T[P] | null };

interface User {
  id: number;
  name: string;
  email: string;
}

// Creates a type where all User properties can be null
type NullableUser = Nullable<User>;

const user: NullableUser = {
  id: 1,
  name: null, // Valid
  email: "user@example.com"
};
```

## Real-World Examples

### Example 1: Form Management

Form data ko handle karna using utility types:

```typescript
interface UserProfile {
  id: number; // server-generated
  name: string;
  email: string;
  bio: string;
  age: number;
  location: string;
  profilePicture: string;
}

// For creating a new user (server generates id)
type CreateUserFormData = Omit<UserProfile, "id">;

// For updating a user (all fields optional)
type UpdateUserFormData = Partial<Omit<UserProfile, "id">>;

function createUser(userData: CreateUserFormData) {
  // API call to create user
}

function updateUser(userId: number, updates: UpdateUserFormData) {
  // API call to update user
}

// Usage
createUser({
  name: "Sara",
  email: "sara@example.com",
  bio: "Software developer",
  age: 28,
  location: "Karachi",
  profilePicture: "sara.jpg"
});

updateUser(123, {
  bio: "Senior software developer",
  location: "Lahore"
});
```

### Example 2: API Response Handling

API response handling using utility types:

```typescript
// API response structure
interface ApiResponse<T> {
  data: T;
  status: number;
  message: string;
  timestamp: Date;
}

interface User {
  id: number;
  name: string;
  email: string;
}

// For list of users
type UserListResponse = ApiResponse<User[]>;

// For single user
type UserDetailResponse = ApiResponse<User>;

// For error response (no data)
type ErrorResponse = Omit<ApiResponse<null>, "data">;

function handleUserListResponse(response: UserListResponse) {
  response.data.forEach(user => {
    console.log(user.name);
  });
}

function handleError(error: ErrorResponse) {
  console.error(`Error ${error.status}: ${error.message}`);
}
```

### Example 3: Configuration Management

Application configuration handling with readonly properties:

```typescript
interface AppConfig {
  api: {
    baseUrl: string;
    timeout: number;
    retries: number;
  };
  ui: {
    theme: "light" | "dark";
    language: string;
  };
  features: Record<string, boolean>;
}

// Make configuration immutable
type ImmutableConfig = Readonly<AppConfig>;

// Create a partial configuration type for updating
type ConfigUpdate = Partial<AppConfig>;

// Usage
const defaultConfig: ImmutableConfig = {
  api: {
    baseUrl: "https://api.example.com",
    timeout: 5000,
    retries: 3
  },
  ui: {
    theme: "light",
    language: "en"
  },
  features: {
    darkMode: true,
    betaFeatures: false
  }
};

function updateConfig(updates: ConfigUpdate) {
  // Merge with existing config
  // But we can't accidentally modify the defaultConfig
}
```

## Conditional Types with Utility Types

Utility types ko conditional types ke saath combine karke aur powerful types bana sakte hain:

```typescript
// Conditional type to handle different response types
type ApiResponse<T, E = Error> = T extends null
  ? { success: false; error: E }
  : { success: true; data: T };

// Usage
interface User {
  id: number;
  name: string;
}

// Success response
type UserResponse = ApiResponse<User>;
// { success: true; data: User }

// Error response
type ErrorResponse = ApiResponse<null, { code: number, message: string }>;
// { success: false; error: { code: number, message: string } }

function handleResponse(response: UserResponse | ErrorResponse) {
  if (response.success) {
    // TypeScript knows response.data exists and is User
    console.log(response.data.name);
  } else {
    // TypeScript knows response.error exists
    console.error(response.error);
  }
}
```

## Best Practices for Using Utility Types

1. **Object Manipulation ke liye `Pick`, `Omit`, `Partial`**: Object types se specific properties lene ya modify karne ke liye in utility types ka use karein.

2. **Immutability ke liye `Readonly`**: Jab data modify nahi hona chahiye, `Readonly` ka use karein.

3. **Dynamic Objects ke liye `Record`**: Key-value pairs handle karne ke liye `Record` best hai.

4. **Avoid Deep Nesting**: Zyada nested utility types code ko complex aur hard to understand bana dete hain.

5. **Custom Utility Types**: Common patterns ke liye apne custom utility types create karein.

## Next Steps
[TypeScript with React](12_typescript_with_react.md) ko padhein. 