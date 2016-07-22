erlang的单元测试相信很多人都用过，体验不好，但是elixir出现后，似乎这个体验有机会赶超rspec，那就哈哈哈了。

初学elixir/mix，苦于没有用武之地，其实可以从测试用例开始，毕竟erlang和elixir编译出来的代码是可以完整互相调用的，那么混合在一起也什么困难的。

# 新建一个rebar工程

-------------------------------------------------------------------------------

在erlang中我是这样尝试写用例的。

```
➜   rebar create-app appid=rebar_mix_test
==> rebar_mix_test (create-app)
Writing src/rebar_mix_test.app.src
Writing src/rebar_mix_test_app.erl
Writing src/rebar_mix_test_sup.erl
```

现在这个简单的otp应用下面挂一个工作者进程，这个进程有一点简单的api函数。

mix_worker.erl 简单易懂，实现了add和double函数。

```
-module(mix_worker).

-behaviour(gen_server).

%% API
-export([start_link/0]).

-export([add/2]).
-export([double/1]).


-record(state, {}).
-define(SERVER, ?MODULE).


-export([init/1, handle_call/3, handle_cast/2, handle_info/2,
         terminate/2, code_change/3]).


start_link() ->
    gen_server:start_link({local, ?SERVER}, ?MODULE, [], []).

add(A, B) ->
    gen_server:call(?MODULE, {add, A, B}).

double(A) ->
    gen_server:call(?MODULE, {double, A}).

init([]) ->
    {ok, #state{}}.


handle_call({add, X, Y}, _From, State) ->
    {reply, X+Y, State};


handle_call({double, X}, _From, State) ->
    {reply, X*2, State};

handle_call(_Request, _From, State) ->
    Reply = ok,
    {reply, Reply, State}.


handle_cast(_Msg, State) ->
    {noreply, State}.

handle_info(_Info, State) ->
    {noreply, State}.


terminate(_Reason, _State) ->
    ok.

code_change(_OldVsn, State, _Extra) ->
    {ok, State}.
```

接下来挂到监督者下面

```

init([]) ->
    {ok, { {one_for_one, 5, 10}, [?CHILD(mix_worker, worker)]} }.

```

编译测试：

```
➜   rebar compile
==> rebar_mix_test (compile)
Compiled src/rebar_mix_test_sup.erl
Compiled src/mix_worker.erl
➜   erl -pa deps/*/ebin ebin/
zsh: no matches found: deps/*/ebin
➜   erl -pa ebin/
Erlang/OTP 18 [erts-7.3] [source] [64-bit] [smp:4:4] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]

Eshell V7.3  (abort with ^G)
1> application:start(rebar_mix_test).
ok
2> observer:start().
ok
3> mix_worker:add(3,3).
6
4> mix_worker:double(232).
464
5>
```

这个mix_worker如果想在eunit里面写单元测试用例是比较难的，我尝试写一个：

test/mix_worker_tests.erl

```
-module(mix_worker_tests).
-compile([export_all]).

-include_lib("eunit/include/eunit.hrl").

add_test() ->
    3 = mix_worker:add(1,2),
    ok.

```

执行测试：

```
➜   rebar eunit
==> rebar_mix_test (eunit)
Compiled test/mix_worker_tests.erl
mix_worker_tests: add_test (module 'mix_worker_tests')...*failed*
in function gen_server:call/2 (gen_server.erl, line 204)
in call from mix_worker_tests:add_test/0 (test/mix_worker_tests.erl, line 7)
**exit:{noproc,{gen_server,call,[mix_worker,{add,1,2}]}}
  output:<<"">>

=======================================================
  Failed: 1.  Skipped: 0.  Passed: 0.
ERROR: One or more eunit tests failed.
ERROR: eunit failed while processing /Users/zhuoyikang/source/services/rebar_mix_test: rebar_abort
```

抓瞎了，mix_worker没有启动，那么怎么测呢？

尝试在测试用例中启动rebar_mix_test：

```

add_test() ->
    ok = application:start(rebar_mix_test),
    3 = mix_worker:add(1,2),
    ok = application:stop(rebar_mix_test),
    ok.
```

