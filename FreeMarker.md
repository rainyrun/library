# FreeMarker

ftl模板 + 数据 --> html页面

模板示例

```ftl
<html>
<head>
  <title>Welcome!</title>
</head>
<body>
  <h1>Welcome ${user}!</h1>
  <p>Our latest product:
  <a href="${latestProduct.url}">${latestProduct.name}</a>!
</body>
</html>
```

## 模板

由以下4部分组成：

- 文本：会被原样输出。
- 插值：FreeMarker将会输出真实的值来替换 `${...}` 内的表达式。
- FTL 标签：FreeMarker模板的语言标签，也被称为“指令”，以 # 开头，如 `#if` 。(用户自定义的FTL标签则需要使用 @ 来代替 #)，不会在输出中打印的。
- 注释：`<#-- content -->` 来标识。FTL注释不会出现在输出中(不出现在访问者的页面中)。

插值 仅仅可以在 文本 中使用。

FTL 标签 不可以在其他 FTL 标签 和 插值中使用。

注释 可以放在 FTL 标签 和 插值中

### 指令(标签)

FreeMarker并不解析FTL标签以外的文本、插值和注释，在HTML属性中使用FTL标签也不会有问题。

FTL是区分大小写的

如果标签没有嵌套内容(在开始标签和结束标签之间的内容)，那么可以只使用开始标签。

FreeMarker会忽略FTL标签中多余的空白标记

#### if

使用 if 指令可以有条件地跳过模板的一些片段。如果 condition 是false(布尔值)，那么介于 <#if condition> 和 </#if> 标签中的内容会被略过。

```ftl
<#if animals.python.price < animals.elephant.price>
  Pythons are cheaper than elephants today.
<#elseif animals.elephant.price < animals.python.price>
  Elephants are cheaper than pythons today.
<#else>
  Elephants and pythons cost the same today.
</#if>
```

可以使用的符号：==, !=, >, <

#### list

```ftl
<#list misc.fruits>
  <p>Fruits:
  <ul>
    <#items as fruit>
      <li>${fruit}<#sep> and</#sep>
    </#items>
  </ul>
<#else>
  <p>We have no fruits.
</#list>
<#-- 整个list标签是一个整体，fruits为0时，显示We have no fruits. -->
```

#### include

使用 include 指令， 我们可以在模板中插入其他文件的内容。

```ftl
<#include "/copyright_footer.html">
```

#### assign

在模板里定义的变量，比定义在数据模型中的同名参数有更高的优先级。

变量类型：

- 简单变量： 它能从模板中的任何位置来访问，或者从使用 include 指令引入的模板访问。可以使用 assign 指令来创建或替换这些变量。因为宏和方法只是变量，那么 macro 指令 和 function 指令 也可以用来设置变量，就像 assign 那样。
- 局部变量：它们只能被设置在宏定义体内，而且只在宏内可见。一个局部变量的生命周期只是宏的调用过程。可以使用 local 指令 在宏定义体内创建或替换局部变量。
- 循环变量：循环变量是由如 list 指令自动创建的，而且它们只在指令的开始和结束标记内有效。宏 的参数是局部变量而不是循环变量。

```ftl
<#assign x += y>
```

#### escape

插值被自动转义。noescape抵消escape的效果

```ftl
<#escape x as x?html>
  ...
  <p>Title: ${book.title}</p>
  <p>Description: <#noescape>${book.description}</#noescape></p>
  <h2>Comments:</h2>
  <#list comments as comment>
    <div class="comment">
      ${comment}
    </div>
  </#list>
  ...
</#escape>
```

### 自定义指令

自定义指令可以使用 macro 指令来定义。（与其说是指令，不如说是宏片段？）

格式 `<#macro 指令名 [参数[="默认值"] ...]>指令内容</#macro>`

Java程序员若不想在模板中实现定义指令，而是在Java语言中实现指令的定义，这时可以使用 freemarker.template.TemplateDirectiveModel 类来扩展

```ftl
<#-- 定义 -->
<#macro greet person color="black">
  <font size="+2" color="${color}">Hello ${person}!</font>
</#macro>
<#-- 使用 -->
<@greet person="Fred"/>

<#-- 嵌套的自定义指令 -->
<#macro border>
  <table border=4 cellspacing=0 cellpadding=4><tr><td>
    <#nested>
    <#nested>
  </tr></td></table>
</#macro>
<#-- 使用 -->
<@border>The bordered text</@border>
```

如果自定义指令没有nested，则使用时，嵌套的内容会被忽略。

在嵌套的内容中，宏的 局部变量 是不可见的。

## 数据类型

扮演目录角色的变量称为 hashes (哈希表或哈希)。哈希表是一种存储变量及其相关且有唯一标识名称的容器。

存储单值的变量称为 scalars (标量)。

存储数组的变量是 sequences (序列)。 序列是存储有序变量的容器。存储的变量可以通过数字索引来检索，索引通常从0开始。

使用示例

```
<!-- fruits是 sequences -->
misc.fruits[1]
<!-- animals[0]是 hashes -->
animals[0].name
```

