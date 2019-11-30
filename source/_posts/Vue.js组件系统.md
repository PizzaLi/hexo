---
title: Vue.js组件系统
date: 2018-10-27 20:23:50
tags:
 - Vue
 - Vue组件系统
categories: Vue
---

vue.js既然是框架，那就不能只是简单的完成数据模板引擎的任务，它还提供了页面布局的功能。本文详细介绍使用vue.js进行页面布局的强大工具，vue.js组件系统。

<!--more-->

#### Vue.js组件系统

每一个新技术的诞生，都是为了解决特定的问题。组件的出现就是为了解决页面布局等等一系列问题。vue中的组件分为两种，全局组件和局部组件。

##### 全局组件的注册

通过Vue.component()创建一个全局组件之后，我们可以在一个通过 `new Vue` 创建的 Vue 根实例中，把这个组件作为自定义元素来使用。

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
    <!--第二步，使用-->
    <global_component></global_component>
  </div>
  <script>
    // 第一步，注册
    Vue.component("global_component", {
      template: `
        <div>
            <h2>Hello Vue</h2>
        </div>
      `
    });

    new Vue({
      el: "#app",
    });
  </script>
</body>
</html>
```

###### 组件的参数

因为组件是可复用的 Vue 实例,所以它们与 `new Vue` 接收相同的选项，例如 `data`、`computed`、`watch`、`methods` 以及生命周期钩子等。仅有的例外是像 `el`这样根实例特有的选项。

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
    <!--第二步，使用-->
    <global_component></global_component>
  </div>
  <script>
    // 第一步，注册
    Vue.component("global_component", {
      data: function () {
        return {
          count: 0
        }
      },
      template: `<button v-on:click="count++">You clicked me {{ count }} times.</button>`
    });

    new Vue({
      el: "#app",
    });
    
  </script>
</body>
</html>
```

###### 组件的复用

每个实例维护自己的一份独立的数据。

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
    <!--第二步，使用-->
    <global_component></global_component>
    <global_component></global_component>
    <global_component></global_component>
  </div>
  <script>
    // 第一步，注册
    Vue.component("global_component", {
      data: function () {
        return {
          count: 0
        }
      },
      template: `<button v-on:click="count++">You clicked me {{ count }} times.</button>`
    });

    new Vue({
      el: "#app",
    });
    
  </script>
</body>
</html>
```

注意当点击按钮时，每个组件都会各自独立维护它的 `count`。因为你每用一次组件，就会有一个它的新**实例**被创建。

###### Data必须是一个函数

data必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝,  也可以写成如下形式

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
    <!--第二步，使用-->
    <global_component></global_component>
    <global_component></global_component>
    <global_component></global_component>
  </div>
  <script>
    // 第一步，注册
    Vue.component("global_component", {
      data(){
        return {
          count: 0
        }
      },
      template: `<button v-on:click="count++">You clicked me {{ count }} times.</button>`
    });

    new Vue({
      el: "#app",
    });

  </script>
</body>
</html>
```

##### 局部组建的注册

全局注册往往是不够理想的。比如，如果你使用一个像 webpack 这样的构建系统，全局注册所有的组件意味着即便你已经不再使用一个组件了，它仍然会被包含在你最终的构建结果中。这造成了用户下载的 JavaScript 的无谓的增加。

全局组件始终是存在的，除非程序结束，如果组件越来越大，那么程序所占用的空间和消耗的性能就会更大。

###### 局部组件的第一种使用方式

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
</head>
<body>
  <div id="component-demo">
    <!--最后在根元素当中使用它-->
    <!--第一种使用方式，会把当前div渲染进DOM-->
    <my-header></my-header>
  </div>
  <script>
    // 定义一个局部组件，其实就是一个变量，它是一个object类型
    // 属性与全局组件是一样的
    let Header = {
      template: `
        <button @click="count++">{{ count }}</button>
      `,
      data() {
        return {
          count: 0
        }
      }
    };

    new Vue({
      el: "#component-demo",
      // 第二部，需要在根实例当中使用它
      components: {
        'my-header': Header
      }
    });
  </script>
</body>
</html>
```

###### 局部组件的第二种使用方式

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
</head>
<body>
  <div id="component-demo">
  </div>
  <script>
    // 定义一个局部组件，其实就是一个变量，它是一个object类型
    // 属性与全局组件是一样的
    let Header = {
      template: `
        <button @click="count++">{{ count }}</button>
      `,
      data() {
        return {
          count: 0
        }
      }
    };

    new Vue({
      el: "#component-demo",
      // 第二种使用方式，不会将div渲染进DOM，以template为根元素
      template: `<my-header></my-header>`,
      // 第二步，需要在根实例当中使用它
      components: {
        'my-header': Header
      }
    });
  </script>
</body>
</html>
```