依然抓瞎：

```
➜   rebar eunit
==> rebar_mix_test (eunit)
mix_worker_tests: add_test (module 'mix_worker_tests')...*failed*
in function mix_worker_tests:add_test/0 (test/mix_worker_tests.erl, line 7)
**error:{badmatch,{error,{"no such file or directory","rebar_mix_test.app"}}}
  output:<<"">>

=======================================================
  Failed: 1.  Skipped: 0.  Passed: 0.
ERROR: One or more eunit tests failed.
ERROR: eunit failed while processing /Users/zhuoyikang/source/services/rebar_mix_test: rebar_abort
➜  
```


那么怎么办呢，要知道erlang里面主要的是进程，无法直接在eunit启动整个环境来跑测试，那要怎么写？

答案是我没什么好办法，只能自己写一些脚本，先把这个otp应用启动起来，然后尝试跑那些用例函数，但是这个时候基本上就无法使用rebar eunit执行，需要变成自己的命令执行，总的来说就是

> 你需要自己搞定你的测试框架，只是可以使用eunit的测试函数。

每次到这个时候我就听到很多人骂erlang了，但是作为一名erlang程序员我无力反驳，直到我最近学习了一下elixir/mix。



# 使用mix 管理应用并编写测试用例

-------------------------------------------------------------------------------

elixir/mix对单元测试用例的编写非常友好，这里我使用mix新建一个同名应用：


```
➜ rebar_mix_test cd ..
➜  services mix new rebar_mix_test
* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/rebar_mix_test.ex
* creating test
* creating test/test_helper.exs
* creating test/rebar_mix_test_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd rebar_mix_test
    mix test

Run "mix help" for more commands.

➜  services cd rebar_mix_test
➜   ls
README.md config    ebin      lib       mix.exs   src       test
➜  
```

执行单元测试

```
➜   mix test
Compiled src/rebar_mix_test_app.erl
Compiled src/rebar_mix_test_sup.erl
Compiled src/mix_worker.erl
Compiled lib/rebar_mix_test.ex
Generated rebar_mix_test app
Consolidated Collectable
Consolidated List.Chars
Consolidated String.Chars
Consolidated Enumerable
Consolidated IEx.Info
Consolidated Inspect
.

Finished in 0.1 seconds (0.1s on load, 0.00s on tests)
1 test, 0 failures

Randomized with seed 193760
➜  
```

里面默认有一个demo测试，内容如下：

```
defmodule RebarMixTestTest do
  use ExUnit.Case
  doctest RebarMixTest

  test "the truth" do
    assert 1 + 1 == 2
  end
end

```

修改为:

```
defmodule RebarMixTestTest do
  use ExUnit.Case
  doctest RebarMixTest

  test "the add" do
    assert :mix_worker.add(1,1) == 2
  end
end

```

接下来修改mix.ex，加了一行：`mod: {:rebar_mix_test_app, []},`

```

  # Configuration for the OTP application
  #
  # Type "mix help compile.app" for more information
  def application do
    [
      mod: {:rebar_mix_test_app, []},
      applications: [:logger]
    ]
  end

```

这样代码会在应用启动时自动执行，由于Elixir调用erlang的代码模块是需要在前面加`:`，所以这里其实是启动了我们之前编写的otp应用rebar_mix_test。

然后执行mix test，一片大好：

```
➜   mix test
Compiled lib/rebar_mix_test.ex
Generated rebar_mix_test app
Consolidated List.Chars
Consolidated Collectable
Consolidated String.Chars
Consolidated Enumerable
Consolidated IEx.Info
Consolidated Inspect
.

Finished in 0.04 seconds (0.04s on load, 0.00s on tests)
1 test, 0 failures

Randomized with seed 55281
➜ 
```


# Elixir的ExUnit

-------------------------------------------------------------------------------

ExUnit是Elixir的单元测试框架，也就是我们刚才小小适用的那个，这个工具非常强大，本文权当抛砖引玉，接下来的功能我也开始去做文档学习了。

[ExUnit](http://elixir-lang.org/docs/stable/ex_unit/ExUnit.html)

have fun ~.
