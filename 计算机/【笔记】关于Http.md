## HTTP发展史
HTTP（Hypertext Transfer Protocol，超文本传输协议）是用于在互联网上传输超文本（如网页）的应用层协议。

### 最早版本0.9
仅用于简单的文档浏览，功能极其有限。

### 正式版本1.0

- 引入了请求头（Request Headers）和响应头（Response Headers），支持更多元的数据类型（如图片、CSS等）。
- 支持不同的 HTTP 方法：GET、POST、HEAD。
- 响应中包含状态码（如 200、404 等）。
- 每个请求/响应后都会关闭 TCP 连接，效率较低，每次请求都要重新建立 TCP 连接，存在较大的连接开销（延迟高）。

### HTTP/1.1

- 持久连接（Persistent Connection）：  默认情况下，TCP 连接在多个请求之间保持打开，减少了连接建立的开销。 Connection: keep-alive | close
- 管道化（Pipelining，实际很少使用）：  允许客户端在未收到前一个响应时发送多个请求，但服务器仍需按顺序响应，实际效果有限且存在队头阻塞问题。 队头阻塞（Head-of-Line Blocking）：  在同一个 TCP 连接中，请求必须按顺序处理，前一个请求未完成会阻塞后续请求。 —— 第一个请求（队头）如果卡住了，后面的请求即使已经处理完毕，也得等着。
- 支持更多的请求方法（如 PUT、DELETE 等）。
- 引入了Host 头，使得在一台服务器上可以托管多个网站（虚拟主机支持）。
- 支持分块传输编码（Chunked Transfer Encoding）

由于管道化存在严重的队头阻塞，而且实现复杂、兼容性差， 实际上大多数 HTTP/1.1 的实现并没有启用管道化，而是采用了 “多个 TCP 连接” 的方式来模拟并发：

- 比如一个网页打开时会建立 多个 TCP 连接（通常是 6~8 个） ，每个连接上按顺序发送请求。

### HTTP/2

- 二进制协议：  不再是文本格式，而是二进制格式，更高效、更紧凑，解析更快。
- 多路复用（Multiplexing）：  允许多个请求和响应在同一个 TCP 连接上并发进行，彻底解决了 HTTP/1.1 的队头阻塞问题。
- 头部压缩（HPACK）：  对 HTTP 头部进行压缩，减少冗余数据传输。
- 服务器推送（Server Push）：  服务器可以主动向客户端推送资源，而无需客户端显式请求。
- 流优先级：  可以为不同请求设置优先级，优化资源加载顺序。

除了服务器推送，其他都默认开启。nginx下开启Server Push方法：

```
location = /index.html {
http2_push /style.css;
http2_push /script.js;
}
```
但实际使用中，由于浏览器智能预加载已经很高效， Server Push 的实际收益有限，需谨慎使用。

### HTTP/3
HTTP/2 仍然运行在 TCP 协议之上，而 TCP 是一个面向字节流、强调可靠性和顺序性的协议。应用层解决了队头阻塞问题，传输层却没解决。
为了解决 HTTP/2 中 TCP 层的队头阻塞问题，HTTP/3 转而基于 QUIC 协议，而 QUIC 基于 UDP 而非 TCP。
- 基于 QUIC（Quick UDP Internet Connections）：  QUIC 是由 Google 提出并标准化（IETF），运行在 UDP 上，集成了 TLS 加密、多路复用、快速连接建立等特性。
- 彻底解决队头阻塞：  每个流（stream）独立传输，即使某个流丢包，也不会影响其他流。
- 更快的连接建立：  由于 QUIC 内置了 TLS，握手过程更快（通常 1-RTT 或 0-RTT）。
- 内置加密：  HTTP/3 通常总是加密的，安全性更高。\* 改进移动网络性能：  更适应高丢包、高延迟的网络环境（如移动网络、Wi-Fi 切换等）。

## HTTPS
 HTTPS = HTTP + TLS/SSL（Transport Layer Security / Secure Sockets Layer）（加密层）

工作原理简述

1. 客户端发起请求（Client Hello）
2. 服务器响应（Server Hello），并发送证书（包含公钥）
3. 客户端验证服务器证书，提取公钥
4. 客户端生成一个随机的对称密钥（称为“会话密钥”或“预主密钥”），用服务器的公钥加密后传给服务器
5. 服务器用自己的私钥解密得到对称密钥
6. 双方后续通信都使用这个对称密钥加密

## 关于TCP的粘包
“粘包”（Sticky Packet） 并不是TCP协议本身的一个错误或问题，而是在使用TCP进行数据通信时，由于TCP是面向字节流的协议，开发者在使用过程中可能遇到的一种现象。

- 发送方依次发送：
  - 包1："Hello"
  - 包2："World"
- 接收方可能收到：
  - 情况1：一次收到 "HelloWorld"（两个包粘在一起了）
  - 情况2：正常分别收到 "Hello" 和 "World"（没粘包）

  TCP 是面向字节流（Byte Stream）的协议，它本身不保留应用层消息的边界。TCP 只保证数据可靠传输，按顺序到达，但不关心你应用层每次发送的数据是“一条消息”还是“多条消息”。
  既然 TCP 不帮我们区分消息边界，那我们就需要在应用层协议上自己定义好消息的格式与边界

- 请求行 + Headers + 空行（\r\n\r\n） ：标识 HTTP 头部结束。
- Content-Length 或 Transfer-Encoding：标识消息体的长度或者传输方式，从而让接收方能准确知道消息体在哪里结束。

什么是`\r\n`？[和普通换行符有什么不一样](https://stackoverflow.com/questions/15433188/what-is-the-difference-between-r-n-r-and-n)

## 关于三次握手、四次挥手

见这篇[博文](https://hackmd.io/@rgnkud/SkUO8ofva)
