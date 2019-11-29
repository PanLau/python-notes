# numpy基础
## numpy属性
- ndim:维度
- shape:行数和列数
- size:元素个数

### 如下为demo
```
import numpy as np

array = np.array([[1, 2, 3], [2, 3, 4]])

print(array)
# 维数
print("number of dim:", array.ndim)
# （行，列）
print("shape:", array.shape)
# 个数
print("size:", array.size)

# 对应结果如下
[[1 2 3]
 [2 3 4]]
number of dim: 2
shape: (2, 3)
size: 6
```

## numpy创建array
- array:创建数组
- dtype:指定数据类型
- zeros:创建数据全为0
- ones:创建数据全为1
- empty:创建数据接近0
- arrange:按指定范围创建数据
- linspace:创建线段

### 如下为demo
```
import numpy as np

# 一维
a = np.array([2, 23, 4], dtype=np.int)
a0 = np.array([2, 23, 4], dtype=np.float32)
# 默认为64位;一般来说，会用到int和float;64位更精确
print(a.dtype)

# 二维
a1 = np.array([[2, 23, 4], [2, 23, 4]])
print(a1)

a2 = np.zeros((3, 4))
print(a2)

a3 = np.ones((3,4), dtype=np.int16)
print(a3)

a4 = np.empty((3,4))
print(a4)

a5 = np.arange(10, 20, 2)
print(a5)

a6 = np.arange(12).reshape((3, 4))
print(a6)

a7 = np.linspace(1, 10, 5)
print(a7)

a8 = np.linspace(1, 10, 6).reshape((2, 3))
print(a8)
```








