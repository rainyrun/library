# CSS

## 我的理解

作用：美化html文档里的元素的展现形式。

工作方式：

1. 通过选择器寻找html里的某个或某些元素
2. 为选择器命中的元素增加样式
3. 如果某个元素被多个选择器命中，则使用最具体的那个选择器的样式。如果具体程度一样，则选择最后的那个选择器样式

css优先级：

行内样式>ID选择器>类选择器>元素选择器。同优先级，就近原则

## 选择器

作用：找到要修饰的元素，为这些元素设置样式。

1. 元素选择器

```css
元素的名称{
	属性名：属性值
}
```

2. id选择器：以#开头，id必须是唯一的

```
#ID名{
	属性名：属性值
}
```

3. 类选择器：以.开头

```
.类名{
  属性名：属性值
}
```

4. 后代选择器：父元素可以是元素、ID、类

```
父元素 子元素1 子元素2{//以空格隔开
  属性名：属性值
}
```

5. 子选择器

```
父元素>子元素{//子元素是父元素的直接孩子时，才会应用
	属性名：属性值
}
```

6. 属性选择器：

```css
元素[属性1][属性2]{ //选中同时包含属性1和属性2的元素
	属性名：属性值
}
元素[属性='属性值']{ //选中属性=属性值的元素
	属性名：属性值
}
```

7. 伪类选择器

```css
a:link{ //未访问状态的链接
	color: green;
}
a:visited{ //已访问的链接
	color: red;
}
a:hover{ //悬停在一个链接上
	color: yellow;
}
```

选择器分组，相互以逗号隔开，如h1, h2

```css
h1, div{
	color:red;
	font-size:50px;
}
#div1{
	color:yellow;
}
.shuiguo{
	color: blue;
}
```

## 引入css

1. 外部样式：通过link标签引入外部css样式

```html
<link rel="stylesheet" href="style.css"/>
```

2. 行内样式：直接在标签中添加一个style属性

```html
<p style="font-family:verdana; color:red">paragraph</p>
```

## 盒子模型



## 常用属性

### 文本

| 属性        | 含义 | 可选项 |
| ---- | ---- | ------ |
| font-family | 字体 |        |
|font-size|字体大小|14px，150%，1.2em，small|
|font-weight|字体的粗细|bold，normal|
|color|文本颜色|red，rgb(80%, 40%, 0%)，rgb(255, 200, 0)，#cc6600|
|text-decoration|文本风格|italic（斜体），oblique（倾斜体），underline（下划线），line-through（删除线），overline（上划线），none（去除装饰）|
|text-align|对齐方式|center，left，right（只能在块元素上设置）|

#### 简写方式

font: (font-style font-variant font-weight) font-size/line-height font-family;（有顺序要求）

### 边框

| 属性 | 含义 | 可选项 |
| ---- | ---- | ------ |
|border-bottom|下边框||
|border-style|边框样式|groove, solid, double, outset, dotted, dashed, inset, ridge|
|border-radius|指定圆角|20px|

#### 简写方式

border：this solid #007e7e;(顺序随意)

### 边距

|属性|含义|可选项|
|-|-|-|
|line-height|行距|1.2em|
|padding|内边距|0px 20px 30px 10px;(上，右，下，左)|
|margin|外边距|同上|
|width|元素内容区的宽度|auto（默认，延伸占满可用的空间）, 500px, 50%|

如果一个内联元素四周都增加外边距，只能看到左边和右边都会增加空间。对内联元素的上边和下边增加内边距，内边距会与其他内联元素重叠。

浏览器上下放置两个块元素时，会折叠它们的外边距，共同外边距是较大的那个

### 背景

| 属性 | 含义 | 可选项 |
| ---- | ---- | ------ |
|background-image|设置一个元素的背景图片|url(images/bg.gif)|
|background-repeat|元素背景图片的重复方式|no-repeat, repeat-x, repeat-y, inherit(按父元素的设置来处理)|
|background-position|背景的对齐方式|top，left|

简写方式

background: white url(images/cocktail.gif) repeat-x;（顺序随意）

### 浮动

| 属性 | 含义 | 可选项 |
| ---- | ---- | ------ |
|float|将元素从正常的文档流中删除，悬浮在页面上|left, right, clear(当元素流入页面时，在这个元素的左边、右边或者两边不允许有浮动的内容)|
|position|元素的位置|static：默认值，元素放在正常文档的流中。<br />absolute：由你来确定元素放在哪儿，会把元素从页面的正常流中删除。<br />fixed：放在相对于浏览器窗口的一个位置上（而不是页面）。<br />relative：正常流入页面，但在显示上有偏移|

overflow: visible, hidden, scroll, auto;//当内容超出元素自身大小时，对内容进行“裁剪”



- CSS 设置文字只显示一行，多余显示省略号

```css
.view-text{
  /**
  留一行
	思路：
	1.设置inline-block属相
	2.强制不换行
	3.固定高度
	4.隐藏超出部分
	5.显示“……”
  */
  display: inline-block;
  white-space: nowrap; 
  width: 100%; 
  overflow: hidden;
  text-overflow:ellipsis;
}

/**
留两行
思路：
	1.超出的文本隐藏
	2.溢出用省略号显示
	3.溢出不换行
	4.将对象作为弹性伸缩盒子模型显示
	5.从上到下垂直排列子元素（设置伸缩盒子的子元素排列方式）
	6.这个属性不是css的规范属性，需要组合上面两个属性，表示显示的行数
  */
  .text2{
	width:200px;
	word-break:break-all;
	display:-webkit-box;
	-webkit-line-clamp:2;
	-webkit-box-orient:vertical;
	overflow:hidden;
}
```

