# HTML

## 我的理解

html是一种标记语言，它将我们呈现出的内容打上标记，使内容成为一个个的元素。

目标：让浏览器能够识别，让我们可以对元素进行一些操作（美化or交互）。

学习html就是要了解

1. 它有哪些标签
2. 如何正确的使用标签标记内容。

## 基本结构

html的基本结构如下：

```html
<!DOCTYPE html><!-- 声明这是一个html5标准的文档，需要放在首行 -->
<html><!-- 表示html文档的开始 -->
<head>
	<meta charset="utf-8" /><!-- 说明文档使用的编码集 -->
	<title>这是窗口的标题</title>
</head>
<body>
<!-- 这里放准备显示的内容 -->
</body>
</html><!-- "/"结束一个元素 -->
```

元素：标签+标签里的内容。如 `<p>这是一个段落</p>` 是一个p元素。

元素分为

1. 块元素。默认情况下，HTML 会自动地在块级元素前后添加一个额外的空行，比如段落、标题元素前后。
2. 内联元素。

标签：`<p>`这是一个标签，标签使用小写

属性：用来描述标签。如 `<p id="tagp"></p>` p元素中的id属性的值是tagp。属性和属性值对大小写不敏感。

HTML 代码中的所有连续的空行（换行）也被显示为一个空行。

在浏览器访问一个目录网页，会默认找这个目录下的默认文件，如index.html

## 常用标签

### 文本标签

```html
<h1>head 1</h1><!-- 标题，有h1, h2, h3, h4, h5, h6几个级别 -->
<p id="tagp">this is a paragraph.</p><!-- 段落 -->
<q>quote</q><!-- 小段引用，不换行 -->
<blockquote cite="http://www.baidu.com"><!-- 大段引用，换行。cite指向引用来源 -->
	<p>hello, this is baidu.
	I use it.</p>
	Because it's short.<br />
</blockquote>
<br /><!-- 换行符，元素内默认不换行，需要换行时需插入换行符 -->
<hr /><!--分割线-->
<p>
	<i>斜体</i>
	<b>加粗</b>
	<strong>带有语义的加粗</strong>
	<em>带有语义的斜体</em>
</p>
```

### 表格

```html
<!-- 表格 -->
<table border="1px" width="400" bgcolor="yellow">
	<tr><!-- 第一行 -->
		<td colspan="2">1-1</td><!-- 第一行的第1个单元格，与第二个单元格合并 -->
		<td>1-3</td><!-- 第一行的第3个单元格 -->
		<td>1-4</td>
	</tr>
	<tr><!-- 第二行 -->
		<td>2-1</td>
		<td>2-2</td>
		<td>2-3</td>
		<td>2-4</td>
	</tr>
</table>
<!-- colspan="2"属性表示合并2个列单元格，合并的另一个单元格需要在表格中删除。合并行单元格使用rowspan -->

```

### a标签

作用：插入一个跳转链接

```html
<!-- 
	1. 链接到外部网页
	2. 链接到本网页的某个元素，并在新窗口打开
		#h2tag表示定位到该页面中id="h2tag"的元素
		target="_blank"表示在新窗口中打开链接
					="_name"，表示在某个名字为_name的窗口中打开
					="_self"，表示在自身页面打开
	3. 定位到本网页的某个元素
	4. 从计算机本地读文件
-->
<a href="http://www.baidu.com" title="link to baidu">baidu</a>
<a href="this.html#h2tag" target="_blank">file</a>
<a href="#top">Back to top</a>
<a href="file:///chapter4/index.html">chapter4</a>
```

### img

```html
<!-- 图片
	img元素是一个空元素，没有内容。
	1. 指向另一个不同网站上的图片，输入图片的完整地址。
		alt属性：描述图片的信息，当图片无法展示时，会显示alt里的内容
		width：图片的宽度
		height：图片的高度
	2. 指向本地图片
	3. 将img元素放在a元素内，成为一个可以跳转的图片链接
-->
<img src="www.rainy.com/imags/width.jpg" alt="this decribe the imag" width="48" height="100"  />
<img src="./images/pic.png" alt="this is a pic" />
<a href="http://www.baidu.com"><img src="./images/pic.png" alt="this is a pic" /></a>
```

### 列表

```html
<!--有序列表-->
<ol>
	<li>list item 1</li>
	<li>list item 2</li>
</ol>

<!--无序列表-->
<ul>
	<li>list item 1</li>
	<li>list item 2</li>
</ul>

<!--定义列表-->
<dl>
	<dt>Burma Shave Signs</dt><!--名词-->
	<dd>Road signs common in the U.S. in the 1920s</dd><!--名词解释-->

	<dt>Route 66</dt>
	<dd>Most famous road in the U.S. highway system.</dd>
</dl>
```

### 表单

包含输入域的Web页面，允许输入信息。提交表单时，这些信息会打包并发送到一个web服务器。

每个表单元素都有一个name属性，当点击提交按钮时，浏览器会将"表单名=表单数据"这样的数据发送到服务器。

#### form标签

作用：向服务器提交数据

属性

- action：指向web服务器上将处理表单的文件。当action为空时，表单提交到当前url路径上。
- method：提交数据的方式
  - get 方式：默认提交方式，会将参数拼接在链接后面，页面有大小限制，4k以内
  - post 方式 会将参数封装在请求体中，没有这样的限制

