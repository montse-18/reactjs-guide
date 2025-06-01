# Chapter 9: Testing in React

## Introduction
This chapter explores **testing** in **React** applications using **TypeScript**. Writing tests ensures the correctness of component functionality and program logic, preventing future errors. The focus is on using **Jest** and **React Testing Library** to write unit tests and rendering tests. We’ll also review best practices for testing to make our code reliable and scalable.

## Introduction to Testing Tools
### Jest
**Jest** is a testing framework included by default in Create React App projects. Its key features include:
- Fast test execution
- Support for assertions (e.g., `expect`)
- Ability to mock functions and modules

### React Testing Library
**React Testing Library** is a library for testing user interfaces, focusing on simulating user behavior. It:
- Renders components in a real DOM
- Tests user interactions rather than internal details
- Is fully compatible with TypeScript

## Setup and Installation
If you’re using Create React App with TypeScript, Jest and React Testing Library are pre-installed. Otherwise, install them:
```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
```

In the `src/setupTests.ts` file, import `jest-dom`:
```typescript
import '@testing-library/jest-dom';
```

## Writing Unit Tests
Unit tests verify the functionality of independent functions and logic.

### Example: Testing a Simple Function
```typescript
// src/utils/math.ts
export const add = (a: number, b: number): number => a + b;

// src/utils/math.test.ts
import { add } from './math';

describe('add function', () => {
  test('should correctly add two numbers', () => {
    expect(add(2, 3)).toBe(5);
    expect(add(-1, 1)).toBe(0);
    expect(add(0, 0)).toBe(0);
  });
});
```
**Explanation**:
- `describe` groups related tests.
- `test` executes a specific test case.
- `expect` verifies the result.

## Writing Rendering Tests
Rendering tests check component rendering and user interactions.

### Example: Testing a Simple Component
```typescript
// src/components/Counter.tsx
import React, { useState } from 'react';

const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <p data-testid="count">Counter: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
};

export default Counter;

// src/components/Counter.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

describe('Counter component', () => {
  test('should display the initial counter value', () => {
    render(<Counter />);
    expect(screen.getByTestId('count')).toHaveTextContent('Counter: 0');
  });

  test('should increment when the button is clicked', () => {
    render(<Counter />);
    const button = screen.getByText('Increment');
    fireEvent.click(button);
    expect(screen.getByTestId('count')).toHaveTextContent('Counter: 1');
  });
});
```
**Explanation**:
- `render` renders the component in the DOM.
- `screen.getByTestId` locates elements using the `data-testid` attribute.
- `fireEvent.click` simulates a user click.

### Testing with TypeScript
To ensure correct types, use the types provided by React Testing Library:
```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, test, expect } from '@jest/globals';
```

## More Complex Tests
### Testing a Component with Props
```typescript
// src/components/Greeting.tsx
import React from 'react';

interface GreetingProps {
  name: string;
}

const Greeting: React.FC<GreetingProps> = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};

export default Greeting;

// src/components/Greeting.test.tsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import Greeting from './Greeting';

describe('Greeting component', () => {
  test('should display the provided name', () => {
    render(<Greeting name="Ali" />);
    expect(screen.getByText('Hello, Ali!')).toBeInTheDocument();
  });
});
```

### Testing Asynchronous Interactions
For testing API requests, use mocking:
```typescript
// src/components/UserList.tsx
import React, { useState, useEffect } from 'react';

interface User {
  id: number;
  name: string;
}

const UserList: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/users')
      .then(res => res.json())
      .then(data => setUsers(data));
  }, []);

  return (
    <ul>
      {users.map(user => (
        <li key={user.id} data-testid="user">
          {user.name}
        </li>
      ))}
    </ul>
  );
};

export default UserList;

// src/components/UserList.test.tsx
import React from 'react';
import { render, screen, waitFor } from '@testing-library/react';
import UserList from './UserList';

global.fetch = jest.fn(() =>
  Promise.resolve({
    json: () => Promise.resolve([{ id: 1, name: 'Ali' }])
  } as Response)
);

describe('UserList component', () => {
  test('should display the list of users', async () => {
    render(<UserList />);
    await waitFor(() => {
      expect(screen.getByTestId('user')).toHaveTextContent('Ali');
    });
  });
});
```
**Explanation**:
- `global.fetch` is mocked to simulate an API request.
- `waitFor` waits for asynchronous operations to complete.

## Best Practices for Testing
- **Focus on User Behavior**: Test observable behavior (e.g., rendered text) rather than internal details (e.g., state).
- **Use data-testid**: Identify elements in tests using the `data-testid` attribute.
- **Independent Tests**: Ensure each test is independent and does not rely on other tests.
- **Test Coverage**: Use tools like `npm test -- --coverage` to check test coverage.
- **Mock Dependencies**: Mock APIs and external modules.
- **TypeScript Types**: Ensure props and state types are respected in tests.

## Practical Exercises
1. **Exercise 1**: Write a simple function (e.g., calculating factorial) and create unit tests for it using Jest.
2. **Exercise 2**: Build a form component with two inputs (name and email) and write tests for correct rendering and form submission.
3. **Exercise 3**: Create a component that displays API data and write tests for rendering the data using a mocked request.
4. **Exercise 4**: Build a component with a button and a counter, and test that the counter increments correctly after multiple clicks.

## Conclusion
In this chapter, we explored Jest and React Testing Library for writing tests in React. We learned how to write unit and rendering tests and used TypeScript to make our code safer. In the next chapter, we will tackle the **final project**, implementing all the concepts learned in a complete application.