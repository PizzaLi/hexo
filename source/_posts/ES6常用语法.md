---
title: ES6常用语法
date: 2018-10-23 23:33:51
tags: 
  - ES6
  - Vue
categories: ES6
---

ES6新增了关于变量的定义，函数扩展，数据结构，类，模块等概念，本文详细介绍常用的ES6语法。

<!--more-->

#### ES6之变量的定义

##### let定义变量

###### 块级作用域

ES6中新增了let语法，用来声明变量，用法类似var，但是使用let定义的变量，只在当前代码块内有效。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        {
            let a = 10;
            var b = 20;
        }

        console.log(a);  // 报错
        console.log(b);  // 打印20

    </script>
</body>
</html>
```

ES5只有全局作用域和函数作用域, 没有块级作用域, 如下代码所示。所以下面代码中的var变量定义会被提示到函数作用域顶端，所以如下代码：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // ES5 只有全局作用域和函数作用域, 没有块级作用域
        var username = "alex";

        function foo() {
            // 因为没有块级作用域, 只有全局作用域和函数作用域
            // 所以var变量定义会被提升到函数作用域顶端
            // 所以如下代码打印undefined
            console.log(username);
            if (true) {
                var username = "pizza";
            }
        }

        foo()
    </script>
</body>
</html>
```

在以上代码中的var修改为let，即可避免全局变量username被if代码块中的username替代。还有一种情况，使用var定义变量时，用来计数的循环变量会泄露为全局变量，如下代码所示：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // ES5 只有全局作用域和函数作用域, 没有块级作用域

        for (var i = 0; i < 10; i++) {
            console.log(i);
        }

        // 此时i在全局作用域生效，值为10
        console.log(i);
    </script>
</body>
</html>
```

同样的，我们可以使用let定义变量，使得循环变量只在当前代码块生效，如下代码所示：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // ES5 只有全局作用域和函数作用域, 没有块级作用域

        for (let i = 0; i < 10; i++) {
            console.log(i);
        }

        // 此时i在块级作用域生效，Uncaught ReferenceError: i is not defined
        console.log(i);
    </script>
</body>
</html>
```

###### 变量提升

在ES5或者更早的版本，我们使用var定义变量，使用该语法定义变量时，JavaScript引擎默认会将函数及变量的定义提升到代码块顶端，也就是说，除了在函数中定义的变量会被提升到函数作用域顶端外，在其他地方定义的变量，都会被定义在全局。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // 变量提升

        console.log(greeting);  // 打印undefined, 因为变量提升
        var greeting = "hello vue!";

        // 函数的提升
        function foo() {
            console.log(greeting);  // 打印hello vue!, 因为本地没有greeting变量
            greeting = "hello foo";
        }

        console.log(greeting);  // 打印hello vue!, 因为函数提升在变量提升之前
        foo()

    </script>
</body>
</html>
```

如上所述，var定义变量会发生“变量提升”现象，即变量可以在声明之前使用，值为“undefined”，这不符合大多数编程语言的规范，也会多多少少带来一些问题。为了解决这个问题，ES6中引入了let命令，使用let定义的变量，必须在声明之后才能使用，否则报错。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // 使用var定义变量，会带来变量提升的现象
        console.log(greeting);
        var greeting = "hello vue!";
        
        // 使用let定义的变量，必须先声明在引用
        console.log(greeting);
        let greeting = "hello vue!"; // Uncaught ReferenceError: greeting is not defined

    </script>
</body>
</html>
```

###### 不能重复定义

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // 使用var定义变量, 因为变量提升现象
        // 所以可以重复定义同一个变量
        var a = 10;
        var a = 10;
        console.log(a);

        // 使用let定义变量, 不能在相同作用域内重复定义同一个变量
        // 因为上面已经使用var定义了a变量, 所以下面的let定义会报错
        let a = 10;
        console.log(a); // Uncaught SyntaxError: Identifier 'a' has already been declared

    </script>
</body>
</html>
```

##### const定义变量

###### 定义后不能修改

const关键字声明一个只读常量，常量一旦被声明，就不能被更改。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        const PI = 3.1415;
        // Uncaught TypeError: Assignment to constant variable.
        PI = 3.14;
    </script>
</body>
</html>
```

###### 声明时赋值

const声明的变量，必须在声明时赋值，不能声明之后赋值，否则会报错。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // Uncaught SyntaxError: Missing initializer in const declaration
        const PI;
    </script>
</body>
</html>
```

###### 不能重复定义

与let命令一样，使用const定义的常量，不能重复定义，不存在变量提升，并只在当前作用域有效。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // 不能重复声明
        // Uncaught SyntaxError: Identifier 'PI' has already been declared
        const PI = 3.1415;
        const PI = 3.14;

        // 不存在变量提升
        // Uncaught ReferenceError: PI is not defined
        console.log(PI);
        const PI = 3.1415;

        // 只在定义域有效
        {
            var a = 10;
            const b = 10;
        }
        
        // 10
        // Uncaught ReferenceError: PI is not defined
        console.log(a);
        console.log(b);
    </script>
</body>
</html>
```

