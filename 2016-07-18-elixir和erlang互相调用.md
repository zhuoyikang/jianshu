Elixr设计目标之一就是要确保兼容性，可以兼容Erlang和其生态系统。Elixir和Erlang 都是运行同样的虚拟机平台(Erlang Virtual Machine)。
不管是在Erlang使用Elixir模块，还是在Elixir中使用Erlang模块都是非常方便的。

# Erlang 调用 Elixir 代码

-------------------------------------------------------------------------------

定义了一个Example.ex 模块，其中有一个sum函数返回两个数的和

    defmodule Example do
      def sum(x, y) do
      x + y
      end
    end

要在Erlang可以调用这个模块，首先要编译这个模块到beam文件,beam是编译好的字节码文件
打开iex，编译这个模块

    iex(1)> c("example.ex")
    [Example]
    iex(2)>

打开erl
使用l(file)函数加载Elixir写好的模块

    1> l('Elixir.Example').
    {module,'Elixir.Example'}
    2> 'Elixir.Example':sum(2, 3).
    3> 5

# Elixir 调用 Erlang 代码

-------------------------------------------------------------------------------

example.erl 

    -module (example).
    -export ([sum/2]).
    
    sum(A, B) -> 
        A + B.

可以直接在Elixir的iex中编译

    iex(1)> c("example.erl")
    [:example]
    iex(2)>

也会生成 Elixir.Example.beam 文件

模块名以冒号开始,执行

    iex(2)> :example.sum(2, 3)
    5
    iex(3)>
