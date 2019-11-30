---
title: Vue.js路由系统
date: 2018-10-28 15:23:00
tags:
 - Vue
 - Vue路由系统
 - Vue生态
categories: Vue
---

一切分离都是为了更好的结合，本文详细介绍了前后端分离架构之后，前端如何实现路由控制，即Vue路由系统。

<!--more-->

#### Vue路由系统

##### VueRouter实现原理

VueRouter的实现原理是根据监控锚点值的改变，从而不断修改组件内容来实现的，我们来试试不使用VueRouter，自己实现路由控制，如下代码：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <script src="../statics/vue-router.js"></script>
  <style>
    body {
      margin: 0;
    }
    .login {
      width: 100%;
      height: 1200px;
      background-color: #0FC57C;
    }
    .register {
      width: 100%;
      height: 1200px;
      background-color: yellow;
    }
    .home {
      width: 100%;
      height: 1200px;
      background-color: #2aabd2;
    }
    a {
      text-decoration: none;
    }
  </style>
</head>
<body>

    <div id="app">
      <a href="#/home">首页</a>
      <a href="#/login">登录</a>
      <a href="#/register">注册</a>
    </div>

    <script>
        let oDiv = document.getElementById("app");

        window.onhashchange = function () {
            console.log(location.hash);
            // vue-router的实现原理是根据锚点值的改变
            // 对页面进行切换
            switch (location.hash) {
                case "#/login":
                    oDiv.innerHTML = `<h3 class='login'>这是登录页面</h3>`;
                    break;
                case "#/register":
                    oDiv.innerHTML = `<h3 class='register'>这是注册页面</h3>`;
                    break;
                default:
                    oDiv.innerHTML = `<h3 class='home'>这是首页</h3>`;
                    break;
            }
        };
    </script>

</body>
</html>
```

可以看到，通过监控锚点值的改变，页面上面的内容可以跳转到我们需要显示的页面。

##### VueRouter安装使用

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <!--1. 引入vue-router-->
  <script src="../statics/vue-router.js"></script>
</head>
<body>

  <div id="app">

  </div>

  <script>
    let Home = {
      template: `
        <div>这是首页</div>
      `
    };

    let Login = {
      template: `
          <div>登录页面</div>
      `
    };

      let Register = {
        template: `
            <div>注册页面</div>
        `
      };

      let App = {
        template: `
          <div>
            <router-link to="/home">首页</router-link>
            <router-link to="/login">登录</router-link>
            <router-link to="/register">注册</router-link>

            <router-view></router-view>
          </div>
        `
      };

      // 2. 在vue中中使用vue-router对象
      Vue.use(VueRouter);

      // 3. 创建一个vue-router对象
      let router = new VueRouter({
        // 此处要特别注意是routes，不是routers
        routes: [
          {
            path: '/home',
            component: Home,
          },
            {
              path: "/login",
              component: Login
            },
            {
              path: "/register",
              component: Register,
            }
        ]
      });

      // 4. 在vue实例中使用新创建的vue-router对象
      new Vue({
        el: "#app",
        template: `<app></app>`,
        components: {
          "app": App,
        },
        router: router
      })
  </script>

</body>
</html>
```

##### VueRouter命名路由

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let Login = {
            template: `
                <div>登录页面</div>
            `
        };

        let Register = {
            template: `
                <div>注册页面</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link :to="{ name: 'login'}">登录</router-link>
                    <router-link :to="{ name: 'register'}">注册</router-link>

                    <router-view></router-view>
                </div>
            `
        };

        // 在vue中中使用vue-router对象
        Vue.use(VueRouter);

        // 创建一个vue-router对象
        let router = new VueRouter({
            // 此处要特别注意是routes，不是routers
            routes: [
                {
                    name: 'login',
                    path: "/login",
                    component: Login
                },
                {
                    name: 'register',
                    path: "/register",
                    component: Register,
                }
            ]
        });

        // 在vue实例中使用新创建的vue-router对象
        new Vue({
            el: "#app",
            template: `<app></app>`,
            components: {
                "app": App,
            },
            router: router
        })
    </script>