##### const的可变与不可变

使用const定义的常量，不可改变，这里的不可改变，指的是该数据类型本身不可改变，比如字符串，数字，假设使用const定义一个对象或者数组，因为对象和数组这两种数据类型本身是可以被新增或者删除元素的，所以，此时const定义的常量可以被改变。

const实际上保证的，并不是变量的值不可改变，而是变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。但对于复合类型的数据（比如对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。因此，将一个对象声明为常量必须非常小心。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // 使用const定一个常量，该常量的类型为object
        const obj = {
            name: "pizza",
            age: 18
        };

        obj.age = 19;
        // 此时obj中的age被修改为19
        // 但是obj所指向的内存地址并没有被改变，也不能被改变
        console.log(obj);

        // Uncaught TypeError: Assignment to constant variable.
        obj = "hello es6";
    </script>
</body>
</html>
```

#### ES6之模板字符串

##### 反引号和${}的使用

在ES5中，字符串拼接我们使用“+”号，ES6中新增了一种方式，叫做模板字符串，下面我们来使用该功能进行字符串的拼接

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <div id="odives5"></div>
    <div id="odives6"></div>
    <script>

        let username = "pizza";
        let age = 18;
        let hobby = "girls";

        // ES5
        console.log(username + " is " + age + " years old and he like " + hobby);

        // ES6
        console.log(`${username} is ${age} years old and he like ${hobby}`);


        let odivES5 = document.getElementById("odives5");
        let odivES6 = document.getElementById("odives6");
        // ES5
        let userinfoES5 = "" +
            "<li>" + username + "</li>" +
            "<li>" + age + "</li>" +
            "<li>" + hobby + "</li>" +
                "";

        // ES6
        let userinfoES6 = `
           <ul>
             <li>${username}</li>
             <li>${age}</li>
             <li>${hobby}</li>
           </ul>
        `;
        odivES5.innerHTML = userinfoES5;
        odivES6.innerHTML = userinfoES6;

    </script>
</body>
</html>
```

#### ES6之数据解构

##### 迭代访问数组和对象

在ES5中，我们访问数组或者对象的数据只能一个个访问，但是在ES6中我们可以使用数据的解构。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script type="text/javascript">
        let obj = {
            name: "pizza",
            age: 18
        };

        let userinfo = ["pizza", "18", "girls"];
        
        // ES5中访问对象
        let name = obj.name;
        let age = obj.age;
        console.log(name, age);
        
        // ES5中访问数组
        let name = userinfo[0];
        let age = userinfo[1];
        let hobby = userinfo[2];
        console.log(name);
        console.log(age);
        console.log(hobby);
        
        
        // ES6中访问对象
        let {name, age} = obj;
        console.log(name, age);
        
        // ES6中访问数组
        let [name, age, hobby] = userinfo;
        console.log(name, age, hobby);
        
    </script>
</body>
</html>
```

#### ES6之函数的扩展

##### 默认值参数

在ES5中，由于函数不支持设置默认值，所以当遇到需要设置默认值的情况的时候，只能采用变通的方式，不过这种变通的方式会存在一些问题。ES6中引入了函数的默认值参数，解决了这些问题。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // ES5中不能使用函数参数默认值，只能采用变通的方法
        // 这种方式会出现一些问题
        function f(num) {
            num = num || 2;
            console.log(num);
        }

        f(1);
        f();
        f(0);

        // ES6中引入函数参数默认值，完美的解决了ES5中存在的问题
        function foo(num=2) {
            console.log(num);
        }

        foo(1);
        foo();
        foo(0);
    </script>

</body>
</html>
```

##### 箭头函数

ES6中引入了箭头函数，其实就相当于Python当中的匿名函数lambda，接下来我们详细看看箭头函数的使用方式以及使用箭头函数时需要注意的地方。

###### 箭头函数的定义

```
// 箭头函数的定义
var f = v => v;

// 等同于
var f = function (v) {
    return v;
}
```

一个或者多个参数的函数定义方式如下：

```
// 零个参数或者多个参数的箭头函数定义方式如下
   var f = (a, b) => a + b;
```

如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用`return`语句返回：

```
// 如果箭头函数的代码块部分多于一条语句，就要使用大括号将它们括起来，并且使用return语句返回
    var f = (a, b) => { return a + b; };

// 所以，箭头函数的定义也可以是如下形式
    var f = () => {};
```

###### 箭头函数的用途

那么，箭头函数有什么用呢？其一，箭头函数可以简化代码，是代码实现更加简洁，比如如下需求：

```
// 判断一个数是否是偶数
// 普通方法
function isEven(num) {
   if (num%2 === 0) {
       return true
   } else {
       return false
   }
}
        
// 箭头函数写法
var isEven = n => n%2 === 0;
        
console.log(isEven(10));
console.log(isEven(9));
```