### 标量

类型如下

- 字符串：就是文本，也就是任意的字符序列。
- 数字：在FreeMarker中，字符串 "50" 和数字 50 是两种完全不同的东西。前者是两个字符的序列，而后者则是可以在数学运算中直接被使用的数值。
- 日期/时间: 可以是日期-时间格式(存储某一天的日期和时间)，或者是日期(只有日期，没有时间)，或者是时间(只有时间，没有日期)。
- 布尔值：对应着对/错(是/否，开/关等值)类似的值。

整数和非整数是不区分的；只有单一的数字类型。比如使用了计算器，计算3/2的结果是1.5而不是1。

### 容器

容器类型如下

- 哈希表：每个子变量都可以通过一个唯一的名称来查找。
- 序列：每个子变量通过一个整数来标识。第一个子变量的标识符是0，子变量的类型也并不需要完全一致。
- 集合：集合是有限制的序列。不能获取集合的大小，也不能通过索引取出集合中的子变量，但是它们仍然可以通过 list 指令来遍历。

## 表达式

FTL 忽略表达式中的多余的空格

- 直接指定值
    - 字符串： 
        - 直接使用："Foo" 或者 'Foo' 
        - 转义："It's \"quoted\"" 或者 'It\'s "quoted"'
        - 原生字符串：r"C:\raw\string"
    - 数字： 123.45
    - 布尔值： true， false
    - 序列： ["foo", "bar", 123.45]；
    - 值域： 
        - `start..end`： 包含结尾的值域。比如 1..4 就是 [1, 2, 3, 4]， 而 4..1 就是 [4, 3, 2, 1]。
        - `start..<end 或 start..!end`： 不包含结尾的值域。比如 1..<4 就是 [1, 2, 3]，4..<1 就是 [4, 3, 2], 而 1..<1 表示 []。
        - `start..*length`： 限定长度的值域，比如 `10..*4` 就是 [10, 11, 12, 13]，`10..*-4` 就是 [10, 9, 8, 7]，而 `10..*0` 表示 []。
        - `start..`： 无右边界值域。这和限制长度的值域很像，只是长度是无限的。比如 1.. 就是 [1, 2, 3, 4, 5, 6, ... ]，直到无穷大。 
    - 哈希表： {"name":"green mouse", "price":150}
- 检索变量
    - 顶层变量： user
    - 从哈希表中检索数据： user.name， user["name"]
    - 从序列中检索数据： products[5]
    - 特殊变量(由FreeMarker引擎本身定义的)： .main
- 字符串操作
    - 插值(或连接)： "Hello ${user}!" (或 "Hello " + user + "!")
    - 获取一个字符： name[0]
    - 字符串切分： 包含结尾： name[0..4]，不包含结尾： name[0..<5]，基于长度(宽容处理)： `name[0..*5]`，去除开头： name[5..]
- 序列操作
    - 连接： users + ["guest"]
    - 序列切分：包含结尾： products[20..29]， 不包含结尾： products[20..<30]，基于长度(宽容处理)： `products[20..*10]`，去除开头： products[20..]
- 哈希表操作
    - 连接： passwords + { "joe": "secret42" }
- 算术运算： (x * 1.5 + 10) / 2 - y % 100
- 比较运算： x == y， x != y， x < y， x > y， x >= y， x <= y， x lt y， x lte y， x gt y， x gte y， 等等。。。。。。
- 逻辑操作： !registered && (firstVisit || fromEurope)
- 内建函数： name?upper_case, path?ensure_starts_with('/')
- 方法调用： repeat("What", 3)
- 处理不存在的值：
    - 默认值： name!"unknown" 或者 (user.name)!"unknown" 或者 name! 或者 (user.name)!
    - 检测不存在的值： name?? 或者 (user.name)??
- 赋值操作： `=, +=, -=, *=, /=, %=, ++, --`

### 内建函数

常用内建函数的示例：

- user?html 给出 user 的HTML转义版本， 比如 & 会由 `&amp;` 来代替。
- user?upper_case 给出 user 值的大写版本 (比如 "JOHN DOE" 来替代 "John Doe")
- animal.name?cap_first 给出 animal.name 的首字母大写版本(比如 "Mouse" 来替代 "mouse")
- user?length 给出 user 值中 字符的数量(对于 "John Doe" 来说就是8)
- animals?size 给出 animals 序列中项目的个数
- 如果在 <#list animals as animal> 和对应的 </#list> 标签中：
    - animal?index 给出了在 animals 中基于0开始的 animal的索引值
    - animal?counter 也像 index， 但是给出的是基于1的索引值
    - animal?item_parity 基于当前计数的奇偶性，给出字符串 "odd" 或 "even"。在给不同行着色时非常有用，比如在 `<td class="${animal?item_parity}Row">` 中。
- product.id?c 表示该字段是给计算机看的，不需要根据local转成当地的样式。

一些内建函数需要参数来指定行为，比如：

