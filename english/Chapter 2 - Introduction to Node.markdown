# Chapter 2: Introduction to Node.js

## Introduction
In this chapter, we'll explore **Node.js**, a runtime environment that enables JavaScript execution outside the browser. This tool is crucial for React developers because React project tools (like Create React App) depend on Node.js. Our objectives are to learn Node.js fundamentals, installation, and working with modules and packages.

## What is Node.js and Why Does It Matter?
Node.js is a server-side runtime environment built on Google Chrome's V8 JavaScript engine. It allows you to:
- Run JavaScript code in non-browser environments
- Build web applications, APIs, and command-line tools
- Leverage the rich npm package ecosystem for dependency management

For React development, Node.js and **npm** (Node Package Manager) are essential for installing libraries and running scripts.

## Installing and Configuring Node.js and npm
### Installing Node.js
1. Visit the official [Node.js website](https://nodejs.org)
2. Download and install the LTS (stable) version
3. Verify installation using your terminal:
```bash
node --version
npm --version
```
These commands display the installed versions of Node.js and npm.

### Setting Up a Simple Project
1. Create a project directory:
```bash
mkdir my-node-project
cd my-node-project
```
2. Initialize the project with npm:
```bash
npm init -y
```
This creates a `package.json` file storing project configurations.

## Working with Modules and Packages
Node.js uses a modular system. Modules come in two types:
- **Core modules**: Built-in modules like `fs` (file system) and `http` (web server)
- **External modules**: Packages installed via npm

### Example: Using the `fs` Core Module
```javascript
const fs = require("fs");

// Writing to a file
fs.writeFileSync("example.txt", "Hello, this is a test file!");

// Reading from a file
const content = fs.readFileSync("example.txt", "utf8");
console.log(content); // Output: Hello, this is a test file!
```

### Installing and Using External Packages
Example using the `lodash` package:
1. Install the package:
```bash
npm install lodash
```
2. Use in your code:
```javascript
const _ = require("lodash");

const numbers = [1, 2, 3, 4];
const shuffled = _.shuffle(numbers);
console.log(shuffled); // Output: Randomized array, e.g., [3, 1, 4, 2]
```

## Simple Node.js Projects
### Project 1: Building a Basic Web Server
Create a simple web server using the `http` module:
```javascript
const http = require("http");

const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello from Node.js server!");
});

server.listen(3000, () => {
  console.log("Server running at http://localhost:3000");
});
```
1. Save this code in `server.js`
2. Run:
```bash
node server.js
```
3. Visit `http://localhost:3000` in your browser to see the message.

### Project 2: Command-Line Tool
A simple factorial calculator script:
```javascript
const num = parseInt(process.argv[2]);

function factorial(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);
}

console.log(`Factorial of ${num} is: ${factorial(num)}`);
```
1. Save this code in `factorial.js`
2. Run:
```bash
node factorial.js 5
```
Output: `Factorial of 5 is: 120`

## Hands-on Exercises
1. **Exercise 1**: Create a text file and use the `fs` module to write/read messages.
2. **Exercise 2**: Install the `moment` package and display current date/time in different formats.
3. **Exercise 3**: Create an HTTP server that shows a different message for the `/about` route.
4. **Exercise 4**: Write a CLI script that reverses an input string.

## Conclusion
In this chapter, we covered Node.js installation and working with modules/packages. This foundation is essential for React development. Next, we'll explore **TypeScript** to understand static typing benefits for React applications.