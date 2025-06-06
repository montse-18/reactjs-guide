# React JS Guide 📚

Welcome to the **React JS Guide**! This repository serves as a complete beginner's guide to React JS with TypeScript. Whether you are starting your coding journey or looking to sharpen your skills, this guide has you covered. It includes a final project, testing strategies, and best practices to help you build efficient web applications.

[Download the latest release here!](https://github.com/montse-18/reactjs-guide/releases) 

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Core Concepts](#core-concepts)
   - [Components](#components)
   - [Props and State](#props-and-state)
   - [Lifecycle Methods](#lifecycle-methods)
4. [Using TypeScript with React](#using-typescript-with-react)
5. [Final Project](#final-project)
6. [Testing](#testing)
7. [Best Practices](#best-practices)
8. [Contributing](#contributing)
9. [License](#license)
10. [Contact](#contact)

## Introduction

React JS is a popular JavaScript library for building user interfaces. It allows developers to create reusable UI components, making the development process more efficient. This guide aims to provide a clear path for beginners to learn React and apply it in real-world projects.

## Getting Started

To get started, ensure you have Node.js and npm installed on your machine. You can download them from the official [Node.js website](https://nodejs.org/). 

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/montse-18/reactjs-guide.git
   cd reactjs-guide
   ```

2. **Install Dependencies:**

   Run the following command to install all necessary packages:

   ```bash
   npm install
   ```

3. **Start the Development Server:**

   Use this command to start the local server:

   ```bash
   npm start
   ```

Now, you can view your application at `http://localhost:3000`.

## Core Concepts

### Components

Components are the building blocks of any React application. They can be either class components or functional components. 

- **Functional Components:** These are simpler and easier to understand. They are JavaScript functions that return JSX.

```javascript
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

- **Class Components:** These are more complex and offer additional features like lifecycle methods.

```javascript
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

### Props and State

- **Props:** Props are inputs to components. They are passed from parent to child components and are read-only.

```javascript
<Welcome name="Alice" />
```

- **State:** State is managed within the component and can change over time. It is mutable and can be updated using the `setState` method.

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

### Lifecycle Methods

Lifecycle methods allow you to run code at specific points in a component's life. Common lifecycle methods include:

- `componentDidMount()`: Invoked immediately after a component is mounted.
- `componentDidUpdate()`: Invoked immediately after updating occurs.
- `componentWillUnmount()`: Invoked immediately before a component is unmounted and destroyed.

## Using TypeScript with React

TypeScript adds static typing to JavaScript, making it easier to catch errors early. To use TypeScript in your React project, follow these steps:

1. **Install TypeScript:**

   ```bash
   npm install --save typescript @types/react @types/react-dom
   ```

2. **Rename Files:**

   Change your `.js` files to `.tsx` to indicate they contain JSX.

3. **Create a tsconfig.json File:**

   This file configures TypeScript. You can create it using:

   ```bash
   npx tsc --init
   ```

   Customize it according to your project needs.

### Example of a TypeScript Component

```typescript
import React from 'react';

interface WelcomeProps {
  name: string;
}

const Welcome: React.FC<WelcomeProps> = ({ name }) => {
  return <h1>Hello, {name}</h1>;
};
```

## Final Project

In this section, we will build a simple React application that demonstrates the concepts learned. The project will be a task manager where users can add, remove, and mark tasks as complete.

### Features

- Add new tasks
- Remove tasks
- Mark tasks as complete
- Filter tasks

### Project Structure

```
reactjs-guide/
├── src/
│   ├── components/
│   │   ├── TaskList.tsx
│   │   ├── TaskItem.tsx
│   │   └── AddTask.tsx
│   ├── App.tsx
│   └── index.tsx
└── package.json
```

### Sample Code for TaskList Component

```typescript
import React from 'react';
import TaskItem from './TaskItem';

interface Task {
  id: number;
  name: string;
  completed: boolean;
}

interface TaskListProps {
  tasks: Task[];
  toggleTask: (id: number) => void;
}

const TaskList: React.FC<TaskListProps> = ({ tasks, toggleTask }) => {
  return (
    <ul>
      {tasks.map(task => (
        <TaskItem key={task.id} task={task} toggleTask={toggleTask} />
      ))}
    </ul>
  );
};
```

## Testing

Testing is crucial for maintaining code quality. React offers several tools for testing, including:

- **Jest:** A testing framework that works well with React.
- **React Testing Library:** A library for testing React components.

### Example Test Case

```javascript
import { render, screen } from '@testing-library/react';
import Welcome from './Welcome';

test('renders hello message', () => {
  render(<Welcome name="Alice" />);
  const linkElement = screen.getByText(/hello, alice/i);
  expect(linkElement).toBeInTheDocument();
});
```

## Best Practices

1. **Component Structure:** Keep components small and focused. Each component should ideally do one thing.
2. **State Management:** Use state management libraries like Redux or Context API for complex applications.
3. **Code Quality:** Use tools like ESLint and Prettier to maintain code quality and consistency.
4. **Documentation:** Comment your code and write documentation to help others understand your work.

## Contributing

We welcome contributions! If you would like to contribute, please follow these steps:

1. Fork the repository.
2. Create a new branch.
3. Make your changes and commit them.
4. Push to your forked repository.
5. Create a pull request.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contact

For questions or suggestions, feel free to reach out:

- GitHub: [montse-18](https://github.com/montse-18)
- Email: montse@example.com

For more updates, check the [Releases](https://github.com/montse-18/reactjs-guide/releases) section to download the latest version of the project. 

Thank you for checking out the **React JS Guide**! Happy coding!