- animal.protected?string("Y", "N") 基于 animal.protected 的布尔值来返回字符串 "Y" 或 "N"。直接打印布尔值，如 ${a == 2} 会引起错误
- animal?item_cycle('lightRow','darkRow') 是之前介绍的 item_parity 更为常用的变体形式。
- fruits?join(", ") 通过连接所有项，将列表转换为字符串， 在每个项之间插入参数分隔符(比如 "orange,banana")
- user?starts_with("J") 根据 user 的首字母是否是 "J" 返回布尔值true或false。

内建函数应用可以链式操作，比如 user?upper_case?html 会先转换用户名到大写形式，之后再进行HTML转义。(这就像可以链式使用.(点)一样)

例子

```
${"222.11"?number}  结果为222.11 
${456?c}            结果为456

<#assign num=20>
${num?string.number}    或   ${num?string("number")}    // 结果为20 
${num?string.currency}  或   ${num?string("currency")}  // 结果为￥20.00 
${num?string.percent}   或   ${num?string("percent")}   // 结果为2,000% 
```

### 方法调用

```ftl
${repeat("Foo", 3)}
```

### 处理不存在的变量

一个不存在的变量和一个是 null 值的变量，对于FreeMarker来说是一样的。

- 指定一个默认值

```ftl
<#-- user不存在时，${user}的值为 visitor -->
<h1>Welcome ${user!"visitor"}!</h1>
<#-- 多级访问的变量，如果animals也可能不存在，需要加括号 -->
(animals.python.price)!0
```

- 询问一个变量是否存在

```ftl
<#if user??><h1>Welcome ${user}!</h1></#if>
<#-- 多级访问的变量 -->
(animals.python.price)??
```

## 程序开发

### 入门

模板

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>模板</title>
</head>
<body>
  <h4>name: ${user}</h4>
  <p>latest product</p>
  <p>product url: ${latestProduct.url}</p>
  <p>product name: ${latestProduct.name}</p>
</body>
</html>
```

java 代码

```java
// 构造 Configuration 实例
Configuration cfg = new Configuration(Configuration.VERSION_2_3_22);
cfg.setDirectoryForTemplateLoading(new File("/where/you/store/templates"));
cfg.setDefaultEncoding("UTF-8");
cfg.setTemplateExceptionHandler(TemplateExceptionHandler.RETHROW_HANDLER);
// 构造数据模型
Map<String, Object> root = new HashMap<>(); // 根哈希表
root.put("user", "Big Joe");
Map<String, Object> latest = new HashMap<>(); // 最新数据的哈希表
root.put("latestProduct", latest);
latest.put("url", "products/greenmouse.html");
latest.put("name", "green mouse");
// 获取模板
Template temp = cfg.getTemplate("test.ftl"); // 读取 /where/you/store/templates/test.ftl 文件
// 合并模板和数据模型
Writer out = new OutputStreamWriter(System.out);
temp.process(root, out);
```

结果

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>模板</title>
</head>
<body>
  <h4>name: Big Joe</h4>
  <p>latest product</p>
  <p>product url: products/greenmouse.html</p>
  <p>product name: green mouse</p>
</body>
</html>
```

### 数据模型

数据模型的基本结构是树状的

对象包装

在内部，模板中可用的变量都是实现了 freemarker.template.TemplateModel 接口的Java对象。

数据模型中，可以使用基本的Java集合类作为变量，因为这些变量会在内部被替换为适当的 TemplateModel 类型。这种功能特性被称作是 对象包装。

包装(转换)这些对象， 需要使用合适的，也就是所谓的 对象包装器 实现(可能是自定义的实现)

## 与spring整合

加入依赖

```xml
<dependency>
    <groupId>org.freemarker</groupId>
    <artifactId>freemarker</artifactId>
    <version>2.3.28</version>
</dependency>
```

注入 freemarkerConfig

```xml
<bean id="freemarkerConfig"
        class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
    <property name="templateLoaderPath" value="/WEB-INF/ftl/" />
    <property name="defaultEncoding" value="UTF-8" />
</bean>
```

使用

```java
@Controller
public class HtmlGen {
    @Autowired
    private FreeMarkerConfigurer config;
    
    @RequestMapping("/genHtml")
    @ResponseBody
    public String gen() throws Exception{
        //生成静态页面
        //1.根据config  获取configuration对象
        Configuration cfg = config.getConfiguration();
        //2.设置模板文件 加载模板文件 /WEB-INF/ftl/相对路径
        Template template = cfg.getTemplate("template.ftl");
        //3.创建数据集  --》从数据库中获取
        Map model = new HashMap<>();
        model.put("springtestkey", "hello");
        //4.创建writer  
        Writer writer = new FileWriter(new File("G:/freemarker/springtestfreemarker.html"));
        //5.调用方法输出
        template.process(model, writer);
        //6.关闭流
        writer.close();
        return "ok";
    }
}
```

template.ftl

```ftl
${springtestkey}
```

输出结果

```
hello
```

## 参考资料

[官网](https://freemarker.apache.org)

[官方参考手册-中文](http://freemarker.foofun.cn/toc.html)