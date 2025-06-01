# فصل ۸: بهینه‌سازی و بهترین روش‌ها در React

## مقدمه
در این فصل، به بررسی روش‌های **بهینه‌سازی عملکرد** اپلیکیشن‌های **React** با استفاده از **TypeScript** و بهترین روش‌های کدنویسی می‌پردازیم. هدف این فصل، یادگیری تکنیک‌هایی برای بهبود سرعت و کارایی اپلیکیشن، کاهش رندرهای غیرضروری، و نوشتن کدهای تمیز و قابل‌نگهداری است. با استفاده از ابزارهایی مثل `useMemo`، `useCallback` و ساختاردهی مناسب پروژه، می‌توانیم اپلیکیشن‌هایی مقیاس‌پذیر بسازیم.

## بهینه‌سازی عملکرد کامپوننت‌ها
برای بهبود عملکرد، باید از رندرهای غیرضروری جلوگیری کنیم و محاسبات سنگین را بهینه کنیم. React ابزارهایی برای این منظور ارائه می‌دهد.

### استفاده از useMemo
هوک `useMemo` برای جلوگیری از محاسبات سنگین در هر رندر استفاده می‌شود.

#### مثال: محاسبه مجموع یک آرایه
```typescript
import React, { useState, useMemo } from 'react';

const ExpensiveCalculation: React.FC = () => {
  const [numbers, setNumbers] = useState<number[]>([1, 2, 3, 4, 5]);
  const [count, setCount] = useState<number>(0);

  // محاسبه سنگین که فقط هنگام تغییر numbers اجرا می‌شود
  const sum = useMemo(() => {
    console.log('محاسبه مجموع...');
    return numbers.reduce((acc, num) => acc + num, 0);
  }, [numbers]);

  return (
    <div>
      <p>مجموع: {sum}</p>
      <p>شمارنده: {count}</p>
      <button onClick={() => setCount(count + 1)}>افزایش شمارنده</button>
      <button onClick={() => setNumbers([...numbers, numbers.length + 1])}>
        اضافه کردن عدد
      </button>
    </div>
  );
};

export default ExpensiveCalculation;
```
**توضیح**:
- `useMemo` مقدار `sum` را ذخیره می‌کند و فقط وقتی `numbers` تغییر کند، دوباره محاسبه می‌شود.
- کلیک روی دکمه "افزایش شمارنده" باعث رندر می‌شود، اما `sum` دوباره محاسبه نمی‌شود.

### استفاده از useCallback
هوک `useCallback` برای جلوگیری از ایجاد توابع جدید در هر رندر استفاده می‌شود، که برای کامپوننت‌های فرزند بهینه‌شده مفید است.

#### مثال: پاس دادن تابع به کامپوننت فرزند
```typescript
import React, { useState, useCallback } from 'react';

interface ChildProps {
  onClick: () => void;
}

const Child: React.FC<ChildProps> = React.memo(({ onClick }) => {
  console.log('Child رندر شد');
  return <button onClick={onClick}>کلیک کن</button>;
});

const Parent: React.FC = () => {
  const [count, setCount] = useState<number>(0);

  // استفاده از useCallback برای جلوگیری از ایجاد تابع جدید
  const handleClick = useCallback(() => {
    console.log('کلیک شد!');
  }, []);

  return (
    <div>
      <p>شمارنده: {count}</p>
      <button onClick={() => setCount(count + 1)}>افزایش</button>
      <Child onClick={handleClick} />
    </div>
  );
};

export default Parent;
```
**توضیح**:
- `React.memo` از رندر غیرضروری `Child` جلوگیری می‌کند.
- `useCallback` تضمین می‌کند که `handleClick` در هر رندر تغییر نکند، بنابراین `Child` فقط یک‌بار رندر می‌شود.

## ساختاردهی پروژه و کد تمیز
برای پروژه‌های بزرگ، ساختاردهی مناسب و رعایت اصول کد تمیز ضروری است.

