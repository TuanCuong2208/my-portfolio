---
title: "AJAX và XMLHttpRequest: Kỹ thuật bất đồng bộ trong JavaScript"
date: 2024-12-07
draft: false
---

## AJAX là gì?

AJAX (Asynchronous JavaScript and XML) là kỹ thuật cho phép web applications gửi và nhận dữ liệu từ server bất đồng bộ mà không cần reload trang. Mặc dù tên có "XML" nhưng ngày nay AJAX thường dùng với JSON.

## XMLHttpRequest - Cách truyền thống

### GET Request cơ bản

```javascript
const xhr = new XMLHttpRequest();

xhr.open('GET', 'https://api.github.com/users/github', true);

xhr.onload = function() {
  if (xhr.status === 200) {
    const data = JSON.parse(xhr.responseText);
    console.log('User data:', data);
  } else {
    console.error('Error:', xhr.status);
  }
};

xhr.onerror = function() {
  console.error('Request failed');
};

xhr.send();
```

### POST Request với XMLHttpRequest

```javascript
const xhr = new XMLHttpRequest();
xhr.open('POST', 'https://jsonplaceholder.typicode.com/posts', true);

xhr.setRequestHeader('Content-Type', 'application/json');

xhr.onload = function() {
  if (xhr.status === 201) {
    const response = JSON.parse(xhr.responseText);
    console.log('Created:', response);
  }
};

const postData = {
  title: 'My Post',
  body: 'This is content',
  userId: 1
};

xhr.send(JSON.stringify(postData));
```

## Theo dõi Progress Upload/Download

```javascript
const xhr = new XMLHttpRequest();

xhr.upload.addEventListener('progress', (e) => {
  if (e.lengthComputable) {
    const percentComplete = (e.loaded / e.total) * 100;
    console.log(`Upload: ${percentComplete.toFixed(2)}%`);
  }
});

xhr.addEventListener('progress', (e) => {
  if (e.lengthComputable) {
    const percentComplete = (e.loaded / e.total) * 100;
    console.log(`Download: ${percentComplete.toFixed(2)}%`);
  }
});

xhr.open('POST', '/api/upload', true);
xhr.send(formData);
```

## Timeout và Abort

```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', 'https://api.example.com/data', true);

xhr.timeout = 5000;

xhr.ontimeout = function() {
  console.error('Request timed out');
};

xhr.onload = function() {
  console.log('Data:', xhr.responseText);
};

xhr.send();

setTimeout(() => {
  xhr.abort();
  console.log('Request aborted');
}, 2000);
```

## AJAX với jQuery

### GET Request

```javascript
$.ajax({
  url: 'https://api.github.com/users/github',
  method: 'GET',
  success: function(data) {
    console.log('User:', data);
  },
  error: function(xhr, status, error) {
    console.error('Error:', error);
  }
});

$.get('https://api.github.com/users/github', function(data) {
  console.log('User:', data);
});
```

### POST Request

```javascript
$.ajax({
  url: 'https://jsonplaceholder.typicode.com/posts',
  method: 'POST',
  data: JSON.stringify({
    title: 'My Post',
    body: 'Content here',
    userId: 1
  }),
  contentType: 'application/json',
  success: function(response) {
    console.log('Created:', response);
  }
});
```

## So sánh XMLHttpRequest vs Fetch API

| Đặc điểm | XMLHttpRequest | Fetch API |
|----------|---------------|-----------|
| Cú pháp | Phức tạp | Đơn giản, Promises |
| Browser support | Tất cả | Modern browsers |
| Progress tracking | Dễ | Khó hơn |

## Ví dụ thực tế: Load dữ liệu động

```javascript
function loadUsers() {
  const xhr = new XMLHttpRequest();
  xhr.open('GET', 'https://jsonplaceholder.typicode.com/users', true);
  
  xhr.onload = function() {
    if (xhr.status === 200) {
      const users = JSON.parse(xhr.responseText);
      displayUsers(users);
    }
  };
  
  xhr.send();
}

function displayUsers(users) {
  const container = document.getElementById('users-container');
  container.innerHTML = '';
  
  users.forEach(user => {
    const userDiv = document.createElement('div');
    userDiv.className = 'user-card';
    userDiv.innerHTML = `
      ${user.name}
      Email: ${user.email}
      Phone: ${user.phone}
    `;
    container.appendChild(userDiv);
  });
}

document.addEventListener('DOMContentLoaded', loadUsers);
```

## Form Submit với AJAX

```javascript
document.getElementById('myForm').addEventListener('submit', function(e) {
  e.preventDefault();
  
  const formData = new FormData(this);
  const xhr = new XMLHttpRequest();
  
  xhr.open('POST', '/api/submit', true);
  
  xhr.onload = function() {
    if (xhr.status === 200) {
      const response = JSON.parse(xhr.responseText);
      alert('Success: ' + response.message);
    } else {
      alert('Error: ' + xhr.status);
    }
  };
  
  xhr.send(formData);
});
```

## Kết luận

XMLHttpRequest là nền tảng của AJAX và vẫn hữu ích trong nhiều trường hợp, đặc biệt khi cần tracking progress. Tuy nhiên, với các dự án mới, Fetch API với async/await là lựa chọn hiện đại và dễ sử dụng hơn.
