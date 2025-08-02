# JavaScript Module

# What is Function Composition?
Combining simple functions to create complex operations. Think assembly line: Input → [Function A] → [Function B] → Output.

## Core Patterns

### 1. Manual Composition 
```javascript
const step1 = add(4, 4);        // 8
const step2 = multiply(2, step1); // 16
```
### 2. Nested Composition
```javascript
const result = multiply(2, add(4, 4)); // 16
// Reads inside-out: add(4,4) first → multiply(2, 8)
```
## 3. Reusable Composed Functions
```javascript
function addFiveThenDouble(x) {
    return multiply(2, add(x, 5));
}
```

# Error checking patters in javascript

1. Try-Catch for Risky Operations (Most Important)

```javascript
try {
    // Any operation that might fail
    const data = JSON.parse(jsonString);
    const result = riskyFunction();
} catch (error) {
    console.log("Error:", error.message);
    // Handle gracefully
}
```

2. Input Validation at Function Start
```javascript
function myFunction(input) {
    // Check existence and type FIRST
    if (!input || typeof input !== 'expected_type') {
        throw new Error("Invalid input");
    }
    // Rest of function...
}
```

3. Null/Undefined Checks Before Access
```javascript
// Option 1: Direct check
if (user && user.name) {
    console.log(user.name);
}

// Option 2: Optional chaining (modern)
console.log(user?.name ?? "Default");
```

4. Check API Response Status
```javascript
const response = await fetch(url);
if (!response.ok) {
    throw new Error(`HTTP ${response.status}`);
}
```
## Quick Reference Card
 > **Before any risky operation**: → Try-Catch 

 > **At start of functions**: → Validate inputs


> **Before accessing properties**: → Check if exists 

> **After API calls**: → Check response.ok

## The Pattern That Covers Most Cases
```javascript
function safeFunction(input) {
    // 1. Validate input
    if (!input) throw new Error("Input required");
    
    try {
        // 2. Risky operation in try-catch
        const result = processInput(input);
        
        // 3. Check result before using
        if (!result) throw new Error("Processing failed");
        
        return result;
    } catch (error) {
        // 4. Handle error gracefully
        console.log("Error:", error.message);
        return null; // or default value
    }
}
```

# Testing With Jest - Understanding Unit Testing in JavaScript


## What is Unit Testing?
Think of unit testing like quality control in a factory. Just as you'd test each component of a car before assembling it, unit testing checks individual functions (units) of your code to ensure they work correctly.

## Jest Framework Structure
Jest organizes tests in a hierarchical way:
```
Project
└── Test File (.test.js)
    └── describe() - Test Suite (groups related tests)
        └── it() - Individual Test Cases
            └── expect().matcher() - Assertions
```

Here's a visual breakdown:
```
describe('Calculator Functions', () => {
    │
    ├── it('should add two numbers', () => { ... })
    ├── it('should subtract two numbers', () => { ... })
    └── it('should multiply two numbers', () => { ... })
});
```
## Key Jest Functions

1. `describe(description, callback)`

    - Purpose: Groups related tests together
    - Think of it as: A folder that contains related test cases
    - Example: describe('add function', () => { ... })

2. `it(description, callback)`

    - Purpose: Defines a single test case
    - Think of it as: One specific scenario you want to verify
    - Example: it('should return 4 when adding 2 and 2', () => { ... })

3. `expect(value).matcher(expected)`

    - Purpose: Makes assertions about what should happen
    - Think of it as: "I expect this result to match my expectation"
    - Common matchers:

        - toEqual(value) - checks if values are equal
        - toBe(value) - checks if values are identical
        - toContain(item) - checks if array contains item

## Example add function
```javascript
// add.test.js
const add = require('./add');

describe('add', () => {  // Group: testing the 'add' function
  it('adds 2 and 2 and returns 4', () => {  // Scenario: specific test case
    expect(add(2, 2)).toEqual(4);  // Assertion: call add(2,2), expect result to equal 4
  });
});
```
> This reads like: "For the add function, it should add 2 and 2 and return 4"

## Setting up a JavaScript project (Simple guide - Deatiled below)
```
# Setup our environment to use node latest version
$ nvm use node

# Create the project directory
$ mkdir my-project
$ cd my-project

# Initialise the NPM project (this will create a file package.json)
$ npm init -y

# Add the jest package to our project
# (this will update package.json and package-lock.json)
$ npm add jest

# Also install jest "globally"
# (this is so we can run the `jest` command)
$ npm install -g jest

# Run our tests
$ jest
```

