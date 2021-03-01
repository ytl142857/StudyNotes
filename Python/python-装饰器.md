# Python-装饰器 decorators

> [菜鸟教程-Python 函数装饰器](https://www.runoob.com/w3cnote/python-func-decorators.html)

## 1. 在函数中返回函数

```python
def hi(name="tl"):
    def greet():
        return "now you are in the greet() function"

    def welcome():
        return "now you are in the welcome() function"

    if name == "tl":
        return greet
    else:
        return welcome

a = hi()
print(a)
print(a())

# <function hi.<locals>.greet at 0x00000264BDCAA0D0>
# now you are in the greet() function
```

注意：在返回的时候返回函数名`welcome`而非`welcome()`，否则会返回函数的返回值

## 2. 将函数作为参数传递

```python
def hi():
    return "hi tl"

def doSomeThingBeforeHi(func):
    print("Im in front of Hi!")
    print(func())

doSomeThingBeforeHi(hi)

# Im in front of Hi!
# hi tl
```

可以看到传递进去了`hi()`

## 3. 装饰器

### 3.1 第一个装饰器

使用装饰器，可以在一个函数的前后执行代码

```python
def a_new_decorator(a_func):
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")

        a_func()

        print("I am doing some boring work after executing a_func()")

    return wrapTheFunction

def a_function_requring_decoration():
    print("I am the function which needs some decoration to remove my foul smell")

a_function_requring_decoration()

a_function_requring_decoration = a_new_decorator(a_function_requring_decoration)

a_function_requring_decoration()

# I am the function which needs some decoration to remove my foul smell
# I am doing some boring work before executing a_func()
# I am the function which needs some decoration to remove my foul smell
# I am doing some boring work after executing a_func()
```

### 3.2 使用'`@`'来生成一个装饰器

```python
def a_new_decorator(a_func):
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")

        a_func()

        print("I am doing some boring work after executing a_func()")

    return wrapTheFunction

@a_new_decorator
def a_function_requring_decoration():
    print("I am the function which needs some decoration to remove my foul smell")

a_function_requring_decoration()
```

`@a_new_decorator`其实和`a_function_requring_decoration = a_new_decorator(a_function_requring_decoration)`是相同意思

### 3.3 装饰器的方法名

按照3.2的方法构建装饰器后，如果我们打印方法名，会出现如下问题：

```python
print(a_function_requring_decoration.__name__)

# wrapTheFunction
```

这里的函数被`warpTheFunction`替代了。它重写了我们函数的名字和注释文档(docstring)。我们可以使用`functools.wraps`解决这一问题

```python
from functools import wraps

def a_new_decorator(a_func):
    @wraps(a_func)
    def wrapTheFunction():
        print("I am doing some boring work before executing a_func()")
        a_func()
        print("I am doing some boring work after executing a_func()")
    return wrapTheFunction

@a_new_decorator
def a_function_requiring_decoration():
    print("I am the function which needs some decoration to "
          "remove my foul smell")

print(a_function_requiring_decoration.__name__)

# a_function_requiring_decoration
```

### 3.4 装饰器蓝本规范

```python
from functools import wraps

def decorator_name(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        if not can_run:
            return "Function will not run"
        else:
            return f(*args, **kwargs)
    return decorated

@decorator_name
def func():
    return("Function is running")

can_run = True
print(func())

can_run = False
print(func())
```

## 4. 类装饰器

```python
class decorator_class(object):
    def __init__(self, func):
        self._func = func
        print("class decorator initing")

    def __call__(self):
        print("class decorator running")
        self._func()
        print("class decorator ending")

@decorator_class
def func():
    print("function!")

print("----------------------")
func()

# class decorator initing
# ----------------------
# class decorator running
# function!
# class decorator ending
```

