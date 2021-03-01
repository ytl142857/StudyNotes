# Python-collections 容器数据类型

> [Python 官方文档-collections](https://docs.python.org/zh-cn/3.8/library/collections.html)
>
> “这个模块实现了特定目标的容器，以提供Python标准内建容器 dict , list , set , 和 tuple 的替代选择。”

## 1. Counter对象

可以用于·快速和方便地计数

```python
from collections import Counter

cnt = Counter()
for word in ['red', 'blue', 'red', 'green', 'blue', 'blue']:
    cnt[word] += 1

print(cnt)

# output:
# Counter({'blue': 3, 'red': 2, 'green': 1})
```

一个 Counter 是一个 dict 的子类，用于计数可哈希对象。它是一个集合，元素像字典键(key)一样存储，它们的计数存储为值。计数可以是任何整数值，包括0和负数。

```python
from collections import Counter

c0 = Counter()
c1 = Counter('red')
c2 = Counter({'red': 4, 'blue': 2})
c3 = Counter(cats=4, dogs=8)

print(c0)
print(c1)
print(c2)
print(c3)

# output:
# Counter()
# Counter({'r': 1, 'e': 1, 'd': 1})
# Counter({'red': 4, 'blue': 2})   
# Counter({'dogs': 8, 'cats': 4}) 
```

Counter对象有一个字典接口，如果引用的键没有任何记录，就返回一个0，而不是弹出一个 KeyError :

```python
c = Counter(['red', 'blue'])
print(c['purple'])

# output:
# 0
```

- `elements()`方法：

返回一个迭代器，其中每个元素将重复出现计数值所指定次。 元素会按首次出现的顺序返回。 如果一个元素的计数值小于一，`elements() `将会忽略它。

```python
c = Counter(['red', 'blue', 'blue', 'red', 'green'])
print(sorted(c.elements()))

# output:
# ['blue', 'blue', 'green', 'red', 'red']
```

- `most_common([n])`方法

返回一个列表，其中包含 n 个最常见的元素及出现次数，按常见程度由高到低排序。 如果 n 被省略或为 `None`，`most_common() `将返回计数器中的 所有 元素。 计数值相等的元素按首次出现的顺序排序。

```python
c = Counter(['red', 'blue', 'blue', 'red', 'green'])
print(c.most_common(2))

# output:
# [('red', 2), ('blue', 2)]
```

- `subtract([iterable-or-mapping])`方法

从迭代对象或映射对象减去元素。是减去，而不是替换。输入和输出都可以是0或者负数。

```python
c = Counter(['red', 'blue', 'blue', 'red', 'green'])
d = Counter(red=2, blue=3)
c.subtract(d)
print(c)

# output:
# Counter({'green': 1, 'red': 0, 'blue': -1})
```

通常的字典方法（`dict`）都可用于`Counter`对象

除了`fromkeys`（没有实现）和`update`（从迭代对象 计数元素或者 从另一个 映射对象 (或计数器) 添加。）

## 2. deque对象

返回一个双端队列对象，可以通过iterable数据创建。

| 方法                      | 作用                                                         |
| ------------------------- | ------------------------------------------------------------ |
| append(x)                 | 右端添加                                                     |
| appendleft(x)             | 左端添加                                                     |
| clear()                   | 移除所有元素                                                 |
| copy()                    | 创建一份浅拷贝                                               |
| count(x)                  | 计算deque中x个数                                             |
| extend(iterable)          | 右侧扩展                                                     |
| extendleft(iterable)      | 左侧扩展                                                     |
| index(x[, start[, stop]]) | 返回x在deque中的位置（start后，stop前），未找到引发ValueError |
| insert(i, x)              | 在i插入x                                                     |
| pop()                     | 右端弹出                                                     |
| popleft()                 | 左端弹出                                                     |
| remove(value)             | 删除元素，未找到引发ValueError                               |
| reverse()                 | 逆序排列                                                     |
| rotate(n=1)               | 向右循环移动n步，如果n是负数，向左循环                       |

使用示例：

```python
from collections import deque

d = deque('hello')
for elem in d:
    print(elem.upper(), end=' ')

# output:
# H E L L O 

d.append('z')
d.appendleft('a')
print(d)

# output:
# deque(['a', 'h', 'e', 'l', 'l', 'o', 'z'])

d.reverse()
print(d)

# output:
# deque(['z', 'o', 'l', 'l', 'e', 'h', 'a'])

d.rotate(-3)
print(d)

# output:
# deque(['l', 'e', 'h', 'a', 'z', 'o', 'l'])
```

## 3. defaultdict对象

返回一个新的类似字典的对象。 defaultdict 是内置 dict 类的子类。它重载了一个方法并添加了一个可写的实例变量。其余的功能与 dict 类相同。

使用示例：用 list 作为 default_factory，很轻松地将（键-值对组成的）序列转换为（键-列表组成的）字典

```python
from collections import defaultdict

s = [('yellow', 1), ('blue', 2), ('yellow', 3), ('blue', 4), ('red', 1)]
d =defaultdict(list)

for k, v in s:
    d[k].append(v)

sorted(d.items())
print(d)

# output:
# defaultdict(<class 'list'>, {'yellow': [1, 3], 'blue': [2, 4], 'red': [1]})
```

## 4. OrderedDict对象

## 5. UserDict对象

## 6. UserList对象

## 7. UserString对象

## 8. ChainMap对象

将多个字典或其他映射组合在一起

注意，一个 ChainMap() 的迭代顺序是通过扫描最后的映射来确定的:

```python
from collections import ChainMap

baseline = {'music': 'bach', 'art': 'rembrandt'}
adjustments = {'art': 'van gogh', 'opera': 'carmen'}

cm = ChainMap(baseline, adjustments)
print(list(cm))

# output:
# ['art', 'opera', 'music']
```

