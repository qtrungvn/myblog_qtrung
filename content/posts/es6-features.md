---
title: "ES6 Features - Những tính năng mới quan trọng của JavaScript"
date: 2024-12-24T00:00:00Z
draft: false
categories: ["JavaScript"]
tags: ["JavaScript", "ES6", "Modern JavaScript", "Programming"]
summary: "Khám phá các tính năng mạnh mẽ của ES6: arrow functions, destructuring, modules, promises và nhiều hơn nữa."
---

# ES6 Features - Những tính năng mới quan trọng của JavaScript

ES6 (ECMAScript 2015) là một bản cập nhật lớn cho JavaScript, giới thiệu nhiều tính năng mới giúp code trở nên hiện đại và dễ đọc hơn. Hãy khám phá những tính năng quan trọng nhất.

## Arrow Functions

Arrow functions cung cấp cú pháp ngắn gọn hơn cho việc định nghĩa functions.

```javascript
// ES5 function
function greet(name) {
  return "Hello " + name;
}

// ES6 arrow function
const greet = (name) => "Hello " + name;

// Arrow function với nhiều dòng
const greetFull = (name) => {
  const message = "Hello " + name;
  return message.toUpperCase();
};

// Arrow function không có tham số
const sayHello = () => "Hello World!";

// Arrow function với một tham số (có thể bỏ dấu ngoặc)
const square = (x) => x * x;
```

### Sự khác biệt về `this`

Arrow functions không có `this` riêng, chúng kế thừa `this` từ scope chứa chúng.

```javascript
// ES5
function Person(name) {
  this.name = name;
  this.sayHello = function () {
    setTimeout(function () {
      console.log("Hello " + this.name); // undefined
    }, 1000);
  };
}

// ES6
class Person {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    setTimeout(() => {
      console.log("Hello " + this.name); // "Hello John"
    }, 1000);
  }
}

const person = new Person("John");
person.sayHello();
```

## Destructuring Assignment

Destructuring cho phép unpack values từ arrays hoặc properties từ objects vào các biến riêng biệt.

### Array Destructuring

```javascript
// Basic array destructuring
const [first, second, third] = [1, 2, 3];
console.log(first, second, third); // 1 2 3

// Skipping elements
const [a, , c] = [1, 2, 3, 4];
console.log(a, c); // 1 3

// Default values
const [x = 5, y = 10] = [1];
console.log(x, y); // 1 10

// Swapping variables
let a = 1,
  b = 2;
[a, b] = [b, a];
console.log(a, b); // 2 1

// Rest pattern
const [head, ...tail] = [1, 2, 3, 4, 5];
console.log(head, tail); // 1 [2, 3, 4, 5]
```

### Object Destructuring

```javascript
// Basic object destructuring
const person = { name: "John", age: 30, city: "New York" };
const { name, age, city } = person;
console.log(name, age, city); // John 30 New York

// Renaming variables
const { name: fullName, age: years } = person;
console.log(fullName, years); // John 30

// Default values
const { name, country = "USA" } = person;
console.log(name, country); // John USA

// Nested destructuring
const user = {
  name: "Alice",
  address: {
    street: "123 Main St",
    city: "Boston",
  },
};

const {
  name,
  address: { city },
} = user;
console.log(name, city); // Alice Boston

// Rest pattern
const { name, ...rest } = person;
console.log(name, rest); // John { age: 30, city: 'New York' }
```

## Template Literals

Template literals cho phép embedded expressions và multi-line strings.

```javascript
// Basic template literal
const name = "World";
const greeting = `Hello ${name}!`;
console.log(greeting); // "Hello World!"

// Multi-line strings
const multiLine = `
    This is a
    multi-line string
    with indentation preserved
