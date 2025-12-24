---
title: ""
date: 2025-12-25T00:00:00Z
draft: false
categories: ["JavaScript"]
tags: ["JavaScript", "Async", "Promises", "Asynchronous Programming"]
summary: "Khám phá async/await - cách hiện đại và dễ đọc để xử lý asynchronous operations trong JavaScript."
---



Async/await là một tính năng của ES2017 giúp viết asynchronous code trông giống như synchronous code, làm cho nó dễ đọc và hiểu hơn. Đây là một bước tiến lớn so với callback hell và promise chains.

## Cơ bản về Async/Await

### Async Functions

Một async function luôn trả về một Promise.

```javascript
// Async function declaration
async function fetchData() {
  return "Data fetched";
}

// Async function expression
const fetchData2 = async function () {
  return "Data fetched";
};

// Async arrow function
const fetchData3 = async () => {
  return "Data fetched";
};

// Tất cả đều trả về Promise
fetchData().then((result) => console.log(result)); // "Data fetched"
```

### Await Operator

Await operator được sử dụng bên trong async functions để chờ một Promise được resolve.

```javascript
async function getUserData() {
  // Giả lập API call
  const response = await fetch("https://api.example.com/user");
  const userData = await response.json();
  return userData;
}

// Sử dụng
getUserData()
  .then((user) => console.log(user))
  .catch((error) => console.error(error));
```

## So sánh với Promises

### Sử dụng Promises

```javascript
function getUserData() {
  return fetch("https://api.example.com/user")
    .then((response) => response.json())
    .then((userData) => {
      return fetch(`https://api.example.com/posts/${userData.id}`)
        .then((response) => response.json())
        .then((posts) => {
          return {
            user: userData,
            posts: posts,
          };
        });
    });
}
```

### Sử dụng Async/Await

```javascript
async function getUserData() {
  try {
    const userResponse = await fetch("https://api.example.com/user");
    const userData = await userResponse.json();

    const postsResponse = await fetch(
      `https://api.example.com/posts/${userData.id}`
    );
    const posts = await postsResponse.json();

    return {
      user: userData,
      posts: posts,
    };
  } catch (error) {
    console.error("Error fetching data:", error);
    throw error;
  }
}
```

Code với async/await dễ đọc và hiểu hơn nhiều!

## Error Handling

### Try/Catch với Async/Await

```javascript
async function fetchUser(userId) {
  try {
    const response = await fetch(`https://api.example.com/users/${userId}`);

    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }

    const user = await response.json();
    return user;
  } catch (error) {
    console.error("Error fetching user:", error);
    throw error; // Re-throw để caller có thể handle
  }
}

// Sử dụng
async function displayUser(userId) {
  try {
    const user = await fetchUser(userId);
    console.log("User:", user);
  } catch (error) {
    console.log("Failed to display user:", error.message);
  }
}
```

### Multiple Async Operations

```javascript
async function fetchMultipleUsers(userIds) {
  const promises = userIds.map((id) => fetchUser(id));
  const users = await Promise.all(promises);
  return users;
}

// Hoặc xử lý từng user một
async function fetchUsersSequentially(userIds) {
  const users = [];
  for (const id of userIds) {
    try {
      const user = await fetchUser(id);
      users.push(user);
    } catch (error) {
      console.error(`Failed to fetch user ${id}:`, error);
    }
  }
  return users;
}
```

## Advanced Patterns

### Async trong Loops

```javascript
// Sequential execution (chờ từng request hoàn thành)
async function processUsersSequentially(userIds) {
  const results = [];
  for (const id of userIds) {
    const user = await fetchUser(id);
    results.push(user);
    console.log(`Processed user ${id}`);
  }
  return results;
}

// Parallel execution (chạy tất cả cùng lúc)
async function processUsersInParallel(userIds) {
  const promises = userIds.map((id) => fetchUser(id));
  const results = await Promise.all(promises);
  console.log("All users processed");
  return results;
}

// Limited concurrency (chạy tối đa n requests cùng lúc)
async function processUsersWithLimit(userIds, limit = 3) {
  const results = [];

  for (let i = 0; i < userIds.length; i += limit) {
    const batch = userIds.slice(i, i + limit);
    const batchPromises = batch.map((id) => fetchUser(id));
    const batchResults = await Promise.all(batchPromises);
    results.push(...batchResults);
  }

  return results;
}
```

### Async Generators

```javascript
// Async generator function
async function* asyncGenerator() {
  let i = 0;
  while (i < 3) {
    // Giả lập async operation
    await new Promise((resolve) => setTimeout(resolve, 1000));
    yield i++;
  }
}

// Sử dụng async generator
async function consumeAsyncGenerator() {
  for await (const value of asyncGenerator()) {
    console.log("Received:", value);
  }
}

consumeAsyncGenerator();
```

### Timeout với Async/Await

```javascript
function timeout(ms) {
  return new Promise((_, reject) => {
    setTimeout(() => reject(new Error("Timeout")), ms);
  });
}

