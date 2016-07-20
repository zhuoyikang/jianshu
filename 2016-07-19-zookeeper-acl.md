[本文摘抄至-说说Zookeeper中的ACL](http://www.wuzesheng.com/?p=2438)

zookeeper中，node的ACL是没有继承关系的，是独立控制的。Zookeeper的ACL，可以从三个维度来理解：一是scheme; 二是user; 三是permission，通常表示为scheme:id:permissions, 下面从这三个方面分别来介绍：


# scheme

-------------------------------------------------------------------------------

scheme对应于采用哪种方案来进行权限管理，zookeeper实现了一个pluggable的ACL方案，可以通过扩展scheme，来扩展ACL的机制。zookeeper-3.4.4缺省支持下面几种scheme:

+ world: 它下面只有一个id, 叫anyone, world:anyone代表任何人，zookeeper中对所有人有权限的结点就是属于world:anyone的
+ auth: 它不需要id, 只要是通过authentication的user都有权限（zookeeper支持通过kerberos来进行authencation, 也支持username/password形式的authentication)
+ digest: 它对应的id为username:BASE64(SHA1(password))，它需要先通过username:password形式的authentication
+ ip: 它对应的id为客户机的IP地址，设置的时候可以设置一个ip段，比如ip:192.168.1.0/16, 表示匹配前16个bit的IP段
+ super: 在这种scheme情况下，对应的id拥有超级权限，可以做任何事情(cdrwa)
+ sasl: sasl的对应的id，是一个通过sasl authentication用户的id，zookeeper-3.4.4中的sasl authentication是通过kerberos来实现的，也就是说用户只有通过了kerberos认证，才能访问它有权限的node.(zookeeper-3.4.4的代码中提供此支持，不过缺省是没有开启的，需要配置才能启用)

# identity

-------------------------------------------------------------------------------

id与scheme是紧密相关的，具体的情况在上面介绍scheme的过程都已介绍，这里不再赘述。

# permission

zookeeper目前支持下面一些权限：

+ CREATE(c): 创建权限，可以在在当前node下创建child node
+ DELETE(d): 删除权限，可以删除当前的node
+ READ(r): 读权限，可以获取当前node的数据，可以list当前node所有的child nodes
+ WRITE(w): 写权限，可以向当前node写数据
+ ADMIN(a): 管理权限，可以设置当前node的permission


```
const PERM_READ   = 1;
const PERM_WRITE  = 2;
const PERM_CREATE = 4;
const PERM_DELETE = 8;
const PERM_ADMIN  = 16;
const PERM_ALL    = 31;
```


