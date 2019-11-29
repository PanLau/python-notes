## Find & Replace in Sublime

> 示例正则文本见附件

#### 1. 常用快捷键
```
搜索
[Ctrl] + [F]

替换
[Ctrl] + [H]

光标批量操作
[Ctrl] + [Alt] + [↓]
或
[Ctrl] + [A], [Ctrl] + [Shift] + [L]

切换正则
[Alt] + [R]
折叠代码
Ctrl+Shift+[
展开代码
Ctrl+Shift+]

多行搜索
[Ctrl] + [Enter]

选中光标所占的文本
[Ctrl] + [D]
继续操作则会选中下一个相同的文本

```

#### 2. 搜索
```
区分大小写

区分顺序
```

```
搜索特殊字符需要 \ 转义
#### 特殊字符： .[{()\^$|?*+

无需转义的字符 \ 也可识别

```

#### 3. 匹配
```
.       - Any Character Except New Line
\d      - Digit (0-9)
\D      - Not a Digit (0-9)
\w      - Word Character (a-z, A-Z, 0-9, _)
\W      - Not a Word Character
\s      - Whitespace (space, tab, newline)
\S      - Not Whitespace (space, tab, newline)
\t      - Tab
\n      - New line

\b      - Word Boundary/Anchors
\B      - Not a Word Boundary
^       - Beginning of a String (插入符 caret)
$       - End of a String

[]      - Matches Characters in brackets
[a-zA-Z0-7]
[^ ]    - Matches Characters NOT in brackets
|       - Either Or
()      - Group
$2

匹配量词
*       - 0 or More
+       - 1 or More
?       - 0 or One
{3}     - Exact Number
{3,}
{,4}
{3,4}   - Range of Numbers (Minimum, Maximum)


#### 一些例子 ####

\d{3}.\d{3}.\d{4}
M(r|s|rs)\.?\s[A-Z]\w*
[a-zA-Z]+@[a-zA-Z]+\.com
[-0-9a-zA-Z.]+@[a-zA-Z-]+\.(com|edu|net)
[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+
[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9.-]+
.+@.+\..+
```


## [正则规则](http://s2.51cto.com/wyfs02/M00/77/DB/wKioL1ZvzI3xJ9TTAAJ4eW9k7oo890.png "正则规则")


#### 4. 替换
```
()      - Group
$2      $符号后面的数字为Group的()的位数，从左往右从1开始记
```

#### 5. 匹配量词


贪婪匹配<br>Greedy | 勉强匹配<br>Lazy | 占有匹配<br>Possessive
---|---|---
? | ?? | ?+
* | *? | *+
+ | +? | ++
> 贪婪匹配：正则表达式趋向于最大长度匹配
> 非贪婪匹配：就是匹配到结果就好，就少的匹配字符
> 默认是贪婪模式；在量词后面直接加上一个问号？就是非贪婪模式。


#### 6. 回溯
```
".+"
".+?"
abc?+c
"[^"]*+"
^(\d{1,3}?)(\d{1,3})$
```
#### 7. 环视

```
1、肯定顺序常规： [a-z]+(?=;)           字母序列后面跟着;
2、肯定顺序变种： (?=[a-z]+$).+$        字母序列

3、肯定逆序常规： (?<=:)[0-9]+          :后面的数字
4、肯定逆序变种： \b[0-9]\b(?<=[13579]) 0-9中的奇数

5、否定顺序常规： [a-z]+\b(?!;)     不以;结尾的字母序列
6、否定顺序变种： (?!.*?[lo0])\b[a-z0-9]+\b  不包含l/o/0的字母数字系列

7、否定逆序常规： (?<!age)=([0-9]+) 参数名不为age的数据
8、否定逆序变种： \b[a-z]+(?<!z)\b  不以z结尾的单词
```