async function fetchWithTimeout(url, timeoutMs = 5000) {
  try {
    const response = await Promise.race([fetch(url), timeout(timeoutMs)]);
    return response;
  } catch (error) {
    if (error.message === "Timeout") {
      throw new Error(`Request to ${url} timed out`);
    }
    throw error;
  }
}
```

## Real-world Examples

### API Service Class

```javascript
class ApiService {
  constructor(baseURL) {
    this.baseURL = baseURL;
  }

  async request(endpoint, options = {}) {
    const url = `${this.baseURL}${endpoint}`;
    const config = {
      headers: {
        "Content-Type": "application/json",
        ...options.headers,
      },
      ...options,
    };

    try {
      const response = await fetch(url, config);

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }

      return await response.json();
    } catch (error) {
      console.error(`API request failed: ${error.message}`);
      throw error;
    }
  }

  async getUsers() {
    return this.request("/users");
  }

  async getUser(id) {
    return this.request(`/users/${id}`);
  }

  async createUser(userData) {
    return this.request("/users", {
      method: "POST",
      body: JSON.stringify(userData),
    });
  }

  async updateUser(id, userData) {
    return this.request(`/users/${id}`, {
      method: "PUT",
      body: JSON.stringify(userData),
    });
  }

  async deleteUser(id) {
    return this.request(`/users/${id}`, {
      method: "DELETE",
    });
  }
}

// Sử dụng
const api = new ApiService("https://jsonplaceholder.typicode.com");

async function demo() {
  try {
    // Tạo user mới
    const newUser = await api.createUser({
      name: "John Doe",
      email: "john@example.com",
    });
    console.log("Created user:", newUser);

    // Lấy danh sách users
    const users = await api.getUsers();
    console.log("Total users:", users.length);

    // Cập nhật user
    if (users.length > 0) {
      const updatedUser = await api.updateUser(users[0].id, {
        name: "Jane Doe",
      });
      console.log("Updated user:", updatedUser);
    }
  } catch (error) {
    console.error("Demo failed:", error);
  }
}

demo();
```

### File Upload với Progress

```javascript
async function uploadFile(file, onProgress) {
  const formData = new FormData();
  formData.append("file", file);

  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();

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
async function handleFileUpload(file) {
  try {
    const result = await uploadFile(file, (progress) => {
      console.log(`Upload progress: ${progress.toFixed(2)}%`);
    });
    console.log("Upload successful:", result);
  } catch (error) {
    console.error("Upload failed:", error);
  }
}
```

### Retry Mechanism

```javascript
async function retryAsync(fn, maxRetries = 3, delay = 1000) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      if (attempt === maxRetries) {
        throw error;
      }

      console.log(`Attempt ${attempt} failed, retrying in ${delay}ms...`);
      await new Promise((resolve) => setTimeout(resolve, delay));
      delay *= 2; // Exponential backoff
    }
  }
}

// Sử dụng
async function unreliableApiCall() {
  // Giả lập API call có thể fail
  if (Math.random() < 0.7) {
    throw new Error("API call failed");
  }
  return { data: "Success!" };
}

async function makeReliableCall() {
  try {
    const result = await retryAsync(unreliableApiCall, 5, 500);
    console.log("Final result:", result);
  } catch (error) {
    console.error("All retries failed:", error);
  }
}
```

## Best Practices

### 1. Luôn sử dụng try/catch

```javascript
// ❌ Bad
async function badExample() {
  const result = await someAsyncOperation();
  return result;
}

// ✅ Good
async function goodExample() {
  try {
    const result = await someAsyncOperation();
    return result;
  } catch (error) {
    console.error("Operation failed:", error);
    throw error;
  }
}
```

### 2. Tránh await trong loops không cần thiết

```javascript
// ❌ Bad - sequential execution
async function badLoop(userIds) {
  const results = [];
  for (const id of userIds) {
    const user = await fetchUser(id); // Chờ từng request
    results.push(user);
  }
  return results;
}

// ✅ Good - parallel execution
async function goodLoop(userIds) {
  const promises = userIds.map((id) => fetchUser(id));
  return await Promise.all(promises);
}
```

### 3. Sử dụng Promise.allSettled cho multiple independent operations

```javascript
async function fetchAllData(userIds, postIds) {
  const [usersResult, postsResult] = await Promise.allSettled([
    Promise.all(userIds.map(fetchUser)),
    Promise.all(postIds.map(fetchPost)),
  ]);

  const users = usersResult.status === "fulfilled" ? usersResult.value : [];
  const posts = postsResult.status === "fulfilled" ? postsResult.value : [];

  return { users, posts };
}
```

### 4. Top-level await (ES2022)

```javascript
// Trong modules, có thể sử dụng await ở top level
const data = await fetchData();
console.log(data); // Không cần wrap trong async function
```

## Kết luận

Async/await đã cách mạng hóa cách chúng ta viết asynchronous code trong JavaScript. Nó giúp code dễ đọc, dễ hiểu và dễ debug hơn. Khi kết hợp với Promises và các patterns khác, async/await cho phép chúng ta viết code asynchronous phức tạp một cách tự nhiên như synchronous code.

Hãy bắt đầu sử dụng async/await trong dự án của bạn để cải thiện chất lượng code!
