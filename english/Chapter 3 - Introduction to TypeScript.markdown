# Chapter 3: Introduction to TypeScript

## Introduction
In this chapter, we'll explore **TypeScript**, a superset of JavaScript that adds static typing capabilities. TypeScript helps you write code with fewer errors and better maintainability, which is especially valuable in large projects like React applications. Our goal is to learn TypeScript fundamentals and prepare you to use it in React projects.

## What is TypeScript and Why Use It?
TypeScript is an open-source programming language developed by Microsoft that builds on JavaScript. Its key features include:
- **Static typing**: Specifying data types (number, string, etc.) to prevent common errors
- **Better tooling**: Enhanced editor support (like VS Code) for autocompletion and error detection
- **JavaScript compatibility**: All valid JavaScript code works in TypeScript

TypeScript helps manage React components and props with greater confidence.

## Installing and Configuring TypeScript
1. Ensure **Node.js** is installed (see Chapter 2)
2. Install TypeScript globally:
```bash
npm install -g typescript
```
3. Verify installation:
```bash
tsc --version
```
4. Create a simple project:
```bash
mkdir my-typescript-project
cd my-typescript-project
npm init -y
npm install typescript --save-dev
```
5. Generate TypeScript configuration:
```bash
npx tsc --init
```
This creates `tsconfig.json` for compiler settings.

## Data Types, Interfaces, and Type Annotations
### Basic Types
TypeScript supports JavaScript types and adds additional ones:
```typescript
let name: string = "Ali"; // string
let age: number = 25; // number
let isStudent: boolean = true; // boolean
let hobbies: string[] = ["Reading", "Sports"]; // string array
let anything: any = 42; // any type (avoid unless necessary)
```

### Interfaces
Interfaces define object structures:
```typescript
interface Person {
  name: string;
  age: number;
  isStudent?: boolean; // optional
}

const person: Person = {
  name: "Sara",
  age: 20
};

function greet(person: Person) {
  return `Hello, ${person.name}! Age: ${person.age}`;
}
console.log(greet(person)); // Output: Hello, Sara! Age: 20
```

### Type Aliases
Define custom types with `type`:
```typescript
type ID = string | number; // Union Type
let userId: ID = "abc123"; // can be string or number
```

## Using TypeScript in JavaScript Projects
Gradually add TypeScript to JavaScript projects:
1. Rename `script.js` to `script.ts`
2. Add type annotations:
```typescript
// Before (JavaScript)
function add(a, b) {
  return a + b;
}

// After (TypeScript)
function add(a: number, b: number): number {
  return a + b;
}
```
3. Compile the file:
```bash
tsc script.ts
```
This generates executable `script.js`.

### Example: DOM Manipulation
TypeScript provides specific DOM types:
```typescript
const button = document.querySelector("#myButton") as HTMLButtonElement;
button.addEventListener("click", () => {
  button.textContent = "Clicked!";
});
```

## Hands-on Exercises
1. **Exercise 1**: Define a `Book` interface with title (string), author (string), and publication year (number). Create an object of this type.
2. **Exercise 2**: Write a function that calculates the average of a number array (with proper types).
3. **Exercise 3**: Create an HTML file with a button and paragraph. Write TypeScript to change paragraph text on button click.
4. **Exercise 4**: Create a union type for `status` that only accepts `"active"`, `"inactive"`, or `"pending"`.

## Conclusion
In this chapter, we covered TypeScript fundamentals, installation, and using types/interfaces. TypeScript helps write more reliable code, which will be invaluable in React projects. Next, we'll enter the world of **React JS with TypeScript** and build our first project.