</body>
</html>
```

此外，还有另一种命名路由方式，如下所示:

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>

                    <router-view name="header"></router-view>
                    <router-view name="footer"></router-view>
                </div>
            `
        };

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: [
                {
                    path: '/',
                    components: {
                        header: {
                            template: `
                                <div>头部</div>
                            `
                        },
                        footer: {
                            template: `
                                <div>底部</div>
                            `
                        }
                    }
                }
            ]
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            router: router,
            components: {
                'app': App
            }
        })
    </script>

</body>
</html>
```

##### VueRouter路由中使用参数

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        // 常用的两种查询方式
        // xxx.html/user/1
        // xxx.html/user/id=1

        let userParams = {
            template: `
                <div>用户一信息</div>
            `
        };

        let userQuery = {
            template: `
                <div>用户二信息</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link :to="{ name: 'userParams', params: { userId: 1} }">用户一</router-link>
                    <router-link :to="{ name: 'userQuery', query: { userId: 2 } }">用户二</router-link>

                    <router-view></router-view>

                </div>
            `,
        };

        // 在vue中使用vue-router
        Vue.use(VueRouter);

        // 创建一个VueRouter对象
        let router = new VueRouter({
            routes: [
                {
                    name: 'userParams',
                    path: '/users/:userId',
                    component: userParams
                },
                {
                    name: 'userQuery',
                    path: '/users',
                    component: userQuery,
                }
            ]
        });

        // 在vue中使用vue-router
        new Vue({
            el: "#app",
            template: `<app></app>`,
            components: {
                'app': App,
            },
            router: router
        })
    </script>

</body>
</html>
```

##### VueRouter参数实现原理

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        // 常用的两种查询方式
        // xxx.html/user/1
        // xxx.html/user/id=1

        let userParams = {
            template: `
                <div>用户一信息</div>
            `,
            created () {
              console.log('this: ', this);
              console.log(this.$route);
              console.log(this.$route.params.userId);
            }
        };

        let userQuery = {
            template: `
                <div>用户二信息</div>
            `,
            created () {
              console.log(this.$route);
              console.log(this.$route.query.userId);
            }
        };

        let App = {
            template: `
                <div>
                    <router-link :to="{ name: 'userParams', params: { userId: 1} }">用户一</router-link>
                    <router-link :to="{ name: 'userQuery', query: { userId: 2 } }">用户二</router-link>

                    <router-view></router-view>

                </div>
            `,
        };

        // 在vue中使用vue-router
        Vue.use(VueRouter);

        // 创建一个VueRouter对象
        let router = new VueRouter({
            routes: [
                {
                    name: 'userParams',
                    path: '/users/:userId',
                    component: userParams
                },
                {
                    name: 'userQuery',
                    path: '/users',
                    component: userQuery,
                }
            ]
        });

        // 在vue中使用vue-router
        new Vue({
            el: "#app",
            template: `<app></app>`,
            components: {
                'app': App,
            },
            router: router,
          created () {
              console.log('this: ', this);
              console.log(this.$route);
            }
        })
    </script>

</body>
</html>
```

##### VueRouter子路由

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .home {
            width: 500px;
            height: 500px;
            background-color: #0FC57C;
        }
        .courses {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }

    </style>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let Home = {
            template: `
                <div class="home">这是首页</div>
            `
        };

        let Courses = {
            template: `
                <div class="courses">课程页面
                    <div class="details">
                        <router-link to="details">课程详情</router-link>
                        <router-link to="students">学员故事</router-link>

                        <router-view></router-view>
                    </div>
                </div>
            `
        };

        let Details = {
            template: `
                <div>课程详情页</div>
            `
        };

        let Students = {
            template: `
                <div>学员故事</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>
                    <router-link to="/courses">课程页面</router-link>

                    <router-view></router-view>
                </div>
            `
        };

        let routes = [
            {
                path: '/',
                component: Home
            },
            {
                path: '/courses',
                component: Courses,
                children: [
                    {
                        path: 'details',
                        component: Details,
                    },
                    {
                        path: 'students',
                        component: Students
                    }
                ]
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes,
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            router: router,
            components: {
                'app': App,
            }
        })

    </script>

</body>
</html>
```

