---
title: ""
date: 2025-12-26T00:00:00Z
draft: false
categories: ["JavaScript"]
tags: ["JavaScript", "Promises", "Asynchronous", "Async Programming"]
summary: ""
---



Promises là một tính năng của ES6 giúp xử lý asynchronous operations một cách dễ dàng và tránh được callback hell. Một Promise đại diện cho một giá trị có thể có trong tương lai.

## Cơ bản về Promises

### Tạo và sử dụng Promise

```javascript
// Tạo một Promise
const promise = new Promise((resolve, reject) => {
  // Asynchronous operation
  setTimeout(() => {
    const success = Math.random() > 0.5;

    if (success) {
      resolve("Operation successful!"); // Thành công
    } else {
      reject(new Error("Operation failed!")); // Thất bại
    }
  }, 1000);
});

// Sử dụng Promise
promise
  .then((result) => {
    console.log("Success:", result);
  })
  .catch((error) => {
    console.error("Error:", error.message);
  });
```

### Promise States

Một Promise có thể ở một trong ba trạng thái:

1. **Pending**: Chưa hoàn thành
2. **Fulfilled**: Hoàn thành thành công
3. **Rejected**: Hoàn thành với lỗi

```javascript
const promise = new Promise((resolve, reject) => {
  console.log("Promise created - Pending state");

  setTimeout(() => {
    resolve("Data received");
    console.log("Promise resolved - Fulfilled state");
  }, 1000);
});

promise.then((result) => {
  console.log("Result:", result);
});
```

## Methods của Promise

### .then() và .catch()

```javascript
// .then() xử lý success case
// .catch() xử lý error case
fetch("https://api.example.com/data")
  .then((response) => {
    if (!response.ok) {
      throw new Error("Network response was not ok");
    }
    return response.json();
  })
  .then((data) => {
    console.log("Data received:", data);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```

### .finally()

`.finally()` được gọi bất kể Promise thành công hay thất bại.

```javascript
function fetchData() {
  return fetch("https://api.example.com/data").then((response) =>
    response.json()
  );
}

fetchData()
  .then((data) => {
    console.log("Data:", data);
  })
  .catch((error) => {
    console.error("Error:", error);
  })
  .finally(() => {
    console.log("Operation completed (success or failure)");
    // Cleanup code here
  });
```

## Promise Chaining

Promises có thể được chain lại với nhau để thực hiện các async operations tuần tự.

```javascript
// Ví dụ: Lấy user data, sau đó lấy posts của user đó
function getUser(userId) {
  return fetch(`https://jsonplaceholder.typicode.com/users/${userId}`).then(
    (response) => response.json()
  );
}

function getUserPosts(userId) {
  return fetch(
    `https://jsonplaceholder.typicode.com/posts?userId=${userId}`
  ).then((response) => response.json());
}

getUser(1)
  .then((user) => {
    console.log("User:", user.name);
    return getUserPosts(user.id);
  })
  .then((posts) => {
    console.log("Posts count:", posts.length);
    return posts;
  })
  .then((posts) => {
    // Có thể chain thêm
    return posts.map((post) => post.title);
  })
  .then((titles) => {
    console.log("Post titles:", titles);
  })
  .catch((error) => {
    console.error("Error in chain:", error);
  });
```

## Static Methods

### Promise.resolve()

Tạo một Promise đã được resolve.

```javascript
// Tạo resolved Promise
const resolvedPromise = Promise.resolve("Immediate value");
resolvedPromise.then((value) => console.log(value)); // 'Immediate value'

// Convert non-Promise value thành Promise
Promise.resolve(42).then((value) => console.log(value)); // 42
```

### Promise.reject()

Tạo một Promise đã bị reject.

```javascript
const rejectedPromise = Promise.reject(new Error("Something went wrong"));
rejectedPromise.catch((error) => console.error(error.message));
```

### Promise.all()

Chờ tất cả Promises hoàn thành (thành công hoặc thất bại).

```javascript
const promise1 = fetch("https://api.example.com/users");
const promise2 = fetch("https://api.example.com/posts");
const promise3 = fetch("https://api.example.com/comments");

Promise.all([promise1, promise2, promise3])
  .then((responses) => {
    // Tất cả responses đều thành công
    return Promise.all(responses.map((response) => response.json()));
  })
  .then((data) => {
    const [users, posts, comments] = data;
    console.log("Users:", users.length);
    console.log("Posts:", posts.length);
    console.log("Comments:", comments.length);
  })
  .catch((error) => {
    // Một trong các Promise thất bại
    console.error("One of the requests failed:", error);
  });
```

### Promise.allSettled() (ES2020)

Chờ tất cả Promises hoàn thành, bất kể thành công hay thất bại.

```javascript
const promises = [
  fetch("https://api.example.com/users"),
  fetch("https://api.example.com/posts"),
  fetch("https://api.example.com/invalid-endpoint"), // Sẽ fail
];

