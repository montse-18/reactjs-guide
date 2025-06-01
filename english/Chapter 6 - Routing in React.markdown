# Chapter 6: Routing in React

## Introduction
In this chapter, we'll explore **routing** in **React** applications using **React Router** and **TypeScript**. Routing enables you to manage different application pages and navigate users between various paths (like home, about, or profile pages). Our goal is to learn how to configure routes, work with dynamic parameters, and use Query Strings in React projects.

## Introducing React Router
**React Router** is a powerful library for managing routing in React applications. We'll use **React Router DOM**, designed for web applications. Key features include:
- **Client-side routing**: Navigate between routes without page reloads
- **Component-based**: Routes are defined as components
- **TypeScript support**: Built-in types for type-safe implementation

## Installing and Setting Up React Router
To use React Router in a TypeScript React project:
1. Install required packages:
```bash
npm install react-router-dom @types/react-router-dom
```
2. Update `index.tsx` to wrap your app in `BrowserRouter`:
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

## Configuring Routes and Navigation
Use the `Routes` and `Route` components to define routes.

### Example: Basic Route Setup
```typescript
import React from 'react';
import { Routes, Route, Link } from 'react-router-dom';

const Home: React.FC = () => <h1>Home Page</h1>;
const About: React.FC = () => <h1>About Us</h1>;
const Contact: React.FC = () => <h1>Contact Us</h1>;

const App: React.FC = () => {
  return (
    <div>
      <nav>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/about">About</Link></li>
          <li><Link to="/contact">Contact</Link></li>
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
**Explanation**:
- `Link` creates navigation links
- `Routes` and `Route` define path-component mappings
- The `/` path renders the Home component

### Programmatic Navigation
Use the `useNavigate` hook for programmatic navigation (e.g., after form submission):
```typescript
import React from 'react';
import { useNavigate } from 'react-router-dom';

const Login: React.FC = () => {
  const navigate = useNavigate();

  const handleLogin = () => {
    // Assume successful login
    navigate('/dashboard');
  };

  return (
    <div>
      <h1>Login</h1>
      <button onClick={handleLogin}>Login</button>
    </div>
  );
};

export default Login;
```

## Dynamic Parameters and Query Strings
### Dynamic Parameters
For dynamic routes (e.g., user profile with ID), use route parameters:
```typescript
import React from 'react';
import { useParams } from 'react-router-dom';

const UserProfile: React.FC = () => {
  const { userId } = useParams<{ userId: string }>();

  return <h1>User Profile: {userId}</h1>;
};

export default UserProfile;
```
In `App.tsx`:
```typescript
<Route path="/user/:userId" element={<UserProfile />} />
```
**Explanation**: Visiting `/user/123` sets `userId` to `123`

### Query Strings
Manage query strings (e.g., `?search=react`) with `useSearchParams`:
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
        placeholder="Search..."
      />
      <p>Searching: {query}</p>
    </div>
  );
};

export default Search;
```
In `App.tsx`:
```typescript
<Route path="/search" element={<Search />} />
```
**Explanation**: The query parameter updates the URL (e.g., `?q=react`)

## Hands-on Exercises
1. **Exercise 1**: Build a simple app with three pages (Home, About, Contact) and a navigation bar using `Link`.
2. **Exercise 2**: Create a `Product` component that displays product info using a dynamic `productId` parameter (e.g., name and price).
3. **Exercise 3**: Build a search form that uses `useSearchParams` to store/search queries in the URL.
4. **Exercise 4**: Create a login button that redirects to `/dashboard` using `useNavigate` after click.

## Conclusion
In this chapter, we explored React Router and its capabilities for managing routing. We learned to implement basic routes, dynamic parameters, and query strings. TypeScript helped us define route and parameter types. Next, we'll explore **Working with APIs in React** to fetch and display data from servers.