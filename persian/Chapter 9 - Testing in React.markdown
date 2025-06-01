# فصل ۹: تست‌نویسی در React

## مقدمه
در این فصل، به بررسی **تست‌نویسی** در اپلیکیشن‌های **React** با استفاده از **TypeScript** می‌پردازیم. تست‌نویسی به ما کمک می‌کند تا از صحت عملکرد کامپوننت‌ها و منطق برنامه مطمئن شویم و از بروز خطاها در آینده جلوگیری کنیم. تمرکز این فصل بر استفاده از ابزارهای **Jest** و **React Testing Library** برای نوشتن تست‌های واحد و تست‌های رندر است. همچنین بهترین روش‌های تست‌نویسی را مرور می‌کنیم تا کدهایمان قابل‌اعتماد و مقیاس‌پذیر باشند.

## معرفی ابزارهای تست
### Jest
**Jest** یک فریم‌ورک تست‌نویسی است که به‌طور پیش‌فرض در پروژه‌های Create React App وجود دارد. ویژگی‌های کلیدی آن:
- اجرای سریع تست‌ها
- پشتیبانی از assertionها (مثل `expect`)
- قابلیت mock کردن توابع و ماژول‌ها

### React Testing Library
**React Testing Library** یک کتابخانه برای تست رابط کاربری است که بر شبیه‌سازی رفتار کاربر تمرکز دارد. این ابزار:
- کامپوننت‌ها را در DOM واقعی رندر می‌کند
- به جای تست جزئیات داخلی، رفتار کاربر را بررسی می‌کند
- با TypeScript کاملاً سازگار است

## نصب و راه‌اندازی
اگر از Create React App با TypeScript استفاده می‌کنید، Jest و React Testing Library به‌طور پیش‌فرض نصب هستند. در غیر این صورت، آن‌ها را نصب کنید:
```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom
```

در فایل `src/setupTests.ts`، کتابخانه `jest-dom` را وارد کنید:
```typescript
import '@testing-library/jest-dom';
```

## نوشتن تست‌های واحد
تست‌های واحد برای بررسی عملکرد توابع و منطق‌های مستقل استفاده می‌شوند.

### مثال: تست یک تابع ساده
```typescript
// src/utils/math.ts
export const add = (a: number, b: number): number => a + b;

// src/utils/math.test.ts
import { add } from './math';

describe('تابع add', () => {
  test('باید دو عدد را درست جمع کند', () => {
    expect(add(2, 3)).toBe(5);
    expect(add(-1, 1)).toBe(0);
    expect(add(0, 0)).toBe(0);
  });
});
```
**توضیح**:
- `describe` گروهی از تست‌ها را تعریف می‌کند.
- `test` یک مورد تست خاص را اجرا می‌کند.
- `expect` برای بررسی نتیجه استفاده می‌شود.

## نوشتن تست‌های رندر
تست‌های رندر برای بررسی رندر کامپوننت‌ها و تعاملات کاربر استفاده می‌شوند.

### مثال: تست یک کامپوننت ساده
```typescript
// src/components/Counter.tsx
import React, { useState } from 'react';

const Counter: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  return (
    <div>
      <p data-testid="count">شمارنده: {count}</p>
      <button onClick={() => setCount(count + 1)}>افزایش</button>
    </div>
  );
};

export default Counter;

// src/components/Counter.test.tsx
import React from 'react';
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

describe('کامپوننت Counter', () => {
  test('باید مقدار اولیه شمارنده را نمایش دهد', () => {
    render(<Counter />);
    expect(screen.getByTestId('count')).toHaveTextContent('شمارنده: 0');
  });

  test('باید با کلیک روی دکمه افزایش یابد', () => {
    render(<Counter />);
    const button = screen.getByText('افزایش');
    fireEvent.click(button);
    expect(screen.getByTestId('count')).toHaveTextContent('شمارنده: 1');
  });
});
```
**توضیح**:
- `render` کامپوننت را در DOM رندر می‌کند.
- `screen.getByTestId` برای پیدا کردن عناصر با ویژگی `data-testid` استفاده می‌شود.
- `fireEvent.click` کلیک کاربر را شبیه‌سازی می‌کند.