对于 `components` 对象中的每个属性来说，其属性名就是自定义元素的名字，其属性值就是这个组件的选项对象。

###### 在局部组件中使用子组件

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <style>
    body {
      margin: 0;
    }
    .box {
      width: 100%;
      height: 50px;
      background-color: #2aabd2;
    }

  </style>
</head>
<body>
  <div id="component-demo">
  </div>
  <script>
    // 定义一个局部组件，其实就是一个变量，它是一个object类型
    // 这个对象的属性与全局组件是一样的(除el属性外)

    let Fcontent = {
      template: `
        <div>
          <span>这是头条</span>

        </div>
      `
    };

    let Header = {
      template: `
        <div v-bind:class='{box: isBox}'>
          <button @click="count++">{{ count }}</button>
          <first-content></first-content>
        </div>
      `,
      data() {
        return {
          count: 0,
          isBox: true
        }
      },
      components: {
        'first-content': Fcontent
      }
    };

    new Vue({
      el: "#component-demo",
      // 第二种使用方式，不会将div渲染进DOM，以template为根元素
      template: `<my-header></my-header>`,
      // 第二步，需要在根实例当中使用它
      components: {
        'my-header': Header
      }
    });
  </script>
</body>
</html>
```

##### 父子组件的通信

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <style>
    body {
      margin: 0;
    }
    .box {
      width: 100%;
      height: 50px;
      background-color: #2aabd2;
    }

  </style>
</head>
<body>
  <div id="component-demo">
  </div>
  <script>
    // 定义一个局部组件，其实就是一个变量，它是一个object类型
    // 属性与全局组件是一样的

    let Fcontent = {
      template: `
        <div>
          <span>这是头条</span>
          {{ fdata }}
        </div>
      `,
      props: ['fdata']
    };

    let Header = {
      template: `
        <div v-bind:class='{box: isBox}'>
          <button @click="count++">{{ count }}</button>
          <first-content :fdata="fathData"></first-content>
        </div>
      `,
      data() {
        return {
          count: 0,
          isBox: true,
          fathData: "我是你爸爸~~~"
        }
      },
      components: {
        'first-content': Fcontent
      }
    };

    new Vue({
      el: "#component-demo",
      // 第二种使用方式，不会将div渲染进DOM，以template为根元素
      template: `<my-header></my-header>`,
      // 第二步，需要在根实例当中使用它
      components: {
        'my-header': Header
      }
    });
  </script>
</body>
</html>
```

##### 子父组件的通信

父组件在mounted的时候，监听一个自定义事件。

给子组件绑定一个click事件之后，通过内建的方法$emit在父组件上触发一个事件，这个事件就是父组件监听的自定义事件。

```vue
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
</head>
<body>

  <div id="app">


  </div>

  <script>
    let myFooter = {
      template: `
            <div>
              <h1>我是儿子</h1>
              <button v-on:click="changeFatherSize">点击修改爸爸的字体</button>
            </div>
          `,
      methods: {
        changeFatherSize: function () {
          this.$emit('change-font', 1);
        }
      },
    };

    let myHeader = {
      template: `
            <div>
              <my-footer v-on:change-font="changeSize"></my-footer>
              <span :style="{ fontSize: fontSize + 'px'}">我是爸爸</span>
            </div>
        `,
      data(){
        return {
          fontSize: 26,
        }
      },
      methods: {
        changeSize: function (value) {
          console.log(value);
          this.fontSize += value;
        }
      },
      components: {
        'my-footer': myFooter
      }
    };

    let App = {
      template: `
        <div>
          <my-header></my-header>
        </div>
      `,
      components: {
        'my-header': myHeader,
      },
    };

    new Vue({
      el: "#app",
      template: `<App></App>`,
      components: {
        App
      }
    })
  </script>

</body>
</html>
```

##### 混入

