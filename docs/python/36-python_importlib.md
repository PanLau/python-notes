###### Create by:along
###### CreateTime 20190424-16:00

## python动态导入模块&动态执行python代码

#### 1   -动态导入模块方法:

```python
## 栗子:在lib下定义模块along_pack
class FirstImport(object):

    def __init__(self, name="along"):

        self.name = name

    def start(self):

        return "你好呀 !" + self.name
```



- <用法1>  直接使用`__import__`模块

  ```python
  lib = __import__('lib.along_pack')

  c = lib.along_pack.FirstImport()

  print(c.start()) ## 你好呀 !along
  ```



- <用法2>  使用`import importlib`里的 `importlib.import_module`方法

  ```python
  import importlib

  a = importlib.import_module('lib.along_pack')

  c = a.FirstImport(name='strong')

  print(c.start()) ## 你好呀 !strong
  ```

> getattr(obj,name,val):返回对象obj中名为name的属性值，如果没有该属性，且给定了val参数，则返回val
>
> hasattr(obj,name):如果对象obj中存在名为name的属性，则返回True
>
> delattr(obj,name):删除obj中名为name的属性
>
> setattr(obj,name,val):将对象obj中名为name的属性值设置为val，如果不存在该属性，则进行创建

>
>
> 两种方法的区别: 方法2 代码更简洁,并且支持reload( )
>
> 1 reload()重新加载，一般用于原模块有变化等特殊情况。
>
> 2 reload()之前该模块必须已经使用import导入模块。
>
> 3 重新加载模块，但原来已经使用的实例还是会使用旧的模块，而新生产的实例会使用新的模块，reload后还是用原来的内存地址。





#### 2   -动态执行python代码:

- exec : 把传入的字符串当代码执行,不返回结果

  ```python
  a = 1
  exec ("a=2") ##无结果输出
  print(a) ## 2
  b = "pandas"
  print(exec("import %s as pd"%b)) ##None
  pd.read_excel(r'E:\Desktop\along_test\along_hetong_test.xlsx')
  ```

- eval : 把传入的字串串当成表达式执行,返回结果

  ```
  a = [1,2]
  b = ['along','strong']
  a_dic = dict(zip(a,b))
  print(a_dic) ## {1: 'along', 2: 'strong'}
  cc = "a_dic[3] ='lp'"
  eval(cc) ##报错
  exec(cc) ##print(a_dict) {1: 'along', 2: 'strong', 3: 'lp'}


  ##两个都可以解析字符串中的字符当成关键字参数进行运算
  dd = 'a+b'
  print(dd) ## 'a+b'

  eval(dd) ##[1, 2, 'along', 'strong']
  exec(dd) ##print(dd) 'a+b'
  ```