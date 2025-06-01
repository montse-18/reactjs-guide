# فصل ۱۰: پروژه نهایی

## مقدمه
در این فصل، تمام مفاهیم آموخته‌شده در فصل‌های قبلی را در یک **پروژه نهایی** ترکیب می‌کنیم. هدف این پروژه، ساخت یک اپلیکیشن کامل **React** با استفاده از **TypeScript** است که شامل مدیریت State، مسیریابی، دریافت داده از API، و بهینه‌سازی باشد. ما یک اپلیکیشن **مدیریت وظایف (Todo App)** می‌سازیم که قابلیت‌های افزودن، ویرایش، حذف وظایف و فیلتر کردن آن‌ها را دارد. این پروژه به شما کمک می‌کند تا مهارت‌های خود را در یک سناریوی واقعی به کار ببرید.

## توضیح پروژه
اپلیکیشن مدیریت وظایف شامل ویژگی‌های زیر است:
- **صفحه اصلی**: نمایش لیست وظایف با قابلیت فیلتر بر اساس وضعیت (همه، کامل‌شده، ناتمام).
- **صفحه افزودن/ویرایش**: فرمی برای افزودن یا ویرایش وظایف.
- **دریافت و ذخیره داده‌ها**: استفاده از یک API برای مدیریت وظایف (با استفاده از `jsonplaceholder` برای شبیه‌سازی).
- **مسیریابی**: استفاده از React Router برای جابه‌جایی بین صفحات.
- **بهینه‌سازی**: استفاده از `useMemo` و `useCallback` برای بهبود عملکرد.

## ساختار پروژه
ساختار پیشنهادی پروژه:
```
src/
  ├── components/
  │   ├── TaskList.tsx        # نمایش لیست وظایف
  │   ├── TaskForm.tsx        # فرم افزودن/ویرایش وظایف
  │   ├── FilterButtons.tsx   # دکمه‌های فیلتر
  ├── pages/
  │   ├── Home.tsx            # صفحه اصلی
  │   ├── TaskEdit.tsx        # صفحه ویرایش وظیفه
  ├── types/
  │   ├── index.ts            # تعریف تایپ‌ها
  ├── hooks/
  │   ├── useTasks.ts         # هوک سفارشی برای مدیریت API
  ├── styles/
  │   ├── global.css          # استایل‌های عمومی
  ├── App.tsx                 # کامپوننت اصلی
  └── index.tsx               # نقطه ورود
```

## تعریف تایپ‌ها
ابتدا تایپ‌های موردنیاز را در `src/types/index.ts` تعریف می‌کنیم:
```typescript
export interface Task {
  id: number;
  title: string;
  completed: boolean;
}

export type FilterType = 'all' | 'completed' | 'pending';
```

## هوک سفارشی برای دریافت داده
در `src/hooks/useTasks.ts` یک هوک سفارشی برای مدیریت درخواست‌های API می‌سازیم:
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
      setState(prev => ({ ...prev, loading: false, error: 'خطا در دریافت وظایف' }));
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
      setState(prev => ({ ...prev, error: 'خطا در افزودن وظیفه' }));
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
      setState(prev => ({ ...prev, error: 'خطا در ویرایش وظیفه' }));
    }
  };

  const deleteTask = async (id: number) => {
    try {
      await axios.delete(`https://jsonplaceholder.typicode.com/todos/${id}`);
      setState(prev => ({ ...prev, tasks: prev.tasks.filter(task => task.id !== id) }));
    } catch (err) {
      setState(prev => ({ ...prev, error: 'خطا در حذف وظیفه' }));
    }
  };

  useEffect(() => {
    fetchTasks();
  }, []);

  return { ...state, addTask, updateTask, deleteTask };
};
```

## کامپوننت‌های اصلی
### TaskList.tsx
کامپوننت برای نمایش و مدیریت لیست وظایف:
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
          <Link to={`/edit/${task.id}`}>ویرایش</Link>
          <button onClick={() => onDelete(task.id)}>حذف</button>
        </li>
      ))}
    </ul>
  );
});

export default TaskList;
```

### TaskForm.tsx
کامپوننت فرم برای افزودن یا ویرایش وظایف:
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
        placeholder="عنوان وظیفه"
      />
      <button type="submit">{id ? 'ویرایش' : 'افزودن'}</button>
    </form>
  );
};

export default TaskForm;
```

### FilterButtons.tsx
کامپوننت برای فیلتر کردن وظایف:
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
        همه
      </button>
      <button onClick={() => setFilter('completed')} disabled={filter === 'completed'}>
        کامل‌شده
      </button>
      <button onClick={() => setFilter('pending')} disabled={filter === 'pending'}>
        ناتمام
      </button>
    </div>
  );
};

export default FilterButtons;
```

## صفحات اصلی
### Home.tsx
صفحه اصلی برای نمایش وظایف و فیلترها:
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

  if (loading) return <p>در حال بارگذاری...</p>;
  if (error) return <p>خطا: {error}</p>;

  return (
    <div>
      <h1>مدیریت وظایف</h1>
      <Link to="/add">افزودن وظیفه جدید</Link>
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
صفحه برای افزودن یا ویرایش وظایف:
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

## کامپوننت اصلی (App.tsx)
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

## استایل‌ها (styles/global.css)
```css
body {
  font-family: Arial, sans-serif;
  direction: rtl;
  text-align: right;
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

## نکات نهایی و منابع برای یادگیری بیشتر
- **تست‌نویسی**: برای اطمینان از کیفیت، تست‌هایی برای کامپوننت‌ها و هوک‌ها بنویسید (فصل ۹).
- **بهینه‌سازی بیشتر**: از `React.memo` و `useCallback` برای کامپوننت‌های دیگر استفاده کنید.
- **منابع پیشنهادی**:
  - مستندات رسمی React: [react.dev](https://react.dev)
  - مستندات React Router: [reactrouter.com](https://reactrouter.com)
  - مستندات TypeScript: [typescriptlang.org](https://typescriptlang.org)
  - آموزش‌های رایگان در YouTube یا دوره‌های آنلاین (مثل Udemy، FreeCodeCamp)

## تمرین‌های عملی
1. **تمرین ۱**: قابلیت فیلتر کردن وظایف بر اساس عنوان (جستجو) را به اپلیکیشن اضافه کنید.
2. **تمرین ۲**: تست‌هایی برای `TaskList` و `TaskForm` بنویسید تا رندر و تعاملات را بررسی کنند.
3. **تمرین ۳**: یک ویژگی برای علامت‌گذاری همه وظایف به‌عنوان کامل‌شده اضافه کنید.
4. **تمرین ۴**: استایل‌های پروژه را با Tailwind CSS بهبود دهید.

## نتیجه‌گیری
در این فصل، یک اپلیکیشن کامل مدیریت وظایف با React و TypeScript ساختیم که شامل مسیریابی، مدیریت State، دریافت داده از API، و بهینه‌سازی بود. این پروژه تمام مفاهیم آموخته‌شده را ترکیب کرد و شما را برای ساخت اپلیکیشن‌های واقعی آماده کرد. با تمرین بیشتر و مطالعه منابع پیشنهادی، می‌توانید مهارت‌های خود را به سطح بالاتری ببرید.