另外，箭头函数还可以简化回调函数的写法，比如，map和sort中的回调函数

```
// 箭头函数可以简化回调函数
let ary = [1, 2, 3];

// 普通函数写法
let ary1 = ary.map(function (x) {
    return x * x
});

// 箭头函数写法
let ary2 = ary.map(x => x * x);

console.log(ary1);
console.log(ary2);

// 将序排列数组
values = [10, 8, 9, 1, 2, 5, 7, 4, 6, 3];

// 普通函数写法
values.sort(function (a, b) {
    return b - a
});

// 箭头函数写法
values.sort((a, b) => { return b - a;});
console.log(values);
```

箭头函数有如此多的妙处，那么我们使用它的时候有哪些需要注意的地方呢？

###### 箭头函数的this指向问题

首先，箭头函数中的this不再随着调用它的对象的改变而改变，this对象变成固定的了，它稳定的指向该箭头函数被定义时的作用域，而不是像普通函数那样，指向函数调用时的作用域。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        
        function foo() {
          console.log(this);
        }

        let obj = {
            func: foo
        };
        // 普通函数中this可变，this指向函数执行时所在的作用域
        // 此时this指向window对象
        foo(1, 2);
        
        // 此时this指向obj这个对象
        obj.func(1, 2);

</body>
</html>
```

下面的代码中this对象指向该箭头函数定义时所在的作用域

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>函数</title>
</head>
<body>
    <script>
        function foo() {
            setTimeout(() => {
                console.log('id:', this.id);
            }, 1000);

            setTimeout(function () {
                console.log('id:', this.id);
            }, 2000);
        }

        var id = 21;

        foo.call({ id: 42 });

    </script>

</body>
</html>
```

这是一个非常重要的不同点，也是vue.js中比较常用的一个知识点。 

#### ES6之类的定义

###### ES5实例化对象的方式

 ES5中的我们常使用构造函数的方式，创建一个实例对象，比如如下示例

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // ES5中使用构造函数的方式创建实例对象
        function Foo(username, age, hobby) {
            this.username = username;
            this.age = age;
            this.hobby = hobby;
        }

        Foo.prototype.showInfo = function () {
            console.log(this.username, this.age, this.hobby);
        };

        let foo = new Foo("peiqi", 73, "girls");

        foo.showInfo();
    </script>
</body>
</html>
```

###### ES6实例化对象的方式

ES6给我们引入了class关键字，我们可以使用class关键字来创建一个类

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <script>
        // ES6中使用class创建类，并使用new方法创建实例对象
        // 我们需要注意：
        // 1. 必须要有constructor方法，如果没有，默认会给一个空的constructor方法
        // constructor () {};
        // 2. 必须要用new调用，不能直接调用，否则报错；
        class Foo {
            constructor (username, age, hobby) {
                this.username = username;
                this.age = age;
                this.hobby = hobby;
            }

            showInfo () {
                console.log(this.username, this.age, this.hobby);
            }
        }

        let foo = new Foo("pizza", 18, "girls");

        foo.showInfo();
    </script>
</body>
</html>
```

###### 类的继承

同样的，我们也可以使用类的继承

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>ES6中的类</title>
</head>
<body>
    <script>
        /*
        // ES5中使用构造函数的方式创建实例对象
        function Foo(username, age, hobby) {
            this.username = username;
            this.age = age;
            this.hobby = hobby;
        }

        Foo.prototype.showInfo = function () {
            console.log(this.username, this.age, this.hobby);
        };

        let foo = new Foo("peiqi", 73, "girls");

        foo.showInfo();
        */

        // ES6中使用class创建类，并使用new方法创建实例对象
        // 我们需要注意：
        // 1. 必须要有constructor方法，如果没有，默认会给一个空的constructor方法
        // constructor () {};
        // 2. 必须要用new调用，不能直接调用，否则报错；
        /*
        class Foo {
            constructor (username, age, hobby) {
                this.username = username;
                this.age = age;
                this.hobby = hobby;
            }

            showInfo () {
                console.log(this.username, this.age, this.hobby);
            }
        }

        let foo = new Foo("wuchao", 18, "girls");

        foo.showInfo();
        */

        // ES6中类的继承
        // 第一步，使用class关键字定义类
        class Baoyuan {
            // 第二步，使用constructor构造函数，初始化实例
            constructor (username, age) {
                this.username = username;
                this.age = age;
                this.money = 100000;
            }

            showInfo () {
                console.log(this.username, this.age, this.money);
            }
        }

        let baoyuan = new Baoyuan("baoyuan", 20);

        baoyuan.showInfo();

        class Alex extends Baoyuan {
            constructor (username, age) {
                super();
                this.username = username;
                this.age = age;
            }
        }

        let alex = new Alex("alex", 2);

        alex.showInfo();
    </script>
</body>
</html>
```

以上就是ES6常用语法介绍。