---
title: Python内置方法
date: 2018-11-24 14:35:54
tags:
 - Python
 - 内置方法
categories: Python内置方法
---

Python中有很多内置方法，比如`new`, `class`, `super`等等，本文详细介绍了Python中的常用内置方法。

<!--more-->

#### Python内置方法介绍

##### Python内置方法介绍之普通方法

###### `super`方法

super方法是Python中的一个常用内置方法，它用来帮助我们调用父类中的方法，从而实现向前兼容，Python官方文档中对super方法的解释是：

```
Return a proxy object that delegates method calls to a parent or sibling class of type. This is useful for accessing inherited methods that have been overridden in a class. The search order is same as that used by getattr() except that the type itself is skipped.
```

意思是，它代表我们调用父类中的方法。

在没有super方法时，我们用以下方式调用super方法:

```
class A(object):
    def __init__(self):
        print("A")

class B(A):
    def __init__(self):
        print("B")
        A.__init__(self)
```

以上调用方式，我们需要用父类的类名直接调用父类中的方法，并将当前类（子类）的实例对象传递给父类（类方法和静态方法等不需要传self的方法除外）。

而如果使用Python2中的super方法，我们可以将以上调用方式，改为如下形式：

```
class A(object):
    def __init__(self):
        print("A")
        
class B(A):
    def __init__(self):
        print("B")
        super(B, self).__init__()
```

注意，必须在父类中继承object对象。

如果使用Python3中的super方法，我们可以进一步将调用方式，简写为如下形式：

```
class A(object):
    def __init__(self):
        print("A")
        
class B(A):
    def __init__(self):
        print("B")
        super().__init__()
```

可以看到，我们不需要再将子类和实例对象传给super方法了，写法更加简洁。

下面看一下多继承

```
class A(object):
    def foo(self):
        print("A.foo")
        
class B(A):
    def foo(self):
        print("B.foo")
        super().foo()
        
class C(A):
    def foo(self):
        print("C.foo")
        super().foo()
        
class D(B, C):
    def foo(self):
        print("D.foo")
        super().foo()
        
d = D()
d.foo()
```

执行结果如下：

```
D.foo
B.foo
C.foo
A.foo
```

可见，查找顺序也是根据D类的mro列表中的顺序来的。

classmethod的调用方式也类似，下面分别是Python2和python3中classmethod的调用方式：

Python2：

```
class A(object):
    @classmethod
    def foo(cls):
        print("A.foo")

class B(A):
    @classmethod
    def foo(cls):
        print("B.foo")
        super(B, cls).foo()
        
B.foo()
```

Python3

```
class A(object):
    @classmethod
    def foo(cls):
        print("A.foo")

class B(A):
    @classmethod
    def foo(cls):
        print("B.foo")
        super().foo()
        
B.foo()
```

注意，因为直接使用类调用classmethod，所以没有实力对象，在使用Python2调用时，super方法的参数可以类名和cls的任意组合，比如, super(B, cls)、super(B, B)、super(cls, B)、super(cls, cls)，建议使用super(B, cls)。

##### Python内置方法介绍之双下划线方法

###### `__getattribute__`

当我们访问对象的属性时，执行该方法（如果有），如果同时存在`__getattr__`方法，后者不会执行，除非`__getattribute__`方法调用它或者`__getattribute__`方法抛出一个**AttributeError**错误。为了避免无限循环，我们必须使该方法返回原生的调用结果，请看：

```python
# -*- coding: utf-8 -*-


class UserInfo:
    def __init__(self, name):
        self.name = name

    def __getattribute__(self, item):
        print("__getattribute__ method execute")
        # 此方式会触发无限循环
        return self.item


u = UserInfo("Joe")
print(u.name)
```

使用如下方式，避免无限循环

```python
# -*- coding: utf-8 -*-


class UserInfo:
    def __init__(self, name):
        self.name = name

    def __getattribute__(self, item):
        print("__getattribute__ method execute")
        # 此方式会触发无限循环
        return object.__getattribute__(self, item)


u = UserInfo("Joe")
print(u.name)
```

如果同时存在`__getattr__`方法，后者不会执行，除非`__getattribute__`方法调用它或者`__getattribute__`方法抛出一个**AttributeError**错误：

```python
# -*- coding: utf-8 -*-


class UserInfo:
    def __init__(self, name):
        self.name = name

    def __getattribute__(self, item):
        print("__getattribute__ method execute")
        # 主动调用__getattr__方法
        raise AttributeError("不存在，再找找")

    def __getattr__(self, item):
        print("__getattr__ method execute")
        return object.__getattribute__(self, item)


u = UserInfo("Joe")
print(u.name)

```

