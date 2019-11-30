---
title: DRF之注册器响应器分页器
date: 2018-12-07 23:54:43
tags:
 - DRF
 - 注册器组件
 - 响应器组件
 - 分页器组件
categories: 
 - DRF
---

本文介绍的是DRF的注册器组件、响应器组件、分页器组件。

<!--more-->

#### 引入

通过前面几节课的学习，我们已经大致的将DRF的主要组件都学习过了，包括使用方式和源码剖析，对于源码剖析，我们不仅仅需要知道DRF是怎样做的，而且需要知道它为什么这么多，这才是我们看源码的目的，要学习优秀的人的思路和解决问题的方式。学习他人优秀的编码风格，能够纠正我们写程序时的坏习惯，也能开阔我们的思路和眼界。

今天我们将要学习DRF的其他几个组件，这几个组件相对前面的组件来讲，比较简单，我们只学会其使用方式即可，不再深入研究其源码了。

#### 今日概要

- url注册器
- 响应器
- 分页器

#### 知识点复习回顾

- 无

#### 今日详细

下面我们正式开始今天的学习。

##### url注册器

通过DRF的视图组件，数据接口逻辑被我们优化到最剩下一个类，接下来，我们使用DRF的url控制器来帮助我们自动生成url，使用步骤如下：

第一步：导入模块

```python
from rest_framework import routers
```

第二步：实例化一个router对象

```python
router = DefaultRouter()
```

第三步：将需要自动生成url的接口注册到router中

```python
router.register(r'books', views.BookView)
```

第四步：生成url

```python
urlpatterns = [
    re_path(r'^', include('router.urls')),
]
```

##### 响应器

之前我们使用DRF的Response类来将数据响应给客户端，不管是POSTMAN还是浏览器，都能浏览到经过格式化后的漂亮的数据，DRF是怎么做的呢？其实就是通过响应器组件

如果我们不需要使用DRF提供给浏览器的格式化后的数据，只需要禁止该响应方式即可：

```python
from rest_framework.renderers import JSONRenderer


class BookView(ModelViewSet):
    renderer_classes = [JSONRenderer]
    throttle_classes = [RateThrottle]
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

这样，浏览器再次访问，接收到的就是普通的json格式数据，而不是经过DRF格式化后的数据，renderer_classes的查找逻辑与之前的解析器等等组件是完全一样的。

##### 分页器

为了服务器性能考虑，也为了用户体验，我们不应该一次将所有的数据从数据库中查询出来，返回给客户端浏览器，如果数据量非常大，这对于服务器来讲，可以说是性能灾难，而对于用户来讲，加载速度将会非常慢。

所以，我们需要控制每次返回给客户端多少数据，这就需要用到分页器，接下来，我们一起来看看DRF的分页器组件。

第一步：导入模块

```python
from rest_framework.pagination import PageNumberPagination
```

第二步：获取数据

```python
books = Book.objects.all()
```

第三步：创建分页器

```python
paginater = PageNumberPagination()
```

第四步：开始分页

```python
paged_books = paginater.paginate_queryset(books, request)
```

第五步：将分页后的数据进行序列化

```python
serialized_data = BookSerializer(paged_books, many=True)
```

第六步：返回数据

```python
return Response(serialized_data.data)
```

###### 几个参数介绍

- page_size：用来控制每页显示多少条数据（全局参数名为PAGE_SIZE）；
- page_query_param：用来提供直接访问某页的数据；
- page_size_query_param：临时调整当前显示多少条数据
- max_page_size：控制page_size_query_param参数能调整的最大条数

###### 自定义分页器

```python
class MyPagination(PageNumberPagination):
    page_size = 2
    page_query_param = 'p'
    page_size_query_param = 'size'
    max_page_size = 5
```



#### 今日总结

- url注册器
- 响应器
- 分页器

#### 作业

作业：请同学们总结这几节课所学的所有知识点，最好能写几篇博客。