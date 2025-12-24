---
title: "Node.js Fundamentals - Xây dựng Server-side Applications"
date: 2024-12-27T00:00:00Z
draft: false
categories: ["JavaScript"]
tags: ["Node.js", "JavaScript", "Backend", "Server-side"]
summary: "Khám phá Node.js - runtime JavaScript cho server-side development với modules, file system, HTTP server."
---

# Node.js Fundamentals - Xây dựng Server-side Applications

Node.js là một runtime JavaScript cho server-side, cho phép chạy JavaScript bên ngoài browser. Với Node.js, bạn có thể xây dựng web servers, APIs, command-line tools và nhiều loại ứng dụng khác.

## Giới thiệu Node.js

### Tại sao Node.js?

- **JavaScript everywhere**: Sử dụng cùng một ngôn ngữ cho cả frontend và backend
- **Non-blocking I/O**: Xử lý nhiều connections đồng thời với ít tài nguyên
- **NPM ecosystem**: Hàng triệu packages có sẵn
- **Fast development**: Rapid prototyping và development

### Cài đặt và chạy Node.js

```bash
# Kiểm tra version
node --version
npm --version

# Chạy file JavaScript
node app.js

# Chạy REPL (Read-Eval-Print Loop)
node
```

## Modules System

Node.js sử dụng CommonJS modules system.

### Tạo và sử dụng modules

```javascript
// math.js - Custom module
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

module.exports = {
  add,
  subtract,
};

// app.js - Main file
const math = require("./math");

console.log(math.add(5, 3)); // 8
console.log(math.subtract(10, 4)); // 6
```

### Built-in modules

```javascript
// File system module
const fs = require("fs");

// Path module
const path = require("path");

// HTTP module
const http = require("http");

// OS module
const os = require("os");

// Events module
const events = require("events");
```

## File System Operations

### Đọc và ghi file

```javascript
const fs = require("fs");

// Đọc file đồng bộ
try {
  const data = fs.readFileSync("file.txt", "utf8");
  console.log("File content:", data);
} catch (error) {
  console.error("Error reading file:", error);
}

// Đọc file bất đồng bộ
fs.readFile("file.txt", "utf8", (error, data) => {
  if (error) {
    console.error("Error reading file:", error);
    return;
  }
  console.log("File content:", data);
});

// Ghi file
const content = "Hello, Node.js!";
fs.writeFile("output.txt", content, (error) => {
  if (error) {
    console.error("Error writing file:", error);
    return;
  }
  console.log("File written successfully");
});

// Ghi file đồng bộ
try {
  fs.writeFileSync("output.txt", content);
  console.log("File written successfully");
} catch (error) {
  console.error("Error writing file:", error);
}
```

### Thao tác với thư mục

```javascript
const fs = require("fs");
const path = require("path");

// Tạo thư mục
fs.mkdir("new-directory", (error) => {
  if (error) {
    console.error("Error creating directory:", error);
    return;
  }
  console.log("Directory created");
});

// Đọc nội dung thư mục
fs.readdir(".", (error, files) => {
  if (error) {
    console.error("Error reading directory:", error);
    return;
  }
  console.log("Files in directory:", files);
});

// Kiểm tra file/directory tồn tại
fs.stat("file.txt", (error, stats) => {
  if (error) {
    console.error("File does not exist");
    return;
  }

  console.log("Is file:", stats.isFile());
  console.log("Is directory:", stats.isDirectory());
  console.log("Size:", stats.size, "bytes");
  console.log("Modified:", stats.mtime);
});
```

### Streams

```javascript
const fs = require("fs");

// Read stream
const readStream = fs.createReadStream("large-file.txt", {
  encoding: "utf8",
  highWaterMark: 1024, // Chunk size
});

readStream.on("data", (chunk) => {
  console.log("Received chunk:", chunk.length, "characters");
});

readStream.on("end", () => {
  console.log("File reading completed");
});

readStream.on("error", (error) => {
  console.error("Error reading file:", error);
});

// Write stream
const writeStream = fs.createWriteStream("output.txt");

writeStream.write("Hello, ");
writeStream.write("Node.js!");
writeStream.end();

// Pipe streams (copy file)
const readStream2 = fs.createReadStream("source.txt");
const writeStream2 = fs.createWriteStream("destination.txt");

readStream2.pipe(writeStream2);

writeStream2.on("finish", () => {
  console.log("File copied successfully");
});
```

## HTTP Server

### Tạo basic HTTP server

```javascript
const http = require("http");

// Tạo server
const server = http.createServer((request, response) => {
  // Set response header
  response.writeHead(200, {
    "Content-Type": "text/html; charset=utf-8",
  });

  // Send response
  response.end("<h1>Hello, Node.js Server!</h1>");
});

// Lắng nghe trên port 3000
const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server running at http://localhost:${PORT}/`);
});
```

### Xử lý routes

```javascript
const http = require("http");
const url = require("url");

