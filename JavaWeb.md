# Java Web

## Web 请求过程

发起一个请求

发起一个HTTP请求的过程，就是建立一个 Socket 通信的过程。只不过写入 outputStream 的数据要符合 HTTP 协议的规范。

不使用浏览器发送 HTTP 请求的方法

1. HTTPClient 是一个开源的处理 HTTP 请求的工具包。
2. curl + URL(Linux系统)

```sh
curl URL > /dev/null
# 查看 HTTP 头的信息
curl -I URL
# 增加 HTTP 头的信息
curl -I URL -H "Cookie:a=b"
```


## 参考资料

《深入分析 Java Web 技术内幕》，许令波