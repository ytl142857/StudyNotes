# Python迭代器&生成器

> [菜鸟教程-Python3 迭代器与生成器](https://www.runoob.com/python3/python3-iterator-generator.html)

## 1. 迭代器

迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。迭代器只能往前不会后退。

迭代器有两个基本的方法：`iter()` 和 `next()`

```python
nums = [1, 2, 3, 4]

it = iter(nums)

print(it)
print(next(it))
print(next(it))
print(next(it))
print(next(it))
print(next(it))

# output:
# <list_iterator object at 0x0000028510E76C10>
# 1
# 2
# 3
# 4
# Traceback (most recent call last):
#   File "test.py", line 10, in <module>      
#     print(next(it))
# StopIteration
```

通过`for`语句遍历迭代器：

```python
nums = [1, 2, 3, 4]

it = iter(nums)
for x in it:
    print(x, end=' ')

# output:
# 1 2 3 4 
```

## 2. 生成器

```python
import sys

def fibonacci(n):
    a, b, counter = 0, 1, 0
    while True:
        if counter > n:
            return
        yield a
        a, b = b, a+b
        counter += 1

f = fibonacci(10)

while True:
    try:
        print(next(f), end=" ")
    except StopIteration:
        sys.exit()
```

跟普通函数不同的是，生成器是一个返回迭代器的函数，只能用于迭代操作，更简单点理解生成器就是一个迭代器。 

在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，返回 yield 的值, 并在下一次执行 next() 方法时从当前位置继续运行。