Promise.allSettled(promises).then((results) => {
  results.forEach((result, index) => {
    if (result.status === "fulfilled") {
      console.log(`Promise ${index} succeeded:`, result.value);
    } else {
      console.log(`Promise ${index} failed:`, result.reason);
    }
  });
});
```

### Promise.race()

Trả về Promise đầu tiên hoàn thành (thành công hoặc thất bại).

```javascript
const fastApi = fetch("https://fast-api.example.com/data");
const slowApi = fetch("https://slow-api.example.com/data");

Promise.race([fastApi, slowApi])
  .then((response) => {
    console.log("Fastest API responded");
    return response.json();
  })
  .then((data) => {
    console.log("Data from fastest API:", data);
  })
  .catch((error) => {
    console.error("Fastest API failed:", error);
  });
```

### Promise.any() (ES2021)

Trả về Promise đầu tiên thành công.

```javascript
const api1 = fetch("https://api1.example.com/data");
const api2 = fetch("https://api2.example.com/data");
const api3 = fetch("https://api3.example.com/data");

Promise.any([api1, api2, api3])
  .then((response) => {
    console.log("First successful API response");
    return response.json();
  })
  .then((data) => {
    console.log("Data from first successful API:", data);
  })
  .catch((error) => {
    // Tất cả APIs đều thất bại
    console.error("All APIs failed:", error);
  });
```

## Tạo Promise-based Functions

### Convert callback-based function thành Promise

```javascript
// Callback-based function
function readFile(filename, callback) {
  // Giả lập async file reading
  setTimeout(() => {
    if (filename === "valid.txt") {
      callback(null, "File content");
    } else {
      callback(new Error("File not found"));
    }
  }, 1000);
}

// Convert to Promise-based
function readFilePromise(filename) {
  return new Promise((resolve, reject) => {
    readFile(filename, (error, data) => {
      if (error) {
        reject(error);
      } else {
        resolve(data);
      }
    });
  });
}

// Sử dụng
readFilePromise("valid.txt")
  .then((content) => console.log("File content:", content))
  .catch((error) => console.error("Error:", error.message));
```

### Promisify utility function

```javascript
function promisify(originalFunction) {
  return function (...args) {
    return new Promise((resolve, reject) => {
      originalFunction(...args, (error, result) => {
        if (error) {
          reject(error);
        } else {
          resolve(result);
        }
      });
    });
  };
}

// Sử dụng
const readFileAsync = promisify(readFile);

readFileAsync("valid.txt")
  .then((content) => console.log(content))
  .catch((error) => console.error(error));
```

## Error Handling Patterns

### Centralized Error Handling

```javascript
class ApiClient {
  async request(url, options = {}) {
    try {
      const response = await fetch(url, options);

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }

      return await response.json();
    } catch (error) {
      // Log error
      console.error(`API request failed for ${url}:`, error);

      // Re-throw để caller có thể handle
      throw error;
    }
  }
}

const api = new ApiClient();

// Tất cả requests đều có error handling tự động
api
  .request("/users")
  .then((users) => console.log(users))
  .catch((error) => {
    // Error đã được log trong ApiClient
    console.log("Failed to fetch users");
  });
```

### Retry Mechanism

```javascript
function retryPromise(fn, maxRetries = 3, delay = 1000) {
  return new Promise((resolve, reject) => {
    function attempt(retriesLeft) {
      fn()
        .then(resolve)
        .catch((error) => {
          if (retriesLeft > 0) {
            console.log(`Retrying... ${retriesLeft} attempts left`);
            setTimeout(() => attempt(retriesLeft - 1), delay);
          } else {
            reject(error);
          }
        });
    }

    attempt(maxRetries);
  });
}

// Sử dụng
const unreliableRequest = () => {
  return new Promise((resolve, reject) => {
    // 70% chance of failure
    if (Math.random() > 0.3) {
      resolve("Success!");
    } else {
      reject(new Error("Request failed"));
    }
  });
};

retryPromise(unreliableRequest, 5, 500)
  .then((result) => console.log("Final result:", result))
  .catch((error) => console.error("All retries failed:", error));
```

## Real-world Examples

### Sequential API Calls

```javascript
async function processUserWorkflow(userId) {
  try {
    // 1. Lấy thông tin user
    const user = await getUser(userId);

    // 2. Lấy danh sách posts của user
    const posts = await getUserPosts(userId);

    // 3. Lấy comments cho mỗi post
    const postsWithComments = await Promise.all(
      posts.map(async (post) => {
        const comments = await getPostComments(post.id);
        return { ...post, comments };
      })
    );

    // 4. Tính thống kê
    const stats = {
      user: user.name,
      totalPosts: posts.length,
      totalComments: postsWithComments.reduce(
        (sum, post) => sum + post.comments.length,
        0
      ),
    };

    return stats;
  } catch (error) {
    console.error("Workflow failed:", error);
    throw error;
  }
}

