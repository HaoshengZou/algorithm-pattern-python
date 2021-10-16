# 使用 Python3 写算法题

这里简单介绍使用 Python3 写算法题时的一些特点。本项目并不是一个 Python3 教程，所以默认大家对 Python3 有一定的了解，对于零基础的同学建议首先了解一下 Python3 的基本语法等基础知识。

## 逻辑

进行 coding 面试时，如果不指定使用的编程语言，一般来讲考察的是做题的思路而不是编程本身，因此不需要从零开始实现一些基础的数据结构或算法，利用语言的一些特性和自带的标准库可以大大简化代码，提高做题速度。下面会总结一些 Python3 常用的特性，标准算法和数据结构。

## 常用特性

Python语言有很多特性可以大大简化代码，下面列举几个常用的。

#### 数组初始化

```Python
# 初始化一个长度为 N 的一维数组
Array = [0] * N

# 初始化一个形状为 MxN 的二维数组(矩阵)
Matrix = [[0] * N for _ in range(M)] # [[0] * N] * M的话，列上"牵一发而动全身"
```

#### 交换元素值

```Python
# c语言风格的交换两个元素值
tmp = a
a = b
b = tmp

# python风格
a, b = b, a
```

#### 连续不等式或等式

```Python
# 判断 a，b，c 是否相等，Python里可以直接写连等
if a == b == c:
    return True

# 不等式也可以
if a <= b < c:
    return True
```

## 标准算法

#### 排序

Python 中排序主要使用 sorted() 和 .sort() 函数，在[官网](https://docs.python.org/3/howto/sorting.html)有详细介绍，大家可以自行阅读。

#### 二分查找和插入

Python 自带的 [bisect](https://docs.python.org/3/library/bisect.html) 库可以实现二分查找和插入，非常方便。

## 标准数据结构

#### 栈

Python 中的栈使用自带的 list 类来实现，可参考[官方文档](https://docs.python.org/3/tutorial/datastructures.html#using-lists-as-stacks)。

`s.append() s.pop()`足矣。

#### 队列

使用 collections 库中的 deque 类实现，可参考[官方文档](https://docs.python.org/3/library/collections.html#collections.deque)。

```python
q = deque(['a', 'b'])
q.append('c')
q.popleft()  # 注意纯pop()还是默认从尾部
```

#### 堆

Python 中没有真的 heap 类，实现堆是使用 list 类配合 heapq 库中的堆算法，且只支持最小堆，最大堆需要通过传入负的优先级来实现，可参考[官方文档](https://docs.python.org/3.8/library/heapq.html)。

#### HashSet，HashTable

分别通过 [set 类](https://docs.python.org/3.8/library/stdtypes.html#set-types-set-frozenset)和 [dict 类](https://docs.python.org/3/library/stdtypes.html#typesmapping)来实现。

## collections 库

Python 的 [collections 库](https://docs.python.org/3/library/collections.html)在刷题时会经常用到，它拓展了一些Python中基础的类，提供了更多功能，例如 defaultdict 可以预设字典中元素 value 的类型，自动提供初始化，Counter 可以直接统计元素出现个数等。

Counter最基本的功能就是初始值为0的defaultdict。

## 总结

以上列举了一些用 Python3 做算法题时可以用到的一些特性，标准算法和数据结构，总结得肯定不全，因为 Python3 真的有很多可以利用的"骚操作"，大家在学习本项目的时候也会见到，一下记不住也没关系，多实战就会了。