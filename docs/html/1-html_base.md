# HTML&CSS入门

## Visual Studio Code
### 1.view in broswer，将默认浏览器设置为谷歌

### 2.快捷键操作：
    复制一行：alt+shift+方向键
    上下移动一行：alt+方向键
    删除一行：ctrl+shift+k
    缩进：tab
    反向缩进：shift+tab
    格式化代码：shift+alt+f
    注释：ctrl+/
    ctrl+enter跳到下一行
    ctrl+shift+enter跳到上一行


## 一、HTML
### 1.表单练习-搜索框的例子
text和btn之间有间隙，必须需要加左右浮动
placeholder是提示内容

### 2.块元素类型的特征（div、p、h、ul、li、body）
默认宽度充满父级；
如果写宽度，标签页也不会排成一排，每个都会占领一排

P标签默认有外边距

### 3，内联元素（a href、span、em、I、b、strong）
不支持宽、高，margin上下、padding上下；

### 4.关于文字的样式一般可以继承；
### 5.自己全部浮动，父级不设置高度，那么高度不会被撑开

### 6.bg属性的高度100%，只有在这种定位时才起作用
background-position: 左 中 右 上 中 下;
基准线是图片

### 7.表格
水平单元格合并：colspan
垂直单元格合并：rowspan
在属性中设置表格的样式，不要在style中设置，且用百分比设置宽度
表格中的文字居中：vertical-align: bottom;

### 8.页面开发流程演示
如果子级标签默认带样式，就不继承父级样式，（可将h1-h6样式的font-size改成100%）；

幻灯片里面的圆点宽度不确定，由左浮动导致其他方案无法到中间去。

## 二、css
一个参数，例如：margin:10px;表示四边外边距10像素；
两个参数，例如：margin:10px 5px;表示上下外边距10像素，左右外边距5像素；
三个参数，例如：margin:10px 5px 2px;表示上外边距10像素，左右边距5像素，下边距2像素；
四个参数，例如：margin:10px 5px 2px 1px;表示上外边距10像素，右外边距5像素，下外边距2像素，左外边距1像素。

## 三、JavaScript
### 1.引入js的三种方式
行间事件
页面script标签嵌入
外部引入

### 2.变量类型
number数字类型；string字符串类型；布尔型；undefined；null；
弱类型语言，变量类型由变量的值来确定
undefined:变量定义了，值未定义

### 3.注释
单行注释
多行注释 vscode快捷键：alt+shift+a

### 4.命名规范
区分大小写
第一个字符必须是字母、下划线或者美元符号
其他字符可以是字母、下划线、美元符或数字

命名风格：匈牙利命名风格

### 5.函数
#### 5.1函数的参数
对象的理解：方法就是函数，属性就是变量
JavaScript是从上往下进行读取的
方法的预解析，变量的预解析
如果函数有两个参数，第二个参数没传，是undefined；实参可以传比形参多；可以不写参数console.log()

#### 5.2.函数的返回值
return后的代码不执行；方法里的变量如果没有用var就是全局变量

### 6.条件语句
==；===；&&；||；！
swith case不加break代表前面和后面执行相同的语句，且这里的数字字符串是来判断类型的
document.getElementById('div1');

### 7.属性的操作
html的属性和js里面属性写法一样

style的属性是对象
style属性中的样式属性，没有“-”的，写法相同
style属性中的样式属性，有“-”的，改成驼峰式
操作的是内联样式，不是选择器里的

字符串里如果是纯数字，可以运算；如果要进行加法运算，需要用parseInt进行转换

### 8.一般下jquery对象前起名字用$
不会返回null，还是返回jQuery对象，只是length为0
子级（包含子级的子级）包含的span
如果对象里有多个标签，值获取第一个标签的样式
全部标签都会设置

