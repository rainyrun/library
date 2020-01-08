# Servlet

servlet 是放在 HTTP Web 服务器上运行的 Java 程序，执行出用户发出请求所要的结果。必须要有支持 servlet 的 Web 服务器才能运行servlet，比如Tomcat。

与web相关的资源，有两种：

1. 静态资源：html、js、css。页面内容不会变。
2. 动态资源：servlet/jsp。页面内容根据请求动态生成

servlet用来处理动态资源。是一个实现了Servlet接口的Java类，接收客户端的请求，产生响应的内容。

实现了servlet接口的类：

- GenericServlet：实现了Servlet程序的基本特征和功能
- HttpServlet：是GenericServlet的子类，专用于HTTP协议。是一个抽象类，但没有抽象方法。

绝大多数 servlet 都继承自 HttpServlet。

## 生命周期

servlet启动过程

```
容器找到servlet类文件 --> 加载类，调用servlet的无参构造函数，调用init方法（容器启动，或者客户使用时）--> 处理请求和响应，调用service方法。
```

servlet的方法

1. init方法：一个servlet只会初始化一次，init方法只会执行一次。
2. service方法：每次访问一个servlet程序时，都会调用这个方法进行处理。HttpServlet类的service方法，会根据请求的类型，调用doGet或doPost方法。
3. destroy方法：该项目从服务器移除，或服务器正常关闭时，执行destroy方法

对servlet的每个请求都在一个单独的线程中运行，任何特定的servlet类都只有一个实例。

## 注册servlet

servlet需要在部署描述符(简称DD)web.xml内注册，才能被访问。

```xml
<!-- web.xml -->
<!-- 注册servlet -->
<servlet> 
	<servlet-name>自定义</servlet-name>
	<servlet-class>全路径(包名.类名)</servlet-class>
  	<!-- 初始化提前 -->
	<load-on-startup>5</load-on-startup>
	<!-- 可以添加初始化参数 -->
	<init-param>
		<param-name>address</param-name>
		<param-value>beijing..</param-value>
	</init-param>
</servlet>

<!-- 映射一个已注册的Servlet的对外访问路径 -->
<servlet-mapping>
	<servlet-name>与servlet中servlet相同</servlet-name>
	<!--
		访问路径必须以/开头（/表示当前Web应用程序的根目录，不是整个web站点的根目录）
		映射路径可以使用 * 通配符
		1. "*.扩展名"。*前不能有/，如*.do表示匹配以".do"结尾的所有url
		2. "/*"结尾。如"/action/*"表示匹配"/action"子路径下的所有url
	-->
	<url-pattern>路径</url-pattern>
</servlet-mapping>
```

servlet只会初始化一次，修改的部署内容，只有在重新部署时才会生效。

初始化提前

默认情况下，只有在初次访问servlet的时候，才会执行init方法。执行一些初始化工作时，可能会在init方法中停留比较长的时间。

在声明servlet时，使用 `load-on-startup` 元素，来提前初始化。数字越小，启动时机越早。一般从2开始。

## ServletConfig

servlet的配置对象，可以获得servlet在配置里的一些信息

ServletConfig 对象的特征

- 每个servlet都有一个
- 用于向servlet传递部署时信息
- 用于访问ServletContext
- 参数在部署描述文件中配置

初始化参数的操作

1. 在DD文件（部署描述符）中，通过 `init-param` 元素设置

2. 在servlet代码中

```java
//可以获得servlet的信息，如初始化参数
ServletConfig config = getServletConfig();
//获取到的是配置servlet里面的servlet-name里的值
String servletName = config.getServletName();
//可以获得具体的某一参数
String address = config.getInitParameter("address");
//获得所有参数
Enumeration<String> names = config.getInitParameterNames();
```

ServletConfig 对象是在 servlet 初始化时建立的，容器从 DD 读出 servlet 初始化参数，交给 ServletConfig 对象，再把 ServletConfig 对象传递给 init 方法。

## ServletContext

特征

- 每个 web 应用都有一个
- 用于访问 web 应用参数
- 相当于一种应用公告栏，可以在这里放置消息（称为属性），应用的其他部分可以访问这些消息。
- 用于得到服务器信息，包括容器名、容器版本、支持的API版本

ServletContext 生命周期

- 创建：服务器启动时，会为托管的每一个web应用程序，创建 servletContext 对象
- 销毁：从服务器移除托管，或关闭服务器
- 作用范围：本web项目

