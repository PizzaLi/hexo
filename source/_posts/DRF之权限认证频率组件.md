---
title: DRF之权限认证频率组件
date: 2018-12-07 23:54:43
tags:
 - DRF
 - 权限组件
 - 认证组件
 - 频率组件
categories: 
 - DRF
---

在编程的世界中，我们认为，用户输入的所有数据都是不可靠的，不合法的，直接使用用户输入的数据是不安全的，不仅如此，我们还需要控制用户的访问行为，接下来，我们要学习认证组件、权限组件、频率组件。

<!--more-->

#### 引入

同学们，通过前面三节课的学习，我们已经详细了解了DRF提供的几个重要的工具，DRF充分利用了面向对象编程的思路，对Django的View类进行了继承，并封装了其as_view方法和dispatch方法，随后提供了几个非常方便的编程工具，比如解析器、序列化。

我们通过解析器，可以对来自客户端的application/json数据进行解析，另外，通过序列化工具，我们能够快速构建一套符合REST规范的api，随后又通过DRF的mixin、view及viewset对这些接口逻辑进行优化。

有了他们，程序员开发Web应用的效率大大提高了，虽然我们也尝试自己动手实现了这些功能，但是既然有了优秀的工具，我们就不必费尽心思重复发明轮子。DRF并不仅仅提供了这几个工具，今天我们就来继续深入学习DRF提供的一些其他的工具。

跟以往一样，我们不仅仅要学会这些工具的使用方式，并且要深入研究他们的源码，希望大家在研究源码的过程中，能够对面向对象编程的思路有更加深刻的认识。

当然，按照惯例，为了方便学习新知识以及阅读源码，我们先来复习回顾一下之前已经学习过的知识。

#### 今日概要

- retrieve方法源码剖析
- 认证组件的使用方式及源码剖析
- 权限组件的使用方式及源码剖析
- 频率组件的使用方式及源码剖析

#### 知识点复习回顾

- 昨日回顾
- Python逻辑运算

##### 知识点复习回顾一：Python逻辑运算

有了前两天的基础，今天看源码我们就没有那么大的压力了，所要复习的知识也仅仅只有一个，那就是Python的逻辑运算，当然，稍后还会有几个简单的知识点，就不单独拿出来复习了。

什么是逻辑运算呢？就是and、or、not。not为取反，比较简单，而and和or表示通过运算，计算表达式的布尔值，判断最终结果为真即止

- and：x and y 表示布尔与，意为，判断and运算之后的最终结果，为真即止，and运算必须表达式两端所有值均为真才能确定最终结果，必须所有值都为真
- or：x and y 笔试布尔或，意为，判断or运算之后的最终结果，为真即止，or运算遇到真即返回，即有一个真值即可。
- not x：取反

看下面的代码吧：

```python
x = 10 and 20 # x = 20
x = 0 and 20 # x = 0
x = 10 or 20 # x = 10
x = 0 or 20 # x = 20
```

好了，知识点复习就这么多。

#### 今日详细

##### mixin之retrieve源码剖析

上节课，我们分析过mixin中create方法的源码，今天，create方法比较简单，今天，我们来分析分析retrieve方法的源码，它比create方法稍微复杂一点点，复杂的地方在于如何获取需要操作的那条数据，因为我们知道，我们传递给不同的视图类的所有方法都是一样的，唯一变化的两个是queryset和serializer_classes。

好了，废话不多说，下面来分析一下：

- Django程序启动，开始初始化，获取配置信息，获取视图类并加载到内存中，获取url及视图类的对应关系
- 开始绑定视图类和url的对应关系，执行as_view()方法
- as_view()方法被执行的时候传递了参数，为字典形式：{ "get": "retrieve", "delete": "destroy", "put": "update" }
- 上一步中执行as_view()方法传递参数的目的是为了完成优化，将delete请求方式重新命名为不同的函数
- ViewSetMixin类重写了as_view()方法，也就是在这个地方将几个函数重新绑定，它并没有重写dispatch方法
- 该方法返回视图函数view，注意在这个函数中有一个行 self = cls(**initkwargs), cls是视图类，执行视图函数时self就指向视图函数的实例对象
- 等待客户端请求
- 请求到来，开始执行视图函数，注意，调用视图函数时的方式是view(request)，而如果url带有参数，调用方式为view(request, xxx=id)的形式
- 显然，我们有命名参数(?P<pk>\d+)，所以此时的调用方式为view(request, pk=id)
- 视图函数中有一行self.kwargs = kwargs，所以pk已经被视图函数找到了
- 视图函数返回self.dispatch()，开始执行dispatch方法，注意self是视图类的实例化对象（每个请求都被封装为一个对象）
- dispatch开始执行get方法，注意此时的get方法会执行retrieve，以为已经被重定向了
- 开始执行retrieve，有一行instance = self.get_object(), 该方法在GenericAPIView中
- 至关重要的是拿到self.kwargs中的pk关键字，然后从queryset中拿到想要的数据
- 返回结果

