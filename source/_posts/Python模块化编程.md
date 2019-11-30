---
title: Python模块化编程
date: 2018-11-21 09:27:35
tags:
 - 模块化编程
categories: Python模块化编程
---

在Python中，我们将处理同一件事情的代码封装到函数中，提高代码的重用，将处理同一件事的多个函数封装到一个py文件中，提高代码重用性的同时，也方便维护和管理。

<!--more-->

#### 什么是模块化编程？

模块化编程是指将处理同一个功能的一个或者多个函数封装到一个py文件中，也指将处理同一类业务逻辑的py文件封装到一个包中，之后，我们可以在任何地方import该模块。

模块化编程使我们更加方便调试代码、提高了代码的可重用性、降低了程序中不同功能之间的耦合性。

#### 为什么需要模块化编程？

模块化编程提供给我们很多的便利性。

本文要介绍的是Python模块化编程中的高级概念，即，将我们的一个py文件（模块）或者一个包直接安装到本地Python解释器环境，使用时，无需再将该模块所在路径添加到PYTHON_PATH中，就像使用os, sys等标准库模块一样，直接import使用。

另外，如果我们编写的是一个服务器程序，可以直接使用自定义命令启动该程序，类似django-admin。

#### 什么时候需要模块化编程？

当我们的程序解决了某一类问题的时候，我们可以考虑将该程序安装到本地环境，供其他所有人使用，或者将我们的程序发布到[Python第三方库中](https://pypi.org/)。

#### 如何使用模块化编程？

好了，介绍完了上面这些概念，接下来，我们开始使用模块化编程，即，将我们写的程序安装到本地Python环境。

###### 从最简单的开始

首先，我们创建一个名为ftp.py的模块，该模块内的代码如下：

```
# -*- coding: utf-8 -*-

def foo():
    print("This is my app for distribution test!")
    
 
def main():
	foo():
	
	
if __name__ == "__main__":
    main()
```

然后，我们创建一个名为setup.py的模块，该模块的内容如下：

```
# -*- coding: utf-8 -*-
from setuptools import setup

setup(
    name='ftp',
    version='1.0',
    author='Pizza Li',
    author_email='libishengno1@foxmail.com',
    description='my ftp',
    py_modules=['ftp'],
)
```

最后，创建一个目录，名为ftp，将以上两个文件(ftp.py, setup.py)放入该目录。

```
# mkdir ftp
# mv ftp.py ./ftp
# mv setup.py ./ftp
```

接下来，使用如下命令将我们的ftp.py安装到本地Python环境中:

```
# cd ftp
# python setup.py install
```

以上过程会出现一些告警提示，但是不影响安装，然后我们可以使用Python的交互式环境进行测试：

```
Python 3.6.3 (default, Oct  4 2017, 06:09:15)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.1.1 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from ftp import ftp

In [2]: ftp.foo
Out[2]: <function ftp.foo()>

In [3]: ftp.foo()
This is my app for distribution test!
```

如上所示，foo函数被成功执行，表明我们的ftp.py被成功安装进本地环境了，我们可以在交互式环境使用，也可以在任何需要它的地方使用该模块。

接下来，解释一下上面的每一个步骤。

- ftp.py是我们要安装到本地或者发布到[Python第三方库中](https://pypi.org/)的功能模块

- setup.py是模块发布和打包工具setuptools规定的文件名，我们在该文件中配置模块的信息，比如模块名，

  版本号，作者等等, 注意单文件打包必须加上py_modules关键字

- ftp目录，必须与要发布的功能模块同名，如ftp

###### 发布package

以上发布的是单文件程序，如何发布多个模块组成的package呢？

接下来，我们将上述程序丰富一下，还是在ftp目录里面，新建一个名为ftp的package，将之前的ftp.py移入该package。此时我们的ftp目录的结构如下：

```
.
├── ftp
│   ├── __init__.py
│   └── ftp.py
└── setup.py

1 directory, 3 files
```

然后，使用同样的命令，将该package安装到本地Python环境。

使用的时候，需要按下面的方式使用，比如`from ftp import xxx`, xxx指的是ftp包里面的具体模块(也可以是package)：

```
from ftp import ftp
```

不能直接`import ftp`。

继续丰富我们的ftp程序，在ftp包中新建三个包，分别名为core(存放程序核心功能), lib(存放公用工具), model(存放配置信息)。另外，与ftp包同一级目录下(即ftp目录)新建一个appftp.yaml，里面是具体的配置文件, 另外还有logger等等，该程序是一个完成的ftp程序，完整代码请见[完整代码](https://github.com/PizzaLi/ftp)。

```
.
├── appftp.yaml
├── ftp
│   ├── __init__.py
│   ├── core
│   │   ├── __init__.py
│   │   ├── client.py
│   │   └── server.py
│   ├── lib
│   │   ├── __init__.py
│   │   └── logger.py
│   └── model
│       ├── __init__.py
│       ├── config.py
│       └── user_info.txt
└── setup.py

5 directories, 13 files
```

我们来看看setup.py文件中新增了哪些内容：

```
# -*- coding: utf-8 -*-
from setuptools import setup, find_packages

setup(
    name='ftp',
    version='1.0',
    packages=find_packages(),
    author='Pizza Li',
    author_email='libishengno1@foxmail.com',
    description='Simple ftp server and client',
    install_requires=['docopt>=0.6.2', 'PyYAML==3.12'],
)
```

如上所示，我们只是新增了`install_requires`这个项目，它表明我们的程序需要依赖哪些外部包，执行`python setup.py install`的时候，我们的发布工具会自动下载符合版本要求的包, 注意对package进行打包必须加上find_packages关键字。

接下来，就可以使用我们的ftp程序了。同样的，我们使用交互式环境，当然，我们也可以在其他py文件中执行同样的操作来使用ftp程序，如下所示：

```
Python 3.6.3 (default, Oct  4 2017, 06:09:15)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.1.1 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from ftp.core import server

In [2]: server = server.FtpServer()

In [3]: server.serve_forever()
2018-11-21 19:35:52,216[ftp.core.server][DEBUG]: Serving on host: 127.0.0.1, port: 10000
2018-11-21 19:35:52,216[ftp.core.server][DEBUG]: Accepting connection from client...
```

###### 在命令行中使用

通过以上方式，我们成功的将程序安装到了本地Python环境，可以在任何需要的地方使用它，但是，有一种需求是，我们想要在命令行中直接通过命令来启动ftp服务器程序，如何实现呢？

很简单，我们只需把服务器和客户端的启动方式放入一个名为scripts的目录中(新建，与ftp包同级)，新建两个文件，并在这两个文件中调用对应的程序即可，如下所示：

ftp-server

```
# -*- coding: utf-8 -*-
from ftp.core.server import FtpServer

if __name__ == '__main__':
    ftpserver = FtpServer()
    ftpserver.serve_forever()

```

ftp-client

```
# -*- coding: utf-8 -*-

"""
Usage:
  ftp-cli.py <host> <port>
  ftp-cli.py -h | --help

Options:
  -h --help     Show this screen.
"""

from docopt import docopt
from ftp.core.client import FtpClient


def main():
    # Get the arguments
    args = docopt(__doc__)
    host = args.get('<host>')
    port = int(args.get('<port>'))
    server_address = host, port
    # Create a ftpclient instance.
    ftpclient = FtpClient(server_address)


if __name__ == '__main__':
    main()
```

然后修改setup.py文件，新增项目scripts，如下所示：

```
# -*- coding: utf-8 -*-
from setuptools import setup, find_packages

setup(
    name='ftp',
    version='1.0',
    packages=find_packages(),
    author='Pizza Li',
    author_email='libishengno1@foxmail.com',
    description='Simple ftp server and client',
    scripts=['scripts/ftp-server', 'scripts/ftp-client'],
    install_requires=['docopt>=0.6.2', 'PyYAML==3.12'],
)
```

再次执行`python setup.py install`，然后就可以直接在命令行中使用了。

启动服务器：

```
# ftp-server
2018-11-21 19:51:02,356[ftp.core.server][DEBUG]: Serving on host: 127.0.0.1, port: 10000
2018-11-21 19:51:02,356[ftp.core.server][DEBUG]: Accepting connection from client...
```

启动客户端：

```
# ftp-client
Usage:
  ftp-cli.py <host> <port>
  ftp-cli.py -h | --help
  
# ftp-client 127.0.0.1 10000
请输入用户名[q: 退出]: pizza
请输入密码[q: 退出]:
FTP/1.0 201 登录失败
请输入用户名[q: 退出]:
```

如上代码所示，此时，我们就已经将我们的程序成功的安装到本地环境，并且可以直接通过命令在终端运行。