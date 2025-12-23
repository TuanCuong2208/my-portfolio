---
title: "So sánh TCP và UDP trong Java: Lựa chọn giao thức phù hợp"
date: 2024-12-11
draft: false
---

## Tổng quan về TCP và UDP

TCP (Transmission Control Protocol) và UDP (User Datagram Protocol) là hai giao thức tầng transport chính trong mô hình TCP/IP. Mỗi giao thức có ưu nhược điểm riêng và phù hợp với các tình huống khác nhau.

## So sánh TCP vs UDP

| Đặc điểm | TCP | UDP |
|----------|-----|-----|
| Kiểu kết nối | Connection-oriented | Connectionless |
| Độ tin cậy | Đảm bảo gửi nhận | Không đảm bảo |
| Thứ tự dữ liệu | Đảm bảo | Không đảm bảo |
| Tốc độ | Chậm hơn | Nhanh hơn |
| Header size | 20 bytes | 8 bytes |
| Kiểm tra lỗi | Có | Có (nhưng đơn giản) |
| Flow control | Có | Không |
| Ứng dụng | HTTP, FTP, Email | DNS, Streaming, Gaming |

## Lập trình TCP trong Java

### TCP Server

```java
import java.io.*;
import java.net.*;

public class TCPServer {
    public static void main(String[] args) {
        try (ServerSocket serverSocket = new ServerSocket(5000)) {
            System.out.println("TCP Server đang chạy...");
            
            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("Client kết nối: " + 
                    clientSocket.getInetAddress());
                
                // Xử lý client trong thread riêng
                new Thread(() -> handleClient(clientSocket)).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    private static void handleClient(Socket socket) {
        try (
            BufferedReader in = new BufferedReader(
                new InputStreamReader(socket.getInputStream())
            );
            PrintWriter out = new PrintWriter(
                socket.getOutputStream(), true
            )
        ) {
            String message = in.readLine();
            System.out.println("Nhận: " + message);
            out.println("Echo: " + message);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### TCP Client

```java
import java.io.*;
import java.net.*;

public class TCPClient {
    public static void main(String[] args) {
        try (Socket socket = new Socket("localhost", 5000)) {
            PrintWriter out = new PrintWriter(
                socket.getOutputStream(), true
            );
            BufferedReader in = new BufferedReader(
                new InputStreamReader(socket.getInputStream())
            );
            
            out.println("Hello from TCP Client");
            String response = in.readLine();
            System.out.println("Server phản hồi: " + response);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Lập trình UDP trong Java

### UDP Server

```java
import java.net.*;

public class UDPServer {
    public static void main(String[] args) {
        try (DatagramSocket socket = new DatagramSocket(5000)) {
            System.out.println("UDP Server đang chạy...");
            
            byte[] buffer = new byte[1024];
            
            while (true) {
                DatagramPacket packet = new DatagramPacket(
                    buffer, buffer.length
                );
                socket.receive(packet);
                
                String message = new String(
                    packet.getData(), 0, packet.getLength()
                );
                System.out.println("Nhận: " + message);
                
                // Gửi lại cho client
                String response = "Echo: " + message;
                byte[] responseData = response.getBytes();
                
                DatagramPacket responsePacket = new DatagramPacket(
                    responseData,
                    responseData.length,
                    packet.getAddress(),
                    packet.getPort()
                );
                
                socket.send(responsePacket);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### UDP Client

```java
import java.net.*;

public class UDPClient {
    public static void main(String[] args) {
        try (DatagramSocket socket = new DatagramSocket()) {
            InetAddress address = InetAddress.getByName("localhost");
            
            String message = "Hello from UDP Client";
            byte[] buffer = message.getBytes();
            
            DatagramPacket packet = new DatagramPacket(
                buffer, buffer.length, address, 5000
            );
            socket.send(packet);
            
            // Nhận phản hồi
            byte[] responseBuffer = new byte[1024];
            DatagramPacket responsePacket = new DatagramPacket(
                responseBuffer, responseBuffer.length
            );
            socket.receive(responsePacket);
            
            String response = new String(
                responsePacket.getData(), 
                0, 
                responsePacket.getLength()
            );
            System.out.println("Server phản hồi: " + response);
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## Khi nào dùng TCP, khi nào dùng UDP?

### Dùng TCP khi:
- Cần đảm bảo dữ liệu được gửi đến đầy đủ và đúng thứ tự
- Ứng dụng quan trọng (banking, e-commerce)
- File transfer, email, web browsing

### Dùng UDP khi:
- Tốc độ quan trọng hơn độ tin cậy
- Có thể chấp nhận mất một số gói tin
- Streaming video/audio, online gaming, DNS queries

## Kết luận

Việc lựa chọn giữa TCP và UDP phụ thuộc vào yêu cầu cụ thể của ứng dụng. TCP phù hợp cho các ứng dụng cần độ tin cậy cao, trong khi UDP tối ưu cho các ứng dụng real-time cần tốc độ.