const server = http.createServer((request, response) => {
  const parsedUrl = url.parse(request.url, true);
  const path = parsedUrl.pathname;
  const method = request.method;

  response.setHeader("Content-Type", "application/json");

  if (path === "/" && method === "GET") {
    response.writeHead(200);
    response.end(JSON.stringify({ message: "Welcome to Node.js API" }));
  } else if (path === "/users" && method === "GET") {
    response.writeHead(200);
    response.end(
      JSON.stringify([
        { id: 1, name: "John Doe" },
        { id: 2, name: "Jane Smith" },
      ])
    );
  } else if (path.startsWith("/users/") && method === "GET") {
    const userId = path.split("/")[2];
    response.writeHead(200);
    response.end(
      JSON.stringify({
        id: userId,
        name: `User ${userId}`,
      })
    );
  } else {
    response.writeHead(404);
    response.end(JSON.stringify({ error: "Route not found" }));
  }
});

server.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

### Xử lý POST data

```javascript
const http = require("http");

const server = http.createServer((request, response) => {
  if (request.method === "POST" && request.url === "/users") {
    let body = "";

    // Nhận data
    request.on("data", (chunk) => {
      body += chunk.toString();
    });

    // Xử lý khi nhận xong
    request.on("end", () => {
      try {
        const userData = JSON.parse(body);
        console.log("Received user data:", userData);

        // Validate data
        if (!userData.name || !userData.email) {
          response.writeHead(400, { "Content-Type": "application/json" });
          response.end(
            JSON.stringify({ error: "Name and email are required" })
          );
          return;
        }

        // Simulate saving to database
        const newUser = {
          id: Date.now(),
          ...userData,
          createdAt: new Date().toISOString(),
        };

        response.writeHead(201, { "Content-Type": "application/json" });
        response.end(JSON.stringify(newUser));
      } catch (error) {
        response.writeHead(400, { "Content-Type": "application/json" });
        response.end(JSON.stringify({ error: "Invalid JSON" }));
      }
    });
  } else {
    response.writeHead(404, { "Content-Type": "application/json" });
    response.end(JSON.stringify({ error: "Route not found" }));
  }
});

server.listen(3000);
```

## NPM và Package Management

### Khởi tạo project

```bash
# Khởi tạo package.json
npm init -y

# Cài đặt dependencies
npm install express cors helmet
npm install --save-dev nodemon

# Cài đặt global packages
npm install -g nodemon
```

### package.json

```json
{
  "name": "my-node-app",
  "version": "1.0.0",
  "description": "A Node.js application",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": ["node", "javascript"],
  "author": "Your Name",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.2",
    "cors": "^2.8.5",
    "helmet": "^7.0.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

## Express.js Framework

Express.js là framework web phổ biến nhất cho Node.js.

### Basic Express app

```javascript
const express = require("express");
const app = express();

// Middleware
app.use(express.json()); // Parse JSON bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies

// Routes
app.get("/", (req, res) => {
  res.json({ message: "Welcome to Express API" });
});

app.get("/users", (req, res) => {
  const users = [
    { id: 1, name: "John Doe", email: "john@example.com" },
    { id: 2, name: "Jane Smith", email: "jane@example.com" },
  ];
  res.json(users);
});

app.get("/users/:id", (req, res) => {
  const userId = parseInt(req.params.id);
  // Simulate database lookup
  const user = { id: userId, name: `User ${userId}` };
  res.json(user);
});

app.post("/users", (req, res) => {
  const { name, email } = req.body;

  if (!name || !email) {
    return res.status(400).json({ error: "Name and email are required" });
  }

  const newUser = {
    id: Date.now(),
    name,
    email,
    createdAt: new Date(),
  };

  res.status(201).json(newUser);
});

// Error handling middleware
app.use((error, req, res, next) => {
  console.error(error.stack);
  res.status(500).json({ error: "Something went wrong!" });
});

// 404 handler
app.use((req, res) => {
  res.status(404).json({ error: "Route not found" });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

### Middleware

```javascript
const express = require("express");
const cors = require("cors");
const helmet = require("helmet");
const morgan = require("morgan");

const app = express();

// Security middleware
app.use(helmet());

// CORS middleware
app.use(
  cors({
    origin: ["http://localhost:3000", "https://yourdomain.com"],
    credentials: true,
  })
);

// Logging middleware
app.use(morgan("combined"));

// Custom middleware
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path} - ${new Date().toISOString()}`);
  next();
});

// Authentication middleware
const authenticate = (req, res, next) => {
  const token = req.header("Authorization");

  if (!token) {
    return res.status(401).json({ error: "Access denied" });
  }

  // Verify token (simplified)
  if (token !== "valid-token") {
    return res.status(401).json({ error: "Invalid token" });
  }

  next();
};

// Protected route
app.get("/protected", authenticate, (req, res) => {
  res.json({ message: "This is a protected route" });
});
```

### Router

```javascript
const express = require("express");
const router = express.Router();

// User routes
router.get("/", (req, res) => {
  res.json({ message: "Get all users" });
});

