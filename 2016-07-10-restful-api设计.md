总是听说Restful，但是一直没有具体的研究，今天把这个事情办了。

# 什么是Rest和Restful

-------------------------------------------------------------------------------

简单的讲，它是：一种设计风格，提供了设计原则和约束条件，而不是架构。而满足这些约束条件和原则的应用程序或设计就是Restful架构或服务。

Restful的API设计强调以**资源为中心**而不是以动作为中心，所有的API应该都是基于名词的描述，而非动词，而动作通过http方法来描述。

> URL定位资源，用HTTP动词（GET,POST,DELETE,DETC）描述操作。


# 非Restful的设计如下

-------------------------------------------------------------------------------

```
# 创建房间
post /create_room
params: room_name
params: room_size

# 更新房间
post /update_room
params: room_name
params: room_size

# 获取房间信息
get /get_room
params: room_name

# 删除房间
post /delete_room
params: room_name
```

这既是以动作为中心，很多API设计者都使用这样的方式来设计API。

# Restful的接口设计

-------------------------------------------------------------------------------

而Restful的接口设计出来是这样的：

```
# 创建房间
post /room
params: room_name
params: room_size

# 更新房间
put /room
params: room_name
params: room_size

# 获取房间信息
get /room
params: room_name

# 删除房间
delete /room
params: room_name
```

主要有两个规则：

1. uri设计只能是名词。
2. 动作通过http方法来描述。

下面是一些例子


```
GET /zoos：列出所有动物园
POST /zoos：新建一个动物园
GET /zoos/ID：获取某个指定动物园的信息
PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/ID：删除某个动物园
GET /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```


# Restful有什么好处？

-------------------------------------------------------------------------------

比较好的解释:

1. 统一的风格能够让各方更加便利的进行交互，也带来了更好的兼容性（这是一个所有遵循一定规范所带来的共通的好处，就像大家都说普通话，交流起来多方便啊）
2. 对资源的操作正好对应相应的HTTP动作（GET、POST、PUT、DELETE），而这些动作正好可以满足我们对资源状态进行操作的需要，也就是说想对资源状态进行什么样的操作就选择什么样的动作，而这些动作又是HTTP协议本身提供的，多么和谐自然啊（就是题主的摘录内容）
3. 请求所造成的影响明确，或者说副作用明确，比如GET肯定是安全的，PUT和DELETE肯定是幂等得，POST肯定是不安全的（这里的肯定是建立在API设计完全遵循“Restful风格”基础上的）
4. 良好的符合“Restful风格”的URI设计，可以让Web接口的功能和整体结构更加清晰，仅仅通过URI就能方便的推测出来接口是做什么的，以及多个资源之间关联性
5. 利用HTTP内容协商（content negotiation）实现资源的多重表述，比如请求方可以把自己需要的格式放到头信息的Accept字段中表述（如Accept: text/json），这样同样一个URI就可以输出多种格式而不再需要在URI里面特别加上一个type=json的参数了
6. 客户端、代理服务器等可以根据HTTP协议规范进行相应的额外处理，比如Cache

# 状态码规则 （Status Codes）

-------------------------------------------------------------------------------

> 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
> 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
> 202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
> 204 NO CONTENT - [DELETE]：用户删除数据成功。
> 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
> 401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
> 403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
> 404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
> 406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
> 410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
> 422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
> 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。


# 参考文章

+ [理解Restful架构](http://www.ruanyifeng.com/blog/2011/09/Restful.html)
+ [Restful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/Restful_api.html)
+ [不明白php的Restful，有什么实际用处吗](https://segmentfault.com/q/1010000000319475)
+ [知乎-Rest 架构该怎么生动地理解？](https://www.zhihu.com/question/27785028)
+ [从消费者的角度评估Rest的价值](http://hippoom.github.io/blogs/value-of-hypermedia-from-client-perspective.html)