## JavaScript Package Management & Jest Setup (Detailed guide)
### What are Packages?
In JavaScript, packages are pre-written code libraries that solve specific problems. They're listed in a special file called package.json located in your project's root directory.

### Steps

### 1. Initialize package.json
    ```
    $ npm init -y
    ```
- The `-y` flag skips all the setup questions
- Creates an empty `package.json` file

### 2. Basic package.json Structure
    ```
    {
    "name": "js-week",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "repository": {},
    "author": "",
    "license": "ISC",
    "bugs": {},
    "homepage": ""
    }
    ```

### 3. Installing Jest (Testing Framework)
Global Installation
```
npm install -g jest
```
- `-g` installs Jest globally on your system
- Allows you to run `jest` command from anywhere

Local Project Installation
```
npm add jest
```
- Must be run inside your project directory
- Installs Jest locally for this specific project
- Creates a `node_modules` folder containing the package files

### Important Notes
### Git Ignore
Add node_modules to your .gitignore file before committing:
```
node_modules/
```
- The node_modules folder can become very large
- Contains all installed packages and their dependencies

### Verification
After installation, running jest should show:
```
"No tests found, exiting with code 1"
```
This means Jest is installed correctly but no test files exist yet.

### Package Files Created

- `package-lock.json` - Locks specific versions of dependencies
- `node_modules/` - Contains all installed packages
- Updated `package.json` with Jest as a dependency

## 🤹 Testing with jest
Use `toBe()` for primitives (numbers, strings, booleans)

Use `toEqual()` for objects and arrays (deep comparison)

Use `toContain()` for checking if arrays/strings contain something

Use `toMatchObject()` for partial object matching

Chain with `.not` to test the opposite: `expect(value).not.toBe(null)`


### Decison tree
```
What are you testing?
│
├── A specific value? → toBe()
├── An object/array? → toEqual()  
├── "Does this exist/work?" → toBeTruthy()/toBeFalsy()
├── "Is X in this array?" → toContain()
├── "Should this break?" → toThrow()
└── Everything else → Check the full reference sheet
```

# Aynchronicity in Javascript

Think of synchronous code like a single-file line at a coffee shop. Each person must complete their order before the next person can start:

```javascript
// Synchronous execution - one step at a time
const greetCustomer = () => {
    console.log("Welcome to the coffee shop!");
    return "greeting complete";
};

const takeOrder = (greeting) => {
    console.log("What would you like to order?");
    return "order taken";
};

const makePayment = (order) => {
    console.log("Processing payment...");
    return "payment complete";
};

// Execution happens in strict order:
const greeting = greetCustomer();  // Step 1: Must finish first
const order = takeOrder(greeting); // Step 2: Waits for step 1
const payment = makePayment(order); // Step 3: Waits for step 2

console.log("Transaction complete!");
```
Each function must completely finish before the next one starts.

### The Problem: Blocking Operations
Now imagine if making coffee took 5 minutes, and everyone had to wait in a single line:
```
Customer 1: Order → Wait 5 min → Get coffee
Customer 2: Must wait for Customer 1 to finish → Order → Wait 5 min → Get coffee
Customer 3: Must wait for Customer 2 to finish → Order → Wait 5 min → Get coffee
```
happens with **blocking operations** in JavaScript!

### JavaScript's Challenge: Single-Threaded Nature
```
JavaScript Engine (Single Thread)
┌─────────────────────────────────┐
│ Can only do ONE thing at a time │
│                                 │
│  Task 1 ──► Task 2 ──► Task 3   │
│                                 │
│  If Task 2 takes forever,       │
│  Task 3 is STUCK waiting!       │
└─────────────────────────────────┘
```
### Solution: Asynchronous Programming

> Asynchronous code is like a coffee shop with multiple baristas and a pickup counter:

```
Cashier (Main Thread):
Customer 1: Takes order → Gives to barista → Immediately helps Customer 2
Customer 2: Takes order → Gives to barista → Immediately helps Customer 3

Baristas (Background):
Working on drinks simultaneously, customers pick up when ready
```
### How Async Code Actually Works

```javascript
console.log("1. Program starts");

// This simulates an async operation (like fetching data)
setTimeout(() => {
    console.log("3. Async operation finished!");
}, 0); // Even with 0 delay, this runs asynchronously

console.log("2. Program continues immediately");

// Output will be:
// 1. Program starts
// 2. Program continues immediately  
// 3. Async operation finished!
```
**Visualization**
```
Execution Timeline:
┌─────────────────────────────────────────────────────────┐
│ Main Thread          │ Background/Event Queue           │
├─────────────────────────────────────────────────────────┤
│ 1. console.log("1")  │                                  │
│ 2. setTimeout()      │ ──► Timer starts                 │
│    (starts timer)    │                                  │
│ 3. console.log("2")  │                                  │
│ 4. Main thread done  │                                  │
│                      │ ──► Timer completes, callback    │
│                      │     added to queue               │
│ 5. Execute callback  │ ◄── console.log("3") runs        │
└─────────────────────────────────────────────────────────┘
```

