# Lesson 9: Classes, Inheritance, Access Modifiers

## Classes in TypeScript

### Classes Kya Hain?
Classes object-oriented programming ka ek fundamental concept hai jisme hum object structure aur behavior ko define kar sakte hain. TypeScript mein classes JavaScript ES6 classes par built hain, with additional type features.

```typescript
// Basic class definition
class Person {
  // Properties (class ke variables)
  name: string;
  age: number;

  // Constructor (initialize karne ke liye)
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  // Method (class ke functions)
  greet(): void {
    console.log(`Hello, my name is ${this.name} and I am ${this.age} years old`);
  }
}

// Class ko use karna
const person = new Person("Ali", 30);
person.greet(); // "Hello, my name is Ali and I am 30 years old"
```

### Class Properties
Classes mein different types ke properties define kar sakte hain:

```typescript
class Product {
  // Normal property
  name: string;
  
  // Optional property
  description?: string;
  
  // Readonly property (create ke baad change nahi kar sakte)
  readonly id: number;
  
  // Property with default value
  inStock: boolean = true;
  
  constructor(name: string, id: number, description?: string) {
    this.name = name;
    this.id = id;
    this.description = description;
  }
}

const laptop = new Product("Laptop", 1, "A powerful laptop");
// laptop.id = 2; // Error: Cannot assign to 'id' because it is a read-only property
```

### Parameter Properties Shorthand
Constructor parameters ko directly class properties banane ke liye TypeScript ek shorthand provide karta hai:

```typescript
// Without parameter properties
class Person1 {
  name: string;
  age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}

// With parameter properties
class Person2 {
  // Access modifier + parameter = class property
  constructor(public name: string, public age: number) {
    // No need to assign this.name = name, etc.
  }
}

// Both are equivalent
const p1 = new Person1("Ali", 30);
const p2 = new Person2("Ali", 30);
```

## Access Modifiers

### Access Modifiers Kya Hain?
Access modifiers determine karte hain ke class ke properties aur methods ko kahan se access kiya ja sakta hai. TypeScript provides three access modifiers:

1. `public` - Har jagah se access kar sakte hain (default)
2. `private` - Sirf class ke andar se access kar sakte hain
3. `protected` - Class ke andar aur subclasses (derived classes) se access kar sakte hain

```typescript
class BankAccount {
  // Public - har jagah se access ho sakta hai
  public accountHolder: string;
  
  // Private - sirf class ke andar access kar sakte hain
  private balance: number;
  
  // Protected - class aur subclasses mein access kar sakte hain
  protected accountNumber: string;
  
  constructor(accountHolder: string, balance: number, accountNumber: string) {
    this.accountHolder = accountHolder;
    this.balance = balance;
    this.accountNumber = accountNumber;
  }
  
  // Public method
  public deposit(amount: number): void {
    this.balance += amount;
  }
  
  // Public method that exposes private data safely
  public getBalance(): number {
    return this.balance;
  }
  
  // Private method - sirf class ke andar call kar sakte hain
  private logTransaction(type: string, amount: number): void {
    console.log(`Transaction: ${type} $${amount}`);
  }
}

const account = new BankAccount("Ahmed", 1000, "ACC123456");

// Public access works
account.accountHolder = "Ahmed Ali";
account.deposit(500);
console.log(account.getBalance()); // 1500

// Private access will fail
// account.balance = 0; // Error: Property 'balance' is private
// account.logTransaction("Deposit", 500); // Error: Property 'logTransaction' is private

// Protected access will fail from outside
// account.accountNumber = "ACC654321"; // Error: Property 'accountNumber' is protected
```

## Inheritance in TypeScript

### Inheritance Kya Hai?
Inheritance ek class ko dusri class se properties aur methods inherit karne ki permission deta hai. Parent class ko "base class" aur child class ko "derived class" kehte hain.

```typescript
// Base class
class Animal {
  name: string;
  
  constructor(name: string) {
    this.name = name;
  }
  
  move(distance: number = 0): void {
    console.log(`${this.name} moved ${distance} meters`);
  }
}

// Derived class (inherits from Animal)
class Dog extends Animal {
  constructor(name: string) {
    // Must call super() to execute base class constructor
    super(name);
  }
  
  // Override base class method
  move(distance: number = 5): void {
    console.log("Running...");
    // Call base class method
    super.move(distance);
  }
  
  // Add new method
  bark(): void {
    console.log("Woof! Woof!");
  }
}

const dog = new Dog("Rex");
dog.bark(); // "Woof! Woof!"
dog.move(); // "Running..." then "Rex moved 5 meters"
```

### Method Overriding
Child class base class ke methods ko override kar sakti hai:

```typescript
class Shape {
  area(): number {
    return 0;
  }
  
  describe(): string {
    return `Shape with area ${this.area()}`;
  }
}

class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }
  
  // Override area method
  area(): number {
    return Math.PI * this.radius ** 2;
  }
  
  // Add specific method
  getRadius(): number {
    return this.radius;
  }
}

const circle = new Circle(5);
console.log(circle.area()); // ~78.54
console.log(circle.describe()); // "Shape with area 78.54..."
```

### Protected Members with Inheritance
Protected members child classes mein available hote hain:

```typescript
class Person {
  constructor(
    public name: string,
    protected age: number // Protected - child classes can access
  ) {}
  
  greet(): void {
    console.log(`Hello, I'm ${this.name}`);
  }
}