混入可以提高代码的重用性。

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


  </div>

  <script>
    let mixins = {
      methods: {
        show: function (name) {
          console.log(`${name} is here`);
        },
        hide: function (name) {
          console.log(`${name} is here`);
        }
      }
    };

    let myAlex = {
      template: `
        <div>
          <button @click="show('alex')">点我显示alex</button>
          <button @click="hide('alex')">点我隐藏alex</button>
        </div>
      `,
      mixins: [mixins]
    };

    let myPeiQi = {
      template: `
        <div>
          <button @click="show('peiqi')">点我显示peiqi</button>
          <button @click="hide('peiqi')">点我隐藏peiqi</button>
        </div>
      `,
      mixins: [mixins],
    };

    let App = {
      template: `
        <div>
          <my-alex></my-alex>
          <my-peiqi></my-peiqi>
        </div>
      `,
      components: {
        'my-alex': myAlex,
        'my-peiqi': myPeiQi,
      },
    };

    new Vue({
      el: "#app",
      template: `<App></App>`,
      components: {
        App
      }
    })
  </script>

</body>
</html>
```

##### 插槽

有时候我们需要向组件传递一些数据，这时候可以使用插槽

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .nav-link {
            width: 100px;
            height: 100px;
            background-color: #2aabd2;
            float: left;
            margin-left: 5px;
            text-align: center;
            line-height: 100px;
        }

    </style>
    <script src="../statics/vue.js"></script>
</head>
<body>
    <div id="app01">
        <com-content>登录</com-content>
        <com-content>注册</com-content>
        <com-content>最热</com-content>
        <com-content>段子</com-content>
        <com-content>42区</com-content>
        <com-content>图片</com-content>
    </div>

    <script>
        Vue.component('com-content', {
          template: `
            <div class="nav-link">
              <slot></slot>
            </div>
          `
        });

        new Vue({
            el: "#app01",
        })
    </script>

</body>
</html>
```

##### 具名插槽

操作使用了组件的复用，如果我们在同一个组件内写入不同的页面呢？此时，我们需要多个插槽，并且给不同的内容命名。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .nav-link {
            width: 100px;
            height: 100px;
            background-color: #2aabd2;
            float: left;
            margin-left: 5px;
            text-align: center;
            line-height: 100px;
        }
    </style>
    <script src="../statics/vue.js"></script>
</head>
<body>
    <div id="app01">
        <base-layout>
            <template slot="header">
                <h1>这是标题栏</h1>
            </template>
            <template>
                <h2>这是内容</h2>
            </template>
            <template slot="footer">
                <h3>这是页脚</h3>
            </template>
        </base-layout>
    </div>

    <script>
        let baseLayout = {
            template: `
                <div class="container">
                  <header>
                      <slot name="header"></slot>
                  </header>
                  <main><slot></slot></main>
                  <footer>
                      <slot name="footer"></slot>
                  </footer>
                </div>
            `
        };

        new Vue({
            el: "#app01",
            components: {
                "base-layout": baseLayout
            }

        })
    </script>

</body>
</html>
```

我们还是可以保留一个未命名插槽，这个插槽是**默认插槽**，也就是说它会作为所有未匹配到插槽的内容的统一出口。

##### 非关系组件之间的通信

非父子组件之间可以通过一个中间Vue实例来进行通信。

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
            <com-main></com-main>
    </div>

    <script>

        let pizza = new Vue();

        let Alex = {
            template: `
                <div>
                    <button @click="alexClick">点击向沛齐道歉</button>
                </div>
            `,
            methods: {
                alexClick: function () {

                    pizza.$emit("alex_apo", "原谅我吧，请你大保健~~~");
                }
            },
        };

        let peiQi = {
            template: `
                <div v-show="isShow">原谅你了~~~</div>
            `,
            mounted () {
                pizza.$on("alex_apo", function (alexsay) {
                    if ( alexsay ) {
                        console.log("原谅你了~~~");
                    }
                });
            },
            data () {
                return {
                    isShow: false
                };
            }
        };

        let App = {
            template: `
                <div id="app">
                    <alex></alex>
                    <peiqi></peiqi>
                </div>
            `,
            components: {
                'alex': Alex,
                'peiqi': peiQi
            }
        };

        new Vue({
            el: "#app",
            template: '<app></app>',
            components: {
                'app': App,
            },
        })

    </script>

</body>
</html>
```

##### 在组件上使用v-model

自定义事件也可以用于创建支持 `v-model` 的自定义输入组件。记住：

```
<input v-model="searchText">
```

等价于：

```
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```

当用在组件上时，`v-model` 则会这样：

```
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```

为了让它正常工作，这个组件内的 `<input>` 必须：

```
将其 value 特性绑定到一个名叫 value 的 prop 上
在其 input 事件被触发时，将新的值通过自定义的 input 事件抛出
```

写成代码之后是这样的：