##### VueRouter子路由append

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .home {
            width: 500px;
            height: 500px;
            background-color: #0FC57C;
        }
        .courses {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }

    </style>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let Home = {
            template: `
                <div class="home">这是首页</div>
            `
        };

        let Courses = {
            template: `
                <div class="courses">课程页面
                    <div class="details">
                        <router-link to="details" append>课程详情</router-link>
                        <router-link to="students">学员故事</router-link>

                        <router-view></router-view>
                    </div>
                </div>
            `
        };

        let Details = {
            template: `
                <div>课程详情页</div>
            `
        };

        let Students = {
            template: `
                <div>学员故事</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>
                    <router-link to="/courses">课程页面</router-link>

                    <router-view></router-view>
                </div>
            `
        };

        let routes = [
            {
                path: '/',
                component: Home
            },
            {
                path: '/courses',
                component: Courses,
                children: [
                    {
                        path: 'details',
                        component: Details,
                    },
                    {
                        path: 'students',
                        component: Students
                    }
                ]
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes,
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            router: router,
            components: {
                'app': App,
            }
        })

    </script>

</body>
</html>
```

使用以上方式，路径是写死在属性中的，所以子路径会不断的append到最后，导致访问不到相应页面。可以采用另一种，动态绑定属性，如下代码所示：

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <script src="../statics/vue-router.js"></script>
  <style>
    body {
      margin: 0;
    }
    .all {
      width: 500px;
      height: 500px;
    }
    .home {
      background-color: #2aabd2;
    }
    .login {
      background-color: #7acce4;
    }
    .register {
      background-color: #4cae4c;
    }
    .courses {
      background-color: #0FC57C;
    }
  </style>
</head>
<body>

  <div id="app"></div>

  <script>
    Vue.use(VueRouter);

    let Home = {
      template: `
        <div class="home all">
          <h1>这是首页页面</h1>
        </div>
      `
    };

    let Login = {
      template: `
        <div class="login all">
          <h1>这是登录页面</h1>
        </div>
      `
    };

    let Register = {
      template: `
        <div class="register all">
          <h1>这是注册页面</h1>
        </div>
      `
    };

    let Courses = {
      template: `
        <div class="courses all">
          <h1>这是课程页面</h1>
          <router-link :to="{ name: 'lightcourses' }" append>轻课</router-link>
          <router-link :to="{ name: 'degreecourses' }">学位课</router-link>

          <router-view><router-view>
        </div>
      `
    };

    let LightCourses = {
      template: `
        <div>
          <h1>这是学位课程页面</h1>
        </div>
      `
    };

    let DegreeCourses = {
      template: `
        <div>
          <h1>这是轻课课程页面</h1>
        </div>
      `
    };

    let App = {
      template: `
        <div>
          <router-link :to="{ name: 'home' }">首页</router-link>
          <router-link :to="{ name: 'login' }">登录</router-link>
          <router-link :to="{ name: 'register' }">注册</router-link>
          <router-link :to="{ name: 'courses' }">课程</router-link>

          <router-view><router-view>
        </div>
      `,
    };

    let routes = [
      {
        name: 'home',
        path: '/home',
        component: Home,
      },
      {
        name: 'login',
        path: '/login',
        component: Login,
      },
      {
        name: 'register',
        path: '/register',
        component: Register
      },
      {
        name: 'courses',
        path: '/courses',
        component: Courses,
        children: [
          {
            name: 'lightcourses',
            path: 'lightcourses',
            component: LightCourses
          },
          {
            name: 'degreecourses',
            path: 'degreecourses',
            component: DegreeCourses
          }
        ]
      }
    ];

    let router = new VueRouter({
      routes: routes,
      mode: 'history',
    });

    new Vue({
      el: "#app",
      template: `<App></App>`,
      components: {
        App,
      },
      router: router,
    })
  </script>

</body>
</html>
```

注意，必须使用name查找组件和路径的对应关系，而不能使用path。

