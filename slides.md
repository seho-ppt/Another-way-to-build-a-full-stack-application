---
download: true
monaco: true
---

# 换一个方式构建全栈应用<uim-rocket class="text-5xl text-red-400 mx-2" />

沈昊

<div class="flex logo">
    <logos-vue class="w-13 h-13"/>
    <img class="ts ml-4" src="https://yinzhuoei-static.oss-cn-beijing.aliyuncs.com/typecho/2021/11/12/824774225724162/Typescript_logo_2020.svg.png"/>
    <img class="graphql ml-4" src="https://yinzhuoei-static.oss-cn-beijing.aliyuncs.com/typecho/2021/11/12/829802977106162/1637568778577.jpg"/>
    <img class="hasura ml-4" src="https://yinzhuoei-static.oss-cn-beijing.aliyuncs.com/typecho/2021/11/12/824774225724162/1637568537843.jpg"/>
</div>

<style>
img{
    width: 50px;
    height: 50px;
}
.logo{
    position: absolute;
    right: 30px;
    bottom: 30px;
}
</style>

---

# 分享概要

<ul>
    <li>从RestApi到GraphQL</li>
    <li>如何构建GraphQL</li>
    <li>Hasura是什么，它能改变应用程序开发方式吗？</li>
    <li>WebApi框架的众神崛起：Midway/TSRPC</li>
    <li>CQRS</li>
    <li>演示在Vue3下与TSRPC,Hasura的畅快开发体验</li>
    <li>反思: 为现有架构带来的不是新技术栈，而是新思想</li>
    <li>小结</li>
</ul>

<style>

li{
    margin-top: 15px;
}

</style>

---

# Restful API

从 2000 年开始 resetful 被定义以来，近 10 年受到无数开发者喜爱，已经成为 WEB API 的一个标准

相信下面的 API 设计已经刻在你的 DNA 里了

```js
// 获取所有的商品
GET http://www.store.com/products
```

<br/>

```js
// 获取某个id为12345的商品
GET http://www.store.com/products/12345
```

<br/>

```js
// 下单购买
POST http://www.store.com/orders
```

<br/>

```js
// 删除某个id为12345商品
DELETE http://www.store.com/products/12345
```

---

# Restful 多端点

使用 restful 我们通常都会访问多个端点来获取数据

<img src="/images/user-center.png" class="m-auto w-120 h-100" />

---

# Restful 返回冗余数据

实际上，我们有时候并不需要这么多数据

```js
GET http://www.store.com/user/1
```

<br/>

后端虽然可以很方便读取 user 表中的公共信息，但是大多数场景做不到 “我想即所得”

```json
{
  "nickname": "纸贵四公子",
  "age": 88,
  "avatar": "吴彦祖.png",
  "dream": "躺平...",
  "sign": "这是一个签名",
  "phone": "手机号",
  "homepage": "www.yinzhuoei.com"
}
```

<br/>

> 我其实只需要 nickname，age，avatar

---

# 探究

发生这种情况原因就是 “客户端获取数据的唯一方法就是返回固定数据结构的 API”

前端应用迭代迅速, Restful API 暴露的 2 大缺陷:

1. 获取冗余数据
2. 获取不足，N+1 问题（当一个 API 解决不了的需求，需要多个 API）

<br/>

> 要是能达到下面这种关系就完美了 😄

---

<img src="/images/ui-change.png" class="m-auto w-150 h-100" />

<div class="text-center mt-4">前端：我谢谢你啊，那我做的事情不是更多了么😠 ？</div>

---

其实做的事情和原来一样，我们可以使用 Graphql 来描述 API，甚至更简单了...<br/>

```graphql
query {
    User(){
        nickname，
        age，
        avatar,
        orders{
            title,
            time
        }
    }
}

```

<br/>

```json
{
  "nickname": "纸贵四公子",
  "age": 88,
  "avatar": "吴彦祖.png",
  "order": [
    {
      "title": "第一个订单",
      "time": "2021-1-1"
    }
  ]
}
```

---

# 为什么推崇 Graphql

它不仅仅解决了 restapi 的痛点，也更符合开发思维

无论是前端还是后端，在编写 graphql 语句的时候，都是对程序设计的重新推理。它并不像 restapi 一样，以前可能后端程序员更关心程序设计，而现在前端开发编写语句将会和后端一样，思路是带着程序走的，无疑 gql 的形式更好理解。

可以举一个简单例子

```js
GET http://www.store.com/products

```

vs

```graphql
query {
   Product(){
        title,
        time
    }
}
```
> 查询逻辑越复杂，gql的优势就会体现的更突出，而且gql还会更好地在前端复用

---

# “重构”个人中心

在我们之前的例子中，展示了淘宝个人中心页面元素，我们可以使用gql冰山一角的功能来完成这个简单的需求

```graphql
query {
   user(id: 10086){
        nickname,
        history{
            title
        }
        wallt{
            amount
        }
        collect{
            title
        }
        message{
            user_id
        }
        follow{
            user_id
        }
        likes{
            user_id
        }
    }
}
```

---
# 如何构建健壮的Graphql

<iframe src="https://codesandbox.io/embed/github/vaiokey/express-graphql/tree/master/server?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="express-graphql"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>