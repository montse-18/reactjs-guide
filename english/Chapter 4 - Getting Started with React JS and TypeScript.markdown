# Chapter 4: Getting Started with React JS and TypeScript

## Introduction
In this chapter, we'll enter the world of **React JS** and learn how to build React projects using **TypeScript**. We'll focus on core React concepts like components, Props, and State, preparing you to build a simple application. By combining TypeScript, our code will become more readable and maintainable.

## What is React JS?
**React JS** is an open-source JavaScript library developed by Facebook for building interactive, single-page user interfaces (SPAs). Its key features include:
- **Component-based architecture**: UI is broken into small, reusable building blocks (components)
- **Virtual DOM**: Optimizes rendering for better performance
- **Unidirectional data flow**: Predictable data flow from parent to child components

TypeScript in React helps us define props and state with explicit types, preventing runtime errors.

## Creating a React Project with TypeScript
We'll use **Create React App** to start a React project with TypeScript:
1. Run this command in your terminal:
```bash
npx create-react-app my-react-app --template typescript
```
2. Navigate to the project folder and run it:
```bash
cd my-react-app
npm start
```
3. Your browser will automatically open `http://localhost:3000` showing the default React page.

### Project Structure
After creating the project, the folder structure will look like:
```
my-react-app/
  ├── src/
  │   ├── App.tsx          # Main component
  │   ├── index.tsx        # Application entry point
  │   ├── App.css          # Styles
  │   └── ...              # Other files
  ├── public/
  ├── package.json         # Dependencies and scripts
  └── tsconfig.json        # TypeScript configuration
```

## Core React Concepts
### Components
Components are the building blocks of React UIs. They can be defined as functions or classes. We'll use **functional components** as they're more modern and simpler.

Example: Simple component with TypeScript:
```typescript
import React from 'react';

const Greeting: React.FC = () => {
  return <h1>Hello, welcome to React!</h1>;
};

export default Greeting;
```

### Props
Props (Properties) pass data from parent to child components. With TypeScript, we can specify prop types:
```typescript
interface GreetingProps {
  name: string;
}

const Greeting: React.FC<GreetingProps> = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};

export default Greeting;
```
Usage in another component:
```typescript
import Greeting from './Greeting';

const App: React.FC = () => {
  return <Greeting name="Ali" />;
};
```

### State
State manages a component's internal dynamic data. We use the `useState` hook to manage state in functional components:
```typescript
import React, { useState } from 'react';

const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};

export default Counter;
```

## React Project Structure
For better organization, follow this structure:
```
src/
  ├── components/       # Reusable components
  ├── pages/            # Page-level components
  ├── styles/           # CSS or other style files
  ├── types/            # TypeScript type definitions
  └── App.tsx           # Main component
```

Example: Type definition in `types/index.ts`:
```typescript
export interface User {
  id: number;
  name: string;
}
```

## Hands-on Exercises
1. **Exercise 1**: Create a `Welcome` component that accepts a `message` (string) prop and displays it in a `<p>` tag.
2. **Exercise 2**: Build a `TodoList` component that accepts an array of strings as props and displays them as a list (`<ul>`).
3. **Exercise 3**: Create a `Counter` component with buttons to increment and decrement a number. Use `useState` with TypeScript.
4. **Exercise 4**: Create a new project with Create React App and TypeScript, then add a simple component to `App.tsx` that displays your name.

## Conclusion
In this chapter, we covered core React JS concepts and using TypeScript in React projects. We set up our first project and worked with components, Props, and State. Next, we'll explore **State Management** in React, diving into advanced hooks and Context API.