### تست با TypeScript
برای اطمینان از تایپ‌های صحیح، از تایپ‌های React Testing Library استفاده کنید:
```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { describe, test, expect } from '@jest/globals';
```

## تست‌های پیچیده‌تر
### تست کامپوننت با Props
```typescript
// src/components/Greeting.tsx
import React from 'react';

interface GreetingProps {
  name: string;
}

const Greeting: React.FC<GreetingProps> = ({ name }) => {
  return <h1>سلام، {name}!</h1>;
};

export default Greeting;

// src/components/Greeting.test.tsx
import React from 'react';
import { render, screen } from '@testing-library/react';
import Greeting from './Greeting';

describe('کامپوننت Greeting', () => {
  test('باید نام دریافتی را نمایش دهد', () => {
    render(<Greeting name="علی" />);
    expect(screen.getByText('سلام، علی!')).toBeInTheDocument();
  });
});
```

### تست تعاملات غیرهمزمان
برای تست درخواست‌های API، از mock کردن استفاده می‌کنیم:
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
    json: () => Promise.resolve([{ id: 1, name: 'علی' }])
  } as Response)
);

describe('کامپوننت UserList', () => {
  test('باید لیست کاربران را نمایش دهد', async () => {
    render(<UserList />);
    await waitFor(() => {
      expect(screen.getByTestId('user')).toHaveTextContent('علی');
    });
  });
});
```
**توضیح**:
- `global.fetch` برای mock کردن درخواست API استفاده شده است.
- `waitFor` برای صبر کردن تا تکمیل عملیات غیرهمزمان به کار می‌رود.

## بهترین روش‌ها برای تست‌نویسی
- **تمرکز بر رفتار کاربر**: به جای تست جزئیات داخلی (مثل state)، رفتار قابل‌مشاهده (مثل متن رندرشده) را تست کنید.
- **استفاده از data-testid**: برای شناسایی عناصر در تست‌ها، از ویژگی `data-testid` استفاده کنید.
- **تست‌های مستقل**: هر تست باید مستقل باشد و به تست‌های دیگر وابسته نباشد.
- **پوشش تست**: از ابزارهایی مثل `npm test -- --coverage` برای بررسی پوشش تست‌ها استفاده کنید.
- **mock کردن وابستگی‌ها**: برای APIها و ماژول‌های خارجی از mock استفاده کنید.
- **تایپ‌های TypeScript**: اطمینان حاصل کنید که تایپ‌های props و state در تست‌ها رعایت می‌شوند.

## تمرین‌های عملی
1. **تمرین ۱**: یک تابع ساده (مثل محاسبه فاکتوریل) بنویسید و با Jest برای آن تست‌های واحد بنویسید.
2. **تمرین ۲**: یک کامپوننت فرم بسازید که دو ورودی (نام و ایمیل) داشته باشد و تست‌هایی برای رندر صحیح و ارسال فرم بنویسید.
3. **تمرین ۳**: یک کامپوننت بسازید که داده‌های API را نمایش دهد و با mock کردن درخواست، تست رندر داده‌ها را بنویسید.
4. **تمرین ۴**: یک کامپوننت با یک دکمه و یک شمارنده بسازید و تست کنید که با چند کلیک، مقدار شمارنده به درستی افزایش یابد.

## نتیجه‌گیری
در این فصل، با ابزارهای Jest و React Testing Library برای تست‌نویسی در React آشنا شدیم. یاد گرفتیم چگونه تست‌های واحد و تست‌های رندر بنویسیم و با استفاده از TypeScript، کدهایمان را ایمن‌تر کنیم. در فصل بعدی، به **پروژه نهایی** می‌پردازیم و تمام مفاهیم آموخته‌شده را در یک اپلیکیشن کامل پیاده‌سازی می‌کنیم.