### ساختار پیشنهادی پروژه
```
src/
  ├── components/       # کامپوننت‌های قابل‌استفاده مجدد
  │   ├── Button/
  │   │   ├── Button.tsx
  │   │   └── Button.css
  ├── pages/           # کامپوننت‌های سطح صفحه
  │   ├── Home.tsx
  │   ├── About.tsx
  ├── hooks/           # هوک‌های سفارشی
  │   ├── useFetch.ts
  ├── types/           # تعریف تایپ‌ها و اینترفیس‌ها
  │   ├── index.ts
  ├── utils/           # توابع کمکی
  │   ├── api.ts
  ├── styles/          # استایل‌های عمومی
  │   ├── global.css
  ├── App.tsx          # کامپوننت اصلی
  └── index.tsx        # نقطه ورود
```

### نکات کد تمیز
- **نام‌گذاری معنادار**: از نام‌های واضح برای کامپوننت‌ها، متغیرها، و توابع استفاده کنید (مثل `UserList` به‌جای `List`).
- **تقسیم‌بندی کامپوننت‌ها**: کامپوننت‌های بزرگ را به بخش‌های کوچک‌تر تقسیم کنید.
- **تایپ‌های TypeScript**: همیشه تایپ‌ها و اینترفیس‌ها را برای props، state، و داده‌های API تعریف کنید.
- **مدیریت خطاها**: خطاها را به‌صورت مناسب مدیریت کنید (مثل استفاده از `try/catch` در APIها).
- **مستندسازی**: برای توابع و کامپوننت‌های پیچیده توضیحات مختصر بنویسید.

#### مثال: هوک سفارشی برای دریافت داده
```typescript
// src/hooks/useFetch.ts
import { useState, useEffect } from 'react';

interface FetchState<T> {
  data: T | null;
  loading: boolean;
  error: string | null;
}

const useFetch = <T>(url: string): FetchState<T> => {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch(url);
        if (!response.ok) throw new Error('خطا در دریافت داده');
        const result: T = await response.json();
        setData(result);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'خطای ناشناخته');
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
};

export default useFetch;
```
**استفاده**:
```typescript
import React from 'react';
import useFetch from '../hooks/useFetch';

interface User {
  id: number;
  name: string;
}

const UserList: React.FC = () => {
  const { data, loading, error } = useFetch<User[]>('https://jsonplaceholder.typicode.com/users');

  if (loading) return <p>در حال بارگذاری...</p>;
  if (error) return <p>خطا: {error}</p>;

  return (
    <ul>
      {data?.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
};

export default UserList;
```

## تمرین‌های عملی
1. **تمرین ۱**: یک کامپوننت بسازید که یک لیست بزرگ (مثل ۱۰۰۰ آیتم) را رندر کند و با استفاده از `useMemo` از محاسبات غیرضروری (مثل فیلتر کردن) جلوگیری کند.
2. **تمرین ۲**: یک کامپوننت فرزند با `React.memo` و یک تابع با `useCallback` بسازید تا رندرهای غیرضروری را کاهش دهید.
3. **تمرین ۳**: پروژه خود را با ساختار پیشنهادی بالا سازمان‌دهی کنید و یک هوک سفارشی برای مدیریت فرم بسازید.
4. **تمرین ۴**: یک کامپوننت بسازید که داده‌های API را با `useFetch` دریافت کند و با TypeScript تایپ‌های مناسب را تعریف کند.

## نتیجه‌گیری
در این فصل، با تکنیک‌های بهینه‌سازی مانند `useMemo` و `useCallback` آشنا شدیم و بهترین روش‌ها برای ساختاردهی پروژه و نوشتن کد تمیز را یاد گرفتیم. TypeScript به ما کمک کرد تا کدهایی ایمن‌تر و قابل‌نگهداری‌تر بنویسیم. در فصل بعدی، به **تست‌نویسی در React** می‌پردازیم تا کیفیت کد خود را تضمین کنیم.