# فصل ۶: مسیریابی در React

## مقدمه
در این فصل، با **مسیریابی** (Routing) در اپلیکیشن‌های **React** با استفاده از **React Router** و **TypeScript** آشنا می‌شویم. مسیریابی به شما امکان می‌دهد صفحات مختلف اپلیکیشن را مدیریت کنید و کاربران را بین مسیرهای مختلف (مانند صفحه اصلی، درباره ما، یا پروفایل) هدایت کنید. هدف این فصل، یادگیری نحوه تنظیم مسیرها، کار با پارامترهای داینامیک، و استفاده از Query String در پروژه‌های ری‌اکت است.

## معرفی React Router
**React Router** یک کتابخانه قدرتمند برای مدیریت مسیریابی در اپلیکیشن‌های React است. نسخه‌ای که استفاده می‌کنیم، **React Router DOM** است که برای اپلیکیشن‌های وب طراحی شده. ویژگی‌های کلیدی آن:
- **مسیریابی سمت کلاینت**: تغییر مسیرها بدون بارگذاری مجدد صفحه.
- **کامپوننت‌محور**: مسیرها به‌صورت کامپوننت تعریف می‌شوند.
- **پشتیبانی از TypeScript**: تایپ‌های آماده برای استفاده امن.

## نصب و راه‌اندازی React Router
برای استفاده از React Router در پروژه React با TypeScript:
1. پکیج‌های موردنیاز را نصب کنید:
```bash
npm install react-router-dom @types/react-router-dom
```
2. فایل `index.tsx` را به‌روزرسانی کنید تا اپلیکیشن در `BrowserRouter` قرار گیرد:
```typescript
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import App from './App';

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);
```

## تنظیم مسیرها و ناوبری
برای تعریف مسیرها، از کامپوننت `Routes` و `Route` استفاده می‌کنیم.

### مثال: تنظیم مسیرهای پایه
```typescript
import React from 'react';
import { Routes, Route, Link } from 'react-router-dom';

const Home: React.FC = () => <h1>صفحه اصلی</h1>;
const About: React.FC = () => <h1>درباره ما</h1>;
const Contact: React.FC = () => <h1>تماس با ما</h1>;

const App: React.FC = () => {
  return (
    <div>
      <nav>
        <ul>
          <li><Link to="/">خانه</Link></li>
          <li><Link to="/about">درباره</Link></li>
          <li><Link to="/contact">تماس</Link></li>
        </ul>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </div>
  );
};

export default App;
```
**توضیح**:
- `Link` برای ایجاد لینک‌های ناوبری استفاده می‌شود.
- `Routes` و `Route` برای تعریف مسیرها و رندر کامپوننت‌های مرتبط استفاده می‌شوند.
- مسیر `/` به صفحه اصلی (Home) اشاره دارد.

### ناوبری برنامه‌ریزی‌شده
برای هدایت کاربران به‌صورت برنامه‌ریزی‌شده (مثلاً پس از ارسال فرم)، از هوک `useNavigate` استفاده می‌کنیم:
```typescript
import React from 'react';
import { useNavigate } from 'react-router-dom';

const Login: React.FC = () => {
  const navigate = useNavigate();

  const handleLogin = () => {
    // فرض کنید لاگین موفق است
    navigate('/dashboard');
  };

  return (
    <div>
      <h1>ورود</h1>
      <button onClick={handleLogin}>ورود</button>
    </div>
  );
};

export default Login;
```

## پارامترهای داینامیک و Query String
### پارامترهای داینامیک
برای مسیرهای داینامیک (مثل پروفایل کاربر با آیدی)، از پارامترها استفاده می‌کنیم:
```typescript
import React from 'react';
import { useParams } from 'react-router-dom';

const UserProfile: React.FC = () => {
  const { userId } = useParams<{ userId: string }>();

  return <h1>پروفایل کاربر: {userId}</h1>;
};

export default UserProfile;
```
در `App.tsx`:
```typescript
<Route path="/user/:userId" element={<UserProfile />} />
```
**توضیح**: مسیر `/user/123` مقدار `userId` را به `123` تنظیم می‌کند.

### Query String
برای مدیریت Query String (مثل `?search=react`)، از هوک `useSearchParams` استفاده می‌کنیم:
```typescript
import React from 'react';
import { useSearchParams } from 'react-router-dom';

const Search: React.FC = () => {
  const [searchParams, setSearchParams] = useSearchParams();
  const query = searchParams.get('q') || '';

  const handleSearch = (e: React.ChangeEvent<HTMLInputElement>) => {
    setSearchParams({ q: e.target.value });
  };

  return (
    <div>
      <input
        type="text"
        value={query}
        onChange={handleSearch}
        placeholder="جستجو..."
      />
      <p>جستجو: {query}</p>
    </div>
  );
};

export default Search;
```
در `App.tsx`:
```typescript
<Route path="/search" element={<Search />} />
```
**توضیح**: با تایپ `?q=react` در آدرس، مقدار `query` به `react` تنظیم می‌شود.

## تمرین‌های عملی
1. **تمرین ۱**: یک اپلیکیشن ساده با سه صفحه (خانه، درباره، تماس) بسازید و یک نوار ناوبری با `Link` اضافه کنید.
2. **تمرین ۲**: یک کامپوننت `Product` بسازید که با پارامتر داینامیک `productId` اطلاعات یک محصول را نمایش دهد (مثلاً نام و قیمت).
3. **تمرین ۳**: یک فرم جستجو بسازید که با استفاده از `useSearchParams`، عبارت جستجو را در URL ذخیره کند و نتایج را نمایش دهد.
4. **تمرین ۴**: یک دکمه لاگین بسازید که با استفاده از `useNavigate`، کاربر را پس از کلیک به صفحه `/dashboard` هدایت کند.

## نتیجه‌گیری
در این فصل، با React Router و قابلیت‌های آن برای مدیریت مسیریابی آشنا شدیم. یاد گرفتیم چگونه مسیرهای پایه، پارامترهای داینامیک، و Query String را پیاده‌سازی کنیم. TypeScript به ما کمک کرد تا تایپ‌های مسیرها و پارامترها را مشخص کنیم. در فصل بعدی، به **کار با APIها در React** می‌پردازیم و یاد می‌گیریم چگونه داده‌ها را از سرور دریافت و نمایش دهیم.