---
title: Vue.js常用指令
date: 2018-10-23 20:32:50
tags: 
 - Vue
 - Vue常用指令
categories: Vue
---

vue.js官方给自己的定为是数据模板引擎，并给出了一套渲染数据的指令。本文详细介绍了vue.js的常用指令。

<!--more-->

#### vue.js常用指令

Vue.js 使用了基于 HTML 的模板语法，最简单的使用vue的方式是渲染数据，渲染数据最常见的形式就是使用“Mustache”语法 (双大括号) 的文本插值。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>vue插值</title>
</head>
<body>
    <div id="app-01">
        {{ message }}
    </div>
    <!--安装方式一-->
    <script src="../../statics/vue.js"></script>
    <!--安装方式二-->
    <!--<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.16/vue.min.js"></script>-->
    <script>
        // 原生js
        let odiv = document.getElementById("app-01");
        odiv.innerText = "Hello Vue!";

        // vue的核心之一是允许采用简洁的模板语法来将数据渲染进DOM系统
        let app01 = new Vue({
            el: "#app-01",
            data: {
                message: "Hello Vue!"
            }
        });
    </script>

</body>
</html>
```

首先创建一个vue实例，并在创建实例的过程中传入一个对象。

该对象的第一个属性名为el，它的值是我们需要渲染的目标标签，我们通过属性查找定位这个标签。

该对象的第二个属性名为data，里面就是我们要渲染给浏览器标签的数据，另外还有其他属性，我们在后面的章节中一一介绍。

##### vue.js模板语法之常用指令

看了上面的代码，可能大家觉得vue也不过如此，原生js代码两行就能完成的事情，vue需要6行代码来实现，还是原生js比较简洁，其实，上面的代码只是给大家演示了挂档的技术，究竟是汽车比较快，还是骑马比较好，我们通过后面的不断学习，来解释这个问题。

接下来，我们来介绍踩油门的技术。

上面的代码中，我们演示了如何将数据渲染进DOM标签，vue帮助我们找到标签并且渲染，对于程序员来说，我们不再需要重复的找标签，绑定事件，然后再找标签，再绑定事件这样的工作了，vue帮我们都做好了，我们只需要关注具体的数据，以及业务逻辑。这也是vue给自己的定位，数据模板引擎。

它是引擎，引擎帮助我们驱动数据渲染到模板。

所以，vue的大部分内容，都是为了渲染数据用的，接下来，我们介绍vue中用来渲染数据的常用指令。



###### 常用指令: v-html

双大括号语法无法渲染HTML标签，我们需要使用v-html。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-html</title>
</head>
<body>
    <div id="app01">
        <div v-html="vue"></div>
    </div>

    <script src="./vue.js"></script>
    <script>
        let app01 = new Vue({
            el: "#app01",
            data: {
                vue: '<h1>Hello Vue!</h1>'
            }
        })
    </script>

</body>
</html>
```

###### 常用指令：v-text

类似双大括号语法渲染数据的另一种方式是使用v-text。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-html</title>
</head>
<body>
    <div id="app01">
        <div v-text="name"></div>
    </div>

    <script src="./vue.js"></script>
    <script>
        let app01 = new Vue({
            el: "#app01",
            data: {
                name: "Alex"
            }
        })
    </script>

</body>
</html>
```

###### 常用指令：v-for

接下来，我们看看数组和对象的渲染方式。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-for</title>
    <script src="../vue模板语法之插值/vue.js"></script>
</head>
<body>
    <div id="app01">
        <h2>alex的爱好</h2>
        <ul>
            <li v-for="item in qianqian">{{ item }}</li>
        </ul>
        <h2>学生的爱好</h2>
        <ul>
            <li v-for="stu in students">{{ stu.name }}的爱好是{{ stu.hobby}}</li>
        </ul>
    </div>

    <script>
        let app01 = new Vue({
            el: "#app01",
            data: {
                qianqian: [
                    "学习",
                    "逛街",
                    "美甲"
                ],
                students: [
                    {
                        name: "alex",
                        hobby: "girls"
                    },
                    {
                        name: "peiqi",
                        hobby: "girls"
                    },
                    {
                        name: "pizza",
                        hobby: "study"
                    }
                ]
            }
        })
    </script>
</body>
</html>
```

###### 常用指令：v-if

