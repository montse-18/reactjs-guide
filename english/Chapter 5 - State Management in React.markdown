# Chapter 5: State Management in React

## Introduction
In this chapter, we'll explore **State** management techniques in **React** applications using **TypeScript**. State is crucial for managing dynamic data in interactive applications. We'll focus on `useState` and `useReducer` hooks, then explore **Context API** for application-level state management. Our goal is to learn effective state management techniques while writing clean, type-safe code.

## Using useState
The `useState` hook is the simplest way to manage state in functional components. With TypeScript, we can explicitly define state data types to prevent errors.

### Example: Managing a Simple Form
```typescript
import React, { useState } from 'react';

interface FormState {
  name: string;
  email: string;
}

const Form: React.FC = () => {
  const [formData, setFormData] = useState<FormState>({
    name: '',
    email: ''
  });

  const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log('Form data:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="name"
        value={formData.name}
        onChange={handleChange}
        placeholder="Name"
      />
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="Email"
      />
      <button type="submit">Submit</button>
    </form>
  );
};

export default Form;
```
**Explanation**:
- `FormState` interface defines the state structure
- `useState` initializes state with explicit typing
- `handleChange` dynamically updates input values

## Using useReducer
For complex state logic, we use the `useReducer` hook. Similar to Redux, it's ideal for states with intricate update logic.

### Example: Todo List Management
```typescript
import React, { useReducer } from 'react';

interface Todo {
  id: number;
  text: string;
  completed: boolean;
}

type TodoAction =
  | { type: 'ADD_TODO'; payload: string }
  | { type: 'TOGGLE_TODO'; payload: number };

interface TodoState {
  todos: Todo[];
}

const initialState: TodoState = { todos: [] };

const todoReducer = (state: TodoState, action: TodoAction): TodoState => {
  switch (action.type) {
    case 'ADD_TODO':
      return {
        todos: [...state.todos, { id: Date.now(), text: action.payload, completed: false }]
      };
    case 'TOGGLE_TODO':
      return {
        todos: state.todos.map(todo =>
          todo.id === action.payload ? { ...todo, completed: !todo.completed } : todo
        )
      };
    default:
      return state;
  }
};

const TodoList: React.FC = () => {
  const [state, dispatch] = useReducer(todoReducer, initialState);
  const [input, setInput] = useState<string>('');

  const handleAdd = () => {
    if (input.trim()) {
      dispatch({ type: 'ADD_TODO', payload: input });
      setInput('');
    }
  };

  return (
    <div>
      <input
        type="text"
        value={input}
        onChange={e => setInput(e.target.value)}
        placeholder="New task"
      />
      <button onClick={handleAdd}>Add</button>
      <ul>
        {state.todos.map(todo => (
          <li
            key={todo.id}
            onClick={() => dispatch({ type: 'TOGGLE_TODO', payload: todo.id })}
            style={{ textDecoration: todo.completed ? 'line-through' : 'none' }}
          >
            {todo.text}
          </li>
        ))}
      </ul>
    </div>
  );
};

export default TodoList;
```
**Explanation**:
- `useReducer` works with a reducer function and initial state
- `TodoAction` and `TodoState` types define action and state structures
- Manages a todo list with add/complete functionality

## State Management with Context API
**Context API** shares state between multiple components, ideal for application-level state.

### Example: Theme Management
```typescript
import React, { createContext, useContext, useState } from 'react';

interface ThemeContextType {
  theme: string;
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

const ThemeProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<string>('light');

  const toggleTheme = () => {
    setTheme(prev => (prev === 'light' ? 'dark' : 'light'));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

const useTheme = () => {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used within ThemeProvider');
  return context;
};

const ThemedComponent: React.FC = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <div style={{ background: theme === 'light' ? '#fff' : '#333', color: theme === 'light' ? '#000' : '#fff' }}>
      <p>Current theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
};

const App: React.FC = () => {
  return (
    <ThemeProvider>
      <ThemedComponent />
    </ThemeProvider>
  );
};

export default App;
```
**Explanation**:
- `ThemeContext` shares theme data
- Custom `useTheme` hook simplifies context access
- TypeScript ensures proper context typing

## Best Practices for State Management
- **Use useState for simple state** (forms, counters)
- **Use useReducer for complex state** with multi-step logic
- **Use Context API for shared state**, but avoid overuse to prevent performance issues
- **Define TypeScript types** to catch errors during development
- **Keep state close to usage** for simpler management

## Hands-on Exercises
1. **Exercise 1**: Create a form component managing name, email, and age with `useState`. Define types using TypeScript.
2. **Exercise 2**: Build a shopping list with `useReducer` supporting add/remove items. Define action and state types.
3. **Exercise 3**: Create a context for language management (e.g., English/Persian) and build a component displaying text based on language.
4. **Exercise 4**: Build a simple app combining `useState` and `useReducer` to manage a form and list.

## Conclusion
In this chapter, we explored different state management techniques in React: `useState` for simple cases, `useReducer` for complex logic, and Context API for shared state. TypeScript helped us write type-safe, reliable code. Next, we'll explore **Routing in React** using React Router.