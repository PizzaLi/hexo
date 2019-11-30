---
title: DRF之视图组件
date: 2018-12-07 23:54:43
tags:
 - DRF
 - 视图组件
categories: 
 - DRF
---

不断的优化我们写的程序，是每个程序员必备的技能和职业素养，也是帮助我们成长的非常重要的手段。

<!--more-->

#### 引入

通过上一节课的学习，我们已经通过DRF的序列化组件，设计出了符合rest规范的GET、POST接口逻辑，我们知道操作数据库主要有增删改查几种方式，增加，查询两种接口逻辑我们都实现完毕了，接下来，我们继续实现剩下的两种，删除和修改。

#### 今日概要

- serializer进行put、delete及获取单条数据的接口设计
- 视图组件的使用及源码剖析

#### 知识点复习回顾

- 昨日回顾
- RESTful api接口规范
- 混入类、多继承
- 函数的参数

在开始之前，按照惯例，我们复习前两节课学习过的知识点、以及学习今天的新知识点所需掌握的知识点。

##### 知识点复习回顾一：RESTful api接口规范

第一节课的那张图大家还有印象吗？如果没有印象，请同学们思考一下，我给大家总结的REST的最重要的一句话，那就是：**url用来唯一定位资源，http请求方式用来定位用户行为。**

根据这句话，我们设计了下面的RESTful api：

![view](DRF之视图组件/DRF002.png)

上面就是关于REST规范的回顾，请牢记：**url用来唯一定位资源，http请求方式用来定位用户行为。**

##### 知识点复习回顾二：混入类、多继承

我有一个Animal类，它包含如下方法：

```python
class Animal(object):
    def eat(self):
        print("Eat")
        
    def walk(self):
        print("Walk")
        
    def sleep(self):
        print("Sleep")
        
    def run(self):
        print("Run")

    def flying(self):
        print("Flying")
        
    def wangwang(self):
        print("Wangwang")
        
    def miao(self):
        print()


class Dog(Animal):pass


class Cat(Animal):pass


class Bird(Animal):pass
```

可以看到，Dog类继承了Animal类，但是Dog并没有飞行和喵的功能，所以，如果直接继承Animal会有一些问题，请同学们思考，如何解决这个问题呢？

好了，其实我们有多中方式可以解决这个问题，比如：

```python
class Animal(object):
    def eat(self):
        print("Eat")

    def walk(self):
        print("Walk")

    def sleep(self):
        print("Sleep")

    def run(self):
        print("Run")


class Flying(object):
    def flying(self):
        print("Flying")


class WangWang(object):
    def wangwang(self):
        print("Wangwang")


class Miao(object):
    def miao(self):
        print()


class Dog(Animal, WangWang):pass


class Cat(Animal, Miao):pass


class Bird(Animal, Flying):pass
```

我们将不同的功能封装到了独立的类中，然后采用一种Mixin（混合类）的方式，其实也就是多继承，来解决这个问题，这在Python中是比较常见的解决方式，比如socketserver模块就用到了这种方式，当我们需要线程的时候，可以继承线程类，当我们需要进程的时候，可以继承进程类。

##### 知识点复习回顾三：函数的参数

接下来，我们一起回顾一下函数的参数，假设我有函数如下：

```python
def func(a, b, c=1, *args, **kwargs):
    print(a, b, c, args, kwargs)

func(1, 2, 3, 4, 5)                # 1, 2, 3, (4, 5) 所有未被匹配到的非key=value型的参数都会被*args接收
func(b=1, a=1, c=2, 4, 5)          # 报错，位置参数不能在关键字参数之后
func(4, 5, c=3, {"name": "pizza"}) # 报错，位置参数不能在关键字参数之后，字典不是关键字参数
func({"name": "pizza"}, 1, 2)      # {'name': 'pizza'} 1 2 () {}
func(1, 2, 3, 4, 5, d=1, e=2)      # 1 2 3 (4, 5) {'d': 1, 'e': 2} 未被匹配到的关键字参数被传递给kwargs
```

#### 今日详细

好了，知识点的复习和补充，咱们就到这里，接下来，正式开始今天的内容，首先开始设计剩下三个接口逻辑。

##### 使用serializer进行put接口设计

根据规范，PUT接口用来定义用户对数据修改的逻辑，也就是update，它的url是这样的， 127.0.0.1/books/1/，请求方式是PUT，1代表的是具体的数据，使用户动态传递的，所以我们的url应该是这样的：

```python
re_path(r'books/(\d+)/$', views.BookFilterView.as_view()),
```

此时我们应该重新定义一个视图类，因为url不一样了，所以在views.py中，需新增一个视图类：

