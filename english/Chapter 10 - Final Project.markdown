# Chapter 10: Final Project

## Introduction
In this chapter, we combine all the concepts learned in previous chapters into a **final project**. The goal is to build a complete **React** application using **TypeScript**, incorporating state management, routing, API data fetching, and optimization. We will create a **Todo App** with features for adding, editing, deleting, and filtering tasks. This project helps you apply your skills in a real-world scenario.

## Project Overview
The Todo App includes the following features:
- **Home Page**: Displays a list of tasks with filtering by status (all, completed, pending).
- **Add/Edit Page**: A form for adding or editing tasks.
- **Data Fetching and Storage**: Uses an API to manage tasks (simulated with `jsonplaceholder`).
- **Routing**: Uses React Router for navigation between pages.
- **Optimization**: Employs `useMemo` and `useCallback` for performance improvements.

## Project Structure
Suggested project structure:
```
src/
  ├── components/
  │   ├── TaskList.tsx        # Displays the task list
  │   ├── TaskForm.tsx        # Form for adding/editing tasks
  │   ├── FilterButtons.tsx   # Filter buttons
  ├── pages/
  │   ├── Home.tsx            # Home page
  │   ├── TaskEdit.tsx        # Task edit page
  ├── types/
  │   ├── index.ts            # Type definitions
  ├── hooks/
  │   ├── useTasks.ts         # Custom hook for API management
  ├── styles/
  │   ├── global.css          # Global styles
  ├── App.tsx                 # Main component
  └── index.tsx               # Entry point
```

## Defining Types
First, define the required types in `src/types/index.ts`:
```typescript
export interface Task {
  id: number;
  title: string;
  completed: boolean;
}

export type FilterType = 'all' | 'completed' | 'pending';
```

## Custom Hook for Data Fetching
In `src/hooks/useTasks.ts`, create a custom hook to manage API requests:
```typescript
import { useState, useEffect } from 'react';
import axios from 'axios';
import { Task } from '../types';

interface TaskState {
  tasks: Task[];
  loading: boolean;
  error: string | null;
}

export const useTasks = () => {
  const [state, setState] = useState<TaskState>({ tasks: [], loading: true, error: null });

  const fetchTasks = async () => {
    try {
      const response = await axios.get<Task[]>('https://jsonplaceholder.typicode.com/todos?_limit=10');
      setState({ tasks: response.data, loading: false, error: null });
    } catch (err) {
      setState(prev => ({ ...prev, loading: false, error: 'Error fetching tasks' }));
    }
  };

  const addTask = async (title: string) => {
    try {
      const response = await axios.post<Task>('https://jsonplaceholder.typicode.com/todos', {
        title,
        completed: false,
        userId: 1,
      });
      setState(prev => ({ ...prev, tasks: [...prev.tasks, response.data] }));
    } catch (err) {
      setState(prev => ({ ...prev, error: 'Error adding task' }));
    }
  };

  const updateTask = async (id: number, updates: Partial<Task>) => {
    try {
      const response = await axios.put<Task>(`https://jsonplaceholder.typicode.com/todos/${id}`, updates);
      setState(prev => ({
        ...prev,
        tasks: prev.tasks.map(task => (task.id === id ? response.data : task)),
      }));
    } catch (err) {
      setState(prev => ({ ...prev, error: 'Error updating task' }));
    }
  };

  const deleteTask = async (id: number) => {
    try {
      await axios.delete(`https://jsonplaceholder.typicode.com/todos/${id}`);
      setState(prev => ({ ...prev, tasks: prev.tasks.filter(task => task.id !== id) }));
    } catch (err) {
      setState(prev => ({ ...prev, error: 'Error deleting task' }));
    }
  };

  useEffect(() => {
    fetchTasks();
  }, []);

  return { ...state, addTask, updateTask, deleteTask };
};
```

## Main Components
### TaskList.tsx
Component for displaying and managing the task list:
```typescript
import React, { useCallback } from 'react';
import { Link } from 'react-router-dom';
import { Task } from '../types';

interface TaskListProps {
  tasks: Task[];
  onToggle: (id: number, completed: boolean) => void;
  onDelete: (id: number) => void;
}

const TaskList: React.FC<TaskListProps> = React.memo(({ tasks, onToggle, onDelete }) => {
  return (
    <ul>
      {tasks.map(task => (
        <li key={task.id}>
          <input
            type="checkbox"
            checked={task.completed}
            onChange={() => onToggle(task.id, !task.completed)}
          />
          <span style={{ textDecoration: task.completed ? 'line-through' : 'none' }}>
            {task.title}
          </span>
          <Link to={`/edit/${task.id}`}>Edit</Link>
          <button onClick={() => onDelete(task.id)}>Delete</button>
        </li>
      ))}
    </ul>
  );
});

export default TaskList;
```

### TaskForm.tsx
Form component for adding or editing tasks:
```typescript
import React, { useState, useEffect } from 'react';
import { useNavigate, useParams } from 'react-router-dom';
import { Task } from '../types';

interface TaskFormProps {
  tasks: Task[];
  onAdd: (title: string) => void;
  onUpdate: (id: number, updates: Partial<Task>) => void;
}