渲染数据的时候，同样也可以使用条件判断，我们来看看。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-if</title>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01">
        <div v-if="role == 'shanshan'">
            <h2>欢迎小美女</h2>
        </div>
        <div v-else-if="role == 'longting'">
            <h2>欢迎小龙女</h2>
        </div>
        <div v-else>
            <h2>滚~~~</h2>
        </div>
    </div>

    <script>
        // 请注意看HTML标签元素，v-if底层使用appendChild实现
        let app01 = new Vue({
            el: "#app01",
            data: {
                role: "shanshan"
            }
        })
    </script>
</body>
</html>
```

通过上面的代码我们可以看出，v-if的作用是控制标签的显示，它通过判断添加标签，底层采用的是appendChild来实现的，下面我们来看一个同样也是控制标签显示的另一个指令v-show。

###### 常用指令：v-show

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-show</title>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01">
        <h1 v-show="isShow">Hello Vue!</h1>
    </div>

    <script>
        // v-show的实现原理是通过控制样式的display
        let app01 = new Vue({
            el: "#app01",
            data: {
                isShow: true
            }
        })
    </script>

</body>
</html>
```

与v-if不同的是，v-show通过样式的display控制标签的显示。

###### v-if和v-show的性能比较

我们简单比较一下二者的区别：

实现方式：v-if底层采用的是appendChild来实现的，v-show通过样式的display控制标签的显示，正因为实现方式上面有差异，导致了他们的加载速度方面产生了差异；

加载性能：v-if加载速度更快，v-show加载速度慢

切换开销：v-if切换开销大，v-show切换开销小

v-if是惰性的，它是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建，v-show 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，v-if有更高的切换开销，而v-show有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用v-show较好，如果在运行时条件很少改变，则使用v-if较好。

###### 常用指令：v-bind

绑定属性，不多说了，注意冒号后面跟标签的属性，属性后面的等号指向数据，它可以简写为 :class, :href。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-bind</title>
    <style>
        .active {
            background-color: #2b84da;
        }
    </style>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01">
        <a v-bind:href='link' v-bind:class="{active: isActive}">去百度</a>
    </div>

    <script>
        // 绑定属性，简写冒号：
        let app01 = new Vue({
            el: "#app01",
            data: {
                link: "https://www.luffycity.com",
                isActive: true
            }
        })
    </script>

</body>
</html>
```

###### 常用指令：v-on

另一个非常重要的指令是v-on，使用v-on我们可以在标签上面绑定事件，注意我们新建的vue实例app01中多了一个属性，methods，在methods中，是我们具体事件的实现方式。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-on</title>
    <style>
    </style>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01">
        <!--方式一: 绑定一个事件-->
        <a v-bind:href='link'
           v-bind:class="{active: isActive}"
           v-on:click="myClick"
           >去百度</a>
        <!--方式二: 绑定多个事件-->
        <button v-on="{click: myClick,
                       mouseenter: mouseEnter,
                       mouseleave: mouseLeave}">
            点我今晚吃鸡~~~
        </button>
    </div>

    <script>
        // 绑定属性，简写冒号：
        let app01 = new Vue({
            el: "#app01",
            data: {
                link: "https://www.baidu.com",
                isActive: false
            },
            methods: {
                myClick: function () {
                    console.log("大吉大利，今晚吃鸡~~~")
                },
                mouseEnter: function () {
                    console.log("鼠标来了~~~");
                },
                mouseLeave: function () {
                    console.log("鼠标走了~~~");
                }
            }
        })
    </script>

</body>
</html>
```

###### 常用指令：v-model

上面演示的是通过vue实例将数据渲染进模板，并且在控制台，我们修改数据之后，修改后的数据能够及时（官方称之为响应式）的渲染到模板层，那么，如果有这样的需求，比如有一个input标签，当用户修改渲染的原始数据后，打印修改后的数据，简单说，我们需要vue实例可以帮我们渲染数据并响应式的监听数据修改，同时我们还需要监听用户行为，如果用户在标签上面修改了数据（之前的修改，指的是通过vue实例app01进行的数据修改），我们需要获取到数据，针对这个需求，我们可以使用v-mode指令。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>v-model</title>
</head>
<body>
    <div id="app01">
        <p>请选择你的性别</p>
        <br>
        <input v-model="name"/>
        <p><input type="text" v-model="name"/></p>
        <p>
            <input type="checkbox" value="男" v-model="gender"/>
            <input type="checkbox" value="女" v-model="gender"/>
        </p>
        <br>
        {{ name }}
        {{ gender }}

        <p>请选择你的女朋友</p>
        <select name="" id="" v-model="girlFriends">
            <option>alex</option>
            <option>pizza</option>
            <option>peiqi</option>
        </select>
        <br>
        {{ girlFriends }}

        <p>
            <textarea v-model="article"></textarea>
        </p>
        <br>
        {{ article }}
    </div>
    <script src="./vue.js"></script>
    <script>
        let app01 = new Vue({
            el: "#app01",
            data: {
                name: "alex",
                gender: [],
                girlFriends: [],
                article: "这是一篇文章",
            }
        })
    </script>