从以上过程中我们可以看出，最关键的一步就是对kwargs的封装，这就是玄机所在，看到这里，你对面向对象有了什么新的领悟吗？对于反射呢，有了跟多的思考和理解吗？

如果没有，不用着急，任何质的飞跃都需要量的积累，等我们写的多了，看得多了，自然就会突破瓶颈。

好了，同志们，这些内容算是对于视图组件的进一步挖掘和吸收，至此，视图组件我们就差不多讲完了。接下来，我们要学习其他工具了。

##### 认证组件

很久很久以前，Web站点只是作为浏览服务器资源（数据）和其他资源的工具，甚少有什么用户交互之类的烦人的事情需要处理，所以，Web站点的开发这根本不关心什么人在什么时候访问了什么资源，不需要记录任何数据，有客户端请求，我即返回数据，简单方便，每一个http请求都是新的，响应之后立即断开连接。

而如今，互联网的世界发生了翻天覆地的变化，用户不仅仅需要跟其他用户沟通交流，还需要跟服务器交互，不管是论坛类、商城类、社交类、门户类还是其他各类Web站点，大家都非常重视用户交互，只有跟用户交互了，才能进一步留住用户，只有留住了用户，才能知道用户需求，知道了用户需求，才会产生商机，有了用户，就等于有了流量，才能够骗到...额...抱歉...是融到钱，有了资金企业才能继续发展，可见，用户交互是非常重要的，甚至可以说是至关重要的一个基础功能。

而谈到用户交互，则必须要谈到我们今天所要学习的知识点，认证、权限和频率。首先我们来看看认证。

###### 登录成功后生成token

之前我们学习过使用cookie和session两种方式可以保存用户信息，这两种方式不同的是cookie保存在客户端浏览器中，而session保存在服务器中，他们各有优缺点，配合起来使用，可将重要的敏感的信息存储在session中，而在cookie中可以存储不太敏感的数据。

今天我们要讲到的是使用token的方式，token称之为令牌。cookie、session和token都有其应用场景，没有谁好谁坏，不过我们开发数据接口类的Web应用，目前用token还是比较多的。

token认证的大致步骤是这样的：

- 用户登录，服务器端获取用户名密码，查询用户表，如果存在该用户且第一次登录（或者token过期），生成token，否则返回错误信息
- 如果不是第一次登录，且token未过期，更新token值

接下来，我们创建两个model，如下所示（token也可以存储在user表中，不过建议存储在user表中）：

```python
from django.db import models

# Create your models here.


class User(models.Model):
    username = models.CharField(max_length=32)
    password = models.CharField(max_length=32)
    user_type_entry = (
        (1, 'Delux'),
        (2, 'SVIP'),
        (3, "VVIP")
    )
    user_type = models.IntegerField(choices=user_type_entry)
    address = models.CharField(max_length=32)

    def __str__(self):
        return self.username


class UserToken(models.Model):
    user = models.OneToOneField("User", on_delete=models.CASCADE)
    token = models.CharField(max_length=128)
```

我们无需实现get方法，因为涉及登录认证，所有写post方法接口，登录都是post请求，视图类如下所示：

```python
from django.http import JsonResponse

from rest_framework.views import APIView

from .models import User, Book, UserToken
from .utils import get_token


class UserView(APIView):

    def post(self, request):
        response = dict()
        try:
            username = request.data['username']
            password = request.data['password']

            user_instance = User.objects.filter(
                user_name=username,
                password=password
            ).first()

            if user_instance:
                access_token = get_token.generater_token()

                UserToken.objects.update_or_create(user=user_instance, defaults={
                    "token": access_token
                })
                response["status_code"] = 200
                response["status_message"] = "登录成功"
                response["access_token"] = access_token
                response["user_role"] = user_instance.get_user_type_display()
            else:
                response["status_code"] = 201
                response["status_message"] = "登录失败，用户名或密码错误"
        except Exception as e:
            response["status_code"] = 202
            response["status_message"] = str(e)

        return JsonResponse(response)
```

简单写了个获取随机字符串的方法用来生成token值：

```python
# -*- coding: utf-8 -*-
import uuid


def generater_token():
    random_str = ''.join(str(uuid.uuid4()).split('-'))
    return random_str
```

以上就是token的简单生成方式，当然，在生产环境中不会如此简单，关于token也有相关的库，好了，我们构造几条数据之后，可以通过POSTMAN工具来创建几个用户的token信息。