const TaskForm: React.FC<TaskFormProps> = ({ tasks, onAdd, onUpdate }) => {
  const { id } = useParams<{ id: string }>();
  const navigate = useNavigate();
  const [title, setTitle] = useState<string>('');

  useEffect(() => {
    if (id) {
      const task = tasks.find(task => task.id === parseInt(id));
      if (task) setTitle(task.title);
    }
  }, [id, tasks]);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (!title.trim()) return;

    if (id) {
      onUpdate(parseInt(id), { title });
    } else {
      onAdd(title);
    }
    navigate('/');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={title}
        onChange={e => setTitle(e.target.value)}
        placeholder="Task title"
      />
      <button type="submit">{id ? 'Edit' : 'Add'}</button>
    </form>
  );
};

export default TaskForm;
```

### FilterButtons.tsx
Component for filtering tasks:
```typescript
import React from 'react';
import { FilterType } from '../types';

interface FilterButtonsProps {
  filter: FilterType;
  setFilter: (filter: FilterType) => void;
}

const FilterButtons: React.FC<FilterButtonsProps> = ({ filter, setFilter }) => {
  return (
    <div>
      <button onClick={() => setFilter('all')} disabled={filter === 'all'}>
        All
      </button>
      <button onClick={() => setFilter('completed')} disabled={filter === 'completed'}>
        Completed
      </button>
      <button onClick={() => setFilter('pending')} disabled={filter === 'pending'}>
        Pending
      </button>
    </div>
  );
};

export default FilterButtons;
```

## Main Pages
### Home.tsx
Home page for displaying tasks and filters:
```typescript
import React, { useState, useMemo } from 'react';
import { Link } from 'react-router-dom';
import TaskList from '../components/TaskList';
import FilterButtons from '../components/FilterButtons';
import { useTasks } from '../hooks/useTasks';
import { Task, FilterType } from '../types';

const Home: React.FC = () => {
  const { tasks, loading, error, updateTask, deleteTask } = useTasks();
  const [filter, setFilter] = useState<FilterType>('all');

  const filteredTasks = useMemo(() => {
    if (filter === 'completed') return tasks.filter(task => task.completed);
    if (filter === 'pending') return tasks.filter(task => !task.completed);
    return tasks;
  }, [tasks, filter]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <div>
      <h1>Task Manager</h1>
      <Link to="/add">Add New Task</Link>
      <FilterButtons filter={filter} setFilter={setFilter} />
      <TaskList
        tasks={filteredTasks}
        onToggle={(id, completed) => updateTask(id, { completed })}
        onDelete={deleteTask}
      />
    </div>
  );
};

export default Home;
```

### TaskEdit.tsx
Page for adding or editing tasks:
```typescript
import React from 'react';
import TaskForm from '../components/TaskForm';
import { useTasks } from '../hooks/useTasks';

const TaskEdit: React.FC = () => {
  const { tasks, addTask, updateTask } = useTasks();

  return <TaskForm tasks={tasks} onAdd={addTask} onUpdate={updateTask} />;
};

export default TaskEdit;
```

## Main Component (App.tsx)
```typescript
import React from 'react';
import { Routes, Route } from 'react-router-dom';
import Home from './pages/Home';
import TaskEdit from './pages/TaskEdit';

const App: React.FC = () => {
  return (
    <div style={{ padding: '20px' }}>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/add" element={<TaskEdit />} />
        <Route path="/edit/:id" element={<TaskEdit />} />
      </Routes>
    </div>
  );
};

export default App;
```

## Styles (styles/global.css)
```css
body {
  font-family: Arial, sans-serif;
  direction: ltr;
  text-align: left;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  display: flex;
  gap: 10px;
  align-items: center;
  margin-bottom: 10px;
}

button {
  margin: 5px;
  padding: 5px 10px;
}

input, textarea {
  margin: 5px;
  padding: 5px;
}
```

## Final Notes and Resources for Further Learning
- **Testing**: Write tests for components and hooks to ensure quality (see Chapter 9).
- **Further Optimization**: Use `React.memo` and `useCallback` for additional components.
- **Recommended Resources**:
  - Official React Documentation: [react.dev](https://react.dev)
  - React Router Documentation: [reactrouter.com](https://reactrouter.com)
  - TypeScript Documentation: [typescriptlang.org](https://typescriptlang.org)
  - Free tutorials on YouTube or online courses (e.g., Udemy, FreeCodeCamp)

## Practical Exercises
1. **Exercise 1**: Add a feature to filter tasks by title (search functionality).
2. **Exercise 2**: Write tests for `TaskList` and `TaskForm` to verify rendering and interactions.
3. **Exercise 3**: Add a feature to mark all tasks as completed.
4. **Exercise 4**: Enhance the project’s styles using Tailwind CSS.

## Conclusion
In this chapter, we built a complete Todo App with React and TypeScript, incorporating routing, state management, API data fetching, and optimization. This project combined all the concepts learned, preparing you for building real-world applications. With further practice and exploration of the recommended resources, you can elevate your skills to the next level.