```python
from rest_framework.views import APIView
from app_serializer import BookSerializer


class BookFilterView(APIView):

    def put(self, request, nid):
        book_obj = Book.objects.get(pk=nid)

        serialized_data = BookSerializer(data=request.data, instance=book_obj)

        if serialized_data.is_valid():
            serialized_data.save()
        else:
            return Response(serialized_data.errors)
```

请注意，在序列化时，我们除了传入data参数外，还需告诉序列化组件，我们需要更新哪条数据，也就是instance，另外，我们使用的序列化类还是之前那个：

```python
class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book

        fields = ('title',
                  'price',
                  'publish',
                  'authors',
                  'author_list',
                  'publish_name',
                  'publish_city'
                  )
        extra_kwargs = {
            'publish': {'write_only': True},
            'authors': {'write_only': True}
        }

    publish_name = serializers.CharField(max_length=32, read_only=True, source='publish.name')
    publish_city = serializers.CharField(max_length=32, read_only=True, source='publish.city')

    author_list = serializers.SerializerMethodField()

    def get_author_list(self, book_obj):
        # 拿到queryset开始循环 [{}, {}, {}, {}]
        authors = list()

        for author in book_obj.authors.all():
            authors.append(author.name)

        return authors
```

使用POSTMAN工具发送一个PUT请求修改数据： PUT  <http://127.0.0.1:9001/serializers/books/1>。

请注意，此时会报错：RuntimeError: You called this URL via PUT, but the URL doesn't end in a slash and you have APPEND_SLASH set. Django can't redirect to the slash URL while maintaining PUT data. Change your form to point to 127.0.0.1:9001/serializers/books/1/ (note the trailing slash), or set APPEND_SLASH=False in your Django settings.

