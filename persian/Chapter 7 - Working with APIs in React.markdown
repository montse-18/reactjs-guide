# فصل ۷: کار با APIها در React

## مقدمه
در این فصل، با نحوه **دریافت و مدیریت داده‌ها از APIها** در اپلیکیشن‌های **React** با استفاده از **TypeScript** آشنا می‌شویم. APIها به ما امکان می‌دهند داده‌های پویا را از سرورها دریافت کنیم و در رابط کاربری نمایش دهیم. تمرکز این فصل بر استفاده از `fetch` و `axios` برای درخواست‌های HTTP، مدیریت عملیات غیرهمزمان، و نمایش داده‌های دریافتی است.

## دریافت داده با Fetch و Axios
برای دریافت داده از APIها، می‌توانیم از `fetch` (داخلی در جاوااسکریپت) یا کتابخانه `axios` استفاده کنیم. هر دو روش برای کار با APIهای REST مناسب هستند.

### استفاده از Fetch
`fetch` یک API داخلی جاوااسکریپت است که برای ارسال درخواست‌های HTTP استفاده می‌شود.

#### مثال: دریافت لیست کاربران
```typescript
import React, { useState, useEffect } from 'react';

interface User {
  id: number;
  name: string;
  email: string;
}

const UserList: React.FC = () => {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/users');
        if (!response.ok) throw new Error('خطا در دریافت داده‌ها');
        const data: User[] = await response.json();
        setUsers(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'خطای ناشناخته');
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <p>در حال بارگذاری...</p>;
  if (error) return <p>خطا: {error}</p>;

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>
          {user.name} - {user.email}
        </li>
      ))}
    </ul>
  );
};

export default UserList;
```
**توضیح**:
- از `useEffect` برای اجرای درخواست هنگام بارگذاری کامپوننت استفاده شده است.
- `User` یک اینترفیس TypeScript برای تعریف ساختار داده‌های دریافتی است.
- State‌های `loading` و `error` برای مدیریت وضعیت بارگذاری و خطاها استفاده می‌شوند.

### استفاده از Axios
`axios` یک کتابخانه محبوب برای ارسال درخواست‌های HTTP است که قابلیت‌های بیشتری نسبت به `fetch` ارائه می‌دهد (مثل مدیریت آسان‌تر خطاها).

#### نصب Axios
```bash
npm install axios
```

#### مثال: دریافت داده با Axios
```typescript
import React, { useState, useEffect } from 'react';
import axios from 'axios';

interface Post {
  id: number;
  title: string;
  body: string;
}

const PostList: React.FC = () => {
  const [posts, setPosts] = useState<Post[]>([]);
  const [loading, setLoading] = useState<boolean>(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    const fetchPosts = async () => {
      try {
        const response = await axios.get<Post[]>('https://jsonplaceholder.typicode.com/posts');
        setPosts(response.data);
      } catch (err) {
        setError(axios.isAxiosError(err) ? err.message : 'خطای ناشناخته');
      } finally {
        setLoading(false);
      }
    };

    fetchPosts();
  }, []);

  if (loading) return <p>در حال بارگذاری...</p>;
  if (error) return <p>خطا: {error}</p>;

  return (
    <div>
      {posts.map(post => (
        <div key={post.id}>
          <h3>{post.title}</h3>
          <p>{post.body}</p>
        </div>
      ))}
    </div>
  );
};

export default PostList;
```
**توضیح**:
- `axios.get` نوع داده پاسخ را با استفاده از generic مشخص می‌کند (`<Post[]>`).
- مدیریت خطاها با `axios.isAxiosError` دقیق‌تر انجام می‌شود.

