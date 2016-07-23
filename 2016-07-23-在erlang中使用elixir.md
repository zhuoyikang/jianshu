Elixir出世了，我想尝鲜，于是乎你可以在rebar里面集成Elixir，这样就可以愉快的玩耍了。

新建rebar.config，添加以下内容：

```

{deps, [
        { rebar_elixir_plugin, ".*",
          {git, "git://github.com/yrashk/rebar_elixir_plugin"}}
       ]}.

{plugins, [rebar_elixir_compiler, rebar_exunit] }.

{deps, [
        { elixir, "1.2.4",
          {git, "git://github.com/elixir-lang/elixir", {tag, "1.2.4"}}}
       ]}.

{lib_dirs, [
            "deps/elixir/lib"
           ]}.


```

执行:

```
rebar get-deps
WARN:  Missing plugins: [rebar_elixir_compiler,rebar_exunit]
WARN:  Missing plugins: [rebar_elixir_compiler,rebar_exunit]
==> rebar_elixir_plugin (get-deps)
==> ecto_demo (get-deps)
```

这时候rebar会获取elixir/rebar_elixir_plugin两项依赖：

+ [`elixir`](https://github.com/elixir-lang/elixir)：elixir源码。
+ [`rebar_elixir_plugin`](https://github.com/yrashk/rebar_elixir_plugin)：是好心人开发的rebar插件，让你可以在rebar项目中自动编译elixir。

编译：

```
rebar compile
WARN:  Missing plugins: [rebar_elixir_compiler,rebar_exunit]
WARN:  Missing plugins: [rebar_elixir_compiler,rebar_exunit]
==> rebar_elixir_plugin (compile)
Compiled src/rebar_exunit.erl
Compiled src/rebar_elixir_compiler.erl
```


刚才`rebar get-deps` 和`rebar compile` 的时候都出现了警告：

```
WARN:  Missing plugins: [rebar_elixir_compiler,rebar_exunit]
WARN:  Missing plugins: [rebar_elixir_compiler,rebar_exunit]
```

我想是不是需要进入deps/elixir下去编译一下，于是：

```
cd deps/elixir
make clean test
cd -
```

再次编译，警告消失：

```
➜  ecto_demo rebar compile
==> rebar_elixir_plugin (pre_compile)
==> rebar_elixir_plugin (compile)
==> ecto_demo (pre_compile)
==> ecto_demo (compile)
```

这时候你就可以在src/文件夹下编写elixir代码了。

新建`src/test.ex`，一个纯粹的elixir代码。

```
defmodule Test do
  def good do
    IO.puts "nice"
  end
end

```

编译:

```
# rebar compile简写为rebar co
rebar co
```

测试执行成功。

```
erl -pa  deps/*/ebin/ ebin/  deps/elixir/lib/*/ebin/
2> 'Elixir.Test':good().
nice
ok
3>
```

增加测试用例 test/ecto_demo_tests.erl:

```
-module(ecto_demo_tests).
-compile([export_all]).

-include_lib("eunit/include/eunit.hrl").

add_test() ->
    'Elixir.Test':good(),
    ok.

```

执行测试成功，可以调用elixir的代码：