获取ServletContext对象

1. 通过 ServletConfig 对象获取：`getServletConfig().getServletContext()`
2. 直接获取：`this.getServletContext()`

作用

1. 获取全局参数

```xml
<!-- DD里的全局参数 -->
<context-param>
	<param-name>name</param-name>
	<param-value>value</param-value>
</context-param>
<context-param>
	<param-name>adminEmail</param-name>
	<param-value>abc@def.com</param-value>
</context-param>
```

```java
ServletContext context = getServletContext();
//获取全局参数
String address = context.getInitParameter("name");
getServletContext().getInitParameter("adminEmail");
```

2. context获取资源文件

```java
ServletContext context = getServletContext();
//获取给定的文件(当前项目下的某个文件)在服务器上面的绝对路径(根目录)
String path = context.getRealPath("/images/product");
//获取web工程下的资源，转化成流对象。
context.getResourceAsStream("file/config.properties")
//根据classloader去获取工程下的资源
InputStream is = this.getClass().getClassLoader().getResourceAsStream("../../file/congfig.properties");
properties.load(is);
```

3. 存取数据，数据共享

属性是一个(String/Object)对，可以设置到ServletContext, HttpServletRequest, HttpSession中的某一个。

```java
//设置属性
getServletContext().setAttribute("count",value);
//获取属性
String value = getServletContext().setAttribute("count");
```
ServletContext对象的属性，是一对（名/对象）对。

## HttpServletRequest

封装了客户端提交过来的一切请求。

```java
//请求头里的数据
Enumeration<String> headerNames = request.getHeaderNames();
while (headerNames.hasMoreElements()){
	String name = (String) headerNames.nextElement();
	String value = request.getHeader(name);
	System.out.println(name + "=" + value);
}

//客户端提交过来的数据
String name = request.getParameter("name");
Map<String, String[]> map = request.getParameterMap();
Set<String> keySet = map.keySet();
```

### BeanUtils

使用BeanUtils工具，将parameters里的数据封装成对象。

BeanUtils 是 Apache commons组件的成员之一，主要用于简化JavaBean封装数据的操作。它可以给JavaBean封装一个字符串数据，也可以将一个表单提交的所有数据封装到JavaBean中。

使用第三方工具，需要导入jar包。

```java
// 将Map数据封装到指定Javabean中，一般用于将表单的所有数据封装到javabean
BeanUtils.populate(Object bean, Map<String,String[]>properties);
// 设置属性值
BeanUtils.setProperty(Object obj,String name,Object value);
// 获得属性值
BeanUtils.getProperty(Object obj,String name);

// 创建时间类型的转换器
DateConverter dt = new DateConverter();
// 设置转换的格式
dt.setPattern("yyyy-MM-dd");
// 注册转换器
ConvertUtils.register(dt, java.util.Date.class);
```

### 中文乱码问题

#### Get请求

因为拼接在url地址中，会对数据进行编码，所以接收到的数据是乱码。

Tomcat默认的编码是ISO-8859-1。将参数转成ISO-8859-1编码的字节码，再用UTF-8编码，即可获得正常数据。

```java
username = new String(username.getBytes("ISO-8859-1"), "UTF-8");
```

也可以通过修改默认编码方式，在 `conf/srever.xml` 文件内，`context` 元素里加上 `URIEncoding = UTF-8`。

#### Post方法

request取数据时，有默认的编码。在取数据前，设置请求体里的编码方式。

```java
request.setCharacterEncoding("UTF-8");
```

## HttpServletResponse

封装了服务器的响应。

```java
// 设置响应内容
// 字符流
response.getWriter().write("<h1>hello response...</h1>");
// 字节流
response.getOutputStream().write("hello".getBytes());
```

### 中文乱码问题

```java
// 指定输出到客户端时使用的编码，需要在响应写入内容之前设置
response.setCharacterEncoding("UTF-8");
// 指定浏览器解码的方式(不管用，待查证补充)
response.setHeader("Content-Type", "text/html; charset=UTF-8");
// 设置响应的数据类型，并告知浏览器
response.setContentType("text/html; charset=UTF-8");

// 查询getBytes()默认使用的码表
String csn = Charset.defaultCharset().name();
```

字节流默认是UTF-8的编码

### 重定向

sendRedirect()方法