# Promises and fetch

## The Async/Await Syntax
Instead of complex callback chains, we use async/await to write cleaner, more readable code:

```javascript
// Declare function as async - this means it contains long-running operations
const fetchData = async () => {
    // await pauses execution until the promise resolves
    const response = await fetch("https://pokeapi.co/api/v2/pokemon/pikachu");
    return response;
}
```

## Handling All Promise States with Try/Catch

```javascript
const fetchPokemon = async () => {
    try {
        // TRY block: Handle successful operations
        
        // First promise: Send HTTP request
        const response = await fetch("https://pokeapi.co/api/v2/pokemon/pikachu");
        
        // Second promise: Convert response to JSON
        const pokemonData = await response.json();
        
        // This only runs if both promises succeed
        console.log(pokemonData);
        return pokemonData;
        
    } catch (error) {
        // CATCH block: Handle any errors that occur
        console.log("Something went wrong:", error);
    }
}
```
### Request Flow Visualization
```
Your Code          Network          Server
    │                 │               │
    │── fetch() ─────→│               │
    │                 │──── HTTP ────→│
    │                 │               │ (Processing...)
    │   (await)       │               │
    │                 │←──── Data ────│
    │←── response ────│               │
    │                 │               │
```

## Making Requests to Your Own Server
When working with your local development server, you just change the URL:
```javascript
const fetchFromMyServer = async () => {
    try {
        // Point to your local server instead of external API
        const response = await fetch("http://localhost:3000/cats");
        
        // Good practice: Check if response is valid
        if (!response.ok) {
            throw new Error(`Server error: ${response.status}`);
        }
        
        const data = await response.json();
        console.log(data);
        return data;
        
    } catch (error) {
        console.log("Request failed:", error);
    }
}
```

## Key Concepts to Remember
- **Async Functions**: Functions marked with `async` can contain `await` statements
- **Await**: Pauses execution until the promise resolves
- **Try/Catch**: Handles both successful responses and errors
- **Fetch**: Built-in function for making HTTP requests
- **JSON Conversion**: Most APIs return JSON, so we use `.json()` to parse it

## Common Pitfalls to Avoid

1. **Forgetting async/await**: If you use `await`, the function must be `async`
2. **Not handling errors**: Always wrap fetch calls in try/catch
3. **Forgetting .json()**: The response object needs to be converted to usable data
4. **Not checking response.ok**: HTTP errors don't automatically throw exceptions
 

 # HTTP vs HTTPS Protocol Notes

## Key Difference
- **HTTP**: Basic web communication protocol (not secure)
- **HTTPS**: HTTP with added security/encryption layer

## When to Use Each

### HTTP (`http://`)
- **Local development servers** (localhost)
- Your Express server running on your computer
- Default for development environments
- Example: `http://localhost:3000/api/data`

### HTTPS (`https://`)
- **Production/live websites** (deployed apps)
- Public APIs and services
- Required for security on the internet
- Example: `https://api.github.com/users`

## Common Mistake in Development

```javascript
// ❌ WRONG - Will fail for local development
const response = await fetch("https://localhost:3000/me")

// ✅ CORRECT - Works with local Express servers
const response = await fetch("http://localhost:3000/me")
```

## Why This Matters

- Local development servers (like Express) run on HTTP by default
- Using HTTPS for localhost will cause connection errors
- When you deploy your app later, you'll switch to HTTPS for security

## Quick Rule of Thumb

- localhost = use http://
- Real websites = use https://
- External APIs = usually https://

## Error You'll See If Wrong Protocol
```
TypeError: Failed to fetch
// or
net::ERR_SSL_PROTOCOL_ERROR
```

# Testing Asynchronous code in Jest

## Jest's Async Testing patterns

```javascript
// Pattern 1: Using async/await (recommended)
it("should fetch data successfully", async () => {
    const result = await myAsyncFunction();
    expect(result).toEqual(expectedValue);
});

// Pattern 2: Testing rejected promises
it("should handle errors properly", async () => {
    await expect(myAsyncFunction()).rejects.toThrow("Error message");
});
```
## Pro tip

Before writing your function, try this in your browser console or a separate file to see the actual API response:

```javascript
fetch("https://.......")
    .then(res => res.json())
    .then(data => console.log(data));
```