## مدیریت درخواست‌های Asynchronous
درخواست‌های API معمولاً غیرهمزمان هستند و نیاز به مدیریت صحیح دارند. نکات کلیدی:
- **استفاده از async/await**: برای خوانایی بهتر کد.
- **مدیریت خطاها**: با استفاده از بلوک‌های `try/catch`.
- **وضعیت بارگذاری**: نمایش پیام یا انیمیشن در حال بارگذاری.
- **لغو درخواست‌ها**: در صورت نیاز (مثلاً هنگام خروج از کامپوننت).

### مثال: لغو درخواست با AbortController
```typescript
import React, { useState, useEffect } from 'react';

const DataFetcher: React.FC = () => {
  const [data, setData] = useState<string | null>(null);

  useEffect(() => {
    const controller = new AbortController();

    const fetchData = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/todos/1', {
          signal: controller.signal
        });
        const result = await response.json();
        setData(result.title);
      } catch (err) {
        if (err instanceof Error && err.name === 'AbortError') {
          console.log('درخواست لغو شد');
        }
      }
    };

    fetchData();

    return () => controller.abort(); // لغو درخواست هنگام خروج
  }, []);

  return <p>{data || 'در حال بارگذاری...'}</p>;
};

export default DataFetcher;
```
**توضیح**: `AbortController` برای لغو درخواست‌ها هنگام unmount شدن کامپوننت استفاده می‌شود تا از memory leak جلوگیری شود.

## کار با REST API و نمایش داده‌ها
برای کار با APIهای REST، معمولاً با متدهای GET، POST، PUT، و DELETE سر و کار داریم.

### مثال: ارسال داده به سرور (POST)
```typescript
import React, { useState } from 'react';
import axios from 'axios';

interface NewPost {
  title: string;
  body: string;
  userId: number;
}

const CreatePost: React.FC = () => {
  const [formData, setFormData] = useState<NewPost>({ title: '', body: '', userId: 1 });
  const [response, setResponse] = useState<string | null>(null);

  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault();
    try {
      const res = await axios.post('https://jsonplaceholder.typicode.com/posts', formData);
      setResponse('پست با موفقیت ایجاد شد!');
    } catch (err) {
      setResponse('خطا در ارسال پست');
    }
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input
          type="text"
          name="title"
          value={formData.title}
          onChange={handleChange}
          placeholder="عنوان"
        />
        <textarea
          name="body"
          value={formData.body}
          onChange={handleChange}
          placeholder="متن پست"
        />
        <button type="submit">ارسال</button>
      </form>
      {response && <p>{response}</p>}
    </div>
  );
};

export default CreatePost;
```
**توضیح**: این فرم داده‌ها را به API ارسال می‌کند و پاسخ را نمایش می‌دهد.

## تمرین‌های عملی
1. **تمرین ۱**: یک کامپوننت بسازید که لیست محصولات را از API (مثل `https://fakestoreapi.com/products`) دریافت و نمایش دهد.
2. **تمرین ۲**: یک فرم برای ایجاد یک پست جدید بسازید که با `axios` داده‌ها را به API ارسال کند (از `https://jsonplaceholder.typicode.com/posts` استفاده کنید).
3. **تمرین ۳**: یک کامپوننت بسازید که با استفاده از `fetch` داده‌های یک کاربر خاص را بر اساس `userId` دریافت کند و نمایش دهد.
4. **تمرین ۴**: یک اپلیکیشن جستجو بسازید که با Query String (مثل `?query=book`) داده‌ها را از یک API فیلتر کند و نمایش دهد.

## نتیجه‌گیری
در این فصل، یاد گرفتیم چگونه با استفاده از `fetch` و `axios` داده‌ها را از APIها دریافت کنیم، درخواست‌های غیرهمزمان را مدیریت کنیم، و داده‌ها را در رابط کاربری نمایش دهیم. TypeScript به ما کمک کرد تا ساختار داده‌ها را مشخص کنیم و از خطاها جلوگیری کنیم. در فصل بعدی، به **بهینه‌سازی و بهترین روش‌ها** در React می‌پردازیم تا عملکرد اپلیکیشن را بهبود دهیم.