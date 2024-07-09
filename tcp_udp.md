
# TCP（传输控制协议）

TCP（传输控制协议，Transmission Control Protocol）是一种面向连接的、可靠的、基于字节流的传输层通信协议。它是因特网协议套件的一部分，确保数据在两个通信端之间可靠、按顺序传输。TCP具有以下几个关键特性：

1. **可靠性**：TCP通过确认（ACK）机制和重传机制保证数据传输的可靠性。发送方在发送数据后，等待接收方的确认。如果在一定时间内没有收到确认，发送方会重新发送数据。
2. **有序性**：TCP使用序列号（sequence number）来确保数据按顺序到达接收方。接收方根据序列号重新组装数据包。
3. **流量控制**：TCP使用滑动窗口机制来进行流量控制，防止发送方发送数据过快，导致接收方处理不过来。
4. **拥塞控制**：TCP通过拥塞控制算法（如慢启动、拥塞避免、快速重传和快速恢复）来防止网络拥塞，确保网络资源的高效利用。

## C++实现TCP

在C++中，实现TCP通信通常使用Socket编程。以下是一个简单的TCP客户端和服务器的示例：

<img src="https://github.com/Youn8ch/Leaning_notes/blob/master/Img/img_5.png" alt="img" style="float: left;" />
 

### TCP服务器

```cpp
#include <iostream>
#include <string>
#include <cstring>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <unistd.h>

int main() {
    int server_fd, new_socket;
    struct sockaddr_in address;
    int opt = 1;
    int addrlen = sizeof(address);
    char buffer[1024] = {0};
    const char *hello = "Hello from server";

    // 创建socket文件描述符
    if ((server_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) {
        perror("socket failed");
        exit(EXIT_FAILURE);
    }

    // 绑定端口和IP
    if (setsockopt(server_fd, SOL_SOCKET, SO_REUSEADDR | SO_REUSEPORT, &opt, sizeof(opt))) {
        perror("setsockopt");
        exit(EXIT_FAILURE);
    }
    address.sin_family = AF_INET;
    address.sin_addr.s_addr = INADDR_ANY;
    address.sin_port = htons(8080);

    // 绑定socket到地址
    if (bind(server_fd, (struct sockaddr *)&address, sizeof(address)) < 0) {
        perror("bind failed");
        exit(EXIT_FAILURE);
    }

    // 监听端口
    if (listen(server_fd, 3) < 0) {
        perror("listen");
        exit(EXIT_FAILURE);
    }

    // 接受客户端连接
    if ((new_socket = accept(server_fd, (struct sockaddr *)&address, (socklen_t*)&addrlen)) < 0) {
        perror("accept");
        exit(EXIT_FAILURE);
    }

    // 读取客户端发送的数据
    read(new_socket, buffer, 1024);
    std::cout << "Message from client: " << buffer << std::endl;

    // 发送数据到客户端
    send(new_socket, hello, strlen(hello), 0);
    std::cout << "Hello message sent" << std::endl;

    close(new_socket);
    close(server_fd);
    return 0;
}
```

### TCP客户端

```cpp
#include <iostream>
#include <string>
#include <cstring>
#include <sys/socket.h>
#include <arpa/inet.h>
#include <unistd.h>

int main() {
    int sock = 0;
    struct sockaddr_in serv_addr;
    const char *hello = "Hello from client";
    char buffer[1024] = {0};

    // 创建socket文件描述符
    if ((sock = socket(AF_INET, SOCK_STREAM, 0)) < 0) {
        std::cout << "
 Socket creation error 
";
        return -1;
    }

    serv_addr.sin_family = AF_INET;
    serv_addr.sin_port = htons(8080);

    // 转换地址
    if (inet_pton(AF_INET, "127.0.0.1", &serv_addr.sin_addr) <= 0) {
        std::cout << "
Invalid address/ Address not supported 
";
        return -1;
    }

    // 连接服务器
    if (connect(sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr)) < 0) {
        std::cout << "
Connection Failed 
";
        return -1;
    }

    // 发送数据到服务器
    send(sock, hello, strlen(hello), 0);
    std::cout << "Hello message sent" << std::endl;

    // 读取服务器返回的数据
    read(sock, buffer, 1024);
    std::cout << "Message from server: " << buffer << std::endl;

    close(sock);
    return 0;
}
```

# TCP 三次握手和两次握手

## TCP三次握手

TCP协议使用三次握手（Three-Way Handshake）来建立可靠的连接。三次握手的过程如下：

1. **第一次握手：客户端发送SYN**  
   客户端向服务器发送一个SYN（synchronize）标志位为1的TCP报文，并选择一个初始序列号`seq`。此时，客户端进入SYN_SENT状态。

   ```
   客户端  ->  服务器：SYN=1, seq=x
   ```

2. **第二次握手：服务器发送SYN-ACK**  
   服务器收到客户端的SYN报文后，发送一个SYN标志位和ACK（acknowledgment）标志位都为1的TCP报文，确认客户端的SYN，并选择自己的初始序列号`seq`。此时，服务器进入SYN_RCVD状态。

   ```
   服务器  ->  客户端：SYN=1, ACK=1, seq=y, ack=x+1
   ```

3. **第三次握手：客户端发送ACK**  
   客户端收到服务器的SYN-ACK报文后，发送一个ACK标志位为1的TCP报文，确认服务器的SYN。此时，客户端和服务器都进入ESTABLISHED状态，完成三次握手。

   ```
   客户端  ->  服务器：ACK=1, seq=x+1, ack=y+1
   ```

三次握手后，客户端和服务器之间的TCP连接正式建立，可以进行数据传输。

## TCP两次握手

在某些特定场景下，可以使用两次握手来建立连接，通常在一些应用层协议中会采用这种方式。两次握手的过程如下：

1. **第一次握手：客户端发送SYN**  
   客户端向服务器发送一个SYN报文，选择一个初始序列号`seq`。

   ```
   客户端  ->  服务器：SYN=1, seq=x
   ```

2. **第二次握手：服务器发送ACK**  
   服务器收到SYN报文后，发送一个ACK报文，确认客户端的SYN。

   ```
   服务器  ->  客户端：ACK=1, ack=x+1
   ```

在这种情况下，服务器不发送SYN报文，因此省略了第三次握手。这种方式在实际应用中较少使用，因为它无法确保连接的可靠性和双方状态的一致性。