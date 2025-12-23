---
title: "HTTP Client trong Java: Sử dụng HttpURLConnection và HttpClient API"
date: 2024-12-14
draft: false
---

## Giới thiệu về HTTP trong Java

HTTP (HyperText Transfer Protocol) là giao thức nền tảng của web. Java cung cấp nhiều cách để thực hiện HTTP request, từ cách truyền thống với `HttpURLConnection` đến API hiện đại `HttpClient` (Java 11+).

## Cách 1: Sử dụng HttpURLConnection

```java
import java.io.*;
import java.net.*;

public class HttpURLConnectionExample {
    public static void main(String[] args) throws IOException {
        URL url = new URL("https://api.github.com/users/github");
        HttpURLConnection conn = (HttpURLConnection) url.openConnection();
        
        conn.setRequestMethod("GET");
        conn.setRequestProperty("User-Agent", "Java Client");
        
        int responseCode = conn.getResponseCode();
        System.out.println("Response Code: " + responseCode);
        
        BufferedReader in = new BufferedReader(
            new InputStreamReader(conn.getInputStream())
        );
        
        String inputLine;
        StringBuilder response = new StringBuilder();
        
        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }
        in.close();
        
        System.out.println(response.toString());
    }
}
```

## Cách 2: HttpClient API (Java 11+)

```java
import java.net.URI;
import java.net.http.*;
import java.net.http.HttpResponse.BodyHandlers;

public class HttpClientExample {
    public static void main(String[] args) throws Exception {
        HttpClient client = HttpClient.newHttpClient();
        
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.github.com/users/github"))
            .header("User-Agent", "Java Client")
            .GET()
            .build();
        
        HttpResponse response = client.send(
            request, 
            BodyHandlers.ofString()
        );
        
        System.out.println("Status Code: " + response.statusCode());
        System.out.println("Response Body: " + response.body());
    }
}
```

## So sánh hai phương pháp

| Tiêu chí | HttpURLConnection | HttpClient |
|----------|-------------------|------------|
| Từ phiên bản | Java 1.1 | Java 11 |
| Cú pháp | Phức tạp | Đơn giản, fluent |
| Async support | Không | Có |
| HTTP/2 | Không | Có |

## Kết luận

Nếu bạn đang dùng Java 11 trở lên, nên sử dụng `HttpClient` API vì nó hiện đại, dễ dùng và hỗ trợ tốt hơn các tính năng mới của HTTP.