因为，如果是GET请求，Django的全局APPEND_SLASH参数为True，所以会在url后面加上/（如果没有），但是如果是PUT或者DELETE请求，APPEND_SLASH不会添加 **/** 到url末尾。而我们上面定义的url是明确以 / 结尾的，所以，我们应该在url后面加上反斜线 / ，或者把url修改为不以斜线结尾。

加上之后，再次发送请求修改数据：PUT <http://127.0.0.1:9001/serializers/books/1/>，查看数据库，发现，数据已经被修改了。

这就是PUT接口逻辑的设计，分为如下几个步骤：

- url设计：re_path(r'books/(\d+)/$', views.BookFilterView.as_view())
- 视图类：重新定义一个视图类
- put方法：在视图类中定义一个put方法
- 序列化：在序列化的过程中，需要传入当前修改的数据行，参数名为instance
- 序列化类：不需要修改
- url路径：请求时，发送的url必须与urls.py中定义的url完全匹配

##### 使用serializer进行delete接口设计

接下来，继续设计delete接口，根据规范，delete接口的url为：127.0.0.1/books/1/，请求方式是DELETE，与put是一致的，都是对用户指定的某行数据进行操作，数字1是动态的，所以我们的url不变：

```python
re_path(r'books/(\d+)/$', views.BookFilterView.as_view()),
```

同样的，视图类和序列化类都不需要重新定义，只需要在视图类中定义一个delete方法即可，如下代码所示：

```python
class BookFilterView(APIView):

    def delete(self, request, nid):
        book_obj = Book.objects.get(pk=nid).delete()

        return Response("")

    def put(self, request, nid):
        book_obj = Book.objects.get(pk=nid)

        serialized_data = BookSerializer(data=request.data, instance=book_obj)

        if serialized_data.is_valid():
            serialized_data.save()
            return Response(serialized_data.data)
        else:
            return Response(serialized_data.errors)
```

用POSTMAN来试试DELETE请求：<http://127.0.0.1:9001/serializers/books/53/>，我们将刚刚添加的数据删除，操作成功，同样的，请注意，请求url必须完全匹配urls.py中定义的url。

##### 使用serializer进行单条数据的接口设计

最后一个接口的设计，是对单条数据进行获取，根据规范url为：127.0.0.1/books/1/，请求方式为GET，根据url和前面两个接口的经验，这次仍然使用之前的视图类，因为根据REST规范，url唯一定位资源，127.0.0.1/books/1/和127.0.0.1/books/是不同的资源，所以，我们不能使用之前那个获取全部数据的视图类。这里肯定不能重用之前的那个get方法，必须重新定义一个get方法。

urls.py不变，新增三个接口逻辑后的视图类如下：

```python
class BookFilterView(APIView):
    def get(self, request, nid):
        book_obj = Book.objects.get(pk=nid)

        serialized_data = BookSerializer(book_obj, many=False)

        return Response(serialized_data.data)

    def delete(self, request, nid):
        book_obj = Book.objects.get(pk=nid).delete()

        return Response("")

    def put(self, request, nid):
        book_obj = Book.objects.get(pk=nid)

        serialized_data = BookSerializer(data=request.data, instance=book_obj)

        if serialized_data.is_valid():
            serialized_data.save()
            return Response(serialized_data.data)
        else:
            return Response(serialized_data.errors)
```

many=False, 当然，也可以不传这个参数，因为默认是False。通过POSTMAN发送请求，成功。三个接口定义完成了，加上上一节课的get和post，两个视图类的接口逻辑如下：

```python
class BookView(APIView):
    def get(self, request):
        origin_books = Book.objects.all()
        serialized_books = BookSerializer(origin_books, many=True)

        return Response(serialized_books.data)

    def post(self, request):
        verified_data = BookSerializer(data=request.data)

        if verified_data.is_valid():
            book = verified_data.save()
            return Response(verified_data.data)
        else:
            return Response(verified_data.errors)


class BookFilterView(APIView):
    def get(self, request, nid):
        book_obj = Book.objects.get(pk=nid)

        serialized_data = BookSerializer(book_obj, many=False)

        return Response(serialized_data.data)

    def delete(self, request, nid):
        book_obj = Book.objects.get(pk=nid).delete()

        return Response("")

    def put(self, request, nid):
        book_obj = Book.objects.get(pk=nid)

        serialized_data = BookSerializer(data=request.data, instance=book_obj)

        if serialized_data.is_valid():
            serialized_data.save()
            return Response(serialized_data.data)
        else:
            return Response(serialized_data.errors)
```

到此为止，我们已经通过序列化组件设计出了符合REST规范的五个常用接口，已经足够优秀了，但是还不够完美，现在假设，我们有多个数据接口，比如（Book，Author，Publish.....）等数据表都需要定义类似的接口，可以预见，我们需要重复定义类似上面的五个接口，这种方式将会导致大量的重复代码出现，显然，我们的程序还有很多可以优化的地方。

请同学们思考，如果是你，你将会如何进行优化呢？

好了，同学们，结合刚刚上课是给大家回顾的混合类和多继承，我们是否可以使用下面的方式呢？

```python
class GetAllData():
    def get(self, request):pass


class GetOneData():
    def get(self, request, nid):pass
    
    
class DeleteOneData():
    def delete(self, request, nid):pass
    
    
class UpdateOneData():
    def put(self, request, nid):pass
    
    
class CreateData():
    def post(self, request):pass
    

class BookView(APIView, GetAllData, CreateData):pass
    

class BookFilterView(APIView, GetOneData, DeleteOneData, UpdateOneData):pass
```

将每个接口都写到独立的类中，然后使用多继承，或者成为mixin的这种方式，就可以对我们的程序进行优化，mixin的方式非常常见，在学网络编程的时候，如果你看过socketserver源码，就会发现，socketserver中就有对mixin的实现，即，假设我们需要进程的时候，我们继承进程类，如果我们需要线程的时候，我们继承线程类即可。

接下来，我们一起来看看DRF是如何做的，其他，它的解决方式与我们上面的方式的思路是一样的。

#### 使用mixin优化接口逻辑

##### mixin的使用方式介绍

urls.py有些区别：

```python
from django.urls import re_path

from mixiner import views

urlpatterns = [
    re_path(r'books/$', views.BookView.as_view()),
    re_path(r'books/(?P<pk>\d+)/$', views.BookFilterView.as_view()),
]
```

views.py

```python
# -*- coding: utf-8 -*-
from rest_framework.mixins import (
    ListModelMixin,
    CreateModelMixin,
    UpdateModelMixin,
    DestroyModelMixin,
    RetrieveModelMixin
)
from rest_framework.generics import GenericAPIView

# 当前app中的模块
from .models import Book
from mixin_serializer import BookSerializer

# Create your views here.


class BookView(ListModelMixin, CreateModelMixin, GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)


class BookFilterView(DestroyModelMixin,
                     UpdateModelMixin,
                     RetrieveModelMixin,
                     GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)
```

DRF将这五个接口定义在不同的ModelMixin中，使用步骤如下：

- 导入ModelMixin
- 视图类继承所需的ModelMix
- 不再继承APIView，需要继承generics.GenericAPIView
- 必须包含两个类变量：queryset，serializer_class
- 接口中不需要定义任何逻辑操作，一切交给mixin
- 每个接口的返回值不同，对应关系：{"get": "list", "delete": "destroy", "put": "update", "get": "retrieve", "post": "create"}

其中有两个get方法，但是分属于不同的视图类，请注意在url中的不同点，因为我们统一给的都是QuerySet，所以，需要通过传入一个名为pk的命名参数，告诉视图组件，用户需要操作的具体数据。

##### mixin源码剖析

到底它是如何做的呢？我们来简单剖析一下源码：

- Django程序启动，开始初始化，读取urls.py, 读取settings, 读取视图类
- 执行as_views(), BookView没有，需要到父类中找
- 几个ModelMixin也没有，GenericAPIView中没有，继续到GenericAPIView（APIView）中找
- 找到了，并且与之前的逻辑是一样的，同时我们发现GenericAPIView中定义了查找queryset和serializer_class类的方法
- as_view()方法返回重新封装的视图函数，开始建立url和视图函数之间的映射关系
- 等待用户请求
- 接收到用户请求，根据url找到视图函数
- 执行视图函数的dispatch方法（因为视图函数的返回值是：return self.dispatch()
- dispatch分发请求，查找到视图类的五个方法中的某个
- 开始执行，比如post请求，返回：self.create()，视图类本身没有，则会到父类中查找
- 最后在CreateModelMixin中查找
- 执行create()方法，获取queryset和serializer_class
- 返回数据

在对单条数据进行操作的几个方法里面，比如retrieve，会执行get_object()方法，该方法会根据lookup_url_kwarg = self.lookup_url_kwarg or self.lookup_field来查找操作对象，我们可以通过修改self.lookup_url_kwarg变量名来自定义参数。

好了，以上就是mixin的源码剖析。

#### 使用view优化接口逻辑

##### view的使用方式介绍

看似已经优化的非常完美了，但是，在一个对性能要求极高的项目里面，我们的程序还可以继续优化，还需要继续优化，不断的优化我们写的程序，是每个程序员必备的技能，也是帮助我们成长的非常重要的手段。同样的思路，同样的方式，我们可以将多个接口封装到一个功能类中，请看下面的代码：

```python
# -*- coding: utf-8 -*-
from rest_framework import generics

# 当前app中的模块
from .models import Book
from .serializer_classes import BookSerializer

# Create your views here.


class BookView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer


class BookFilterView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

是不是非常简单，你想到了吗？

#### 使用viewset优化接口逻辑

这样就结束了吗？哈哈哈，还是那句话，看似已经优化的非常完美了，但是，在一个对性能要求极高的项目里面，我们的程序还可以继续优化，还需要继续优化，不断的优化我们写的程序，是每个程序员必备的技能，也是帮助我们成长的非常重要的手段。

##### viewset的使用方式介绍

urls.py有变化哦：

```python
from django.urls import re_path

from viewsetter import views

urlpatterns = [
    re_path(r'books/$', views.BookView.as_view({
        'get': 'list',
        'post': 'create'
    })),
    re_path(r'books/(?P<pk>\d+)/$', views.BookView.as_view({
        'get': 'retrieve',
        'put': 'update',
        'delete': 'destroy'
    })),
]
```

我们给as_view()方法传递了参数，这就是最神奇的地方。 

```python
# -*- coding: utf-8 -*-
# django rest framework组件
from rest_framework.viewsets import ModelViewSet

# 当前app中的模块
from .models import Book
from .serializer_classes import BookSerializer

# Create your views here.


class BookView(ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

使用方式非常简单，接下来，我们直接看源码。

##### viewset源码剖析

- Django程序启动，开始初始化，读取urls.py, 读取settings, 读取视图类
- 执行as_views(), BookView没有，需要到父类（ModelViewSet）中找
- ModelViewSet继承了mixins的几个ModelMixin和GenericViewSet，显然ModelMixin也没有，只有GenericViewSet中有
- GenericViewSet没有任何代码，只继承了ViewSetMixin和generics.GenericAPIView(这个我们已经认识了)
- 继续去ViewSetMixin中查找，找到了as_view类方法，在重新封装view函数的过程中，有一个self.action_map = actions
- 这个actions就是我们给as_view()传递的参数
- 绑定url和视图函数(actions)之间的映射关系
- 等待用户请求
- 接收到用户请求，根据url找到视图函数
- 执行视图函数的dispatch方法（因为视图函数的返回值是：return self.dispatch()
- dispatch分发请求，查找到视图类的五个方法中的某个
- 开始执行，比如post请求，返回：self.create()，视图类本身没有，则会到父类中查找
- 最后在CreateModelMixin中查找
- 执行create()方法，获取queryset和serializer_class
- 返回数据

这就是viewset的优化方案，整个优化方案最重要的地方就是urls.py中我们传入的参数，然后对参数进行映射关系绑定。

今天的全部内容到此就结束了。

#### 今日总结

- serializer进行put、delete及获取单条数据的接口设计
- 视图组件的使用及源码剖析

#### 作业

请设计出其余三个接口并将源码流程用图形详细画出来。