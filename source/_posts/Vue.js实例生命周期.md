---
title: Vue.js实例生命周期
date: 2018-10-28 13:24:31
tags:
 - Vue
 - Vue生命周期
categories: Vue
---

有时候，我们需要在实例创建过程中进行一些初始化的工作，以帮助我们完成项目中更复杂更丰富的需求开发，针对这样的需求，Vue提供给我们一系列的钩子函数。

本文详细介绍了Vue实例在创建和销毁的过程中，我们可以利用的钩子函数。

<!--more-->

#### Vue生命周期

下面，我们结合官方文档提供的Vue实例生命周期图，来进行钩子函数的解析。

![lifycycle](Vue.js实例生命周期/lifecycle.png)

##### beforeCreate

在实例初始化之后，数据观测 (data observer) 和 event/watcher 事件配置之前被调用。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.js"></script>
</head>
<body>

    <div id="app">
        {{ name }}
        <button @click="myClick">点击修改数据</button>
    </div>

    <script>

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            data: {
                name: "Alex"
            },
            methods: {
                myClick: function () {
                    this.name = "Pizza";
                }
            },
            beforeCreate() {
                console.group("beforeCreate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            });
    </script>

</body>
</html>
```

##### created

在实例创建完成后被立即调用。在这一步，实例已完成以下的配置：数据观测 (data observer)，属性和方法的运算，watch/event 事件回调。然而，挂载阶段还没开始，`$el` 属性目前不可见。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.js"></script>
</head>
<body>

    <div id="app">
        {{ name }}
        <button @click="myClick">点击修改数据</button>
    </div>

    <script>

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            data: {
                name: "Alex"
            },
            methods: {
                myClick: function () {
                    this.name = "Pizza";
                }
            },
            beforeCreate() {
                console.group("beforeCreate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            created() {
                console.group("Created");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            }
        })
    </script>

</body>
</html>
```

##### beforeMount

在挂载开始之前被调用：相关的 `render` 函数首次被调用。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.js"></script>
</head>
<body>

    <div id="app">
        {{ name }}
        <button @click="myClick">点击修改数据</button>
    </div>

    <script>
        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            data: {
                name: "Alex"
            },
            methods: {
                myClick: function () {
                    this.name = "Pizza";
                }
            },
            beforeCreate() {
                console.group("beforeCreate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            created() {
                console.group("Created");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            beforeMount() {
                console.group("beforeMount");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            }
        })
    </script>

</body>
</html>
```

##### mounted

`el` 被新创建的 `vm.$el` 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 `mounted` 被调用时 `vm.$el` 也在文档内。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.js"></script>
</head>
<body>

    <div id="app">
        {{ name }}
        <button @click="myClick">点击修改数据</button>
    </div>

    <script>

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            data: {
                name: "Alex"
            },
            methods: {
                myClick: function () {
                    this.name = "Pizza";
                }
            },
            beforeCreate() {
                console.group("beforeCreate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            created() {
                console.group("Created");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            beforeMount() {
                console.group("beforeMount");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            mounted() {
                console.group("mounted");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
        })
    </script>

</body>
</html>
```

##### beforeUpdate

数据更新时调用，发生在虚拟 DOM 打补丁之前。这里适合在更新之前访问现有的 DOM，比如手动移除已添加的事件监听器。

**该钩子在服务器端渲染期间不被调用，因为只有初次渲染会在服务端进行**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.js"></script>
</head>
<body>

    <div id="app">
        {{ name }}
        <button @click="myClick">点击修改数据</button>
    </div>

    <script>

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            data: {
                name: "Alex"
            },
            methods: {
                myClick: function () {
                    this.name = "Pizza";
                }
            },
            beforeCreate() {
                console.group("beforeCreate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            created() {
                console.group("Created");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            beforeMount() {
                console.group("beforeMount");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            mounted() {
                console.group("mounted");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            beforeUpdate() {
                console.group("beforeUpdate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            }
        })
    </script>

</body>
</html>
```

##### updated

由于数据更改导致的虚拟 DOM 重新渲染和打补丁，在这之后会调用该钩子。

当这个钩子被调用时，组件 DOM 已经更新，所以你现在可以执行依赖于 DOM 的操作。然而在大多数情况下，你应该避免在此期间更改状态。如果要相应状态改变，通常最好使用[计算属性](https://cn.vuejs.org/v2/api/#computed)或 [watcher](https://cn.vuejs.org/v2/api/#watch) 取而代之。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.js"></script>
</head>
<body>

    <div id="app">
        {{ name }}
        <button @click="myClick">点击修改数据</button>
    </div>

    <script>

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            data: {
                name: "Alex"
            },
            methods: {
                myClick: function () {
                    this.name = "Pizza";
                }
            },
            beforeCreate() {
                console.group("beforeCreate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            created() {
                console.group("Created");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            beforeMount() {
                console.group("beforeMount");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            mounted() {
                console.group("mounted");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            beforeUpdate() {
                console.group("beforeUpdate");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            },
            updated() {
                console.log("updated");
                console.log("el: ", this.$el);
                console.log("data: ", this.$data);
                console.log("name: ", this.name);
                console.log("myClick: ", this.myClick);
                console.log("innerHTML: ", document.getElementById("app").innerHTML);
            }
        })
    </script>

</body>
</html>
```

##### activated

keep-alive 组件激活时调用。`<keep-alive>` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们

**该钩子在服务器端渲染期间不被调用。**

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
        <App></App>
    </div>

    <script>
        let Laside = {
            template: `
                <div>
                    <h1>{{ message }}</h1>
                    <button @click="changeData">点击修改数据</button>
                </div>
            `,
            data () {
                return {
                    message: "Hello Vue!"
                }
            },
            methods: {
                changeData: function () {
                    this.mes = "Pizza is here!";
                }
            },
            // 组件的创建和销毁对性能有影响
            beforeDestroy () {
                console.log("beforeDestroy");
            },
            destroyed () {
                console.log("destroyed");
            },
            activated () {
                console.log("activated");
            },
        };

        let App = {
            template: `
                <div >
                    <keep-alive>
                        <Laside v-if="isShow"></Laside>
                    </keep-alive>
                    <button @click="showHide">创建消除组件</button>
                </div>
            `,
            components: {
                "Laside": Laside,
            },
            methods: {
                showHide: function () {
                    this.isShow = !this.isShow;
                }
            },
            data () {
                return {
                    isShow: true,
                }
            }
        };

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            components: {
                App,
            }
        })
    </script>

</body>
</html>
```



##### deactivated

keep-alive 组件停用时调用。

**该钩子在服务器端渲染期间不被调用。**

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
        <App></App>
    </div>

    <script>
        let Laside = {
            template: `
                <div>
                    <h1>{{ mes }}</h1>
                    <button @click="changeData">点击修改数据</button>
                </div>
            `,
            data () {
                return {
                    message: "Hello Vue!"
                }
            },
            methods: {
                changeData: function () {
                    this.mes = "Pizza is here!";
                }
            },
            // 组件的创建和销毁对性能有影响
            beforeDestroy () {
                console.log("beforeDestroy");
            },
            destroyed () {
                console.log("destroyed");
            },
            activated () {
                console.log("activated");
            },

            deactivated () {
                console.log("deactivated");
            }
        };

        let App = {
            template: `
                <div >
                    <keep-alive>
                        <Laside v-if="isShow"></Laside>
                    </keep-alive>
                    <button @click="showHide">创建消除组件</button>
                </div>
            `,
            components: {
                "Laside": Laside,
            },
            methods: {
                showHide: function () {
                    this.isShow = !this.isShow;
                }
            },
            data () {
                return {
                    isShow: true,
                }
            }
        };

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            components: {
                App,
            }
        })
    </script>

</body>
</html>
```



##### beforeDestroy

实例销毁之前调用。在这一步，实例仍然完全可用。频繁的创建和销毁组件对性能的影响很大，因此可以使用activated和deactivated。

**该钩子在服务器端渲染期间不被调用。**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.js"></script>
</head>
<body>

    <div id="app">
        <App></App>
    </div>

    <script>
        let Laside = {
            template: `
                <div>
                    <h1>{{ mes }}</h1>
                    <button @click="changeData">点击修改数据</button>
                </div>
            `,

            data () {
                return {
                    mes: "Hello Vue!"
                }
            },

            methods: {
                changeData: function () {
                    this.mes = "Pizza is here!";
                }
            },

            // 组件的创建和销毁对性能有影响
            beforeDestroy() {
                console.log("beforeDestroy");
            },
        };

        let App = {
            template: `
                <div >
                    <Laside v-if="isShow"></Laside>
                    <button @click="showHide">创建消除组件</button>
                </div>
            `,
            components: {
                "Laside": Laside,
            },
            methods: {
                showHide: function () {
                    this.isShow = !this.isShow;
                }
            },
            data () {
                return {
                    isShow: true,
                }
            }
        };

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            components: {
                App,
            }
        })
    </script>

</body>
</html>
```

##### destroyed

Vue 实例销毁后调用。调用后，Vue 实例指示的所有东西都会解绑定，所有的事件监听器会被移除，所有的子实例也会被销毁。

**该钩子在服务器端渲染期间不被调用。**

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
        <App></App>
    </div>

    <script>
        let Laside = {
            template: `
                <div>
                    <h1>{{ mes }}</h1>
                    <button @click="changeData">点击修改数据</button>
                </div>
            `,

            data () {
                return {
                    mes: "Hello Vue!"
                }
            },

            methods: {
                changeData: function () {
                    this.mes = "Pizza is here!";
                }
            },

            // 组件的创建和销毁对性能有影响
            beforeDestroy() {
                console.log("beforeDestroy");
            },

            destroyed () {
                console.log("destroyed");
            }
        };

        let App = {
            template: `
                <div >
                    <Laside v-if="isShow"></Laside>
                    <button @click="showHide">创建消除组件</button>
                </div>
            `,
            components: {
                "Laside": Laside,
            },
            methods: {
                showHide: function () {
                    this.isShow = !this.isShow;
                }
            },
            data () {
                return {
                    isShow: true,
                }
            }
        };

        new Vue({
            el: "#app",
            // 在template中使用组件与在body中使用组件是一样的
            // template: `<cont></cont>`,
            components: {
                App,
            }
        })
    </script>

</body>
</html>
```

好了，关于Vue.js实例的生命周期就介绍到这里。

##### 案例：created

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <script src="../statics/jquery-3.3.1.min.js"></script>
</head>
<body>

  <div id="app"></div>

  <script>
    new Vue({
      el: "#app",
      created() {
        console.log(this);
        $.ajax({
          url: "https://www.luffycity.com/api/v1/course_sub/category/list/",
          type: 'get',
          success: function (data) {
            console.log(data);
          }
        })
      }
    })
  </script>

</body>
</html>
```