##### VueRouter子路由非append

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .home {
            width: 500px;
            height: 500px;
            background-color: #0FC57C;
        }
        .courses {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }

    </style>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let Home = {
            template: `
                <div class="home">这是首页</div>
            `
        };

        let Courses = {
            template: `
                <div class="courses">课程页面
                    <div class="details">
                        <router-link to="/courses/details">课程详情</router-link>
                        <router-link to="/courses/students">学员故事</router-link>

                        <router-view></router-view>
                    </div>
                </div>
            `
        };

        let Details = {
            template: `
                <div>课程详情页</div>
            `
        };

        let Students = {
            template: `
                <div>学员故事</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>
                    <router-link to="/courses">课程页面</router-link>

                    <router-view></router-view>
                </div>
            `
        };

        let routes = [
            {
                path: '/',
                component: Home
            },
            {
                path: '/courses',
                component: Courses,
                children: [
                    {
                        path: '/courses/details',
                        component: Details,
                    },
                    {
                        path: '/courses/students',
                        component: Students
                    }
                ]
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes,
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            router: router,
            components: {
                'app': App,
            }
        })

    </script>

</body>
</html>
```

##### VueRouter路由重定向redirect

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .home {
            width: 500px;
            height: 500px;
            background-color: #0FC57C;
        }
        .login {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }

    </style>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let Home = {
            template: `
                <div class="home">这是首页</div>
            `
        };

        let Login = {
            template: `
                <div class="login">这是登录页面</div>
            `
        };

        let Details = {
            template: `
                <div>课程详情页</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>
                    <router-link to="/login">登录页面</router-link>
                    <router-link to="/details">课程详情页</router-link>

                    <router-view></router-view>
                </div>
            `
        };

        let routes = [
            {
                path: '/',
                component: Home
            },
            {
                path: '/login',
                component: Login,
            },
            {
                path: '/details',
                redirect: '/login',
                component: Details
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes,
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            router: router,
            components: {
                'app': App,
            }
        })

    </script>

</body>
</html>
```

##### VueRouter手动路由

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .home {
            width: 500px;
            height: 500px;
            background-color: #0FC57C;
        }
        .details {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }
    </style>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let Home = {
            template: `
                <div>
                    <div class="home">这是首页</div>
                </div>
            `,
        };

        let Details = {
            template: `
                <div class="details">课程详情页</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>
                    <button @click="myClick">点击跳转到课程详情页面</button>

                    <router-view></router-view>
                </div>
            `,
            methods: {
                myClick: function () {
                    this.$router.push('/details');
                }
            }
        };

        let routes = [
            {
                path: '/',
                component: Home
            },
            {
                path: '/details',
                component: Details
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes,
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            router: router,
            components: {
                'app': App,
            }
        })

    </script>

</body>
</html>
```

##### VueRouter路由钩子

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .home {
            width: 500px;
            height: 500px;
            background-color: #0FC57C;
        }
        .login {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }
        .user {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }

    </style>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let homePage = {
            template: `
                <div class="home"><h3>这是首页</h3></div>
            `
        };

        let loginPage = {
            template: `
                <div class="login"><h3>这是登录页面</h3></div>
            `
        };

        let userPage = {
            template: `
                <div class="user"><h3>这是用户管理页面</h3></div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>
                    <router-link to="/login">登录</router-link>
                    <router-link to="userinfo">用户</router-link>

                    <router-view></router-view>
                </div>
            `
        };

        let routes = [
            {
                path: '/',
                component: homePage
            },
            {
                path: '/login',
                component: loginPage,
            },
            {
                path: '/userinfo',
                component: userPage,
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes,
        });

        router.beforeEach(function (to, from, next) {
            // 如果next的参数是具体的路径，则跳转到该路径
            // 如果next没有被传入参数，则什么都不做，保持原来不变
            // 如果next的参数是一个error实例，跳转终止
            next();
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            components: {
                'app': App
            },
            router: router,
        })
    </script>

</body>
</html>
```

##### VueRouter在路径中去掉#号