</body>
</html>
```

###### 常用指令：指令修饰符

用户可以输入任何数据类型，但有时候，我们需要限定用户输入的数据类型，可以在指令后面加上number修饰符。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01">
        <table border="1">
            <thead>
                <tr>
                    <th>学科</th>
                    <th>成绩</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Python基础</td>
                    <td><input type="text" v-model.number="python"/></td>
                </tr>
                <tr>
                    <td>前端</td>
                    <td><input type="text" v-model.lazy="web"/></td>
                </tr>
                <tr>
                    <td>Django</td>
                    <td><input type="text" v-model.trim="django"/></td>
                </tr>
            </tbody>
        </table>
    </div>

    <script>
        let app01 = new Vue({
            el: "#app01",
            data: {
                python: 75,
                web: 98,
                django: 88
            }
        })
    </script>

</body>
</html>
```

###### 常用指令：计算属性

计算属性用来监听多个数据，每次页面加载，计算属性中的函数立即执行，但是只要原数据不被修改，那么，就不会触发重新计算，计算属性会使用计算后的缓存结果，只当原数据修改时，才会重新计算并将结果缓存起来。计算属性的计算结果可以当做data中的数据一样使用。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01">
        <table border="1">
            <thead>
                <tr>
                    <th>学科</th>
                    <th>成绩</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                    <td>Python基础</td>
                    <td><input type="text" v-model.number="python"/></td>
                </tr>
                <tr>
                    <td>前端</td>
                    <td><input type="text" v-model.trim="web"/></td>
                </tr>
                <tr>
                    <td>Django</td>
                    <td><input type="text" v-model.lazy="django"/></td>
                </tr>
                <tr>
                    <td>总分</td>
                    <td>{{ python + web + django }}</td>
                </tr>
                <tr>
                    <td>平均分</td>
                    <td>{{ avgScore }}</td>
                </tr>
            </tbody>
        </table>
    </div>

    <script>
        // 计算属性放在缓存当中，只有数据修改时才重新计算
        let app01 = new Vue({
            el: "#app01",
            data: {
                python: 75,
                web: 98,
                django: 88
            },
            computed: {
                sumScore: function () {
                    return this.python + this.web + this.django;
                },
                avgScore: function () {
                    return this.sumScore/3;
                }
            }
        })
    </script>

</body>
</html>
```

计算属性是通过过滤多个数据之后的结果，不一定是data中已存在的数据。

###### 常用指令：侦听属性

计算属性用来监听多个属性，我们也可以使用它来监听一个属性，但是页面加载即执行计算并不符合我们的需求，如何只在数据被修改后出发相应事件，vue提供给我们的是watch，即侦听属性。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.min.js"></script>
</head>
<body>
  <div id="app">
    <p>Original message: {{ message }}</p>
    <p>Computed message: {{ reversedMessage }}</p>
    <p>Full name: {{ fullName }}</p>
    <p><input type="text" v-model="firstName"/></p>
    <p><input type="text" v-model="lastName"/></p>
  </div>

  <script>
    new Vue({
      el: "#app",
      data: {
        message: "Hello Vue!",
        firstName: "Foo",
        lastName: "Bar",
        fullName: "Foo Bar",
      },
      computed: {
        reversedMessage: {
          get: function () {
            console.log(1);
            // 多次刷新reversedMessage不会改变, 除非message发生改变
            return this.message.split('').reverse().join('');
          }
        }
      },
      watch: {
        firstName: function (value) {
          console.log(value);
          this.fullName = value + ' ' + this.lastName
        },
        lastName: function (value) {
          console.log(value);
          this.fullName = this.firstName + ' ' + value
        }
      }
    })
  </script>
</body>
</html>
```