`;

// Expressions in template literals
const a = 5,
  b = 10;
const result = `The sum of ${a} and ${b} is ${a + b}.`;
console.log(result); // "The sum of 5 and 10 is 15."

// Tagged template literals
function highlight(strings, ...values) {
  return strings.reduce(
    (result, string, i) =>
      result + string + (values[i] ? `<strong>${values[i]}</strong>` : ""),
    ""
  );
}

const name = "John";
const age = 30;
const highlighted = highlight`My name is ${name} and I am ${age} years old.`;
console.log(highlighted); // "My name is <strong>John</strong> and I am <strong>30</strong> years old."
```

## Default Parameters

Functions có thể có default values cho parameters.

```javascript
// ES5
function greet(name) {
  name = name || "World";
  return "Hello " + name;
}

// ES6
function greet(name = "World") {
  return `Hello ${name}`;
}

console.log(greet()); // "Hello World"
console.log(greet("John")); // "Hello John"

// Default parameters với destructuring
function createUser({ name = "Anonymous", age = 18, city = "Unknown" } = {}) {
  return { name, age, city };
}

console.log(createUser()); // { name: 'Anonymous', age: 18, city: 'Unknown' }
console.log(createUser({ name: "John", age: 25 })); // { name: 'John', age: 25, city: 'Unknown' }
```

## Rest và Spread Operators

### Rest Parameters

```javascript
// Rest parameters
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4)); // 10

// Rest với destructuring
const [first, second, ...rest] = [1, 2, 3, 4, 5];
console.log(first, second, rest); // 1 2 [3, 4, 5]

// Rest in function parameters
function multiply(multiplier, ...numbers) {
  return numbers.map((num) => num * multiplier);
}

console.log(multiply(2, 1, 2, 3)); // [2, 4, 6]
```

### Spread Operator

```javascript
// Spread với arrays
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2];
console.log(combined); // [1, 2, 3, 4, 5, 6]

// Copy array
const original = [1, 2, 3];
const copy = [...original];

// Spread với objects
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3, d: 4 };
const merged = { ...obj1, ...obj2 };
console.log(merged); // { a: 1, b: 2, c: 3, d: 4 }

// Override properties
const updated = { ...obj1, b: 20 };
console.log(updated); // { a: 1, b: 20 }

// Spread in function calls
const numbers = [1, 2, 3, 4, 5];
console.log(Math.max(...numbers)); // 5
```

## Enhanced Object Literals

ES6 cải thiện cách định nghĩa objects.

```javascript
// Shorthand property names
const name = "John";
const age = 30;
const person = { name, age }; // { name: 'John', age: 30 }

// Shorthand method names
const calculator = {
  add(a, b) {
    return a + b;
  },
  subtract(a, b) {
    return a - b;
  },
  multiply(a, b) {
    return a + b;
  }, // Oops! Should be multiply
};

// Computed property names
const propName = "dynamicProperty";
const obj = {
  [propName]: "value",
  ["computed" + "Key"]: "another value",
};
console.log(obj.dynamicProperty); // 'value'
console.log(obj.computedKey); // 'another value'
```

## Classes

ES6 giới thiệu class syntax cho JavaScript.

```javascript
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // Instance method
  greet() {
    return `Hello, my name is ${this.name}`;
  }

  // Getter
  get birthYear() {
    return new Date().getFullYear() - this.age;
  }

  // Setter
  set birthYear(year) {
    this.age = new Date().getFullYear() - year;
  }

  // Static method
  static createAnonymous() {
    return new Person("Anonymous", 0);
  }
}

// Inheritance
class Student extends Person {
  constructor(name, age, grade) {
    super(name, age);
    this.grade = grade;
  }

  study() {
    return `${this.name} is studying`;
  }

  // Override method
  greet() {
    return `Hi, I'm ${this.name}, a student in grade ${this.grade}`;
  }
}

const student = new Student("Alice", 16, 10);
console.log(student.greet()); // "Hi, I'm Alice, a student in grade 10"
console.log(student.birthYear); // 2008
```

## Modules

ES6 giới thiệu module system cho JavaScript.

```javascript
// math.js
export const PI = 3.14159;

