---
title: "Lập trình Socket với Java: Xây dựng ứng dụng Client-Server cơ bản"
date: 2024-12-15
draft: false
---

## Giới thiệu

Socket programming là nền tảng của lập trình mạng, cho phép các ứng dụng giao tiếp với nhau qua mạng. Trong bài viết này, chúng ta sẽ tìm hiểu cách xây dựng một ứng dụng client-server đơn giản bằng Java.

## Socket là gì?

Socket là một điểm cuối (endpoint) trong giao tiếp hai chiều giữa hai chương trình chạy trên mạng. Java cung cấp package `java.net` với các class như `Socket` và `ServerSocket` để làm việc với socket.

## Xây dựng Server

```java
import java.io.*;
import java.net.*;

public class SimpleServer {
    public static void main(String[] args) {
        try (ServerSocket serverSocket = new ServerSocket(8080)) {
            System.out.println("Server đang lắng nghe tại port 8080...");
            
            Socket clientSocket = serverSocket.accept();
            System.out.println("Client đã kết nối!");
            
            BufferedReader in = new BufferedReader(
                new InputStreamReader(clientSocket.getInputStream())
            );
            
            String message = in.readLine();
            System.out.println("Nhận từ client: " + message);
            
            clientSocket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Xây dựng Client

```java
import java.io.*;
import java.net.*;

public class SimpleClient {
    public static void main(String[] args) {
        try (Socket socket = new Socket("localhost", 8080)) {
            PrintWriter out = new PrintWriter(
                socket.getOutputStream(), true
            );
            
            out.println("Xin chào từ Client!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Kết luận

Qua bài viết này, bạn đã hiểu được cơ bản về socket programming với Java. Đây là nền tảng để xây dựng các ứng dụng mạng phức tạp hơn như chat application, file transfer, hay game multiplayer.