接下来，如何对已经登录成功的用户实现访问授权呢？也就是说，只有登录过的用户（有token值）才能访问特定的数据，该DRF的认证组件出场了。

##### DRF认证组件使用

首先，我们来看一看，DRF认证组件的使用方式，首先，我们必须新建一个认证类，之后的认证逻辑就包含在这个类里面：

```python
class UserAuth(object):

    def authenticate_header(self, request):
        pass

    def authenticate(self, request):
        user_post_token = request.query_params.get('token')

        token_object = UserToken.objects.filter(token=user_post_token).first()
        if token_object:
            return token_object.user.username, token_object.token
        else:
            raise APIException("认证失败")
```

实现方式看上去非常简单，到token表里面查看token是否存在，然后根据这个信息，返回对应信息即可，然后，在需要认证通过才能访问的数据接口里面注册认证类即可：

```python
class BookView(ModelViewSet):

    authentication_classes = [UserAuth, UserAuth2]

    queryset = Book.objects.all()
    serializer_class =  BookSerializer
```

至于为什么这么写，接下来，我们一起分析源码，大家就都非常清楚了。

###### DRF认证源码剖析

前面的步骤都差不多，我们来看有差别的地方，我们说，request对象是APIView重写的，这个是在dispatch方法里面实现的，继续往后看dispatch方法，我们会看到self.initial方法，就是在这个方法里面，我们会看到认证、权限、频率几个组件的实现：

- 执行self.initial()方法
- 执行self.perform_authentication(request)，方法，注意，新的request对象被传递进去了
- 该方法只有一行request.user，根据之前的经验，解析器(request.data)，我们知道这个user肯定也是request对的一个属性方法
- 所料不错，该方法继续执行self._authenticate()，注意此时的self是request对象
- 该方法会循环self.authenticators，而这个变量是在重新实例化request对象时通过参数传递的
- 传递该参数是通过get_authenticatos()的返回值来确定的，它的返回值是
  - [ auth for auth in self.authentication_classes ]
  - 也就是我们的BookView里面定义的那个类变量，也就是认证类
- 一切都明朗了，循环取到认证类，实例化，并且执行它的authenticate方法
  - 这就是为什么认证类里面需要有该方法
  - 如果没有该方法，认证的逻辑就没办法执行
  - 至于类里面的header方法，照着写就行，有兴趣的可以研究源码，这里就不细究了
- 该方法如果执行成功就返回一个元组，执行完毕
  - 如果失败，它会捕捉一个APIException
  - 如果我们不希望认证通过，可以raise一个APIException

这就是认证组件的实现方式，非常简单。

###### 多个认证类的实现

并且，我们还可以指定多个认证类，只是需要注意的是，如果需要返回什么数据，请在最后一个认证类中返回，因为如果在前面返回，在self._authentication()方法中会对返回值进行判断，如果不为空，认证的过程就会中止，多个认证类的实现方式如下：

 ```python
class UserAuth2(object):

    def authenticate(self, request):
        raise APIException("认证失败")


class UserAuth(object):

    def authenticate_header(self, request):
        pass

    def authenticate(self, request):
        user_post_token = request.query_params.get('token')

        token_object = UserToken.objects.filter(token=user_post_token).first()
        if token_object:
            return token_object.user.username, token_object.token
        else:
            raise APIException("认证失败")


class BookView(ModelViewSet):

    authentication_classes = [UserAuth, UserAuth2]
 ```

如果不希望每次都写那个无用的authenticate_header方法，我们可以这样：

```python
from rest_framework.authentication import BaseAuthentication

class UserAuth2(BaseAuthentication):

    def authenticate(self, request):
        raise APIException("认证失败")


class UserAuth(BaseAuthentication):

    def authenticate(self, request):
        user_post_token = request.query_params.get('token')

        token_object = UserToken.objects.filter(token=user_post_token).first()
        if token_object:
            return token_object.user.user_name, token_object.token
        else:
            raise APIException("认证失败")
```

继承BaseAuthentication类即可。

###### 全局认证

如果希望所有的数据接口都需要认证怎么办？很简单，还是根据之前的经验，就是这句代码：

```python
authentication_classes=api_settings.DEFAULT_AUTHENTICATION_CLASSES
```

如果认证类自己没有authentication_classes，就会到settings中去找，通过这个机制，我们可以将认证类写入到settings文件中即可实现全局认证：

```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'authenticator.utils.authentication.UserAuth',
        'authenticator.utils.authentication.UserAuth2',
    ),
}
```

好了，认证到这里就差不多了。接下来继续介绍权限组件