在创建路由实例时，我们可以新增一个属性，该属性用来去掉在路径中显示#号，它的属性名为mode，属性值为'history'。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
  <script src="../statics/vue.min.js"></script>
  <script src="../statics/vue-router.js"></script>
  <style>
    body {
      margin: 0;
    }
    .box {
      line-height: 100px;
      text-align: center;
    }
    .home {
      background-color: #2aabd2;
      width: 500px;
      height: 500px;
    }
    .login {
      background-color: #2b9b2d;
      width: 500px;
      height: 500px;
    }
    .register {
      background-color: #4cae4c;
      width: 500px;
      height: 500px;
    }
  </style>
</head>
<body>

  <div id="app"></div>

  <script>
    Vue.use(VueRouter);

    let Home = {
      template: `
        <div class="home">
          <h1>这是首页页面</h1>
        </div>
      `
    };

    let Login = {
      template: `
        <div class="login">
          <h1>这是登录页面</h1>
        </div>
      `
    };

    let Register = {
      template: `
        <div class="register">
          <h1>这是注册页面</h1>
        </div>
      `
    };

    let App = {
      template: `
        <div>
          <router-link :to="{ name: 'home' }">首页</router-link>
          <router-link :to="{ name: 'login' }">登录</router-link>
          <router-link :to="{ name: 'register' }">注册</router-link>

          <router-view><router-view>
        </div>
      `,
    };

    let routes = [
      {
        name: 'home',
        path: '/home',
        component: Home,
      },
      {
        name: 'login',
        path: '/login',
        component: Login,
      },
      {
        name: 'register',
        path: '/register',
        component: Register
      }
    ];

    let router = new VueRouter({
      routes: routes,
      mode: 'history',
    });

    new Vue({
      el: "#app",
      template: `<App></App>`,
      components: {
        App,
      },
      router: router,
    })
  </script>

</body>
</html>
```

##### VueRouter路由钩子实现登录认证

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
        .home {
            width: 500px;
            height: 500px;
            background-color: #0FC57C;
        }
        .login {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }
        .user {
            width: 500px;
            height: 500px;
            background-color: #5b80b2;
        }

    </style>
    <script src="../statics/vue.min.js"></script>
    <!--1. 引入vue-router-->
    <script src="../statics/vue-router.js"></script>
</head>
<body>

    <div id="app"></div>

    <script>
        let homePage = {
            template: `
                <div class="home"><h3>这是首页</h3></div>
            `
        };

        let loginPage = {
            template: `
                <div class="login"><h3>这是登录页面</h3></div>
            `
        };

        let userPage = {
            template: `
                <div class="user">
                    <router-link to="/userinfo/userInt">自我介绍</router-link>
                    <router-link to="/userinfo/userStudy">学习中心</router-link>

                    <router-view></router-view>
                    <h3>这是用户管理页面</h3>
                </div>
            `
        };

        let userIntr = {
            template: `
                <div>自我介绍页面</div>
            `
        };

        let userStudy = {
            template: `
                <div>学习中心</div>
            `
        };

        let App = {
            template: `
                <div>
                    <router-link to="/">首页</router-link>
                    <router-link to="/login">登录</router-link>
                    <router-link to="userinfo">用户</router-link>

                    <router-view></router-view>
                </div>
            `
        };

        let routes = [
            {
                path: '/',
                component: homePage
            },
            {
                path: '/login',
                component: loginPage,
            },
            {
                path: '/userinfo',
                // meta对象里面的属性可以自定义, 都会加载到route实例中
                meta: { required_login: true },
                component: userPage,
                children: [
                    {
                        path: '/userinfo/userIntr',
                        meta: { required_login: true },
                        component: userIntr
                    },
                    {
                        path: '/userinfo/userStudy',
                        meta: { required_login: true },
                        component: userStudy,
                    }
                ]
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes,
        });

        router.beforeEach(function (to, from, next) {
            // 如果next的参数是具体的路径，则跳转到该路径
            // 如果next没有被传入参数，则什么都不做，保持原来不变
            // 如果next的参数是一个error实例，跳转终止
            if ( to.meta.required_login ) {
                next('/login');
            } else {
                next();
            }
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            components: {
                'app': App
            },
            router: router,
        })
    </script>

</body>
</html>
```

