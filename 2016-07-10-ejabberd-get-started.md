ejabberd是成熟的开源xmpp服务器，最近要做相关开发，这里做做记录。

+ [github](https://github.com/processone/ejabberd)
+ [官网](https://docs.ejabberd.im/)

# 模块的在线更新

-------------------------------------------------------------------------------

当我们修改了模块的代码时,如何更新模块代码而不用重启Ejabberd服务器呢?
ejabberdctl 命令提供了一个update子命令, 如下:


```
ejabberdctl update mod_name
```

执行上述命令行即可动态跟新模块代码.
