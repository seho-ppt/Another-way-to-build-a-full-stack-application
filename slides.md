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

> 查询逻辑越复杂，gql 的优势就会体现的更突出，而且 gql 还会更好地在前端复用

---

# “重构”个人中心

在我们之前的例子中，展示了淘宝个人中心页面元素，我们可以使用 gql 冰山一角的功能来完成这个简单的需求

```graphql
query {
  user(id: 10086) {
    nickname
    history {
      title
    }
    wallt {
      amount
    }
    collect {
      title
    }
    message {
      user_id
    }
    follow {
      user_id
    }
    likes {
      user_id
    }
  }
}
```

---

# 如何构建健壮的 Graphql

<iframe src="https://codesandbox.io/embed/serene-dhawan-o5s8w?fontsize=14&hidenavigation=1&theme=dark"
     style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;"
     title="serene-dhawan-o5s8w"
     allow="accelerometer; ambient-light-sensor; camera; encrypted-media; geolocation; gyroscope; hid; microphone; midi; payment; usb; vr; xr-spatial-tracking"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

<br/>

---

# 构建 graphql 的成本比 restful 低么？

> 需要定义大量的 type 和 resolvers，做复杂查询仍然很累

如果我们需要过滤，分页，模糊搜索，批量插入，更新，删除等, 就得付出大量的心智维护各种各样的 Type

> 如果有一款引擎接管我们的 graphql + 数据库，然后暴露给客户端，那么构建查询结果的成本基本等于 0

Hasura 就是一个优秀的 Graphql 引擎，所以我们新的工作流程就是:

<img src="/images/add-hasura.png" class="m-auto w-260 h-60" />

---

# hasura: 一睹为快

数据库引擎: postgres

<video autoplay loop controls>
  <source src="/video/screencast-localhost_8080-2021.11.24-17_00_36.mp4" type="video/mp4">
</video>

<style>
  video{
    width: 90vw;
    height: 60vh;
  }
</style>

---

# 浅析 hasura

<br/>

<img src="/images/hasura-content.png" class="m-auto w-200 h-80" />

---

# 细细一品

hasura 强归强，但是并没有解决我们的问题，即“需求变更时”，后端代码尽可能不参与更改

hasura 的 mutation 和 query 功能，能满足绝大部分程序的需要，但是对于复杂业务逻辑来说，单纯的 mutation 已经满足不了我们了:

```graphql
mutation MyMutation {
  insert_products(
    objects: { desc: "介绍", price: "12", title: "产品第一", push_user_id: 1 }
  ) {
    returning {
      id
    }
  }
}
```

> 比如说我们需要新增一个产品，并且给用户增加积分且同步给第三方推送服务，这种需求我们优先考虑 Action 实现

所以我们需要将“读(hasura query)”和“写(hasura action)”分离，各司其职。

hasura 提供了 action 功能可以让开发者编写复杂的自定义逻辑，我们可以使用 action 去实现诸如删除/下单/提交等复杂业务逻辑

---

<img src="/images/hasura-actions.png" class="m-auto" />

<br/>

> action 的回调实现我们可以用 serverless 函数或者熟悉的 restapi

> 图来源于https://hasura.io/docs/latest/graphql/core/actions/index.html

---

# CQRS

CQRS 是“命令查询责任分离”（Command Query Responsibility Segregation）的缩写

在刚刚的章节中，大家或许对 CQRS 有了一定的了解，下面我们可以看一下 CQRS 的原图，帮助加深理解

<img src="/images/cqrs.png" class="m-auto w-100 h-80">

---

# 捋一捋 CQRS

深入理解

CQRS 的终极奥义并不是读写分离，而是读操作能够多元化，能够实现数据的多重表示。

Query Model 和 Command Model 是有区别的，前者可以支持各种有效的读模式，比如说我们在应用中看到的详情/列表/搜索功能（或者说不局限于物理实现，比如说使用 Redis😊 ）；

然后我们可以通过消息队列或者其他方式将 Command Model 中的变更同步到 Query Model 中

CQRS 并没有严格规定同步的机制，你不仅可以通过消息队列来同步，也可以同步的更新 2 个模型，我们的目的就是保证写操作和读操作一致即可。

### 什么样子的场景适合用微服务？

<br/>

1. 与大多数应用程序一样，我们通常不会区分读和写，但是当 2 个操作不能满足业务需求，或者想在现有情况下对程序减负，那么此时引进 CQRS 会很有必要。
2. 我们如果对 2 个操作进行分离，那么读操作就可以拥有自己的数据库/缓存，那么反之，写操作的变更/伸缩也不会受制于读操作

---

# 构建客户端和服务端

我们需要选择一个易于演示的全栈框架

