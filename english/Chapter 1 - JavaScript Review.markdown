# Chapter 1: JavaScript Review

## Introduction
In this chapter, we'll review key JavaScript concepts essential for learning React. If you're already familiar with JavaScript, this chapter will help reinforce your knowledge. If you're new to JavaScript, don't worry—we'll explain concepts simply with practical examples. The goal is to build a solid foundation for better understanding React and TypeScript.

## Core JavaScript Concepts
### Variables
Variables store data. In JavaScript, we use `let`, `const`, and `var` to declare variables:
- `let`: For variables whose values may change
- `const`: For constant values
- `var`: Older declaration method (less recommended)

```javascript
let name = "Ali"; // mutable
const age = 25; // immutable
var score = 100; // legacy method
name = "Sara"; // allowed
// age = 26; // Error: const cannot be reassigned
```

### Functions
Functions are reusable code blocks. They can be declared as regular functions or arrow functions:
```javascript
// Regular function
function greet(name) {
  return `Hello, ${name}!`;
}

// Arrow function
const add = (a, b) => a + b;

console.log(greet("Ali")); // Output: Hello, Ali!
console.log(add(2, 3)); // Output: 5
```

### Arrays
Arrays store collections of data. Use methods like `map`, `filter`, and `forEach` to manipulate arrays:
```javascript
const numbers = [1, 2, 3, 4];
const doubled = numbers.map(num => num * 2); // [2, 4, 6, 8]
const evens = numbers.filter(num => num % 2 === 0); // [2, 4]
numbers.forEach(num => console.log(num)); // Prints 1, 2, 3, 4
```

### Objects
Objects store structured data:
```javascript
const person = {
  name: "Ali",
  age: 25,
  greet: function() {
    return `Hello, I'm ${this.name}!`;
  }
};
console.log(person.name); // Output: Ali
console.log(person.greet()); // Output: Hello, I'm Ali!
```

## DOM Manipulation and Event Handling
JavaScript interacts with web pages using the **DOM** (Document Object Model). You can select and modify HTML elements:
```javascript
// Selecting an element
const button = document.querySelector("#myButton");

// Adding an event
button.addEventListener("click", () => {
  alert("Button clicked!");
});
```

Example: Changing paragraph text on button click:
```html
<button id="myButton">Click Me</button>
<p id="myParagraph">Initial text</p>

<script>
  const button = document.querySelector("#myButton");
  const paragraph = document.querySelector("#myParagraph");
  button.addEventListener("click", () => {
    paragraph.textContent = "Text changed!";
  });
</script>
```

## Advanced Concepts
### Closures
Closures are functions that retain access to variables from their outer scope:
```javascript
function counter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}
const myCounter = counter();
console.log(myCounter()); // 1
console.log(myCounter()); // 2
```

### Promises and Async/Await
Used for handling asynchronous operations (like API requests):
```javascript
// Promise
const fetchData = new Promise((resolve, reject) => {
  setTimeout(() => resolve("Data received!"), 1000);
});

fetchData.then(data => console.log(data)); // Output after 1s: Data received!

// Async/Await
async function getData() {
  const data = await fetchData;
  console.log(data);
}
getData();
```

## Hands-on Exercises
1. **Exercise 1**: Create a number array and square all numbers using `map`.
2. **Exercise 2**: Write a function that takes a person's name and age as input and returns an object with these properties.
3. **Exercise 3**: Create a button and paragraph in HTML. On button click, change the paragraph text color.
4. **Exercise 4**: Write a function using Promises that displays a message after 2 seconds.

## Conclusion
In this chapter, we reviewed fundamental and advanced JavaScript concepts. This knowledge is essential for understanding React and working with components. In the next chapter, we'll explore **Node.js** to become familiar with JavaScript's runtime environment and development tools.