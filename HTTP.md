# HTTP

响应报文：协议版本、状态码、原因短语、可选的响应首部字段、实体主体

HTTP是一种无状态协议，不保存状态。使用Cookie技术可以保持状态。

HTTP方法
GET：获取资源
POST：传输实体的主体
PUT：传输文件
HEAD：获得报文首部
DELETE：删除文件
OPTIONS：询问支持的方法
TRACE：追踪路径
CONNECT：要求用隧道协议链接代理

GET和POST的区别
1. 请求路径不同。post请求在url后面不跟任何数据；get请求在url后跟数据(不安全，能带的数据有限)
2. 带上的数据不同。post请求使用流的方式写数据；get请求在地址栏上跟数据
3. 由于post请求使用流的方式写数据，所以一定需要一个Content-Length的头来说明数据的长度。


在HTTP/1.1中，所有的连接默认都是持久连接。

管线化：在持久连接的基础上实现。不用等待响应即可直接发送下一请求。

HTTP报文
请求报文
响应报文

报文首部
空行（CR+LF）
报文主体

请求报文结构：
请求行
请求首部字段
通用首部字段
实体首部字段
其他
空行（CR+LF）
报文主体

响应报文主体
状态行
响应首部字段
通用首部字段
实体首部字段
其他
空行（CR+LF）
报文主体

内容编码
gzip
compress
deflate
identity

用MIME类型（如"text/html"）来描述标记数据类型(Content-type)

内容协商机制是指客户端和服务器端就响应的资源内容进行交涉，然后提供给客户端最为合适的资源。内容协商会以响应资源的语言、字符集、编码方式等作为判断的基准。
内容协商技术有3种类型
1. 服务器驱动协商
2. 客户端驱动协商
3. 透明协商

状态码

首部字段

通用首部字段
- Catche-Control
	- public：所有用户都可以利用缓存
	- private：只有特定对象可以使用缓存
	- no-cache：防止从缓存中返回过期资源
	- no-store：不使用缓存
	- max-age：如果资源的缓存时间比指定数值小，就接收
	- s-maxage：同上，只对代理有效。
	- min-fresh：过了min-fresh的资源都无法作为响应返回。
	- max-stale：即使过期，只要仍处于max-stale指定的时间内，仍旧会被客户端接收
	- only-if-cached：只返回缓存的资源
	- must-revalidate：返回缓存资源前会再次验证是否有效。忽略max-stale指令
	- no-transform：不能改变实体主体的媒体类型
- Connection
控制不再转发给代理的首部字段。
	- Connection：不再转发给代理的首部字段
管理持久连接
	- close：HTTP/1.1版本默认的连接都是持久连接。想明确断开时，使用close
	- Keep-Alive：HTTP/1.1版本之前的连接都是非持久连接。如果想维持连接，使用Keep-Alive
- Date：创建报文的日期和时间
- Pragma：遗留字段。[no-cache]。只用在客户端发送的请求中。客户端会要求所有的中间服务器不返回缓存的资源。
- Trailer：说明在报文主体后记录了哪些首部字段。[首部字段]
- Transfer-Encoding：规定了传输报文主体时采用的编码方式。[chunked]
- Upgrade：检查协议是否可使用更高的版本进行通信。
- via：追踪请求和响应报文的传输路径

请求首部字段
- Accept：通知服务器用户代理能够处理的媒体类型和优先级。使用type/subtype;q=0.8形式。如text/html
- Accept-Charset：字符集和字符集优先顺序。
- Accept-Encoding：内容编码[gzip, compress, deflate, identity]
- Accept-Language：语言
- Authorization：认证信息
- Except：期望出现的某种行为
- From：电子邮件地址
- Host：主机名和端口号

响应首部字段
- Accept-Ranges：告知客户端是否能处理范围请求。[bytes, none]
- Age：告知客户端，服务器在多久前建立了响应。[xx秒]
- ETag：服务器为每份资源分配对应的ETag值。有强、弱之分。
- Location：提示资源已重定向
- Server：告知服务器应用程序信息
- Vary

实体首部字段
- Allow：通知客户端能够支持的HTTP方法
- Content-Language：实体主体使用的语言
- Content-Location：报文主体返回资源对应的URI
- Expires：将资源失效的日期告诉客户端。

为cookie服务的首部字段
- Set-Cookie：服务器告知客户端
	- expires：有效期。省略时为浏览器会话时间段
	- path：限制cookie发送范围
	- domain：可使用cookie的域名
	- secure：仅在https时可使用cookie
	- HttpOnly：使javascript无法获得cookie