市面上的 Typescript 的真正意义上的全栈框架其实非常少，例如 midway.js, uniapp (unicloud)，tsrpc...

前两者虽说可以在一个平台上实现 2 种端的代码，诸如此类:

```ts
// server.js
const main = () => {
  return ["1", "2", "3"];
};
```

```html
// app.vue //
这里仅仅是举一个例子，证明在客户端直接使用远端数据是多么愚蠢，和10几年前的前后端不分离没有太大区别
<search-db> </search-db>
```

而且前两者在全栈领域表现的不是特别优秀，没有彻底解决 JS 全栈开发的痛点:

1. 没有利用 TypeScript 去增强应用
2. 全栈应用应该是一体的，在本地开发需要更加方便
3. 通信能力单一

---

# TSRPC

死忠粉在此，写全栈，我只用 TSRPC 🐶

我们在构建之前，确认我们需要完成的目标:

1. 构造一个客户端页面（产品列表，新增产品）
2. 构造一个服务端并且带有一个简单 API
3. 将 API 作为 Action 交给 Hasura
4. 完善客户端代码，体验 Hasura+TSRPC 带来的乐趣

<br/>

> 在 ppt 讲述过程中，我只会给大家展示部分核心流程，具体的代码预览会放在后面的环节，最终也会把 demo 链接留下

---

# 编写 API 的 Protocols

> TSRPC 定义的 protocols 可以在前后端通用，即无需定义 2 次就可以使得 API 传输类型严格约束

```ts
export interface ReqAddProduct {
  /** 产品标题 */
  title: string;
  /** 产品介绍 */
  desc: string;
  /** 产品价格 */
  price: string;
  /** 推送产品的用户 */
  pushUserId: number;
}

export interface ResAddProduct {
  /** 服务端内容创建时间 */
  id?: number;
}
```

---

# 编写 API 的逻辑

ORM 框架：TypeOrm

```ts {1-5|6|7|all}
import { ApiCall } from "tsrpc";
import {
  ReqAddProduct,
  ResAddProduct,
} from "../shared/protocols/PtlAddProduct";
import { Products } from "../entities/Products";
import { getRepository } from "typeorm";

export async function ApiAddProduct(
  call: ApiCall<ReqAddProduct, ResAddProduct>
) {
  const res = await getRepository(Products).save(call.req);
  call.succ({
    id: res.id,
  });
}
```

<br/>

<div v-click="4">
  也许你会问：沈公子你这个逻辑不严谨啊，为啥没有判断传入的参数都敢直接入库呀😠 ？
</div>

<br/>

<div v-click="5">
  事实上，得益于TS，TSRPC不仅支持我们在客户端传入参数时做参数校验，而且还会在传输到后端函数之前做一次 “运行时校验”
</div>

---

# TS 的运行时校验

非分享重点，简单概括一下

JS 是解释性语言，而 TS 并非是一门独立的语言，我们如果想借助 TS 类型系统做一些数据的校验，我们不可能把 TS 的包塞到工程中，这显然是一个笑话。
所以我们通常会有几种方案:

1. JSON SCHEMA
2. 基于装饰器/或者其他库

但是很少有一种方案，让后端定义一个 TS Interface 就搞定了运行时校验，而且支持复杂类型，TSRPC 就做到了。
那么复杂到什么情况呢 🤔️ ？

<div style="height: 25vh;overflow: scroll;">

```ts
interface Colorful {
  color: string;
}
interface Circle {
  radius: number;
}
export interface Info {
  title: "小乔的诶吃五（h5）" | "测试双煞" | "测试姐";
  desc: Record<string, string>;
  price: string | number;
  other: Colorful & Circle;
}
```

</div>



---

# 完善 API

别忘记了我呀，我是 hasura

为了实现 CQRS，我们本应该写完 api，就可以让客户端去调用了，届时客户端读取操作会通过 graphql 去 hasura 获取，写操作直接调用我们刚刚的 API，看样子十分完美...

但是当我们回想现有应用程序的时候，权限永远是最重要的；如果不借助 hasura 的权限系统，就要在 Action API 中自己重新实现一套权限系统，否则我们的 API 将没有任何安全可言。

重新实现一套权限系统，这显然不可能的 😠 。所以这也就是为什么我们非要把 Action 交给 hasura 管理

我们在之前就已经预览了 hasura-action 的图例，我们这里再加深一下大家对 hasura action 的理解。

<img src="/images/hasura-action-handler.png">

---

# 将 Action 添加到 hasura 中

<div style="width: 100%;height: 400px;overflow: auto;">
  <img class="m-auto" src="/images/add-action.jpg">
</div>

---

# 编写客户端代码