```
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

现在 `v-model` 就应该可以在这个组件上完美地工作起来了：

```
<custom-input v-model="searchText"></custom-input>
```

如下是在组件中使用v-model示例：

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
  </div>

  <script>
    let Model = {
      template: `
        <div>
            <input
              v-bind:value="value"
              v-on:input="$emit('input', $event.target.value)"
            />
            <h1>{{ value }}</h1>
      `,
      props: ['value']
    };

    let App = {
      template: `
        <div>
            <custom-input v-model="searchText"></custom-input>
      `,
      components: {
        'custom-input': Model,
      },
      data(){
        return {
          searchText: "",
        }
      }
    };

    new Vue({
      el: "#app",
      template: `<App></App>`,
      components: {
        App,
      }
    })
  </script>
</body>
</html>
```

##### 使用组件的注意事项

###### 注意事项一：单个根元素

当构建一个内容页面的组件时，我们的组件可能包含多个HTML标签。

```
<h1>Hello World</h1>
<h2>Hello Vue</h2>
```

然而如果你在模板中尝试这样写，Vue 会显示一个错误，并解释道 **every component must have a single root element (每个组件必须只有一个根元素)**。你可以将模板的内容包裹在一个父元素内，来修复这个问题，例如：

```
<div>
  <h1>Hello World</h1>
  <h2>Hello Vue</h2>
</div>
```

###### 注意事项二：解析特殊HTML元素

有些 HTML 元素，诸如 `<ul>`、`<ol>`、`<table>` 和 `<select>`，对于哪些元素可以出现在其内部是有严格限制的。而有些元素，诸如 `<li>`、`<tr>` 和 `<option>`，只能出现在其它某些特定的元素内部。

这会导致我们使用这些有约束条件的元素时遇到一些问题。例如：

```
<table>
  <blog-post-row></blog-post-row>
</table>
```

这个自定义组件 `<blog-post-row>` 会被作为无效的内容提升到外部，并导致最终渲染结果出错。幸好这个特殊的 `is` 特性给了我们一个变通的办法：

```
<table>
  <tr is="blog-post-row"></tr>
</table>
```

需要注意的是如果我们从以下来源使用模板的话，这条限制是不存在的：

```
字符串 (例如：template: '...')
单文件组件 (.vue)
<script type="text/x-template">
```