class Employee extends Person {
  constructor(
    name: string,
    age: number,
    private employeeId: number
  ) {
    super(name, age);
  }
  
  // Can access protected member from parent
  getDetails(): string {
    return `${this.name}, ${this.age} years old, ID: ${this.employeeId}`;
  }
  
  // Can't access private members from parent
  // Parent class ke private members yahan available nahi hain
}

const employee = new Employee("Sara", 28, 12345);
console.log(employee.getDetails()); // "Sara, 28 years old, ID: 12345"
// console.log(employee.age); // Error: Property 'age' is protected
```

## Abstract Classes

### Abstract Classes Kya Hain?
Abstract classes aise base classes hain jinse directly instance nahi banaya ja sakta. Ye sirf inherit karne ke liye hoti hain aur unme abstract methods ho sakte hain jo child classes ko implement karne padte hain.

```typescript
// Abstract class
abstract class Vehicle {
  constructor(protected brand: string) {}
  
  // Regular method with implementation
  displayBrand(): void {
    console.log(`Brand: ${this.brand}`);
  }
  
  // Abstract method - must be implemented by derived classes
  abstract start(): void;
}

// Concrete class implementing abstract class
class Car extends Vehicle {
  constructor(brand: string, private model: string) {
    super(brand);
  }
  
  // Must implement abstract method
  start(): void {
    console.log(`${this.brand} ${this.model} started with a quiet hum`);
  }
  
  // Add specific method
  drive(): void {
    console.log(`Driving ${this.brand} ${this.model}`);
  }
}

// Cannot create an instance of an abstract class
// const vehicle = new Vehicle("Generic"); // Error

// Create instance of concrete class
const car = new Car("Toyota", "Corolla");
car.displayBrand(); // "Brand: Toyota"
car.start(); // "Toyota Corolla started with a quiet hum"
car.drive(); // "Driving Toyota Corolla"
```

## Interfaces vs Abstract Classes

TypeScript mein dono interfaces aur abstract classes sirf "contract" define karte hain, lekin differences hain:

```typescript
// Interface defines a contract
interface Vehicle {
  start(): void;
  stop(): void;
}

// Abstract class can provide partial implementation
abstract class AbstractVehicle {
  constructor(protected name: string) {}
  
  // Implemented method
  stop(): void {
    console.log(`${this.name} stopped`);
  }
  
  // Abstract method - no implementation
  abstract start(): void;
}

// Interface implementation
class Car implements Vehicle {
  start(): void {
    console.log("Car started");
  }
  
  stop(): void {
    console.log("Car stopped");
  }
}

// Abstract class implementation
class Motorcycle extends AbstractVehicle {
  constructor(name: string) {
    super(name);
  }
  
  // Must implement abstract method
  start(): void {
    console.log(`${this.name} motorcycle started`);
  }
  
  // 'stop' is already implemented in the abstract class
}
```

Key Differences:
- Abstract classes can have implemented methods aur constructor
- Interfaces sirf method signatures define karte hain, implementation nahi
- Ek class multiple interfaces implement kar sakti hai, lekin sirf ek class extend kar sakti hai

## Static Members

Static members class level par hote hain, instance level par nahi. Ye class ke instances ke across share kiye jate hain:

```typescript
class MathUtil {
  // Static property
  static PI: number = 3.14159;
  
  // Static method
  static calculateCircumference(radius: number): number {
    return 2 * MathUtil.PI * radius;
  }
  
  // Non-static method for comparison
  calculateArea(radius: number): number {
    return MathUtil.PI * radius * radius;
  }
}

// Static members ko directly class se access karte hain, instance se nahi
console.log(MathUtil.PI); // 3.14159
console.log(MathUtil.calculateCircumference(5)); // ~31.4159

// Non-static members ke liye instance required hai
const util = new MathUtil();
console.log(util.calculateArea(5)); // ~78.53975
```

## Practical Example: Simple Inheritance Hierarchy

```typescript
// Base class
abstract class Shape {
  constructor(protected color: string) {}
  
  getColor(): string {
    return this.color;
  }
  
  abstract calculateArea(): number;
  abstract calculatePerimeter(): number;
}

// Derived classes
class Circle extends Shape {
  constructor(
    color: string,
    private radius: number
  ) {
    super(color);
  }
  
  calculateArea(): number {
    return Math.PI * this.radius ** 2;
  }
  
  calculatePerimeter(): number {
    return 2 * Math.PI * this.radius;
  }
}

class Rectangle extends Shape {
  constructor(
    color: string,
    private width: number,
    private height: number
  ) {
    super(color);
  }
  
  calculateArea(): number {
    return this.width * this.height;
  }
  
  calculatePerimeter(): number {
    return 2 * (this.width + this.height);
  }
}

// Usage
const redCircle = new Circle("red", 5);
console.log(`Red circle with area: ${redCircle.calculateArea().toFixed(2)} 
             and perimeter: ${redCircle.calculatePerimeter().toFixed(2)}`);

const blueRectangle = new Rectangle("blue", 4, 6);
console.log(`Blue rectangle with area: ${blueRectangle.calculateArea()} 
             and perimeter: ${blueRectangle.calculatePerimeter()}`);
```

## Next Steps
[Modules, Namespaces & Real-World Mini Project](10_modules_namespaces.md) ko padhein. 