服务器告知浏览器地址有变，浏览器重新发起请求。（地址为重定向后的地址）

```java
// 假设用户输入的url是：http://www.baidu.com/myApp/bar.do
// 1. 完整URL
sendRedirect("http://www.baidu.com"); // 指向：http://www.baidu.com`
// 2. 相对于原先的请求URL
sendRedirect("foo/struff.html"); // 指向：http://www.baidu.com/myApp/foo/struff.html
// 3. 相对于web应用本身
sendRedirect("/foo/struff.html"); // 指向：http://www.baidu.com/foo/struff.html
```

或设置响应字段

```java
response.setStatus(302);
response.setHeader("Location", "url.html");
```

## 请求属性和请求分派

请求转发

服务器发现地址有变，直接请求变化后的地址。地址栏上的地址不变。只能转发自己项目的资源路径。

```java
// 请求转发
request.getRequestDispatcher("url.html").forward(request, response);

// 在 HttpServletRequest 作用域设置属性后，在转发请求
// 1. 把数据放在请求作用域中
request.setAttribute("styles", result);
// 2. 为视图得到一个分派器，result.jsp是资源的相对路径
RequestDispatcher view = request.getRequestDispatcher("result.jsp");
// 3. 告诉jsp接管请求
view.forward(request, response);

// 从 servletContext 得到 RequestDispatcher。路径需要以/开头（表示从web应用的根开始）
RequestDispatcher view = getServletContext().getRequestDispatcher("/result.jsp");
```

如果已提交了响应，就不能再转发请求

## 下载资源

DefaultServlet 专门用于处理在服务器上的静态资源。静态资源可以被直接下载。

`<a href="download/aa.txt">aa</a>`

手动编码提供下载

```java
// html代码:<a href="Demo1?filename=aa.zip">aa</a>
// Demo1是servlet的名字
// 获得要下载的文件名
String fileName = request.getParameter("filename");
// 获得文件路径
String path = getServletContext().getRealPath("download/" + fileName);
// 让浏览器以下载的方式提醒用户
response.setHeader("Content-Dispotion", "attachment; filename=" + filename);
// 创建输入流
InputStream is = new FileInputStream(path);

//在响应中输出
OutputStream os = response.getOutputStream();
int len = 0;
byte[] buffer = new byte[1024];
while((len = is.read(buffer)) != -1){
	os.wirte(buffer, 0, len);
}
//关闭文件
os.close();
is.close();
//文件名带有中文，是ie或chrome，使用URLEncoding编码，如果是firefox，使用base64编码
String clientType = request.getHeader("User-Agent");
if(clientType.contains("firefox")){
  
}
else{
	fileName = URLEncoding.encode(fileName,"UTF-8");
}


public static String base64EncodeFileName(String fileName) {
	BASE64Encoder base64Encoder = new BASE64Encoder();
	try {
		return "=?UTF-8?B?"
				+ new String(base64Encoder.encode(fileName
						.getBytes("UTF-8"))) + "?=";
	} catch (UnsupportedEncodingException e) {
		e.printStackTrace();
		throw new RuntimeException(e);
	}
}
```

## 上传资源

form表单增加 enctype 属性，请求体参数会将二进制文件上传。

```html
<form action="" method="post" enctype="multipart/form-data">
	<input type="file" name="file"/>
</form>
```

java代码

```java
// request.getParameter(key)获取到的是请求体里的键值对参数。form表单上传文件时，无法获取到对应的参数。

// 通过request获取到一个输入流对象
InputStream is = request.getInputStream();
// 打印输入流对象中的内容(内容是request请求体中的参数，可以通过自己解析，获得参数内容)
int i = is.read();
while(i != -1){
	char c = (char) i;
	System.out.print(c);
	i = is.read();
}
```

使用 commons-fileupload.jar 来实现上传。依赖包：commons-io.jar

```java
// 解析request里的参数
try{
	DiskFileItemFactory fac = new DiskFileItemFactory();
	ServletFileUpload upload = new ServletFileUpload(fac);
	List<FileItem> list = upload.parseRequest(request);
} catch(Exception e){
	e.printStackTrace();
}
// 遍历集合
for(FileItem item : list){
	if(item.isFormField()){
		// 是普通项
		item.getFieldName();// 参数名
		item.getName();// 文件名，普通项为null
		item.getString();// 参数值
		// item.getString("utf-8");
	}
	else{
		// 是上传项
		item.getFieldName();// 参数名
		item.getName();// 文件名
		item.getInputStream();// 二进制数据
	}
}
```

预览上传的图片

js代码

```js
// 定位到input-file元素
$("#upload_pimage").change(function(){
  	// 定位到img元素(用来展示图片)
		$("#pimage").attr("src",URL.createObjectURL($(this)[0].files[0]));
});
```

## Cookie

服务器给客户端的小数据，存储在客户端。

应用场景：自动登录、浏览记录、购物车。

http的请求是无状态的，服务器没有用户的使用记录。为了保持登录状态，记录用户信息。

默认关闭浏览器，cookie失效。可以设置cookie有效期。

```java
// 发送cookie到客户端
Cookie cookie = new Cookie("aa", "bb");
response.addCookie(cookie);

