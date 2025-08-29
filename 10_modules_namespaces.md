# Lesson 10: Modules, Namespaces & Real-World Mini Project

## Modules in TypeScript

### Modules Kya Hain?
Modules ek tarika hai code ko organize karne aur reuse karne ka. TypeScript mein modules se aap apne code ko alag-alag files mein divide kar sakte hain, phir unko import/export kar sakte hain jahan zarurat ho.

### Export Statements
File se code ko export karne ke liye:

```typescript
// mathUtils.ts file
// Named exports
export function add(a: number, b: number): number {
  return a + b;
}

export function subtract(a: number, b: number): number {
  return a - b;
}

export const PI = 3.14159;

// Default export (ek file mein sirf ek default export ho sakta hai)
export default function multiply(a: number, b: number): number {
  return a * b;
}
```

### Import Statements
Exported code ko dusri file mein import karne ke liye:

```typescript
// app.ts file
// Named imports
import { add, subtract, PI } from './mathUtils';

// Default import (koi bhi naam de sakte hain)
import multiply from './mathUtils';

// Named imports with alias
import { add as addition } from './mathUtils';

// Import all exports into a namespace
import * as MathUtils from './mathUtils';

// Usage
console.log(add(5, 3));                // 8
console.log(subtract(10, 4));          // 6
console.log(multiply(2, 3));           // 6
console.log(PI);                       // 3.14159
console.log(addition(2, 2));           // 4
console.log(MathUtils.add(1, 2));      // 3
```

### Re-exporting
Ek module se dusre module mein code re-export karna:

```typescript
// shapes.ts
export interface Circle {
  radius: number;
}

export interface Rectangle {
  width: number;
  height: number;
}

// index.ts (barrel file)
// Re-export from other modules
export { Circle, Rectangle } from './shapes';
export { add, subtract } from './mathUtils';

// Another file can now import from the barrel
import { Circle, Rectangle, add } from './index';
```

## Namespaces in TypeScript

### Namespaces Kya Hain?
Namespaces TypeScript ka ek purana tarika hai related functionalities ko group karne ka. Modern TypeScript mein modules zyada prefer kiye jate hain, but namespaces ka concept samajhna important hai.

```typescript
// Using namespace
namespace Geometry {
  // Interface inside namespace
  export interface Point {
    x: number;
    y: number;
  }
  
  // Class inside namespace
  export class Circle {
    constructor(public center: Point, public radius: number) {}
    
    area(): number {
      return Math.PI * this.radius ** 2;
    }
  }
  
  // Nested namespace
  export namespace Utils {
    export function distanceBetweenPoints(p1: Point, p2: Point): number {
      return Math.sqrt((p2.x - p1.x)**2 + (p2.y - p1.y)**2);
    }
  }
}

// Using the namespace
const point: Geometry.Point = { x: 0, y: 0 };
const circle = new Geometry.Circle(point, 5);
console.log(circle.area());

// Using the nested namespace
const point2: Geometry.Point = { x: 3, y: 4 };
const distance = Geometry.Utils.distanceBetweenPoints(point, point2);
console.log(distance); // 5
```

### Namespace vs Module
Namespaces aur modules mein key differences:

1. Modules file-based hote hain, namespaces code-based
2. Modules explicit imports require karte hain, namespaces global scope mein jaate hain
3. Modern applications mein modules prefer kiye jate hain
4. Namespaces ka use mostly legacy applications mein dekhne ko milta hai

## Modules aur Namespaces ke Benefits

### Modules ke Benefits
1. **Code Organization**: Code ko logical units mein divide karna
2. **Encapsulation**: Private aur public exports control
3. **Reusability**: Code ko reuse karna different parts mein
4. **Dependency Management**: Clear dependencies between files

### Namespaces ke Benefits
1. **Global Usage**: Namespace ko globally access kar sakte hain
2. **Bundling**: Related code ko single namespace mein bundle karna
3. **Legacy Support**: Purane TypeScript code ke liye compatibility

## Real-World Mini Project: Task Management System

Ab hum ek simple Task Management System banayenge using TypeScript modules, interfaces aur classes:

### Project Structure
```
src/
  ├── models/
  │   ├── Task.ts
  │   └── User.ts
  ├── services/
  │   ├── TaskService.ts
  │   └── UserService.ts
  └── index.ts
```

### Implementation

#### 1. Task Model
```typescript
// src/models/Task.ts
export type TaskStatus = "pending" | "in-progress" | "completed" | "cancelled";

export interface Task {
  id: number;
  title: string;
  description?: string;
  status: TaskStatus;
  createdAt: Date;
  updatedAt: Date;
  assignedTo?: number; // User ID
}

export interface TaskCreateDTO {
  title: string;
  description?: string;
  assignedTo?: number;
}

export interface TaskUpdateDTO {
  title?: string;
  description?: string;
  status?: TaskStatus;
  assignedTo?: number;
}
```

