---
title: "Fetch API trong JavaScript: Gửi HTTP Request hiện đại"
date: 2024-12-10
draft: false
---

## Giới thiệu Fetch API

Fetch API là một interface hiện đại trong JavaScript để thực hiện HTTP requests. Nó thay thế XMLHttpRequest cũ với cú pháp đơn giản hơn và dựa trên Promises.

## Cú pháp cơ bản

```javascript
fetch(url, options)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error('Error:', error));
```

## GET Request

### Ví dụ đơn giản

```javascript
fetch('https://api.github.com/users/github')
  .then(response => {
    if (!response.ok) {
      throw new Error('Network response was not ok');
    }
    return response.json();
  })
  .then(data => {
    console.log('User data:', data);
    console.log('Name:', data.name);
    console.log('Bio:', data.bio);
  })
  .catch(error => {
    console.error('Fetch error:', error);
  });
```

### Với async/await (Cách hiện đại)

```javascript
async function fetchUserData() {
  try {
    const response = await fetch('https://api.github.com/users/github');
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    console.log('User data:', data);
    return data;
    
  } catch (error) {
    console.error('Error fetching data:', error);
  }
}

fetchUserData();
```

## POST Request

```javascript
async function createPost() {
  const newPost = {
    title: 'My New Post',
    body: 'This is the content',
    userId: 1
  };
  
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify(newPost)
    });
    
    if (!response.ok) {
      throw new Error(`HTTP error! status: ${response.status}`);
    }
    
    const data = await response.json();
    console.log('Created post:', data);
    return data;
    
  } catch (error) {
    console.error('Error creating post:', error);
  }
}

createPost();
```

## PUT Request (Cập nhật)

```javascript
async function updatePost(postId) {
  const updatedPost = {
    id: postId,
    title: 'Updated Title',
    body: 'Updated content',
    userId: 1
  };
  
  try {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/posts/${postId}`,
      {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(updatedPost)
      }
    );
    
    const data = await response.json();
    console.log('Updated post:', data);
    
  } catch (error) {
    console.error('Error updating post:', error);
  }
}

updatePost(1);
```

## DELETE Request

```javascript
async function deletePost(postId) {
  try {
    const response = await fetch(
      `https://jsonplaceholder.typicode.com/posts/${postId}`,
      {
        method: 'DELETE'
      }
    );
    
    if (response.ok) {
      console.log('Post deleted successfully');
    }
    
  } catch (error) {
    console.error('Error deleting post:', error);
  }
}

deletePost(1);
```

## Headers và Authentication

```javascript
async function fetchWithAuth() {
  try {
    const response = await fetch('https://api.example.com/data', {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer YOUR_TOKEN_HERE',
        'Accept': 'application/json'
      }
    });
    
    const data = await response.json();
    return data;
    
  } catch (error) {
    console.error('Error:', error);
  }
}
```

## Xử lý nhiều requests đồng thời

```javascript
async function fetchMultipleUsers() {
  try {
    const promises = [
      fetch('https://api.github.com/users/github'),
      fetch('https://api.github.com/users/microsoft'),
      fetch('https://api.github.com/users/google')
    ];
    
    const responses = await Promise.all(promises);
    const users = await Promise.all(
      responses.map(response => response.json())
    );
    
    console.log('All users:', users);
    return users;
    
  } catch (error) {
    console.error('Error fetching users:', error);
  }
}

fetchMultipleUsers();
```

## Kết luận

Fetch API là công cụ mạnh mẽ và hiện đại để thực hiện HTTP requests trong JavaScript. Kết hợp với async/await, code trở nên dễ đọc và dễ bảo trì hơn nhiều so với XMLHttpRequest truyền thống.