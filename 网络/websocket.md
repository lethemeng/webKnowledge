# [WebSocket](https://www.cnblogs.com/chyingp/p/websocket-deep-in.html)

websocket 是为了解决是能客服端发起单向请求而提出的解决方案，原来为了实现实时获取消息采用的是轮询的方式，但这种方式效率低效。

WebSocket 最大的特点就是服务器可以主动向客户端推送信息

1. 建立在 TCP 协议之上，服务端的实现比较容易

2. 与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443，并且握手阶段采用 HTTP 协议

3. 数据格式比较轻量，性能开销小，通信高效。

4. 可以发送文本，也可以发送二进制数据。

5. 没有同源限制，客户端可以与任意服务器通信。

6. 协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL。

![websocket](../img/websocket.png)

![websocket](../img/websocket_http.jpg)

http://www.ruanyifeng.com/blog/2017/05/websocket.html

在单个 TCP 连接上进行全双工通讯的协议。在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建**持久性**的连接，并进行**双向**数据传输。

- Socket.onopen 连接建立时触发

- Socket.onmessage 客户端接收服务端数据时触发

- Socket.onerror 通信发生错误时触发

- Socket.onclose 连接关闭时触发

- ### webSocket.send() 发送数据

```
// WebSocket 对象作为一个构造函数，用于新建 WebSocket 实例
var ws = new WebSocket('ws://localhost:8080');
// 实例对象的当前状态
switch (ws.readyState) {
  case WebSocket.CONNECTING:
    // do something
    break;
  case WebSocket.OPEN:
    // do something
    break;
  case WebSocket.CLOSING:
    // do something
    break;
  case WebSocket.CLOSED:
    // do something
    break;
  default:
    // this never happens
    break;
}
```