#### input标签

作用：输入数据。是空元素。

属性

- type：指定输入项的类型
  - text：文本，在浏览器上创建一个单行控件。
  - password：密码框，输入的文本会加掩码
  - radio：单选按钮。关联的单选按钮都必须有相同的name属性
  - checkbox：复选框。相同的复选框业共用一个name
  - file：上传文件
  - submit：提交按钮
  - button：普通按钮
  - reset：重置按钮
  - hidden：隐藏域
  - date：弹出日期选择控件，指定日期（H5）
  - tel：弹出手机号定制键盘（H5）
  - number：只允许输入数字（H5），min和 max属性用来指明可以输入的最小值和最大值。
  - range：显示一个滑动条（H5），类似number。step属性指定值的间隔数
  - color：弹出一个颜色选择器（H5）。
  - email：某些移动浏览器可以使用email定制键盘输入（H5）。
- placeholder：显示在输入框内的提示信息
- name：元素的名字。服务器将使用这个元素名
- id：有唯一性的名字
- required：可以用于所有表单控件。指示这个域是必要的。没有填写就无法提交表单。

#### textarea标签

作用：创建一个多行的文本域，文本在文本区放不下时，有滚动条。不是空元素，元素内容会作为初始文本展示在文本区。

属性

- cols：指定宽度
- rows：指定高度
- name：为元素指定一个唯一的名字。

#### select标签

作用：显示一个下拉列表。和option标签结合使用

属性

- multiple：把单选菜单变成多选菜单。

option标签：select内的选择项。元素的内容作为菜单项的描述。

- value：菜单项的值

option没有name属性，select已经为整个表单指定了名字属性。

```html
<form action="../web.html" method="post">
	用户名：<input type="text" /><br />
	照片：<input type="file" /><br />
	<!-- type="radio"想要实现单选效果，需要给同一组radio起同一个name -->
	性别：
  <!-- checked 表示选中 -->
  <input type="radio" name="sex" checked />男
  <input type="radio" name="sex" />女<br />	
	爱好：
	<input type="checkbox" />swim
	<input type="checkbox" />running
	<input type="checkbox" />climing<br />	
	要求：
	<textarea cols="40" rows="6">初始文本</textarea><br />
	籍贯
	<select>
    <!-- selected 表示选中 -->
		<option selected>aaa</option>
		<option>bbb</option>
	</select><br />	
	<input type="submit" value="提交按钮" /><br />	
	<input type="button" value="普通按钮" /><br />	
	<!-- 隐藏域：不在页面上显示，用来存放页面上一些ID信息 -->
	<input type="hidden" value="sieng@163.com" /><br />	
	出生日期
	<input type="datatime-local" /><br />
	手机号
	<input type="tel" placeholder="input numbers" /><br />
</form>
```

#### fieldset和legend

用来将公共元素组织在一起。fieldset包围一组input，legend为这一组提供一个标签。

```html
<fieldset>
  <legend>Condiments</legend>
    <input />
    <input />
</fieldset>
```

### 布局标签

指div和span标签

作用：用来描述页面的逻辑布局，不在页面上显示。

div标签：块元素，划分逻辑分区。默认占一行，自动换行

span标签：内联元素，元素的逻辑分组。内容显示在同一行

```html
<div>abc</div>
<span>abc</span>
```

### 代码标签

```html
<!-- 代码，放在该标签里的内容，会原封不动的展示出来（包括换行） -->
<code>
	int main(){
	println("hello, world!");
	return 0;
}
//ab;
</code>
```

### 不推荐标签(font, frameset)

```html
<font></font><!-- 文字样式。尽量使用css调整样式 -->

<!--framset（H5不支持）
	作用：定义一个框架集，用来组织多个frame元素。如果使用framset，需要将body标签去掉。
	属性：
		clos：按列划分页面
		rows：按行划分页面
	另一个网页的target如果是rightFrame，可以跳转到该框架里
-->
<frameset cols="10%,*"><!-- 分成左右两部分，左边占10%，右边占剩余的90% -->
	<frame src="aaa.html" /><!-- 第一部分 -->
	<frameset rows="15%,*"><!-- 第二部分(分成上下两部分，上边占15%，下边占85%) -->
		<frame src="bbb.html" />
		<frame src="ccc.html" name="rightFrame" />
	</frameset>
</frameset>

<!-- bbb.html -->
<a href="www.baidu.com" target="rightFrame" >baidu</a>
<!-- 在rightFrame的区域内，显示百度的页面 -->
```

### 导入CSS

1. 使用style标签 `<style></style>`，中间存放css内容
2. 使用link标签 `<link rel="stylesheet" href="style.css"/>`，href指向css文件

### 导入JS

1. 使用 `<script></script>` 标签，中间存放JS内容
2. 使用script标签的src属性 `<script type="text/javascript" src="example.js"></script>`，src指向js文件

在解析外 JS 代码时，页面的处理会暂时停止。

`<script>` 元素的src属性可以包含来自外部域的 JavaScript 文件。

```html
<script type="text/javascript" src="http://www.somewhere.com/afile.js"></script>
````

`<script>` 元素会按出现的先后顺序被解析。

## 参考资料

HTML手册