##### 权限组件

与认证组件几乎差不多，我们直接看使用方式吧

###### 权限组件使用

定义权限类：

```python
class UserPerms():
    message = "您没有权限访问该数据"
    def has_permission(self, request, view):
        if request.user.user_type > 2:
            return True
        else:
            return False
```

同样的逻辑，同样的方式，只是执行权限的方法名与执行认证的方法名不一样而已，名为has_permission，并且需要将当前的视图类传递给该方法。

视图类中加入permission_classes变量：

```python
class BookView(ModelViewSet):

    authentication_classes = [UserAuth]
    permission_classes = [UserPerms2]

    queryset = Book.objects.all()
    serializer_class =  BookSerializer
```

###### 权限组件源码剖析

权限组件的源码与认证组件是一样的。

#### 频率组件

##### 使用自定义方式实现对ip地址进行访问频率控制

使用方式介绍，上面两个组件也是几乎一样，只是用来做判断的逻辑不一样而已，下面是作业的答案：

throttles.py（该方式没有DRF提供的方式简洁）

```python
import time
import math

from rest_framework import exceptions


class MyException(exceptions.Throttled):
    default_detail = '连接次数过多'
    extra_detail_plural = extra_detail_singular = '请在{wait}秒内访问'

    def __init__(self, wait=None, detail=None, code=None):
        super().__init__(wait=wait, detail=detail, code=code)


class VisitThrottle():
    user_visit_information = dict()
    visited_times = 1
    period = 60
    allow_times_per_minute = 5
    first_time_visit = True

    def allow_request(self, request, view):
        self.request_host = request_host = request.META.get("REMOTE_ADDR")
        current_user_info = self.user_visit_information.get(request_host, None)

        if not self.__class__.first_time_visit:
            self.user_visit_information[request_host][0] += 1
            current_visit_times = self.user_visit_information[request_host][0]

            if current_visit_times > self.allow_times_per_minute:
                if self._current_time - current_user_info[1] <= self.period:
                    if len(current_user_info) > 2:
                        current_user_info[2] = self._time_left
                    else:
                        current_user_info.append(self._time_left)

                    view.throttled = self.throttled
                    return None
                else:
                    self.__class__.first_time_visit = True

        if self.first_time_visit:
            self.__class__.first_time_visit = False
            self._initial_infomation()

        return True

    def wait(self):
        return self.period - self.user_visit_information[self.request_host][2]

    def throttled(self, request, wait):
        raise MyException(wait=wait)

    @property
    def _current_time(self):
        return time.time()

    @property
    def _time_left(self):
        return math.floor(self._current_time - self.user_visit_information.get(self.request_host)[1])

    def _initial_infomation(self):
        self.user_visit_information[self.request_host] = [self.visited_times, self._current_time]
```

视图类中：

```python
class BookView(ModelViewSet):
    throttle_classes = [ VisitThrottle ]
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

###### 使用DRF简单频率控制实现对用户进行访问频率控制

局部访问频率控制

```python
from rest_framework.throttling import SimpleRateThrottle


class RateThrottle(SimpleRateThrottle):
    rate = '5/m'

    def get_cache_key(self, request, view):
        return self.get_ident(request)
```

rate代表访问评率，上面表示每分钟五次，get_cache_key是必须存在的，它的返回值告诉当前频率控制组件要使用什么方式区分访问者（比如ip地址）。

之后在视图中使用即可：

```python
from .utils.throttles import RateThrottle

# Create your views here.


class BookView(ModelViewSet):
    throttle_classes = [ RateThrottle ]
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

###### 全局访问频率控制

首先定义一个频率控制类，并且必须继承SimpleRateThrottle这个类，它是DRF提供的一个方便的频率控制类，请看下面的代码：

```python
class RateThrottle(SimpleRateThrottle):
    scope = "visit_rate"

    def get_cache_key(self, request, view):
        return self.get_ident(request)
```

另外，我们需要在全局配置频率控制参数

```python
REST_FRAMEWORK = {
    "DEFAULT_THROTTLE_CLASSES": ('throttler.utils.throttles.RateThrottle',),
    "DEFAULT_THROTTLE_RATES": {
        "visit_rate": "5/m"
    }
}
```

这样就实现了，每分钟最多五次访问的逻辑。

#### 今日总结

- retrieve方法源码剖析
- 认证组件的使用方式及源码剖析
- 权限组件的使用方式及源码剖析
- 频率组件的使用方式及源码剖析

#### 作业

作业一：请同学们将认证、权限、频率三个组件的源码流程图画出来

作业二：请同学们根据之前看源码的经验，利用DRF的频率组件实现限制ip每分钟不能超过5次访问。