侦听属性必须是data中的已存在的数据。

###### 常用指令：自定义指令

遇到一些复杂的需求，vue提供的指令不能完美的处理，有时候我们需要自定义指令，针对这一需求，vue提供了自定义指令，如下所示：

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>自定义属性</title>
    <style>
        .box {
            width: 100px;
            height: 100px;
            border: 1px;
            background-color: #ff0000;
        }
    </style>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01" class="box" v-pos.left.bottom="post">

    </div>

    <script>
        Vue.directive("pos", function (el, bindding) {
            let decorators = bindding.modifiers;
            if (bindding.value) {
                el.style['position'] = 'fixed';
                for (let key in decorators) {
                    el.style[key] = 0;
                }
            } else {
                el.style.position = "static";
            }
        });
        // 自定义属性
        let app01 = new Vue({
            el: "#app01",
            data: {
                post: true
            }
        })
    </script>

</body>
</html>
```

el是我们自定义指令所在的DOM元素；

bindding是自定义指令的一些具体数据，请记住，最重要的一点是，不管是现在还是将来，任何情况下，我们都需要根据数据进行业务处理，所以，此处最关键的也是数据，即bindding.value。

###### 常用指令：获取DOM元素

某些情况下，我们需要直接获取DOM元素，并对元素进行一些加工处理。vue提供给我们$refs来获取DOM元素

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>获取DOM</title>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app01">
        <div ref="myBox">alex</div>
        <button v-on:click="myAlex">点击alex变红</button>
    </div>


    <script>
        // 错误实例button放在div外面
        let app01 = new Vue({
            el: "#app01",
            methods: {
                myAlex: function () {
                    this.$refs.myBox.style.color = "red";
                }
            }
        })
    </script>
</body>
</html>
```

在DOM元素上面绑定了ref之后，vue根实例上面就会出现`$refs`变量，它是一个object类型，key为ref后面的自定义名称，而value为DOM元素。我们通过`this.$refs`拿到object，之后就可以通过自定义的key名，来找到DOM元素

##### 案例：todoList版本一

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>todolist</title>

    <script src="./vue.js"></script>
    <!-- 引入样式 -->
    <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
    <!-- 引入组件库 -->
    <script src="https://unpkg.com/element-ui/lib/index.js"></script>

    <style>
        .text {
            font-size: 14px;
        }

        .item {
            margin-bottom: 18px;
        }

        .clearfix:after {
            display: table;
            content: "";
        }

        .clearfix:after {
            clear: both
        }

        .box-card {
            width: 480px;
            margin: 20px 400px;
        }

        .left {
            float: left;
            width: 50%;
        }

        .right {
            float: right;
            width: 50%;
        }

</style>
</head>
<body>
    <div id="todolist">
        <el-card class="box-card">
            <div slot="header" class="clearfix">
                <span>TodoList</span>
                <input  id="waitInput" v-on:keyup.enter="todoEnter"
                        style="margin-left: 20px; width: 250px;"
                        v-model="waitInput"
                        placeholder="请输入待办事项"
                />
                <el-button
                        style="float: right;
                        padding: 3px 0"
                        type="text"
                        v-on:click="todoEnter">添加待办
                </el-button>

            </div>
            <div class="left">
                <div style="text-align: center; margin-bottom: 10px; color: red;">待办</div>

                <div v-for="(todo, index) in todoThings" class="text item">
                    <input  v-on:click="addDone(index)"
                            style="margin-right: 15px;"
                            type="checkbox"
                            v-model="currentThing"
                            />{{ todo }}
                    <img v-on:click="delThing(index)"
                         style="width: 30px; height: 20px; float: right;
                         margin-right: 30px"
                         src="./delete.png"/>
                </div>
            </div>

            <div class="right">
                <div style="text-align: center; margin-bottom: 10px; color: green;">已办</div>
                <div v-for="(done, index) in doneThings" class="text item">
                    <input  v-on:click="delDone(index)"
                            style="margin-right: 15px;"
                            type="checkbox"
                            />{{ done }}
                    <img v-on:click="delThing(index)"
                         style="width: 30px; height: 20px; float: right;
                         margin-right: 30px"
                         src="./delete.png"/>
                </div>
            </div>
        </el-card>
    </div>

    <script>
        new Vue({
            el: "#todolist",
            data: {
                waitInput: "",
                currentThing: "",
                checked: true,
                todoThings: ['写代码', '五道口吃火锅', '超市买鸡蛋', '图书馆看书', '看电影', '看演唱会', '游泳', '跑步'],
                doneThings: ['看书', '写博客', '散步', '与朋友聊天', '打电话给父母']
            },
            methods: {
                todoEnter: function () {
                    if ( this.waitInput ) {
                        this.todoThings.push(this.waitInput);
                        this.waitInput = "";
                    }
                },
                addDone: function (index) {
                    event.currentTarget.checked = false;
                    let done = this.todoThings[index];
                    this.todoThings.splice(index, 1);
                    this.doneThings.push(done);
                },
                delDone: function (index) {
                    let notDone = this.doneThings[index];
                    this.doneThings.splice(index, 1);
                    this.todoThings.push(notDone);
                },
                delThing: function (index, currentlist) {

                    if ( currentlist === this.todoThings ) {
                        this.todoThings.splice(index, 1);
                    } else {
                        this.doneThings.splice(index, 1);
                    }
                }
            }
        })
    </script>
