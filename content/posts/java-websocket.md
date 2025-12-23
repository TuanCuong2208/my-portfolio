---
title: "WebSocket Server với Java: Giao tiếp thời gian thực"
date: 2024-12-13
draft: false
---

## WebSocket là gì?

WebSocket là giao thức cung cấp kênh giao tiếp song công (full-duplex) qua một kết nối TCP duy nhất. Khác với HTTP truyền thống (request-response), WebSocket cho phép server chủ động gửi dữ liệu đến client.

## Ứng dụng của WebSocket

- Chat applications
- Real-time notifications
- Live sports scores
- Collaborative editing
- Online gaming

## Xây dựng WebSocket Server với Java EE

```java
import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.util.Set;
import java.util.concurrent.CopyOnWriteArraySet;

@ServerEndpoint("/chat")
public class ChatServer {
    private static Set clients = new CopyOnWriteArraySet<>();
    
    @OnOpen
    public void onOpen(Session session) {
        clients.add(session);
        System.out.println("Kết nối mới: " + session.getId());
    }
    
    @OnMessage
    public void onMessage(String message, Session session) {
        System.out.println("Nhận tin nhắn: " + message);
        
        // Broadcast đến tất cả clients
        for (Session client : clients) {
            try {
                client.getBasicRemote().sendText(message);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    
    @OnClose
    public void onClose(Session session) {
        clients.remove(session);
        System.out.println("Ngắt kết nối: " + session.getId());
    }
    
    @OnError
    public void onError(Session session, Throwable throwable) {
        System.err.println("Lỗi: " + throwable.getMessage());
    }
}
```

## WebSocket với Spring Boot

```java
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

public class ChatWebSocketHandler extends TextWebSocketHandler {
    
    @Override
    protected void handleTextMessage(
        WebSocketSession session, 
        TextMessage message
    ) throws Exception {
        String payload = message.getPayload();
        System.out.println("Nhận: " + payload);
        
        session.sendMessage(new TextMessage("Echo: " + payload));
    }
}
```

## Cấu hình WebSocket trong Spring Boot

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.*;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {
    
    @Override
    public void registerWebSocketHandlers(
        WebSocketHandlerRegistry registry
    ) {
        registry.addHandler(new ChatWebSocketHandler(), "/chat")
                .setAllowedOrigins("*");
    }
}
```

## Kết luận

WebSocket là công nghệ quan trọng cho các ứng dụng real-time. Java cung cấp nhiều cách để implement WebSocket, từ Java EE chuẩn đến Spring Framework.
