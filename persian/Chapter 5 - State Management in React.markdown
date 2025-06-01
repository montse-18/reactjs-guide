# فصل ۵: مدیریت State در React

## مقدمه
در این فصل، به بررسی روش‌های مدیریت **State** در اپلیکیشن‌های **React** با استفاده از **TypeScript** می‌پردازیم. State بخش مهمی از اپلیکیشن‌های تعاملی است که داده‌های پویا را مدیریت می‌کند. ما روی هوک‌های `useState` و `useReducer` تمرکز می‌کنیم و سپس به **Context API** برای مدیریت State در سطح اپلیکیشن می‌پردازیم. هدف این فصل، یادگیری روش‌های مؤثر برای مدیریت State و نوشتن کدهای تمیز و تایپ‌شده است.

## استفاده از useState
هوک `useState` ساده‌ترین راه برای مدیریت State در کامپوننت‌های تابعی است. با TypeScript، می‌توانیم نوع داده‌های State را مشخص کنیم تا از خطاها جلوگیری شود.

### مثال: مدیریت یک فرم ساده
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
    console.log('داده‌های فرم:', formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        name="name"
        value={formData.name}
        onChange={handleChange}
        placeholder="نام"
      />
      <input
        type="email"
        name="email"
        value={formData.email}
        onChange={handleChange}
        placeholder="ایمیل"
      />
      <button type="submit">ارسال</button>
    </form>
  );
};

export default Form;
```
**توضیح**:
- `FormState` یک اینترفیس برای تعریف ساختار State است.
- `useState` با مقدار اولیه یک شیء تعریف شده و نوع آن مشخص شده است.
- تابع `handleChange` به‌صورت دینامیک مقادیر ورودی را به‌روزرسانی می‌کند.

## استفاده از useReducer
برای مدیریت State‌های پیچیده‌تر، از هوک `useReducer` استفاده می‌کنیم. این هوک مشابه Redux است و برای حالاتی که منطق به‌روزرسانی پیچیده‌ای دارند مناسب است.

### مثال: مدیریت یک لیست وظایف (Todo List)
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
        placeholder="وظیفه جدید"
      />
      <button onClick={handleAdd}>اضافه کن</button>
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
**توضیح**:
- `useReducer` با یک تابع `reducer` و یک State اولیه کار می‌کند.
- تایپ‌های `TodoAction` و `TodoState` برای مشخص کردن ساختار اکشن‌ها و State استفاده شده‌اند.
- این مثال یک لیست وظایف را مدیریت می‌کند که می‌توانید وظایف را اضافه کرده و وضعیت آن‌ها را تغییر دهید.

## مدیریت State با Context API
برای به اشتراک گذاشتن State بین چندین کامپوننت، از **Context API** استفاده می‌کنیم. این روش برای مدیریت State در سطح اپلیکیشن مناسب است.

### مثال: مدیریت تم (Theme) اپلیکیشن
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
  if (!context) throw new Error('useTheme باید داخل ThemeProvider استفاده شود');
  return context;
};

const ThemedComponent: React.FC = () => {
  const { theme, toggleTheme } = useTheme();

  return (
    <div style={{ background: theme === 'light' ? '#fff' : '#333', color: theme === 'light' ? '#000' : '#fff' }}>
      <p>تم فعلی: {theme}</p>
      <button onClick={toggleTheme}>تغییر تم</button>
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
**توضیح**:
- `ThemeContext` برای به اشتراک گذاشتن داده‌های تم ایجاد شده است.
- `useTheme` یک هوک سفارشی است که دسترسی به Context را ساده می‌کند.
- TypeScript تضمین می‌کند که مقادیر Context به‌درستی تایپ‌شده باشند.

## بهترین روش‌ها برای مدیریت State
- **از useState برای State‌های ساده** استفاده کنید (مثل مقادیر فرم یا شمارنده‌ها).
- **از useReducer برای State‌های پیچیده** با منطق‌های چندمرحله‌ای استفاده کنید.
- **از Context API برای State‌های مشترک** بین کامپوننت‌ها استفاده کنید، اما از زیاده‌روی پرهیز کنید تا عملکرد کاهش نیابد.
- **تایپ‌های TypeScript را مشخص کنید** تا خطاها در زمان توسعه شناسایی شوند.
- **State را نزدیک به محل استفاده نگه دارید** تا مدیریت آن ساده‌تر باشد.

## تمرین‌های عملی
1. **تمرین ۱**: یک کامپوننت فرم بسازید که نام، ایمیل و سن را با `useState` مدیریت کند. نوع داده‌ها را با TypeScript مشخص کنید.
2. **تمرین ۲**: یک لیست خرید با `useReducer` بسازید که بتوانید اقلام را اضافه و حذف کنید. نوع اکشن‌ها و State را تعریف کنید.
3. **تمرین ۳**: یک Context برای مدیریت زبان اپلیکیشن (مثل فارسی/انگلیسی) ایجاد کنید و یک کامپوننت بسازید که متن را بر اساس زبان نمایش دهد.
4. **تمرین ۴**: یک اپلیکیشن ساده بسازید که از `useState` و `useReducer` به‌صورت ترکیبی برای مدیریت یک فرم و یک لیست استفاده کند.

## نتیجه‌گیری
در این فصل، با روش‌های مختلف مدیریت State در ری‌اکت آشنا شدیم: از `useState` برای موارد ساده، `useReducer` برای منطق‌های پیچیده، و Context API برای State‌های مشترک. TypeScript به ما کمک کرد تا کدهایی تایپ‌شده و قابل‌اعتماد بنویسیم. در فصل بعدی، به **مسیریابی در React** با استفاده از React Router می‌پردازیم.