</body>
</html>
```

##### 案例：todoList版本二

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>todolist</title>

    <script src="./vue.js"></script>
    <!-- 引入样式 -->
    <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
    <!-- 引入组件库 -->
    <script src="https://unpkg.com/element-ui/lib/index.js"></script>

    <style>
        .text {
            font-size: 14px;
        }

        .item {
            margin-bottom: 18px;
        }

        .clearfix:after {
            display: table;
            content: "";
        }

        .clearfix:after {
            clear: both
        }

        .box-card {
            width: 480px;
            margin: 20px 400px;
        }

        .left {
            float: left;
            width: 50%;
        }

        .right {
            float: right;
            width: 50%;
        }

</style>
</head>
<body>
    <div id="todolist">
        <el-card class="box-card">
            <div slot="header" class="clearfix">
                <span>TodoList</span>
                <input  id="waitInput" v-on:keyup.enter="todoEnter"
                        style="margin-left: 20px; width: 250px;"
                        v-model="waitInput"
                        placeholder="请输入待办事项"
                />
                <el-button
                        style="float: right;
                        padding: 3px 0"
                        type="text"
                        v-on:click="todoEnter">添加待办
                </el-button>

            </div>
            <div class="left">
                <div style="text-align: center; margin-bottom: 10px; color: red;">待办</div>

                <div v-for="(todo, index) in todoThings" class="text item">
                    <input  v-on:click="moveThings(index, todoThings)"
                            style="margin-right: 15px;"
                            type="checkbox"
                            v-model="currentThing"
                            />{{ todo }}
                    <img v-on:click="delThing(index, todoThings)"
                         style="width: 30px; height: 20px; float: right;
                         margin-right: 30px"
                         src="./delete.png"/>
                </div>
            </div>

            <div class="right">
                <div style="text-align: center; margin-bottom: 10px; color: green;">已办</div>
                <div v-for="(done, index) in doneThings" class="text item">
                    <input  v-on:click="moveThings(index, doneThings)"
                            style="margin-right: 15px;"
                            type="checkbox"
                            />{{ done }}
                    <img v-on:click="delThing(index, doneThings)"
                         style="width: 30px; height: 20px; float: right;
                         margin-right: 30px"
                         src="./delete.png"/>
                </div>
            </div>
        </el-card>
    </div>

    <script>
        new Vue({
            el: "#todolist",
            data: {
                waitInput: "",
                currentThing: "",
                checked: true,
                todoThings: ['写代码', '五道口吃火锅', '超市买鸡蛋', '图书馆看书', '看电影', '看演唱会', '游泳', '跑步'],
                doneThings: ['看书', '写博客', '散步', '与朋友聊天', '打电话给父母']
            },
            methods: {
                todoEnter: function () {
                    if ( this.waitInput ) {
                        this.todoThings.push(this.waitInput);
                        this.waitInput = "";
                    }
                },
                moveThings: function (index, currentlist) {
                    event.currentTarget.checked = false;
                    let spliceList = currentlist === this.todoThings ? this.todoThings : this.doneThings;
                    let pushList = spliceList === this.todoThings ? this.doneThings : this.todoThings;
                    let thing = currentlist[index];
                    spliceList.splice(index, 1);
                    pushList.push(thing);
                },
                delThing: function (index, currentlist) {
                    if ( currentlist === this.todoThings ) {
                        this.todoThings.splice(index, 1);
                    } else {
                        this.doneThings.splice(index, 1);
                    }
                }
            }
        })
    </script>
</body>
</html>
```