客户端代码需要构建一个产品列表和一个新增产品页面

我们其实也可以在 VSCode 借助插件快速的编写 gql 语句 (也可以在 hasura 控制台可视化的组装 gql 语句)：

<img src="/images/graphql-code.gif" class="m-auto">

---

# 编写完整的 gql

<div style="height: 40vh;overflow: scroll;">

```ts
export const GET_PRODUCTS = gql`
  query getProducts(
    $limit: Int = 10
    $offset: Int
    $title: String
    $desc: String
    $minPrice: Int
  ) {
    products(
      where: {
        title: { _like: $title }
        desc: { _like: $desc }
        price: { _gte: $minPrice }
      }
      limit: $limit
      offset: $offset
      order_by: { created_at: desc }
    ) {
      id
      title
      desc
      price
      push_user {
        id
        phone
      }
    }
    products_aggregate(
      where: {
        title: { _like: $title }
        desc: { _like: $desc }
        price: { _gte: $minPrice }
      }
    ) {
      aggregate {
        count
      }
    }
  }
`;
```

</div>
<br/>

- 支持对 title，desc 模糊查询
- 支持对产品的最小价格的查询

---

# 前端核心代码

<div style="height: 60vh;overflow: scroll;">

```ts {1-7|8-13|all}
// 构造查询
const {
  result: product,
  loading: productLoading,
  fetchMore,
} = useQuery<
  {
    products: FormData[] | null;
    products_aggregate: { aggregate: { count: number } };
  },
  Partial<FormState>
>(GET_PRODUCTS, {
  limit: formState.value.limit,
  offset: formState.value.offset,
});

watch(product, () => {
  formData.value = product.value!.products;
  pagination.value.total = product.value!.products_aggregate.aggregate.count;
});

const getList = () => {
  const { title, minPrice, desc, limit } = formState.value;
  searchState.value = {
    title: title !== "" ? `%${title}%` : null,
    desc: desc !== "" ? `%${desc}%` : null,
    minPrice: minPrice ? Number(minPrice) : null,
    limit,
    offset: (pagination.value.current - 1) * limit,
  };
  fetchMore({
    variables: searchState.value,
  });
};
```

</div>

---

# 编写 gql mutation 语句

这个 mutation 语句就是我们的 action api

```ts
export const ADD_PRODUCT = gql`
  mutation addProduct($params: AddProductInput!) {
    addProduct(params: $params) {
      id
    }
  }
`;
```

前端只需要使用对应的 hook 来构造这个 mutation 即可

```ts
const {
  mutate: addProductMutate,
  loading: addProductMutateLoading,
  onDone: onAddProductDone,
} = useMutation<{ id: string }, { params: AddFormState }>(ADD_PRODUCT);
```

useMutation 和 useQuery 函数一样，都可以传入返回值和参数的泛型

```ts
function useMutation<TResult, TVariables>
```

---

# 页面使用 API 完成添加操作

```ts
// 添加产品
const handleAdd = () => {
  addProductMutate({
    params: {
      ...addFormState.value,
      price: Number(addFormState.value.price),
    },
  });
};

// 添加之后的回调
onAddProductDone(() => {
  addFormRef.value.resetFields();
  pagination.value.current = 1;
  addVisible.value = false;
  getList();
});
```

<br/>

开始代码演示～～～

---

# 一点反思

有待改进的问题

1. 如果你看到这里，你可能会疑惑；graphql 和 hasura 好是好，方便了前后端，大大节省了开发时间和协调工作。但是后端开发同学当工作量大大减轻，剩下来的时间干什么，这就是后端架构的意义了。可能会去进行系统设计，研究领域知识等；这些在以前业务中占比很少的部分，在这样前后端协作的方式中，是需要付出大量时间实践和沉淀的。

2. 表面上看前端包揽了设计+前端交互+后端查询代码编写（希望别锤死我 😄 ）但是实际上，前端做的事情比以前更少了；对于设计而言太多的 UI 库可以供我们使用，原子化 css 的发展也帮助了前端工程师进一步减轻负担，后端查询代码编写过程中也更能帮助前端程序员对业务的理解。

3. 近几年 serverless 的突起，更代表了前端正在随着浪潮被冲向后端领域，而后端势必会朝着更深层次发展...

---

# 总结一下

帮助大家消化一下

- 我们今天初识了 Hasura 这个优秀的引擎
- 然后对 graphql 的了解更深一点了
- 其次在我们构建 demo 的时候，又简单的了解了 tsrpc 这款全栈框架
- 简单了解了 CQRS 架构

随后我们再放一张我们应用构建完毕的图例

---

# 完结

<div style="height: 90vh; overflow: scroll;">
<img src="/images/core.png">
</div>