export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

export default function multiply(a, b) {
  return a * b;
}

// app.js
import multiply, { PI, add, subtract } from "./math.js";

console.log(add(5, 3)); // 8
console.log(multiply(4, 2)); // 8
console.log(PI); // 3.14159

// Import everything
import * as MathUtils from "./math.js";
console.log(MathUtils.add(1, 2)); // 3
```

## Promises

Promises giúp xử lý asynchronous operations.

```javascript
// Creating a promise
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = Math.random() > 0.5;
    if (success) {
      resolve("Success!");
    } else {
      reject(new Error("Failed!"));
    }
  }, 1000);
});

// Using a promise
promise
  .then((result) => console.log(result))
  .catch((error) => console.error(error));

// Promise chaining
function fetchUser(id) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (id === 1) {
        resolve({ id: 1, name: "John" });
      } else {
        reject(new Error("User not found"));
      }
    }, 500);
  });
}

function fetchPosts(userId) {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve([
        { id: 1, title: "Post 1", userId },
        { id: 2, title: "Post 2", userId },
      ]);
    }, 500);
  });
}

fetchUser(1)
  .then((user) => {
    console.log("User:", user);
    return fetchPosts(user.id);
  })
  .then((posts) => {
    console.log("Posts:", posts);
  })
  .catch((error) => {
    console.error("Error:", error);
  });

// Promise.all - run multiple promises in parallel
const promise1 = Promise.resolve(1);
const promise2 = Promise.resolve(2);
const promise3 = Promise.resolve(3);

Promise.all([promise1, promise2, promise3])
  .then((values) => console.log(values)) // [1, 2, 3]
  .catch((error) => console.error(error));
```

## Symbols

Symbols là primitive type mới, đại diện cho unique identifiers.

```javascript
// Creating symbols
const sym1 = Symbol();
const sym2 = Symbol("description");
const sym3 = Symbol("description");

console.log(sym1 === sym2); // false
console.log(sym2 === sym3); // false (even with same description)

// Symbol as object property key
const obj = {};
const key = Symbol("unique key");

obj[key] = "value";
console.log(obj[key]); // 'value'
console.log(Object.keys(obj)); // [] (symbol keys are not enumerable)

// Well-known symbols
const arr = [1, 2, 3];

// Custom iterator
arr[Symbol.iterator] = function* () {
  for (let i = this.length - 1; i >= 0; i--) {
    yield this[i];
  }
};

for (let num of arr) {
  console.log(num); // 3, 2, 1
}
```

## Maps và Sets

ES6 giới thiệu Map và Set - alternatives tốt hơn cho objects và arrays.

```javascript
// Map
const map = new Map();

// Set values
map.set("name", "John");
map.set("age", 30);
map.set({ key: "object" }, "object value");

// Get values
console.log(map.get("name")); // 'John'
console.log(map.has("age")); // true
console.log(map.size); // 3

// Iterate over map
for (let [key, value] of map) {
  console.log(key, value);
}

// Set
const set = new Set();

// Add values
set.add(1);
set.add(2);
set.add(2); // duplicate, ignored
set.add("text");

console.log(set.has(1)); // true
console.log(set.size); // 3

// Iterate over set
for (let value of set) {
  console.log(value);
}

// Remove duplicates from array
const numbers = [1, 2, 2, 3, 3, 4];
const uniqueNumbers = [...new Set(numbers)];
console.log(uniqueNumbers); // [1, 2, 3, 4]
```

## Kết luận

ES6 đã biến JavaScript thành một ngôn ngữ hiện đại và mạnh mẽ hơn. Việc nắm vững các tính năng này sẽ giúp bạn viết code JavaScript tốt hơn, dễ đọc hơn và hiệu quả hơn. Hãy bắt đầu áp dụng chúng vào dự án của bạn!