##### 案例：todoList版本三

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>使用Vue实现todoList</title>
  <script src="../statics/vue.min.js"></script>
  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .container {
      width: 500px;
      height: auto;
      margin: 0 auto;
    }
    .container .header {
      text-align: center
    }
    li {
      list-style-type: none
    }
    div.left {
      float: left;
      margin-left: 50px;
    }
    div.right {
      float: right;
      margin-right: 50px;
    }
    li {
      margin-top: 5px;
    }
  </style>
</head>
<body>

  <div id="app">
    <div class="container">
      <!--头部添加待办-->
      <div class="header">
        <input id="addthings" @keyup.enter="todoEnter" v-model="todo">
        <span @click="todoEnter">添加待办</span>
      </div>

      <!--已办和待办最外层的盒子-->
      <div class="box">
        <!--已办事项-->
        <div class="left">
          <span>待办事项</span>
          <div class="todos">
            <ul>
              <li v-for="(todo, index) in todoThings" :key="index">
                <input type="radio" name="todo" v-on:click="addThing(index)"><span>{{ todo }}</span>
              </li>
            </ul>
          </div>
        </div>

        <!--待办事项-->
        <div class="right">
          <span>已办事项</span>
          <div class="dones">
            <ul>
              <li v-for="(done, index) in doneThings" :key="index">
                  <input type="radio" name="done" v-on:click="delThing(index)"><span>{{ done }}</span>
              </li>
            </ul>
          </div>
        </div>

      </div>

    </div>

  </div>
  <script>
    new Vue({
      el: "#app",
      data: {
        todo: '',
        addDone: '',
        todoThings: ['写代码', '五道口吃火锅', '超市买鸡蛋', '图书馆看书', '看电影', '看演唱会', '游泳', '跑步'],
        doneThings: ['看书', '写博客', '散步', '跟朋友聊天', '打电话给父母', '学炒菜', '洗衣服', '打扫房间']
      },
      methods: {
        todoEnter: function () {
          if (this.todo) {
            this.todoThings.push(this.todo);
            this.todo = '';
          }
        },
        addThing: function (index) {
          event.currentTarget.checked = false;
          things = this.todoThings.splice(index, 1)[0];
          this.doneThings.push(things);
        },
        delThing: function (index) {
          event.currentTarget.checked = false;
          things = this.doneThings.splice(index, 1)[0];
          this.todoThings.push(things);
        }
      }
    })
  </script>
</body>
</html>
```

##### 案例：轮播图

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>使用Vue实现轮播图版本一</title>
  <script src="../statics/vue.min.js"></script>
  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .btn {
      width: 120px;
      height: 30px;
      border-radius: 20px;
      margin-left: 20px;
      background-color: yellowgreen;
    }
    p {
      width: 800px;
      height: 500px;
      margin: 0 auto;
    }
  </style>
</head>
<body>
  <div id="app">
    <p><img :src="imageArray[currentIndex].imgSrc" v-on="{ mouseenter: mouseEnter, mouseleave: mouseLeave}" alt="" width="800px"/></p>
    <p>
      <button v-on:click="prevImg" class="btn">上一张</button>
      <button v-on:click="nextImg" class="btn">下一张</button>
    </p>
  </div>
  <script>
    new Vue({
      el: "#app",
      data: {
        imageArray: [
          { id: 1, imgSrc: "../images/timg1.jpg" },
          { id: 2, imgSrc: "../images/timg2.jpg" },
          { id: 3, imgSrc: "../images/timg3.jpg" },
          { id: 4, imgSrc: "../images/timg4.jpg" },
          { id: 5, imgSrc: "../images/timg5.jpg" },
        ],
        currentIndex: 0,
      },
      methods: {
        prevImg: function () {
          this.currentIndex = this.currentIndex - 1;
          if (this.currentIndex < 0) {
            this.currentIndex = this.imageArray.length - 1;
          }
        },
        nextImg: function () {
          this.currentIndex = this.currentIndex + 1;
          if (this.currentIndex === this.imageArray.length) {
            this.currentIndex = 0;
          }
        },
        mouseEnter: function () {
          clearInterval(this.interVal);
        },
        mouseLeave: function () {
          this.setInterVal();
        },
          setInterVal: function() {
              this.interVal = setInterval(() => {
              this.currentIndex = this.currentIndex + 1;
              if (this.currentIndex === this.imageArray.length) {
                this.currentIndex = 0;
              }
            }, 3000)
          }
      },
      created(){
        this.setInterVal();
      }
    })
  </script>
</body>
</html>
```

