# eclipse

[官方文档](https://help.eclipse.org/neon/index.jsp)

[小图标的解释](https://help.eclipse.org/neon/index.jsp?topic=/org.eclipse.jdt.doc.user/reference/ref-icons.htm)

[常用图标的解释](https://blog.csdn.net/wuqilianga/article/details/79097371)

## 快捷键

注：在mac下，使用command代替ctrl

代码整理

|作用|快捷键|
|---|---|
|缩略补全（内容辅助键）|`alt+/` 如，输入main(syso)后，按 `alt+/`|
|单行注释|`ctrl+/` ；取消 `ctrl+/`|
|多行注释||
|格式化(整理代码格式)|ctrl+shift+f|
|选中并上移|alt + ⬆|
|删除一行|ctrl + D|
|快速生成返回值|ctrl + 2|

查找和定位

|作用|快捷键|
|---|---|
|搜索并查看某类的源码|ctrl + shift + t|
|查看当前类下的所有成员|ctrl + o|
|查看继承体系，家族树|ctrl + t 或 f4|
|查找资源|ctrl + shift + r|
|跳转到对应类的源码|ctrl + 单击|
|查找之前编辑过的文件|ctrl + e|
|回到上次编辑位置|ctrl + q 或 ctrl + [ 或 ctrl + ]|
|窗口最大化和还原|Ctrl+ m|
|快速向下、向上查找选中的内容|Ctrl+K、Ctrl+Shift+K|
|选中光标右侧(左侧)的一行|ctrl + shift + ➡️ (⬅)|
|移动光标到行头、行尾|ctrl + ⬅ (➡️)| 

[快捷键待整理](https://www.cnblogs.com/iamfy/archive/2012/07/11/2586869.html)

## 项目导入依赖的jar包

1. 右击要导入jar包的项目，点properties 
2. 左边选择java build path,右边选择libraries 
3. 选择add External jars 
4. 选择jar包

## 自动扩展导入列表

菜单选项 Source --> Organize Imports 

将类似 `importjava.util.*;` 扩展成 `import java.util.ArrayList; import java.util.Date;`

## 跨项目debug

debug 图标旁边的小三角 --> debug configuration --> 左侧选择一个项目 --> 右边的tab标签中选择source --> 点击add添加要联调的项目 --> 点击debug即可

## 导出war包

选中某个项目，右键 --> Export --> Web --> war 选择到出目录即可