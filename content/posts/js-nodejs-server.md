---
title: "Xây dựng HTTP Server đơn giản với Node.js"
date: 2024-12-08
draft: false
---

## Giới thiệu Node.js

Node.js là một JavaScript runtime được xây dựng trên V8 engine của Chrome. Nó cho phép chạy JavaScript ở phía server, rất mạnh mẽ cho việc xây dựng các ứng dụng network.

## HTTP Server cơ bản

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain; charset=utf-8');
  res.end('Xin chào từ Node.js Server!');
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server đang chạy tại http://localhost:${PORT}`);
});
```

## Xử lý Routes

```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const path = parsedUrl.pathname;
  const method = req.method;
  
  res.setHeader('Content-Type', 'application/json');
  
  if (path === '/' && method === 'GET') {
    res.statusCode = 200;
    res.end(JSON.stringify({
      message: 'Chào mừng đến với API',
      endpoints: ['/users', '/products']
    }));
  }
  
  else if (path === '/users' && method === 'GET') {
    res.statusCode = 200;
    res.end(JSON.stringify({
      users: [
        { id: 1, name: 'Nguyễn Văn A' },
        { id: 2, name: 'Trần Thị B' }
      ]
    }));
  }
  
  else {
    res.statusCode = 404;
    res.end(JSON.stringify({ error: 'Route không tồn tại' }));
  }
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`Server đang chạy tại http://localhost:${PORT}`);
});
```

## Xử lý POST Request

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  if (req.url === '/api/users' && req.method === 'POST') {
    let body = '';
    
    req.on('data', chunk => {
      body += chunk.toString();
    });
    
    req.on('end', () => {
      try {
        const userData = JSON.parse(body);
        
        console.log('Nhận được user data:', userData);
        
        res.statusCode = 201;
        res.setHeader('Content-Type', 'application/json');
        res.end(JSON.stringify({
          message: 'Tạo user thành công',
          user: userData
        }));
        
      } catch (error) {
        res.statusCode = 400;
        res.end(JSON.stringify({ error: 'Invalid JSON' }));
      }
    });
  } else {
    res.statusCode = 404;
    res.end('Not Found');
  }
});

server.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

## REST API đơn giản

```javascript
const http = require('http');
const url = require('url');

let users = [
  { id: 1, name: 'John Doe', email: 'john@example.com' },
  { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
];

let nextId = 3;

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const path = parsedUrl.pathname;
  const method = req.method;
  
  res.setHeader('Content-Type', 'application/json');
  
  if (path === '/api/users' && method === 'GET') {
    res.statusCode = 200;
    res.end(JSON.stringify(users));
  }
  
  else if (path.match(/^\/api\/users\/\d+$/) && method === 'GET') {
    const id = parseInt(path.split('/')[3]);
    const user = users.find(u => u.id === id);
    
    if (user) {
      res.statusCode = 200;
      res.end(JSON.stringify(user));
    } else {
      res.statusCode = 404;
      res.end(JSON.stringify({ error: 'User not found' }));
    }
  }
  
  else if (path === '/api/users' && method === 'POST') {
    let body = '';
    
    req.on('data', chunk => {
      body += chunk.toString();
    });
    
    req.on('end', () => {
      const newUser = JSON.parse(body);
      newUser.id = nextId++;
      users.push(newUser);
      
      res.statusCode = 201;
      res.end(JSON.stringify(newUser));
    });
  }
  
  else {
    res.statusCode = 404;
    res.end(JSON.stringify({ error: 'Route not found' }));
  }
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`REST API Server running on http://localhost:${PORT}`);
});
```

## Sử dụng Express.js

```javascript
const express = require('express');
const app = express();

app.use(express.json());

app.get('/', (req, res) => {
  res.json({ message: 'Chào mừng đến với Express API' });
});

app.get('/users/:id', (req, res) => {
  const userId = req.params.id;
  res.json({ userId, name: 'User ' + userId });
});

app.post('/users', (req, res) => {
  const userData = req.body;
  res.status(201).json({
    message: 'User created',
    user: userData
  });
});

const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Express server running on port ${PORT}`);
});
```

## Kết luận

Node.js là công cụ mạnh mẽ để xây dựng server-side applications. Module http built-in cho phép tạo server cơ bản, trong khi Express.js giúp code ngắn gọn và dễ bảo trì hơn.
