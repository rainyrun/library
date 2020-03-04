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

编辑相关快捷键

1. 【ALT+/】自动提示方法
2. 【Ctrl+O】显示类中方法和属性的大纲
3. 【Ctrl+/】快速添加/取消注释，
4. 【Ctrl+Shift+/】、【Ctrl+Shift+\】块注释/销注释/XML注释
5. 【Ctrl+D】删除当前行
6. 【Ctrl+M】窗口最大化和还原
7. 【Ctrl+Shift+S】全局保存
8. 【Ctrl+1】快速修正
9. 【Shift+Enter】、【Shift+Ctrl+Enter】插入空行。在当前光标位置下面或上面插入空行。
10. 【Ctrl+Alt+↓】、【Ctrl+Alt+↑】复制当前行到下一行(上一行)
11. 【Alt+↑】、【Alt+↓】上下两行交换位置。
12. 【Ctrl + Shift + M】引入某个类（接口）
 
查看和定位快捷键

1. 【Ctrl+K】、【Ctrl++Shift+K】快速向下和向上查找选定的内容
2. 【Ctrl+Shift+T】查找工作空间（Workspace）构建路径中的可找到Java类文件
3. 【Ctrl+Shift+R】查找工作空间（Workspace）中的所有文件（包括Java文件），也可以使用通配符。
4. 【Ctrl+G】查找当前元素的声明
5. 【Ctrl+Shift+G】查找类、方法和属性的引用。
6. 【Ctrl+Shift+O】快速生成import
7. 【Ctrl+Shift+F】 规范代码 
8. 【ALT+Shift+W】查找当前文件所在项目中的路径
9. 【Ctrl+L】定位到当前编辑器的某一行，对非Java文件也有效。
10. 【Alt+←】、【Alt+→】后退历史记录和前进历史记录
11. 【F3】快速定位光标位置的某个类、方法和属性。
12. 【F4】显示类的继承关系，并打开类继承视图。
13. 【先敲“/”在敲两个**，然后回车或在方法名之前按Alt+Shift+J可以添加Javadoc 注释】方法注释
14. 【Ctrl+H】查找。可以在整个工程查找或者查找替换
 
调试快捷键

1. 【Ctrl+Shift+B】：在当前行设置断点或取消设置的断点。
2. 【F11】：调试最后一次执行的程序。
3. 【Ctrl+F11】：运行最后一次执行的程序。
4. 【F5】：单步跟踪到方法中。
5. 【Ctrl+F5】：单步跳入选择。
6. 【Shift+F5】：使用过滤器单步执行。
7. 【F6】：单步执行程序。
8. 【F7】：执行完方法，返回到调用此方法的后一条语句。
9. 【F8】：继续执行，到下一个断点或程序结束。
10. 【Ctrl+D】：显示。
11. 【Ctrl+R】：运行至行。
12. 【Ctrl+U】：执行
 
常用编辑器快捷键

1. 【Ctrl+C】：复制。
2. 【Ctrl+X】：剪切。
3. 【Ctrl+V】：粘贴。
4. 【Ctrl+S】：保存文件。
5. 【Ctrl+Z】：撤销。
6. 【Ctrl+Y】：重复。
7. 【Ctrl+F】：查找。

查看快捷键定义的地方 Window->Preferences->General->Keys。

取消对“块注释的格式化” Windows->Preferences->Java->Code Style->Formatter->Edit->Comments，然后取消对“Enable block comment formatting"的勾选。

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



设置eclipse的tab

eclipse --> preferences --> general --> editors --> text editors --> 勾选 insert spaces for tab

IDE 的 text file encoding 设置为 UTF-8; IDE 中文件的换行符使用 Unix 格式

eclipse --> preferences --> general --> workspace --> text file encoding 和 new text file line delimiter