// 获取客户端带来的cookie
Cookie[] cookies = request.getCookies();
if(cookies != null){
	for(Cookie c : cookies){
		//遍历
	}
}

//设置有效期。负值表示关闭浏览器后过期，默认是-1
cookie.setMaxAge(expiry);
//设新的值
cookie.setValue("new");
//只有请求了指定域名，才会带上cookie
cookie.setDomain(".baidu.com");
//只有访问了该路径，才会带上cookie
cookie.setPath("/domain");

//清除cookie
cookie.setMaxAge(0);
```
cookie大小和个数有限制

Cookie分类

- 会话cookie：关闭浏览器，cookie就删除，失效
- 持久cookie：在指定时间内，都一直有效

移除cookie

1. 得到以前的cookie，然后重新设置有效期

```java
Cookie[] cookies = request.getCookies();
Cookie cookie = CookieUtil.findCookie(cookies, "cookiename");
cookie.setMaxAge(0);
response.addCookie(cookie);
```

2. 创建新cookie覆盖掉原来的cookie

```java
Cookie cookie = new Cookie("cookiename", "anything");
cookie.setMaxAge(0);
response.addCookie(cookie);
```

创建对象的几种方法

1. 直接new
2. 单例模式（提供静态方法）
3. 工厂方法

## HttpSession

会话，基于cookie的一种会话机制。session是数据存放在服务器。

使用 HttpSession 对象保存跨多个请求的会话状态。

服务器会给客户端一个会话id（JSESSIONID），通过这个会话id来识别客户端以前的信息。容器会自动完成这个工作。

会话id通过cookie来交换，该cookie是自动生成的。

```java
HttpSession session = request.getSession();
String id = session.getId();
session.setAttribute(name, value);
session.getAttribute(name);
//移除属性
session.removeAttribute(name);
//强制干掉对话，里面存放的数据全部消失
session.invalidate();
//判断session是不是新创建的，返回boolean值
session.isNew();
//要么得到null，要么得到一个已有的session
request.getSession(false);

//向这个url增加额外的会话id信息
response.encodeURL("/beer.do");
//把请求重定向到另外一个url，但是还想使用同一个会话
response.encodeRedirectURL("/beer.do");
```

session默认有效期为30分钟（在 web.xml 的 session-config 字段配置的）

调用 request.getSession 就会创建；关闭服务器或会话过期，就会销毁

会话有3种死法

1. 超时
	

在DD中配置会话超时，与在所创建的每一个会话上调用 setMaxInactiveInterval() 有同样的效果。会话超时时间设置为-1，则永远不会到期

```xml
<session-config>
	<session-timeout>15</session-timeout> <!-- 单位是分钟 -->
</session-config>
```

设置一个特定会话的会话超时

```java
session.setMaxInactiveInterval(20 * 60) // 单位秒
```

2. 在会话对象上调用 invalidate()

3. 应用结束（崩溃或取消部署）

对于分布式应用，会话对象不会在多个VM上复制，只会进行迁移。

因为sessionid是通过cookie传递的，当关闭浏览器后，这个cookie如果没有设置有效期，就会被删除，而sessionid也就没有了。

如果下次访问，想要取到sessionid，可以在服务器手动设置装有sessionId的cookie的有效期

```java
String id = request.getSession().getId();
Cookie cookie = new Cookie("JSESSIONID", id);
cookie.setMaxAge(60*60*24*7);//7day
response.addCookie(cookie);
```

## 监听器

监听某一个事件的发生，状态的改变。

如监听上下文（context-param）初始化事件，可以获取到上下文初始化参数，在初始化前，做连接数据库等操作。

内部机制：接口回调

总共有8个 划分成三种类型

- 监听三个作用域创建和销毁
- 监听三个作用域属性状态变更
- 监听httpSession

三个作用域

- request --- HttpServletRequest
- session --- HttpSession
- application --- ServletContext

### 监听三个作用域创建和销毁

监听器的接口

- ServletContextListener
- HttpServletRequestListener
- HttpSessionListener

作用

ServletContextListener，在ServletContext创建的时候，完成一些初始化工作，执行自定义任务调度。

在DD中注册监听者
```xml
<listener>
	<listener-class>com.example.beersession</listener-class>