##### 案例：使用组件实现路飞导航栏

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.min.js"></script>
    <!-- 引入样式 -->
    <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">
    <!-- 引入组件库 -->
    <script src="https://unpkg.com/element-ui/lib/index.js"></script>
    <style>
        body {
            margin: 0;
            padding: 0;
        }
        .header {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
        }
        .el-menu {
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .footer {
            position: fixed;
            bottom: 0;
            left: 0;
            width: 100%;
        }
        .header img {
            position: absolute;
            left: 80px;
            top: -4px;
            width: 118px;
            height: 70px;
            z-index: 999;
        }

    </style>
</head>
<body>

    <div id="app">

    </div>

    <template id="header">
        <div class="header">
            <img src="https://www.luffycity.com/static/img/head-logo.a7cedf3.svg"/>
            <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal">
                <el-menu-item index="1">首页</el-menu-item>
                <el-menu-item index="2">免费课程</el-menu-item>
                <el-menu-item index="3">轻课</el-menu-item>
                <el-menu-item index="4">学位课程</el-menu-item>
                <el-menu-item index="5">智能题库</el-menu-item>
                <el-menu-item index="6">公开课</el-menu-item>
                <el-menu-item index="7">内部教材</el-menu-item>
                <el-menu-item index="8">老男孩教育</el-menu-item>
            </el-menu>
        </div>

    </template>

    <template id="footer">
        <div class="footer">
            <el-menu class="el-menu-demo" mode="horizontal" background-color="black">
                <el-menu-item index="1">关于我们</el-menu-item>
                <el-menu-item index="2">联系我们</el-menu-item>
                <el-menu-item index="3">商业合作</el-menu-item>
                <el-menu-item index="4">帮助中心</el-menu-item>
                <el-menu-item index="5">意见反馈</el-menu-item>
                <el-menu-item index="6">新手指南</el-menu-item>
            </el-menu>
        </div>
    </template>
    
    <script>
        let pageHeader = {
            template: "#header",
            data() {
                return {
                    activeIndex: "1",
                }
            }
        };

        let pageFooter = {
            template: "#footer",
        };

        let App = {
            template: `
                <div>
                    <div>
                        <page-header></page-header>
                    </div>
                    <div>
                        <page-footer></page-footer>
                    </div>
                </div>
            `,
            components: {
                'page-header': pageHeader,
                'page-footer': pageFooter,
            }
        };

        new Vue({
            el: "#app",
            template: `<app></app>`,
            components: {
                'app': App,
            }
        })

    </script>
</body>
</html>
```

以上就是关于Vue组件的全部介绍。

##### 案例：组件实现Vue导航栏

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <style>

  </style>
  <script src="../statics/vue.min.js"></script>
  <style>
    .box {
      display: none;
    }
    .active {
      display: block;
    }
    li {
      list-style: none;
    }
    a {
      text-decoration: none;
    }
  </style>
</head>
<body>
  <div id="app">
    <global-component></global-component>
  </div>

  <script>

    Vue.component('global-component', {
      template: `
        <div>
          <div style="float: left; width: 100px; height: 30px;" v-for="(item, index) in books" v-on:mouseenter="mouseEnter(index)" v-on:mouseleave="mouseLeave">{{ item.title }}
            <span style="display: none" v-text="currentIndex"></span>
            <ul :class="{box: true, active: currentIndex===index}">
              <li><a :href="item.sourceAry.linkSrc">{{ item.sourceAry.name }}</a></li>
            </ul>
          </div>
        </div>
      `,
      data(){
        return {
          currentIndex: "",
          books: [
            { title: "学习", sourceAry: {id: 1, name: "教程", linkSrc: ""}},
            { title: "生态系统", sourceAry: {id: 2, name: "hhh", linkSrc: ""}},
            { title: "团队", sourceAry: {id: 3, name: 'hehehe', linkSrc: ""}},
            { title: "支持Vue", sourceAry: {id: 4, linkSrc: "", name: '一次性赞助'}},
          ],
          isBox: true
        }
      },
      methods: {
        mouseEnter: function (index) {
          this.currentIndex = index;
        },
        mouseLeave: function () {
          this.currentIndex = "";
        }
      }
    });

    new Vue({
      el: "#app",
    })
  </script>

</body>
</html>
```

##### 案例：轮播图升级版（使用全局组件实现轮播图）

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
    <app-carousel></app-carousel>
  </div>

  <script>
    Vue.component('app-carousel', {
      template: `
          <div class="container">
            <div class="img"><img :src="imageArray[currentIndex].imgSrc" v-on="{ mouseenter: mouseEnter, mouseleave: mouseLeave}"></div>
            <div class="circle">
              <span v-for="(circle, index) in circleCount" :class="{current: currentIndex===index}" v-on:click="changePic(index)"></span>
            </div>
          </div>
      `,
      data() {
        return {
          imageArray: [
            { id: 1, imgSrc: "../images/timg1.jpg" },
            { id: 2, imgSrc: "../images/timg2.jpg" },
            { id: 3, imgSrc: "../images/timg3.jpg" },
            { id: 4, imgSrc: "../images/timg4.jpg" },
            { id: 5, imgSrc: "../images/timg5.jpg" },
          ],
          currentIndex: 0,
          circleCount: 5
        }
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
    });

    new Vue({
      el: "#app"
    })
  </script>

</body>
</html>
```

##### 案例：轮播图升级版（使用局部组件实现轮播图）

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
  </div>

  <script>
    let Carousel = {
      template: `
          <div class="container">
            <div class="img"><img :src="imageArray[currentIndex].imgSrc" v-on="{ mouseenter: mouseEnter, mouseleave: mouseLeave}"></div>
            <div class="circle">
              <span v-for="(circle, index) in circleCount" :class="{current: currentIndex===index}" v-on:click="changePic(index)"></span>
            </div>
          </div>
      `,
      data() {
        return {
          imageArray: [
            { id: 1, imgSrc: "../images/timg1.jpg" },
            { id: 2, imgSrc: "../images/timg2.jpg" },
            { id: 3, imgSrc: "../images/timg3.jpg" },
            { id: 4, imgSrc: "../images/timg4.jpg" },
            { id: 5, imgSrc: "../images/timg5.jpg" },
          ],
          currentIndex: 0,
          circleCount: 5
        }
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
        changePic: function(index) {
          this.currentIndex = index;
        }
      },
      created(){
        this.setInterVal();
      }
    };

    let App = {
      template: `
        <div>
          <app-carousel></app-carousel>
        </div>
      `,
      components: {
        'app-carousel': Carousel
      }
    };

    new Vue({
      el: "#app",
      template: `<App></App>`,
      components: {
        App
      }
    })
  </script>

</body>
</html>
```

