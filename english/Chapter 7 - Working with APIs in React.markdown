# Chapter 7: Working with APIs in React

## Introduction
In this chapter, we'll learn how to **fetch and manage data from APIs** in **React** applications using **TypeScript**. APIs allow us to retrieve dynamic data from servers and display it in our user interfaces. We'll focus on using `fetch` and `axios` for HTTP requests, managing asynchronous operations, and displaying received data.

## Data Fetching with Fetch and Axios
We can use either `fetch` (built into JavaScript) or the `axios` library to retrieve data from APIs. Both methods are suitable for working with REST APIs.

### Using Fetch
`fetch` is a built-in JavaScript API for making HTTP requests.

#### Example: Fetching User List
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
        if (!response.ok) throw new Error('Failed to fetch data');
        const data: User[] = await response.json();
        setUsers(data);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    fetchUsers();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

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
**Explanation**:
- `useEffect` executes the request when the component mounts
- `User` interface defines the structure of received data
- `loading` and `error` states handle request status

### Using Axios
`axios` is a popular HTTP client library with enhanced features like better error handling.

#### Install Axios
```bash
npm install axios
```

#### Example: Fetching Data with Axios
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
        setError(axios.isAxiosError(err) ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    fetchPosts();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

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
**Explanation**:
- `axios.get` specifies response data type using generics (`<Post[]>`)
- `axios.isAxiosError` provides precise error handling

## Managing Asynchronous Requests
API requests are typically asynchronous and require proper handling. Key considerations:
- **Use async/await**: For cleaner, more readable code
- **Error handling**: Implement `try/catch` blocks
- **Loading states**: Show loading indicators during requests
- **Request cancellation**: Cancel requests when components unmount

### Example: Canceling Requests with AbortController
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
          console.log('Request canceled');
        }
      }
    };

    fetchData();

    return () => controller.abort(); // Cancel request on unmount
  }, []);

  return <p>{data || 'Loading...'}</p>;
};

export default DataFetcher;
```
**Explanation**: `AbortController` prevents memory leaks by canceling requests when components unmount.

## Working with REST APIs and Displaying Data
REST APIs typically use GET, POST, PUT, and DELETE methods.

### Example: Sending Data to Server (POST)
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
      setResponse('Post created successfully!');
    } catch (err) {
      setResponse('Error creating post');
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
          placeholder="Title"
        />
        <textarea
          name="body"
          value={formData.body}
          onChange={handleChange}
          placeholder="Post content"
        />
        <button type="submit">Submit</button>
      </form>
      {response && <p>{response}</p>}
    </div>
  );
};

export default CreatePost;
```
**Explanation**: This form sends data to an API and displays the response.

## Hands-on Exercises
1. **Exercise 1**: Build a component that fetches and displays product data from an API (e.g., `https://fakestoreapi.com/products`).
2. **Exercise 2**: Create a form to submit new posts using `axios` to `https://jsonplaceholder.typicode.com/posts`.
3. **Exercise 3**: Build a component that fetches user data by `userId` using `fetch`.
4. **Exercise 4**: Create a search app that filters API data using query parameters (e.g., `?query=book`).

## Conclusion
In this chapter, we learned to fetch data from APIs using `fetch` and `axios`, manage asynchronous operations, and display data in UIs. TypeScript helped us define data structures and prevent errors. Next, we'll explore **Optimization and Best Practices** to improve application performance.