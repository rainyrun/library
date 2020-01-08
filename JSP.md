# JSP

Java Server Page

从用户角度看待，是一个网页；从程序员角度看，其实是一个java类，它继承了servlet，实际上是一个servlet

jsp中可以写java代码 `<% java代码 %>`

## 指令

格式： `<%@ 指令名 %>`

### page 指令

本页面的信息

属性

- language：表明jsp中可以写java代码
- contentType：告诉浏览器这个文件的类型
- pageEncoding：jsp内容编码
- extends：指定jsp翻译成java文件后继承的父类。
- import：导包
- session：[true, false]，控制是否创建session对象。
- errorPage：当页面上出现错误时，跳转到errorPage指定的页面
- isErrorPage：[true, false]，表明当前页面是一个错误显示页面。

### include 指令

包含另一个jsp里的内容

`<%@ include file="other.jsp" %>`

背后细节：把另一个页面全部内容都拿过来。

### taglib 指令

`<%@ taglib prefix="" uri="" %>`

- uri：标签库路径
- prefix：标签库别名

## 动作标签

在body标签内，格式：`<jsp:动作标签></jsp:动作标签>`

forward

前往page指向的页面（请求转发）

`<jsp:forward page="other.jsp"></jsp:forward>`

include

动态包含指定的页面，不是将页面元素直接拿过来，而是将运行结果拿过来

`<jsp:include page="other.jsp"></jsp:include>`

param

在包含或跳转某个页面的时候，加入这个参数。

```jsp
<!-- 发参数 -->
<jsp:forward page="other.jsp">
	<jsp:param name="address" value="beijing" />
</jsp:forward>

<!-- 收参数 -->
<%= request.getParameter("address") %>
```

## JSP 内置对象

可以在jsp页面中使用这些对象，不用创建

作用域对象，作用域表示这些对象可以存值，他们的取值范围有限定。使用 set/getAttribute 来 存/取 值。

- pageContext ：(PageContext)，作用域只在当前页面
- request ：(HttpServletRequest)，作用域在当次请求，只要做出了响应，这个域中的值就没有了
- session ：(HttpSession)，作用域仅限于一次会话
- application ：(ServletContext)，作用域为整个工程

其他内置对象

- out ：(JspWriter)
- response ：(HttpServletResponse)
- exception ：(Throwable)
- config ：(ServletConfig)
- page ：(Object)，指向当前对象

作用域

- pageScope
- requestScope
- sessionScope
- applicationScope

请求头

- header
- headerValues

请求参数

- param
- paraValues

- cookie

全局初始化参数

- initParam

## 打印

`<%= 打印信息 %>`

## EL 表达式

为了简化jsp中的java代码

写法

`${表达式}`

作用

1. 取4个作用域中的值

```jsp
<!-- 存值 -->
<% pageContext.setAttribute("name", "page") %>

<!-- 取值 -->
${ pageScope.name }
<!-- 没有指定作用域时，依次从page,request,session,application中查找 -->
${ name }
```

2. 取4个作用域中的数组值

```jsp
<!-- 存值 -->
<%
	String[] a = {"aa", "bb", "cc"};
	pageContext.setAttribute("array", a) 
%>

<!-- 取值：数组、list -->
${ array[0] }
<!-- Map -->
${ map.name }
${ map["name"] }

<!-- 存值 -->
<%
	User user = new User("zhangsan", 18);
	session.setAttribute("u", user);
%>

${ u.name }<!-- 实际执行的是u.getName()方法 -->
${ u.age }
<!-- 判断对象是否为空 -->
${ empty u }
```

## 运算

```jsp
${ a > b }
${ a gt b }
${stu.age != 16}
${stu.age == 15}
```

## JSTL

JSP Standard Tag Library

简化jsp的代码编写，替换 `<% ... %>` 的写法，一般与EL表达式配合。

需要导入jstl.jar standard.jar文件

```jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

### core 常用标签

#### set

属性

- var ：声明一个对象
- value ：对象的值
- scope ：作用域

#### if

属性

- test ：接EL表达式
- val ：定义一个对象flag，去接收前面表达式的值
- scope ：作用范围

#### forEach

属性

- begin ：从begin的值开始
- end ：到end到值结束
- var ：结果赋值给var定义的变量
- step ：步长
- items ：表示遍历哪个元素，需要使用EL表达式
- varStatus：代表循环过程中临时存在的状态值
  - count：表示当前输出的元素个数

```jsp
<!-- 默认存到page中 -->
<c:set var="name" value="zhangsan"></c:set>

<c:if test="${ age > 16 }" var="flag" >
	这里的内容会被执行
</c:if>

<c:forEach begin="1" end="10" var="i" step="2">
	${ i }
</c:forEach>

<c:forEach var="i" items="${list}" varStatus="status">
	${ i.name }
  ${ status.count }<!-- 第几个对象 -->
</c:forEach>
```

### function 常用标签

```jsp
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/function" %>
```

contains

```jsp
<c:if test="${fn:contains('原字符串', '检查是否包含的字符串')}"></c:if>
```

## 日期转换

1. 使用jstl标签转换日期

```jsp
<%@taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt"%>
<fmt:formatDate value="${time}" pattern="yyyy-MM-dd HH:mm:ss"/> 
```

2. 后端使用simpleFormate转换成字符串传给前端

## 获取 properties 文件中的属性值

jsp页面

```jsp
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%
    // properties 配置文件名称(classpath下的文件路径)
    ResourceBundle res = ResourceBundle.getBundle("properties/application");
%>

//获取properties配置文件中的属性值
var getDataJsonFreq = <%=res.getString("getDataJsonFreq")%>;
var showAllPointFreq = <%=res.getString("showAllPointFreq")%>;
```
properties配置文件

```
##获取实时数据频率(1000=1秒)
getDataJsonFreq = 1000;
##页面刷新频率(1000=1秒)
showAllPointFreq = 1000;
```