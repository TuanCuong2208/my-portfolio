---
title: "WebSocket Client với JavaScript: Kết nối real-time đến Server"
date: 2024-12-09
draft: false
---

## WebSocket trong JavaScript

JavaScript hỗ trợ WebSocket API ngay trong trình duyệt, cho phép tạo kết nối real-time với server mà không cần thư viện bên ngoài.

## Tạo WebSocket Connection

```javascript
// Tạo kết nối WebSocket
const socket = new WebSocket('ws://localhost:8080/chat');

// Event: Khi kết nối được mở
socket.addEventListener('open', (event) => {
  console.log('Đã kết nối đến WebSocket server');
  socket.send('Hello Server!');
});

// Event: Khi nhận message từ server
socket.addEventListener('message', (event) => {
  console.log('Message từ server:', event.data);
});

// Event: Khi có lỗi
socket.addEventListener('error', (event) => {
  console.error('WebSocket error:', event);
});

// Event: Khi kết nối đóng
socket.addEventListener('close', (event) => {
  console.log('Kết nối đã đóng');
});
```

## Xây dựng Chat Application

### HTML

```html



  
  WebSocket Chat
  
    #messages {
      height: 300px;
      border: 1px solid #ccc;
      overflow-y: auto;
      padding: 10px;
      margin-bottom: 10px;
    }
    .message {
      margin: 5px 0;
      padding: 5px;
      background: #f0f0f0;
      border-radius: 4px;
    }
  


  WebSocket Chat
  
  
  Gửi
  
  


```

### JavaScript (chat.js)

```javascript
class ChatClient {
  constructor(url) {
    this.socket = new WebSocket(url);
    this.messageContainer = document.getElementById('messages');
    this.messageInput = document.getElementById('messageInput');
    this.sendButton = document.getElementById('sendButton');
    
    this.init();
  }
  
  init() {
    this.socket.onopen = () => {
      this.addMessage('Hệ thống: Đã kết nối đến server', 'system');
    };
    
    this.socket.onmessage = (event) => {
      this.addMessage(event.data, 'received');
    };
    
    this.socket.onerror = (error) => {
      this.addMessage('Lỗi: ' + error.message, 'error');
    };
    
    this.socket.onclose = () => {
      this.addMessage('Hệ thống: Đã ngắt kết nối', 'system');
    };
    
    this.sendButton.onclick = () => this.sendMessage();
    
    this.messageInput.onkeypress = (e) => {
      if (e.key === 'Enter') {
        this.sendMessage();
      }
    };
  }
  
  sendMessage() {
    const message = this.messageInput.value.trim();
    
    if (message && this.socket.readyState === WebSocket.OPEN) {
      this.socket.send(message);
      this.addMessage('Bạn: ' + message, 'sent');
      this.messageInput.value = '';
    }
  }
  
  addMessage(text, type) {
    const messageDiv = document.createElement('div');
    messageDiv.className = `message ${type}`;
    messageDiv.textContent = text;
    
    this.messageContainer.appendChild(messageDiv);
    this.messageContainer.scrollTop = this.messageContainer.scrollHeight;
  }
}

const chat = new ChatClient('ws://localhost:8080/chat');
```

## WebSocket States

```javascript
const socket = new WebSocket('ws://localhost:8080');

switch (socket.readyState) {
  case WebSocket.CONNECTING:
    console.log('Đang kết nối...');
    break;
  case WebSocket.OPEN:
    console.log('Đã kết nối');
    break;
  case WebSocket.CLOSING:
    console.log('Đang đóng kết nối...');
    break;
  case WebSocket.CLOSED:
    console.log('Đã đóng kết nối');
    break;
}
```

## Tự động kết nối lại

```javascript
class ReconnectingWebSocket {
  constructor(url) {
    this.url = url;
    this.reconnectInterval = 3000;
    this.connect();
  }
  
  connect() {
    this.socket = new WebSocket(this.url);
    
    this.socket.onopen = () => {
      console.log('Đã kết nối');
    };
    
    this.socket.onclose = () => {
      console.log('Mất kết nối, đang thử kết nối lại...');
      setTimeout(() => this.connect(), this.reconnectInterval);
    };
    
    this.socket.onerror = (error) => {
      console.error('Lỗi WebSocket:', error);
      this.socket.close();
    };
  }
  
  send(data) {
    if (this.socket.readyState === WebSocket.OPEN) {
      this.socket.send(data);
    } else {
      console.warn('WebSocket chưa sẵn sàng');
    }
  }
}
```

## Kết luận

WebSocket API trong JavaScript rất mạnh mẽ và dễ sử dụng. Nó là lựa chọn tốt nhất cho các ứng dụng cần giao tiếp real-time như chat, notifications, live updates.