// Helper functions (giả lập)
function getUser(id) {
  return fetch(`https://jsonplaceholder.typicode.com/users/${id}`).then((r) =>
    r.json()
  );
}

function getUserPosts(userId) {
  return fetch(
    `https://jsonplaceholder.typicode.com/posts?userId=${userId}`
  ).then((r) => r.json());
}

function getPostComments(postId) {
  return fetch(
    `https://jsonplaceholder.typicode.com/comments?postId=${postId}`
  ).then((r) => r.json());
}
```

### Timeout với Promise

```javascript
function withTimeout(promise, timeoutMs) {
  const timeoutPromise = new Promise((_, reject) => {
    setTimeout(() => reject(new Error("Operation timed out")), timeoutMs);
  });

  return Promise.race([promise, timeoutPromise]);
}

// Sử dụng
const slowOperation = new Promise((resolve) => {
  setTimeout(() => resolve("Slow result"), 3000);
});

withTimeout(slowOperation, 2000)
  .then((result) => console.log("Result:", result))
  .catch((error) => console.log("Error:", error.message)); // "Operation timed out"
```

### Promise-based File Upload

```javascript
function uploadFile(file) {
  const formData = new FormData();
  formData.append("file", file);

  return fetch("/upload", {
    method: "POST",
    body: formData,
  }).then((response) => {
    if (!response.ok) {
      throw new Error(`Upload failed: ${response.statusText}`);
    }
    return response.json();
  });
}

// Sử dụng với progress (sử dụng XMLHttpRequest)
function uploadFileWithProgress(file, onProgress) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    const formData = new FormData();
    formData.append("file", file);

    xhr.upload.addEventListener("progress", (event) => {
      if (event.lengthComputable && onProgress) {
        const percentComplete = (event.loaded / event.total) * 100;
        onProgress(percentComplete);
      }
    });

    xhr.addEventListener("load", () => {
      if (xhr.status >= 200 && xhr.status < 300) {
        resolve(JSON.parse(xhr.responseText));
      } else {
        reject(new Error(`Upload failed: ${xhr.statusText}`));
      }
    });

    xhr.addEventListener("error", () => {
      reject(new Error("Upload failed"));
    });

    xhr.open("POST", "/upload");
    xhr.send(formData);
  });
}

// Sử dụng
const fileInput = document.getElementById("fileInput");
fileInput.addEventListener("change", async (event) => {
  const file = event.target.files[0];
  if (file) {
    try {
      const result = await uploadFileWithProgress(file, (progress) => {
        console.log(`Upload progress: ${progress.toFixed(2)}%`);
      });
      console.log("Upload successful:", result);
    } catch (error) {
      console.error("Upload failed:", error);
    }
  }
});
```

## Best Practices

### 1. Luôn handle errors

```javascript
// ❌ Bad
fetchData().then((data) => console.log(data));

// ✅ Good
fetchData()
  .then((data) => console.log(data))
  .catch((error) => console.error("Error:", error));
```

### 2. Trả về Promises từ functions

```javascript
// ❌ Bad - inconsistent API
function getData(callback) {
  // Sometimes returns Promise, sometimes uses callback
}

// ✅ Good - always return Promise
function getData() {
  return fetch("/api/data").then((response) => response.json());
}
```

### 3. Sử dụng Promise.all cho parallel operations

```javascript
// ❌ Bad - sequential
async function getAllData() {
  const users = await getUsers();
  const posts = await getPosts();
  const comments = await getComments();
  return { users, posts, comments };
}

// ✅ Good - parallel
async function getAllData() {
  const [users, posts, comments] = await Promise.all([
    getUsers(),
    getPosts(),
    getComments(),
  ]);
  return { users, posts, comments };
}
```

### 4. Tránh Promise constructor anti-patterns

```javascript
// ❌ Bad - Promise constructor anti-pattern
function badAsyncOperation() {
  return new Promise((resolve, reject) => {
    someAsyncOperation((error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });
  });
}

// ✅ Good - use util.promisify or manual promisification
const util = require("util");
const promisifiedOperation = util.promisify(someAsyncOperation);
```

## Kết luận

Promises đã cách mạng hóa cách chúng ta xử lý asynchronous operations trong JavaScript. Chúng giúp tránh callback hell, cung cấp error handling tốt hơn và cho phép chain operations một cách dễ dàng. Khi kết hợp với async/await, Promises tạo nên một hệ thống asynchronous programming mạnh mẽ và dễ sử dụng.

Hãy bắt đầu sử dụng Promises trong code của bạn để viết asynchronous code tốt hơn!