##### 案例：轮播图升级版

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .container {
      margin: 0 auto;
      width: 800px;
      height: 500px;
      position: relative;
      top: 80px;
    }
    .img {
      width: 800px;
      height: 500px;
      position: absolute;

    }
    img {
      width: 800px;
      height: 500px;
    }
    .circle {
      width: 130px;
      height: 2px;
      position: absolute;
      left: 50%;
      margin-left: -65px;
      bottom: 30px;
    }
    .circle span {
      padding: 1px 10px;
      background-color: #c2ccd1;
      border-radius: 50%;
      margin-left: 5px;
      cursor: pointer;
    }
    .circle span.current {
      background-color: #F90;
    }
  </style>
</head>
<body>
  <div id="app">
    <div class="container">
      <div class="img"><img :src="imageArray[currentIndex].imgSrc" v-on="{ mouseenter: mouseEnter, mouseleave: mouseLeave}"></div>
      <div class="circle">
        <span v-for="(circle, index) in circleCount" :class="{current: currentIndex===index}" v-on:click="changePic(index)"></span>
      </div>
    </div>
  </div>

  <script>
    new Vue({
      el: "#app",
      data: {
        imageArray: [
          { id: 1, imgSrc: "../images/timg1.jpg" },
          { id: 2, imgSrc: "../images/timg2.jpg" },
          { id: 3, imgSrc: "../images/timg3.jpg" },
          { id: 4, imgSrc: "../images/timg4.jpg" },
          { id: 5, imgSrc: "../images/timg5.jpg" },
        ],
        currentIndex: 0,
        circleCount: 5
      },
      methods: {
        prevImg: function () {
          this.currentIndex = this.currentIndex - 1;
          if (this.currentIndex < 0) {
            this.currentIndex = this.imageArray.length - 1;
          }
        },
        nextImg: function () {
          this.currentIndex = this.currentIndex + 1;
          if (this.currentIndex === this.imageArray.length) {
            this.currentIndex = 0;
          }
        },
        mouseEnter: function () {
          clearInterval(this.interval);
        },
        mouseLeave: function () {
          this.setInterVal();
        },
        setInterVal: function () {
          this.interval = setInterval(() => {
            this.currentIndex = this.currentIndex + 1;
            if (this.currentIndex === this.imageArray.length) {
              this.currentIndex = 0;
            }
          }, 3000)
        },
        changePic: function (index) {
          this.currentIndex = index;
        }
      },
      created(){
        this.setInterVal();
      }
    })
  </script>

</body>
</html>
```

##### 案例：音乐播放器

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>使用Vue实现音乐播放器</title>
  <script src="../statics/vue.min.js"></script>
  <style>
    * {
      margin: 0;
      padding: 0;
    }
    .active {
      background-color: red;
    }
  </style>
</head>
<body>

  <div id="app">
    <audio :src="songs[currentIndex].songSrc" controls="controls" loop="loop" autoplay="autoplay"></audio>
    <ul>
      <li v-for="song in songs" :key="song.id" @click="playSong(song.id)" :class="{ active: song.id===currentIndex+1 }">
        <h2>歌手：{{ song.artist }}</h2>
        <p>歌名：{{ song.name }}</p>
      </li>
    </ul>
  </div>

  <script>
    new Vue({
      el: "#app",
      data: {
        songs: [
          { id: 1, songSrc: '../medias/不再让你孤单.mp3', artist: "李必胜", name: '不再让你孤单' },
          { id: 2, songSrc: '../medias/漂洋过海来看你.mp3', artist: "Pizza Li", name: '漂洋过海来看你' },
          { id: 3, songSrc: '../medias/成都.mp3', artist: '必胜', name: '成都' },
          { id: 4, songSrc: '../medias/晚风.mp3', artist: 'Pizza', name: '晚风' },
        ],
        currentIndex: 0
      },
      methods: {
        playSong(index) {
          this.currentIndex = index - 1;
        }
      }
    })
  </script>

</body>
</html>
```

好了，以上就是vue常用指令介绍。