##### VueRouter实现路飞首页轮播图

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script src="../statics/vue.min.js"></script>
    <script src="../statics/vue-router.js"></script>
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
        .banner_img {
            width: 100%;
            height: 480px;
        }

    </style>
</head>
<body>

    <div id="app">

    </div>

    <template id="header">
        <div class="header">
            <img src="https://www.luffycity.com/static/img/head-logo.a7cedf3.svg"/>
            <el-menu :default-active="activeIndex" class="el-menu-demo" mode="horizontal" :router="true">
                <el-menu-item v-for="( item, index ) in headerLinks" :index="item.path" :key="index">{{ item.name }}</el-menu-item>
            </el-menu>
        </div>
    </template>

    <template id="homeimg">
        <el-carousel indicator-position="outside" height="480px">
            <el-carousel-item v-for="item in homePageImgArray" :key="item">
                <img :src="item.sources" class="banner_img"/>
            </el-carousel-item>
        </el-carousel>
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
        // 如果需要动态绑定属性，必须加上冒号，否则属性后面的值会被当成固定的

        let Home = {
            template: "#homeimg",
            data() {
                return {
                    homePageImgArray: [
                        { sources: 'https://hcdn1.luffycity.com/static/frontend/index/banner1(4)_1539945492.0492468.png', id: 1 },
                        { sources: 'https://hcdn1.luffycity.com/static/frontend/index/%E9%AA%91%E5%A3%AB(1)_1539945488.713867.png', id: 2 },
                        { sources: 'https://hcdn1.luffycity.com/static/frontend/index/banner%203_1538021864.9051478.png', id: 3 },
                        { sources: 'https://hcdn1.luffycity.com/static/frontend/index/banner11_1538122470.2779157.png', id: 4 },
                        { sources: 'https://hcdn1.luffycity.com/static/frontend/index/home-banner4_1535545832.4715614.png', id: 5 },
                    ]
                }
            }
        };

        let freeCourses = {
            template: `
                <div>这是免费课程页面</div>
            `
        };

        let lightCourses = {
            template: `
                <div>这是轻课页面</div>
            `
        };

        let degreeCourses = {
            template: `
                <div>这是学位课程页面</div>
            `
        };

        let Questions = {
            template: `
                <div>这是智能题库页面</div>
            `
        };

        let openCourses = {
            template: `
                <div>这是公开课页面</div>
            `
        };

        let innerCourses = {
            template: `
                <div>这是内部教材页面</div>
            `
        };

        let pageHeader = {
            template: "#header",
            data() {
                return {
                    activeIndex: "1",
                    headerLinks: [
                        { path: '/', name: "首页" },
                        { path: '/free_courses', name: '免费课程'},
                        { path: '/light_courses', name: '轻课'},
                        { path: '/degree_courses', name: '学位课程'},
                        { path: '/questions', name: '智能题库'},
                        { path: '/open_courses', name: '公开课'},
                        { path: '/inner_courses', name: '内部教材'}
                    ],
                    footerLinks: []
                }
            }
        };

        let pageFooter = {
            template: "#footer",
        };

        let App = {
            template: `
                <div id="app">
                    <div>
                        <page-header></page-header>
                    </div>
                    <div>
                        <page-footer></page-footer>
                    </div>
                    <router-view></<router<-view>
                </div>
            `,
            components: {
                'page-header': pageHeader,
                'page-footer': pageFooter,
            }
        };

        let routes = [
            {
                path: '/',
                component: Home
            },
            {
                path: '/free_courses',
                component: freeCourses
            },
            {
                path: '/light_courses',
                component: lightCourses
            },
            {
                path: '/degree_courses',
                component: degreeCourses
            },
            {
                path: '/questions',
                component: Questions
            },
            {
                path: '/open_courses',
                component: openCourses
            },
            {
                path: '/inner_courses',
                component: innerCourses
            }
        ];

        Vue.use(VueRouter);

        let router = new VueRouter({
            routes: routes
        });

        new Vue({
            el: "#app",
            template: `<app></app>`,
            components: {
                'app': App,
            },
            router: router,
            data: {

            }
        })

    </script>
</body>
</html>
```

以上就是关于VueRouter的介绍。