router.get("/:id", (req, res) => {
  const userId = req.params.id;
  res.json({ message: `Get user ${userId}` });
});

router.post("/", (req, res) => {
  const userData = req.body;
  res.json({ message: "Create user", data: userData });
});

router.put("/:id", (req, res) => {
  const userId = req.params.id;
  const userData = req.body;
  res.json({ message: `Update user ${userId}`, data: userData });
});

router.delete("/:id", (req, res) => {
  const userId = req.params.id;
  res.json({ message: `Delete user ${userId}` });
});

module.exports = router;
```

```javascript
// app.js
const express = require("express");
const userRoutes = require("./routes/users");

const app = express();

app.use(express.json());
app.use("/api/users", userRoutes);

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

## Events và EventEmitter

```javascript
const EventEmitter = require("events");

// Tạo custom event emitter
class Logger extends EventEmitter {
  log(message) {
    console.log(`Log: ${message}`);
    this.emit("logged", { message, timestamp: new Date() });
  }

  error(message) {
    console.error(`Error: ${message}`);
    this.emit("error", new Error(message));
  }
}

const logger = new Logger();

// Lắng nghe events
logger.on("logged", (data) => {
  console.log("Log event received:", data);
});

logger.on("error", (error) => {
  console.error("Error event received:", error.message);
});

// Sử dụng
logger.log("Application started");
logger.error("Something went wrong");
```

## Environment Variables

```javascript
// .env file
PORT=3000
NODE_ENV=development
DATABASE_URL=mongodb://localhost:27017/myapp
JWT_SECRET=mysecretkey
```

```javascript
// Sử dụng dotenv package
require("dotenv").config();

const express = require("express");
const app = express();

const PORT = process.env.PORT || 3000;
const NODE_ENV = process.env.NODE_ENV || "development";

app.get("/", (req, res) => {
  res.json({
    message: "Hello World",
    environment: NODE_ENV,
    port: PORT,
  });
});

app.listen(PORT, () => {
  console.log(`Server running in ${NODE_ENV} mode on port ${PORT}`);
});
```

## Error Handling

### Global error handler

```javascript
const express = require("express");
const app = express();

app.use(express.json());

// Async error handling middleware
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

// Route với async error handling
app.get(
  "/users",
  asyncHandler(async (req, res) => {
    // Simulate database error
    throw new Error("Database connection failed");
    res.json({ users: [] });
  })
);

// Custom error class
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
  }
}

// Middleware tạo errors
app.use((req, res, next) => {
  const error = new AppError("Not found", 404);
  next(error);
});

// Global error handler
app.use((error, req, res, next) => {
  const statusCode = error.statusCode || 500;
  const message = error.message || "Internal Server Error";

  console.error(`Error ${statusCode}: ${message}`);

  res.status(statusCode).json({
    success: false,
    error: message,
    ...(process.env.NODE_ENV === "development" && { stack: error.stack }),
  });
});
```

## Best Practices

### 1. Project Structure

```
my-node-app/
├── src/
│   ├── controllers/
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   ├── services/
│   ├── utils/
│   └── app.js
├── tests/
├── .env
├── package.json
└── README.md
```

### 2. Security

```javascript
const express = require("express");
const helmet = require("helmet");
const rateLimit = require("express-rate-limit");
const mongoSanitize = require("express-mongo-sanitize");
const xss = require("xss-clean");

const app = express();

// Security middleware
app.use(helmet());

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
});
app.use("/api/", limiter);

// Data sanitization
app.use(mongoSanitize()); // Prevent NoSQL injection
app.use(xss()); // Prevent XSS attacks

// CORS
const cors = require("cors");
app.use(
  cors({
    origin: process.env.CLIENT_URL,
    credentials: true,
  })
);
```

### 3. Performance

```javascript
const express = require("express");
const compression = require("compression");

const app = express();

// Compression middleware
app.use(compression());

// Static files caching
app.use(
  express.static("public", {
    maxAge: "1d", // Cache for 1 day
  })
);

// Database connection pooling
const mongoose = require("mongoose");
mongoose.connect(process.env.DATABASE_URL, {
  maxPoolSize: 10, // Maintain up to 10 socket connections
  serverSelectionTimeoutMS: 5000, // Keep trying to send operations for 5 seconds
  socketTimeoutMS: 45000, // Close sockets after 45 seconds of inactivity
});
```

## Kết luận

Node.js cung cấp một nền tảng mạnh mẽ để xây dựng server-side applications. Với ecosystem NPM phong phú và khả năng xử lý non-blocking I/O, Node.js phù hợp cho việc xây dựng APIs, real-time applications, và microservices.

Express.js làm cho việc phát triển web applications trở nên dễ dàng hơn với routing, middleware, và error handling. Khi kết hợp với các best practices về security và performance, bạn có thể xây dựng các ứng dụng Node.js production-ready.

Hãy bắt đầu xây dựng ứng dụng Node.js đầu tiên của bạn!
