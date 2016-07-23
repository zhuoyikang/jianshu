Erlangy由于社区落后，这么多年连个像样的ORM都没有，每次遇到DB数据管理我都抓瞎，直到遇到了Elixir/Ecto，但是Ecto只有Mix项目才能集成，那么问题来了：

> 我是老牌Erlang项目能不能用？

答案是，先试一试吧。[Ecto](https://hex.pm/packages/ecto)

下面我建一个erlang项目，尝试把erlang/rebar/ecto/mix融合在一起。


# 新建一个能够编译elixir的rebar项目

-------------------------------------------------------------------------------

## 新建一个普通rebar项目


```
mkdir ecto_demo
cd ecto_demo
rebar create-app appid=ecto_demo
==> ecto_demo (create-app)
Writing src/ecto_demo.app.src
Writing src/ecto_demo_app.erl
Writing src/ecto_demo_sup.erl
```

## 增加rebar项目对elixir的编译支持

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

```
➜  ecto_demo rebar eunit
==> ecto_demo (pre_eunit)
==> ecto_demo (eunit)
Compiled test/ecto_demo_tests.erl
  Test passed.
➜  ecto_demo
```

下面就可以愉快的编写elixir代码了。


# 使用mix来获取到ecto等依赖

-------------------------------------------------------------------------------

使用rebar/rebar3是无法获取到ecto的代码的，因为ecto不支持rebar和erlang.mk的，但是呢它支持mix，而我需要一个mix环境来执行测试用例，那我同样也可以用mix来获取到ecto，反正他们获取下来都会拷贝到deps下面。

至于我为什么要使用mix，主要是因为：[使用elixir来优化erlang的单元测试用例](http://www.jianshu.com/p/0548d6282811)


那么下面就集成mix。

## 集成mix

回到文件夹的上层，新建一个同名的mix项目：

```
cd  ..
➜  services mix new ecto_demo
* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/ecto_demo.ex
* creating test
* creating test/test_helper.exs
* creating test/ecto_demo_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd ecto_demo
    mix test

Run "mix help" for more commands.

cd ecto_demo
mix test 
ok.
```

## 通过mix获取到ecto

需要修改mix.exs，增加ecto/mariaex的依赖。

```
defmodule EctoDemo.Mixfile do
  use Mix.Project

  def project do
    [app: :ecto_demo,
     version: "0.0.1",
     elixir: "~> 1.2",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps]
  end


  def application do
    [applications: [:logger, :mariaex, :ecto]]
  end


  defp deps do
    [
      {:mariaex, "~> 0.7.4"},
      {:ecto, "~> 2.0.0-rc.3"}
    ]
  end
end

```

获取依赖:

```
mix deps.get
....
....
mix test
```

成功之后就可以看到deps下面有一大堆ecto的依赖了。

```
➜  ls deps
bbmustache          db_connection       elixir              getopt              providers
cf                  decimal             erlware_commons     mariaex             rebar_elixir_plugin
connection          ecto                exrm                poolboy             relx
```

似乎可以愉快的玩耍了。