</listener>
```

### 监听三个作用域属性状态变更

可以监听在作用域中，值的添加、替换、移除动作。

监听器的接口

- ServletContextAttributeListener
- ServletRequestAttributeListener
- HttpSessionAttributeListener

### 监听httpSession

不用注册，让JavaBean实现该接口即可。

监听器接口

- HttpSessionBindingListener：监听属性的绑定、移除。
- HttpSessionActivationListener：监听session的属性是钝化、活化。

钝化：把内存中的数据存储到硬盘

活化：把硬盘中的数据读取到内存中

如何让session在一定时间内钝化？配置即可

1. 在tomcat里的 conf/context.xml 里配置（对所有运行在服务器上的项目生效）
2. 在conf/Catalina/localhost/context.xml 里配置（对localhost生效）
3. 在自己的Web工程项目中的 META-INF/context.xml 配置（只对当前项目生效）

```xml
<Context>
	<!-- maxIdleSwap="1" 表示1分钟不用就钝化 -->
	<Manager className="org.apache.catalina.session.PersistentManager" maxIdleSwap="1">
		<!-- directory表示钝化后的文件存放位置 -->
		<Store className="org.apache.catalina.session.FileStore" directory="dir" />
	</Manager>
</Context>
```

## 过滤器

过滤器就是java组件，请求发送到servlet之前，可以用过滤器截获和处理请求。servlet结束工作后，在响应发送回客户之前，可以用过滤器处理响应。

常见用途

1. 对敏感词汇过滤
2. 统一设置编码
3. 自动登录

如何使用？

1. 定义一个类，实现 Filter 接口。
2. 注册filter

```xml
<filter>
	<filter-name>name</filter-name>
	<filter-class>com.example.classname</filter-class>
	<init-param>
		<param-name>paramname</param-name>
		<param-value>value</param-value>
	</init-param>
</filter>

<!-- 映射到url -->
<filter-mapping>
	<filter-name>name</filter-name>
	<url-pattern>*.do</url-pattern>
	<!-- 可以映射到多个url -->
	<url-pattern>/myProject/*</url-pattern>
</filter-mapping>

<!-- 映射到servlet -->
<filter-mapping>
	<filter-name>name</filter-name>
	<servlet-name>servlet</servlet-name>
</filter-mapping>

<!-- 为通过请求分派请求的web资源声明一个过滤器映射 -->
<filter-mapping>
	<filter-name>name</filter-name>
	<servlet-name>servlet</servlet-name>
	<!-- 表示对客户请求启用过滤器，默认 -->
	<dispatcher>REQUEST</dispatcher>
	<!-- or -->
	<dispatcher>INCLUDE</dispatcher>
	<!-- 只要转发都拦截 -->
	<dispatcher>FORWARD</dispatcher>
	<!-- 出错都拦截 -->
	<dispatcher>ERROR</dispatcher>
</filter-mapping>
```

url-pattern 的格式，与servlet的路径匹配相同

1. 全路径匹配。以 `/` 开始
2. 目录匹配。以 `/` 开始，以 `*` 结束
3. 后缀名匹配。以 `/` 开始，以 `*.后缀名` 结束

过滤器的生命周期

- init()
- doFilter()
- destroy()

过滤器在服务器启动时创建，在服务器停止时销毁

声明和确定过滤器顺序

过滤器可以链到一起，执行顺序由dd控制。

1. 过滤器的url-pattern映射总是在servlet映射前。
2. 过滤器总是在servlet之前执行。
3. 相同映射的过滤器按照声明顺序执行。

```java
//执行其他filter。如果没有此句，会停在该过滤器出不去。
chain.doFilter(request, response);
```

## 参考资料

《Head First Servlet & JSP》