#### 2. User Model
```typescript
// src/models/User.ts
export interface User {
  id: number;
  name: string;
  email: string;
  role: "admin" | "user";
  createdAt: Date;
}

export interface UserCreateDTO {
  name: string;
  email: string;
  role?: "admin" | "user";
}
```

#### 3. Task Service
```typescript
// src/services/TaskService.ts
import { Task, TaskCreateDTO, TaskStatus, TaskUpdateDTO } from "../models/Task";

export class TaskService {
  private tasks: Task[] = [];
  private nextId: number = 1;

  getAllTasks(): Task[] {
    return this.tasks;
  }

  getTaskById(id: number): Task | undefined {
    return this.tasks.find(task => task.id === id);
  }

  getTasksByStatus(status: TaskStatus): Task[] {
    return this.tasks.filter(task => task.status === status);
  }

  getTasksByAssignee(userId: number): Task[] {
    return this.tasks.filter(task => task.assignedTo === userId);
  }

  createTask(taskDTO: TaskCreateDTO): Task {
    const now = new Date();
    
    const newTask: Task = {
      id: this.nextId++,
      title: taskDTO.title,
      description: taskDTO.description,
      status: "pending",
      createdAt: now,
      updatedAt: now,
      assignedTo: taskDTO.assignedTo
    };
    
    this.tasks.push(newTask);
    return newTask;
  }

  updateTask(id: number, taskDTO: TaskUpdateDTO): Task | undefined {
    const taskIndex = this.tasks.findIndex(task => task.id === id);
    
    if (taskIndex === -1) {
      return undefined;
    }
    
    const updatedTask: Task = {
      ...this.tasks[taskIndex],
      ...taskDTO,
      updatedAt: new Date()
    };
    
    this.tasks[taskIndex] = updatedTask;
    return updatedTask;
  }

  deleteTask(id: number): boolean {
    const initialLength = this.tasks.length;
    this.tasks = this.tasks.filter(task => task.id !== id);
    return initialLength !== this.tasks.length;
  }
}
```

#### 4. User Service
```typescript
// src/services/UserService.ts
import { User, UserCreateDTO } from "../models/User";

export class UserService {
  private users: User[] = [];
  private nextId: number = 1;

  getAllUsers(): User[] {
    return this.users;
  }

  getUserById(id: number): User | undefined {
    return this.users.find(user => user.id === id);
  }

  createUser(userDTO: UserCreateDTO): User {
    const now = new Date();
    
    const newUser: User = {
      id: this.nextId++,
      name: userDTO.name,
      email: userDTO.email,
      role: userDTO.role || "user",
      createdAt: now
    };
    
    this.users.push(newUser);
    return newUser;
  }

  deleteUser(id: number): boolean {
    const initialLength = this.users.length;
    this.users = this.users.filter(user => user.id !== id);
    return initialLength !== this.users.length;
  }
}
```

#### 5. Main Application File
```typescript
// src/index.ts
import { TaskService } from "./services/TaskService";
import { UserService } from "./services/UserService";
import { Task } from "./models/Task";
import { User } from "./models/User";

// Create services
const taskService = new TaskService();
const userService = new UserService();

// Create users
const adminUser: User = userService.createUser({
  name: "Admin User",
  email: "admin@example.com",
  role: "admin"
});

const regularUser: User = userService.createUser({
  name: "Regular User",
  email: "user@example.com"
});

console.log("All Users:", userService.getAllUsers());

// Create tasks
const task1: Task = taskService.createTask({
  title: "Setup Project",
  description: "Setup the initial project structure and dependencies",
  assignedTo: adminUser.id
});

const task2: Task = taskService.createTask({
  title: "Create Models",
  description: "Define data models and DTOs",
  assignedTo: regularUser.id
});

console.log("All Tasks:", taskService.getAllTasks());

// Update task
const updatedTask = taskService.updateTask(task1.id, {
  status: "in-progress"
});

console.log("Updated Task:", updatedTask);

// Get tasks by assignee
const adminTasks = taskService.getTasksByAssignee(adminUser.id);
console.log("Admin Tasks:", adminTasks);

// Get tasks by status
const inProgressTasks = taskService.getTasksByStatus("in-progress");
console.log("In Progress Tasks:", inProgressTasks);

// Delete a task
taskService.deleteTask(task2.id);
console.log("After deletion:", taskService.getAllTasks());
```

## How to Run the Project

1. Project setup:
```bash
mkdir task-management
cd task-management
npm init -y
npm install typescript --save-dev
npx tsc --init
```

2. Update tsconfig.json:
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

3. Create the project structure and files as shown above.

4. Compile and run:
```bash
npx tsc
node dist/index.js
```

## Module vs Namespace: Modern Approach

Modern TypeScript applications prefer module system over namespaces. Here's why:

1. **Explicit Dependencies**: Modules clearly show dependencies between files
2. **Tree Shaking**: Bundlers like webpack can remove unused code
3. **Lazy Loading**: Modules can be loaded on demand for better performance
4. **Compatibility**: Better compatibility with modern JavaScript and other libraries

## Next Steps
[Utility Types (Partial, Required, etc.)](11_utility_types.md) ko padhein. 