###### Create by:along


# type( ) and isinstance( )

### type() 方法的语法:

type(name, bases, dict)，其中name是类的名称，bases是基类的元组，dict是类内定义的命名空间变量。当有一个参数时它的返回值是对象类型, 有三个参数时，返回值是新的类型对象
> name就是类名
> base是要继承的父类
> dict包含了里面所有的方法和变量

###  isinstance() 方法的语法:


isinstance(object, classinfo)，其中object 是实例对象，变量，classinfo 可以是直接或间接类名、基本类型或者由它们组成的元组（如tuple,dict,int,str,float,list,set,bool,class类等）。如果对象的类型与classinfo相同则返回 值为True，否则返回值为 False
```python
## classinfo为元组时
a = 998
isinstance(a,(int,tuple,list)) ##True
```

---------------------


```python
n = 999
type(n)         ## int
type(n) is int  ## True
```


---
```python
a = 998
isinstance(a,int) ## True
```



- 在判断子类上这两个函数不一样。type()不会认为子类是父类的类型，不考虑继承关系；isinstance()会认为子类是父类的类型，考虑继承关系。
---
```python
class A:
    pass

class B(A):
    pass
```
---

```python
print(type(A()))            ##<class '__main__.A'>
print(type(A())==A)         ##True
print(isinstance(A(), A))   ##True
```



---

```python
print(type(B()))            ##<class '__main__.B'>
print(type(B())==A)         ##False
print(isinstance(B(), A))   ##True
```




---




>  涉及版本的问题


## Python新式类(new-style class)与经典类(旧式类)(old-style class)的区别
```
旧式类跟新式类的type()结果是不一样的。旧式类都是<type 'instance'>。
```

```python
## Python 2.x中默认都是经典类，只有显式继承了object才是新式类
class A:
  pass
class B:
  pass
class C(object):
  pass
print 'old style class',type(A()) ##old style class <type 'instance'>
print 'old style class',type(B()) ##old style class <type 'instance'>
print 'new style class',type(C()) ##new style class <class '__main__.C'>
print type(A()) == type(B())      ##True
```



### Python中类分两种：旧式类和新式类：

➤新式类都从object继承，经典类不需要,默认从object继承。

➤新式类的MRO(method resolution order 基类搜索顺序)算法采用C3算法广度优先搜索，而旧式类的MRO算法是采用深度优先搜索

➤新式类相同父类只执行一次构造函数，经典类重复执行多次。



#### 广度(宽度)优先搜索,深度优先搜索：

广度优先(Breadth-First Search)

```
宽度优先搜索算法（又称广度优先搜索）是最简便的图的搜索算法之一
其别名又叫BFS，属于一种盲目搜寻法，目的是系统地展开并检查图中的所有节点，以找寻结果。它并不考虑结果的可能位置，彻底地搜索整张图，直到找到结果为止。
```
![宽度优先遍历1](/uploads/201810/analyst/attach_155e96ec6617b0a5.gif "宽度优先遍历1")
![宽度优先遍历2](/uploads/201810/analyst/attach_155e96f0d1f73620.jpg "宽度优先遍历2")

深度优先(Depth-First Search)：

![](/uploads/201810/analyst/attach_155e96fab9d2d9c7.png)


可能的路径：A->B->E（没有路了 回溯到A)->C->F->H->G->D（没有路，最终回溯到A,A也没有未访问的相邻节点，本次搜索结束）

思路：1 从顶点A出发
2 依次从A的未被访问的邻接点出发，对图进行深度优先遍历；直到图中所有顶点均被访问为止




# 总结

#### 不存在说isinstance比type更好。只有哪个更适合需求