30分钟学习Elixir，给Erlang程序员看到Elixir语法学习

# Why Elixir 

-------------------------------------------------------------------------------

首先是我为毛要学习Elixir?

Elixir是Rails核心成员开发的Erlang前端语言，Elixir的语法很多和Ruby很像，同样也继承了Ruby的魔性：“让你喜爱上编程！” ，另外Elixir植根于于Erlang 这样的老牌稳定的并发虚拟机，让诟病Erlang语法的各位程序员又有了一个新的选择。


+ 类似Ruby的优雅语法给。
+ 稳定的Erlang虚拟机。
+ 并发
+ lisp/宏的复活？

Elixir社区贡献了很多库比如ORM来填补Erlang的各种落后，社区复活了

+ [Awesome Elixir](https://github.com/h4cc/awesome-elixir): 列出了很多好用的Elixir库
+ [官网](http://elixir-lang.org/)
+ [标准库](http://elixir-lang.org/docs/stable/elixir/Kernel.html)
+ [本文Follow的是官网的Getting Started](http://elixir-lang.org/getting-started/introduction.html)


# 安装

-------------------------------------------------------------------------------

Mac OS X

```

Homebrew
Update your homebrew to latest: brew update
Run: brew install elixir
Macports
Run: sudo port install elixir
```


# Hello，Elixir

-------------------------------------------------------------------------------

交互模式

```
iex> 40 + 2
42
iex> "hello" <> " world"
"hello world"
```

运行脚本

```
IO.puts "Hello world from Elixir"
```

```
elixir simple.exs
Hello world from Elixir
```



# 基础数据类型

-------------------------------------------------------------------------------


```
iex> 1          # integer
iex> 0x1F       # integer
iex> 1.0        # float
iex> true       # boolean
iex> :atom      # atom / symbol
iex> "elixir"   # string
iex> [1, 2, 3]  # list
iex> {1, 2, 3}  # tuple
```


```
# Basic arithmetic
iex> 1 + 2
3
div(10, 2)
5
rem 10, 3
1
iex> round(3.58)
4
iex> trunc(3.58)
3

# Booleans

iex> true
true
iex> true == false
false
iex> is_boolean(true)
true
iex> is_boolean(1)
false

# Atoms

iex> :hello
:hello
iex> :hello == :world
false

# Strings

iex> "hellö"
"hellö"
iex> "hellö #{:world}"
"hellö world"
iex> is_binary("hellö")
true
iex> byte_size("hellö")
6
iex> String.length("hellö")
5

# Anonymous functions

iex> add = fn a, b -> a + b end
#Function<12.71889879/2 in :erl_eval.expr/5>
iex> is_function(add)
true
iex> is_function(add, 2)
true
iex> is_function(add, 1)
false
iex> add.(1, 2)
3

# (Linked) Lists

iex> list = [1, 2, 3]
iex> hd(list)
1
iex> tl(list)
[2, 3]

# Tuples

iex> {:ok, "hello"}
{:ok, "hello"}
iex> tuple_size {:ok, "hello"}
2
iex> elem(tuple, 1)
"hello"
iex> tuple = {:ok, "hello"}
{:ok, "hello"}
iex> put_elem(tuple, 1, "world")
{:ok, "world"}
iex> tuple
{:ok, "hello"}
```




# 基础操作符

-------------------------------------------------------------------------------

```
# 列表连接
iex> [1, 2, 3] ++ [4, 5, 6]
[1, 2, 3, 4, 5, 6]
iex> [1, 2, 3] -- [2]
[1, 3]

# 字符串连接
iex> "foo" <> "bar"
"foobar"

# 逻辑操作符
iex> true and true
true
iex> false or is_atom(:example)
true
```

类型排序

```
number < atom < reference < functions < port < pid < tuple < maps < list < bitstring
```

# 模式匹配

-------------------------------------------------------------------------------

对于Elixir来说，可以用=对变量进行多次赋值。

```
iex(5)> x=1
1
iex(6)> x=2
2
```

但是=实际上是匹配运算符。

```
iex> 1 = x
1
iex> 2 = x
** (MatchError) no match of right hand side value: 1
```

更复杂一点的匹配

```

# 普通匹配
iex> {a, b, c} = {:hello, "world", 42}
{:hello, "world", 42}
iex> a
:hello
iex> b
"world"

# 元组数量不对，匹配失败
iex> {a, b, c} = {:hello, "world"}
** (MatchError) no match of right hand side value: {:hello, "world"}

# 类型不同，匹配失败
iex> {a, b, c} = [:hello, "world", 42]
** (MatchError) no match of right hand side value: [:hello, "world", 42]

# 使用pin操作符^来对变量进行匹配而不是赋值
iex> x = 1
1
iex> ^x = 2
** (MatchError) no match of right hand side value: 2
iex> {y, ^x} = {2, 1}
{2, 1}
iex> y
2
iex> {y, ^x} = {2, 2}
** (MatchError) no match of right hand side value: {2, 2}

```


# 条件语句

-------------------------------------------------------------------------------

**case**

```
iex> case {1, 2, 3} do
...>   {4, 5, 6} ->
...>     "This clause won't match"
...>   {1, x, 3} ->
...>     "This clause will match and bind x to 2 in this clause"
...>   _ ->
...>     "This clause would match any value"
...> end
"This clause will match and bind x to 2 in this clause"
```

```
iex> x = 1
1
iex> case 10 do
...>   ^x -> "Won't match"
...>   _  -> "Will match"
...> end
"Will match"
```

```
iex> case {1, 2, 3} do
...>   {1, x, 3} when x > 0 ->
...>     "Will match"
...>   _ ->
...>     "Would match, if guard condition were not satisfied"
...> end
"Will match"
```

**cond**

```
iex> cond do
...>   2 + 2 == 5 ->
...>     "This will not be true"
...>   2 * 2 == 3 ->
...>     "Nor this"
...>   1 + 1 == 2 ->
...>     "But this will"
...> end
"But this will"
```

**if and unless**

```
iex> if true do
...>   "This works!"
...> end
"This works!"
iex> unless true do
...>   "This will never be seen"
...> end
nil
```

**do end 代码块**

```
iex> is_number(if true do
...>  1 + 2
...> end)
true
```

**类型断言**

```
comparison operators (==, !=, ===, !==, >, >=, <, <=)
    boolean operators (and, or, not)
    arithmetic operations (+, -, *, /)
    arithmetic unary operators (+, -)
    the binary concatenation operator <>
the in operator as long as the right side is a range or a list

all the following type check functions:
    is_atom/1
    is_binary/1
    is_bitstring/1
    is_boolean/1
    is_float/1
    is_function/1
    is_function/2
    is_integer/1
    is_list/1
    is_map/1
    is_nil/1
    is_number/1
    is_pid/1
    is_port/1
    is_reference/1
    is_tuple/1
    plus these functions:
    abs(number)
    binary_part(binary, start, length)
    bit_size(bitstring)
    byte_size(bitstring)
    div(integer, integer)
    elem(tuple, n)
    hd(list)
    length(list)
    map_size(map)
    node()
    node(pid | ref | port)
    rem(integer, integer)
    round(number)
    self()
    tl(list)
    trunc(number)
    tuple_size(tuple)
```

# Binaries, strings and char lists
    
-------------------------------------------------------------------------------

**字符串-string：a UTF-8 encoded binary**

字节数和字符数是不同的。

```
iex> string = "hełło"
"hełło"
iex> byte_size(string)
7
iex> String.length(string)
5
```

获取代码点

```
iex> ?a
97
iex> ?ł
322
iex> String.codepoints("hełło")
["h", "e", "ł", "ł", "o"]
```

**二进制-binary-bitstring** 

```
iex> <<0, 1, 2, 3>>
<<0, 1, 2, 3>>
iex> byte_size(<<0, 1, 2, 3>>)
4
```

1 bit操作

```

iex> <<1 :: size(1)>>
<<1::size(1)>>
iex> <<2 :: size(1)>> # truncated
<<0::size(1)>>
iex> is_binary(<< 1 :: size(1)>>)
false
iex> is_bitstring(<< 1 :: size(1)>>)
true
iex> bit_size(<< 1 :: size(1)>>)
1

```

模式匹配

```
iex> <<0, 1, x>> = <<0, 1, 2>>
<<0, 1, 2>>
iex> x
2
iex> <<0, 1, x>> = <<0, 1, 2, 3>>
** (MatchError) no match of right hand side value: <<0, 1, 2, 3>>
```

模式抽取

```
# 抽取了x
iex> <<0, 1, x :: binary>> = <<0, 1, 2, 3>>
<<0, 1, 2, 3>>
iex> x
<<2, 3>>
```


**字符列表：Char Lists: a list of characters:**

```
iex> 'hełło'
[104, 101, 322, 322, 111]
iex> is_list 'hełło'
true
iex> 'hello'
'hello'

```

和字符转之间的转化

```
iex> to_char_list "hełło"
[104, 101, 322, 322, 111]
iex> to_string 'hełło'
"hełło"
iex> to_string :hello
"hello"
iex> to_string 1
"1"
```

# 关键字列表和Maps

-------------------------------------------------------------------------------

**关键字列表**

```
iex> list = [{:a, 1}, {:b, 2}]
[a: 1, b: 2]
iex> list == [a: 1, b: 2]
true
iex> list[:a]
1
```

新奇语法

```
iex> list ++ [c: 3]
[a: 1, b: 2, c: 3]
iex> [a: 0] ++ list
[a: 0, a: 1, b: 2]
```


Keyword lists are important because they have three special characteristics:

+ Keys 必须是原子。
+ Keys 是有序的，顺序是开发者自己指定的。。
+ Keys 可以重复。

神器[the Ecto library ](https://github.com/elixir-lang/ecto)通过这样的代码来指定查询语句。

```
query = from w in Weather,
      where: w.prcp > 0,
      where: w.temp < 20,
      select: w
```


**map:关联数组**

```
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> map[:a]
1
iex> map[2]
:b
iex> map[:c]
nil
```

map对比关键字列表有以下区别:

+ Maps 允许任何类型作为key.
+ Maps 的Keys是无序的，因为是关联数组嘛。


```
iex> %{} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> %{:a => a} = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}
iex> a
1
iex> %{:c => c} = %{:a => 1, 2 => :b}
** (MatchError) no match of right hand side value: %{2 => :b, :a => 1}

```

Map模块提供了一些方便的操作函数

```
iex> Map.get(%{:a => 1, 2 => :b}, :a)
1
iex> Map.to_list(%{:a => 1, 2 => :b})
[{2, :b}, {:a, 1}]
```

可以直接通过"点号"获取属性

```
iex> map = %{:a => 1, 2 => :b}
%{2 => :b, :a => 1}

iex> map.a
1
iex> map.c
** (KeyError) key :c not found in: %{2 => :b, :a => 1}

iex> %{map | :a => 2}
%{2 => :b, :a => 2}
iex> %{map | :c => 3}
** (KeyError) key :c not found in: %{2 => :b, :a => 1}
```

嵌套数据操作

```
iex> users = [
  john: %{name: "John", age: 27, languages: ["Erlang", "Ruby", "Elixir"]},
  mary: %{name: "Mary", age: 29, languages: ["Elixir", "F#", "Clojure"]}
]
[john: %{age: 27, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
 mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}]
 
iex> users[:john].age
27

iex> users = put_in users[:john].age, 31
[john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
 mary: %{age: 29, languages: ["Elixir", "F#", "Clojure"], name: "Mary"}]
 
iex> users = update_in users[:mary].languages, &List.delete(&1, "Clojure")
[john: %{age: 31, languages: ["Erlang", "Ruby", "Elixir"], name: "John"},
 mary: %{age: 29, languages: ["Elixir", "F#"], name: "Mary"}] 
 
```

# 模块

-------------------------------------------------------------------------------

定义模块

```
iex> defmodule Math do
...>   def sum(a, b) do
...>     a + b
...>   end
...> end

iex> Math.sum(1, 2)
3
```

编译模块

```
defmodule Math do
  def sum(a, b) do
    a + b
  end
end

# elixirc math.ex

iex> Math.sum(1, 2)
3
```

使用defp定义的是私有函数，不能被外部调用

```
defmodule Math do
  def sum(a, b) do
    do_sum(a, b)
  end

  defp do_sum(a, b) do
    a + b
  end
end

IO.puts Math.sum(1, 2)    #=> 3
IO.puts Math.do_sum(1, 2) #=> ** (UndefinedFunctionError)
```

erlang高级货

```
defmodule Math do
  def zero?(0), do: true
  def zero?(x) when is_integer(x), do: false
end
```

函数捕获/函数引用

```
iex> Math.zero?(0)
true
iex> fun = &Math.zero?/1
&Math.zero?/1
iex> is_function(fun)
true
iex> fun.(0)
true

# 本地函数或者被导入的函数，也可以被捕获。

iex> &is_function/1
&:erlang.is_function/1
iex> (&is_function/1).(fun)
true
```

捕获语法也可以用来创建简单函数

```
iex> fun = &(&1 + 1)
#Function<6.71889879/1 in :erl_eval.expr/5>
iex> fun.(1)
2
```

&1代表了传入参数的第一个函数。上面代码等同于 fn x -> x+1 end/


默认函数参数

```
defmodule Concat do
  def join(a, b, sep \\ " ") do
    a <> sep <> b
  end
end

IO.puts Concat.join("Hello", "world")      #=> Hello world
IO.puts Concat.join("Hello", "world", "_") #=> Hello_world

```

默认参数可以是表达式

```
defmodule DefaultTest do
  def dowork(x \\ IO.puts "hello") do
    x
  end
end

iex> DefaultTest.dowork
hello
:ok
iex> DefaultTest.dowork 123
123
iex> DefaultTest.dowork
hello
:ok
```

多函数体的函数需要实现一个函数声明来指定默认值

```
defmodule Concat do
  def join(a, b \\ nil, sep \\ " ")

  def join(a, b, _sep) when is_nil(b) do
    a
  end

  def join(a, b, sep) do
    a <> sep <> b
  end
end

IO.puts Concat.join("Hello", "world")      #=> Hello world
IO.puts Concat.join("Hello", "world", "_") #=> Hello_world
IO.puts Concat.join("Hello")         
```

一不小心就会导致函数覆盖

```
defmodule Concat do
  def join(a, b) do
    IO.puts "***First join"
    a <> b
  end

  def join(a, b, sep \\ " ") do
    IO.puts "***Second join"
    a <> sep <> b
  end
end
```

编译时报错：

```
concat.ex:7: warning: this clause cannot match because a previous clause at line 2 always matches
```

1. 当两个参数时第一个函数永远匹配
2. 当三个参数是第二个函数被调用。



# 递归

-------------------------------------------------------------------------------

和Erlang相比只是换了个语法。


```
defmodule Recursion do
  def print_multiple_times(msg, n) when n <= 1 do
    IO.puts msg
  end

  def print_multiple_times(msg, n) do
    IO.puts msg
    print_multiple_times(msg, n - 1)
  end
end

Recursion.print_multiple_times("Hello!", 3)
# Hello!
# Hello!
# Hello!

```

另一个简单循环demo

```
defmodule Math do
  def double_each([head | tail]) do
    [head * 2 | double_each(tail)]
  end

  def double_each([]) do
    []
  end
end
```


Enum 的Map  Reduce

```
iex> Enum.reduce([1, 2, 3], 0, fn(x, acc) -> x + acc end)
6
iex> Enum.map([1, 2, 3], fn(x) -> x * 2 end)
[2, 4, 6]
```

可以换成捕获语法

```
iex> Enum.reduce([1, 2, 3], 0, &+/2)
6
iex> Enum.map([1, 2, 3], &(&1 * 2))
[2, 4, 6]
```
    
# 枚举和流

-------------------------------------------------------------------------------

**可枚举的**

Elixir 提供了enumerables的概念和[the Enum module](http://elixir-lang.org/docs/stable/elixir/Enum.html)

```
iex> Enum.map([1, 2, 3], fn(x) -> x * 2 end)
[2, 4, 6]

iex> map = %{a: 1, b: 2}
iex> Enum.map(map, fn {k, v} -> {k, v * 2} end)
[a: 2, b: 4]
```

Range

```
iex> Enum.map(1..3, fn x -> x * 2 end)
[2, 4, 6]
iex> Enum.reduce(1..3, 0, &+/2)
6
```


*Eager vs Lazy： 立即求值和惰性求值*

Enum中的函数都是立即求值。

```
iex> odd? = &(rem(&1, 2) != 0)
#Function<6.80484245/1 in :erl_eval.expr/5>
iex> Enum.filter(1..3, odd?)
[1, 3]
```

**[PipleLine|管道](http://elixir-lang.org/docs/stable/elixir/Kernel.html#%7C%3E/2)**

神奇的小玩意。

```
1..100_000 |> Enum.map(&(&1 * 3)) |> Enum.filter(odd?) |> Enum.sum
```

如果不使用管道，上面的代码会变成这样：

```
# 是不是难以阅读多了。
Enum.sum(Enum.filter(Enum.map(1..100_000, &(&1 * 3)), odd?))
```

**Stream**

相对于Enum模块是立即求值，[Stream模块](http://elixir-lang.org/docs/stable/elixir/Stream.html)是惰性求值。

```
1..100_000 |> Stream.map(&(&1 * 3))
#Stream<[enum: 1..100000, funs: [#Function<34.16982430/1 in Stream.map/2>]]>
```

注意上面返回的是一个Stream而不是一个值，这个stream被执行后才返回`值`。

同理

```
iex> stream = Stream.cycle([1, 2, 3])
#Function<15.16982430/2 in Stream.cycle/1>
iex> Enum.take(stream, 10)
[1, 2, 3, 1, 2, 3, 1, 2, 3, 1]


iex> stream = Stream.unfold("hełło", &String.next_codepoint/1)
#Function<39.75994740/2 in Stream.unfold/2>
iex> Enum.take(stream, 3)
["h", "e", "ł"]

```

所以Stream是惰性求值。

# 进程

-------------------------------------------------------------------------------

**spawn**

```
iex> spawn fn -> 1 + 2 end
#PID<0.43.0>
iex> Process.alive?(pid)
false


iex> self()
#PID<0.41.0>
iex> Process.alive?(self())
true

```


**send and receive**

```
ex> send self(), {:hello, "world"}
{:hello, "world"}
iex> receive do
...>   {:hello, msg} -> msg
...>   {:world, msg} -> "won't match"
...> end
"world"
```


超时

```
iex> receive do
...>   {:hello, msg}  -> msg
...> after
...>   1_000 -> "nothing after 1s"
...> end
"nothing after 1s"
```


**Links**


spawn_link/1


```
iex> spawn fn -> raise "oops" end
#PID<0.58.0>

[error] Process #PID<0.58.00> raised an exception
** (RuntimeError) oops
    :erlang.apply/2
```

spawn_link/1之后

``` erlang
iex> spawn_link fn -> raise "oops" end
#PID<0.41.0>

** (EXIT from #PID<0.41.0>) an exception was raised:
    ** (RuntimeError) oops
        :erlang.apply/2
```

因为shell会捕获所有异常并将其良好的显示，所以你需要在一个文件中做这个实验。


``` erlang
# spawn.exs
spawn_link fn -> raise "oops" end

receive do
  :hello -> "let's wait until the process fails"
end
```

```
$ elixir spawn.exs

** (EXIT from #PID<0.47.0>) an exception was raised:
    ** (RuntimeError) oops
        spawn.exs:1: anonymous fn/0 in :elixir_compiler_0.__FILE__/1

```


这一次进程失败并且把父进程连累的一起退出了，因为他们link了。


**Tasks**

Tasks是一个更上层的模块提供了更好的错误隔离和交互

```
iex(1)> Task.start fn -> raise "oops" end
{:ok, #PID<0.55.0>}

15:22:33.046 [error] Task #PID<0.55.0> started from #PID<0.53.0> terminating
** (RuntimeError) oops
    (elixir) lib/task/supervised.ex:74: Task.Supervised.do_apply/2
    (stdlib) proc_lib.erl:239: :proc_lib.init_p_do_apply/3
Function: #Function<20.90072148/0 in :erl_eval.expr/5>
    Args: []

```

Instead of spawn/1 and spawn_link/1, we use Task.start/1 and Task.start_link/1 to return {:ok, pid} rather than just the PID. This is what enables Tasks to be used in supervision trees. Furthermore, Task provides convenience functions, like Task.async/1 and Task.await/1, and functionality to ease distribution.

**State**

Erlang状态循环。

```
defmodule KV do
  def start_link do
    Task.start_link(fn -> loop(%{}) end)
  end

  defp loop(map) do
    receive do
      {:get, key, caller} ->
        send caller, Map.get(map, key)
        loop(map)
      {:put, key, value} ->
        loop(Map.put(map, key, value))
    end
  end
end
```


# IO和文件系统

-------------------------------------------------------------------------------

**The IO module**

```
iex> IO.puts "hello world"
hello world
:ok
iex> IO.gets "yes or no? "
yes or no? yes
"yes\n"
```

**The File module**

```
iex> {:ok, file} = File.open "hello", [:write]
{:ok, #PID<0.47.0>}
iex> IO.binwrite file, "world"
:ok
iex> File.close file
:ok
iex> File.read "hello"
{:ok, "world"}
```


```
case File.read(file) do
  {:ok, body}      -> # do something with the `body`
  {:error, reason} -> # handle the error caused by `reason`
end
```


```
{:ok, body} = File.read(file)
```


**Path**


```
iex> Path.join("foo", "bar")
"foo/bar"
iex> Path.expand("~/hello")
"/Users/jose/hello"
```

**Processes and group leaders**

实际上File.open返回的是一个进程。

```
iex> {:ok, file} = File.open "hello", [:write]
{:ok, #PID<0.47.0>}
```


**iodata**

可以直接写列表哟。

```
iex> IO.puts 'hello world'
hello world
:ok
iex> IO.puts ['hello', ?\s, "world"]
hello world
:ok

```

# alias,require and import

-------------------------------------------------------------------------------



**alias**

别名只是换了个名字

```
defmodule Math do
  alias Math.List, as: List
end
```

as 可以忽略

一次别名多个

```
alias MyApp.{Foo, Bar, Baz}
```

**require**

require和元编程相关。

宏是一些在编译时展开的代码块。想要使用宏必须`require`

```
iex> Integer.is_odd(3)
** (CompileError) iex:1: you must require Integer before invoking the macro Integer.is_odd/1
iex> require Integer
Integer
iex> Integer.is_odd(3)
true
```

Integer.is_odd/1 is defined as a macro


+ 模块不需要require。
+ 如果要使用模块中定义的宏则需要require。


**import**

和以前的Erlang的导入一样，但是似乎没人用。

```
iex> import List, only: [duplicate: 2]
List
iex> duplicate :ok, 3
[:ok, :ok, :ok]
```

也可以按类型来导入


```
import Integer, only: :macros
import Integer, only: :functions
```

import是词法域的。

```
defmodule Math do
  def some_function do
    import List, only: [duplicate: 2]
    duplicate(:ok, 10)
  end
end
```

**use**

use和require的区别:

> use requires the given module and then calls the __using__/1 callback on it allowing the module to inject some code into the current context. 
> Generally speaking, the following module:

```
defmodule Example do
  use Feature, option: :value
end
```

is compiled into

```
defmodule Example do
  require Feature
  Feature.__using__(option: :value)
end
```


**动态调用**

模块放到变量中

```
iex> mod = :lists
:lists
iex> mod.flatten([1, [2], 3])
[1, 2, 3]

```

模块是可以嵌套的

```
defmodule Foo do
  defmodule Bar do
  end
end

# 等价
defmodule Elixir.Foo do
  defmodule Elixir.Foo.Bar do
  end
  alias Elixir.Foo.Bar, as: Bar
end
```

# 模块属性

-------------------------------------------------------------------------------

模块属性主要有3个作用:

+ 可以给模块添加更多的说明。
+ 可以当成常量使用
+ 编译期间的模块数据临时存储。


**作为记号**

+ @moduledoc - provides documentation for the current module.
+ @doc - provides documentation for the function or macro that follows the attribute.
+ @behaviour - (notice the British spelling) used for specifying an OTP or user-defined behaviour.
+ @before_compile - provides a hook that will be invoked before the module is compiled. This makes it possible to inject functions inside the module exactly before compilation.

```
defmodule Math do
  @moduledoc """
  Provides math-related functions.

  ## Examples

      iex> Math.sum(1, 2)
      3

  """

  @doc """
  Calculates the sum of two numbers.
  """
  def sum(a, b), do: a + b
end
```

编译后可以通过h查看

```
$ elixirc math.ex
$ iex

iex> h Math # Access the docs for the module Math
...
iex> h Math.sum # Access the docs for the sum function
...
```


**作为常量**

```
defmodule MyServer do
  @initial_state %{host: "147.0.0.1", port: 3456}
  IO.inspect @initial_state
end
```

使用没有被初始化的常量会造成编译时警告

```
defmodule MyServer do
  @unknown
end
warning: undefined module attribute @unknown, please remove access to @unknown or explicitly set it before access
```

**作为临时数据存储**

Plugin允许开发者自定义自己的模块。

```
defmodule MyPlug do
  use Plug.Builder

  plug :set_header
  plug :send_ok

  def set_header(conn, _opts) do
    put_resp_header(conn, "x-header", "set")
  end

  def send_ok(conn, _opts) do
    send(conn, 200, "ok")
  end
end

IO.puts "Running MyPlug with Cowboy on http://localhost:4000"
Plug.Adapters.Cowboy.http MyPlug, []
```


上文中，plug是一个宏，它把每一个请求需要的插件都记录到一个模块变量里，但这需要更多的元编程知识才能看的懂，这里先忽略。


# 结构体structs

-------------------------------------------------------------------------------

structs是在map的基础上建立起来的一层抽象。

**定义struct**

```
iex> defmodule User do
...>   defstruct name: "John", age: 27
...> end
```

使用

```
iex> %User{}
%User{age: 27, name: "John"}
iex> %User{name: "Meg"}
%User{age: 27, name: "Meg"}

```


访问和更新结构体

```
iex> john = %User{}
%User{age: 27, name: "John"}
iex> john.name
"John"
iex> meg = %{john | name: "Meg"}
%User{age: 27, name: "Meg"}
iex> %{meg | oops: :field}
** (KeyError) key :oops not found in: %User{age: 27, name: "Meg"}
```

字段抽取

```
iex> %User{name: name} = john
%User{age: 27, name: "John"}
iex> name
"John"
```

struct 差不多就是map

```
iex> is_map(john)
true
iex> john.__struct__
User
```

但是为maps实现的协议并没有为struct实现

```
ex> john = %User{}
%User{age: 27, name: "John"}
iex> john[:name]
** (UndefinedFunctionError) undefined function: User.fetch/2
iex> Enum.each john, fn({field, value}) -> IO.puts(value) end
** (Protocol.UndefinedError) protocol Enumerable not implemented for %User{age: 27, name: "John"}
```

但是因为struct就是map，所以它可以和Map模块很好的一起工作

```
iex> kurt = Map.put(%User{}, :name, "Kurt")
%User{age: 27, name: "Kurt"}
iex> Map.merge(kurt, %User{name: "Takashi"})
%User{age: 27, name: "Takashi"}
iex> Map.keys(john)
[:__struct__, :age, :name]

```

# 协议:Protocals

-------------------------------------------------------------------------------

**protocals基础**

Protocals是elixr实现多态的一种方法，类比Erlang的behavour，golang的interface。

定义一个protocal

```
defprotocol Blank do
  @doc "Returns true if data is considered blank/empty"
  def blank?(data)
end
```

实现：

```
# Integers are never blank
defimpl Blank, for: Integer do
  def blank?(_), do: false
end

# Just empty list is blank
defimpl Blank, for: List do
  def blank?([]), do: true
  def blank?(_),  do: false
end

# Just empty map is blank
defimpl Blank, for: Map do
  # Keep in mind we could not pattern match on %{} because
  # it matches on all maps. We can however check if the size
  # is zero (and size is a fast operation).
  def blank?(map), do: map_size(map) == 0
end

# Just the atoms false and nil are blank
defimpl Blank, for: Atom do
  def blank?(false), do: true
  def blank?(nil),   do: true
  def blank?(_),     do: false
end
```

使用：

```
iex> Blank.blank?(0)
false
iex> Blank.blank?([])
true
iex> Blank.blank?([1, 2, 3])
false
```


为struct实现protocal

```

defmodule User do
    defstruct name: "John", age: 27
end

defimpl Blank, for: User do
    def blank?(_), do: false
end
```

那么就可以了

```
iex> Blank.blank?(%{})
true
iex> Blank.blank?(%User{})

```

**实现any**


对所有类型实现protocal可以很快变成一段乱麻，但是庆幸有any这个东西。

```
defimpl Blank, for: Any do
  def blank?(_), do: false
end
```

Any也就是所有类型的意思。


**集成Deriving**


```
defmodule DeriveUser do
  @derive Blank
  defstruct name: "john", age: 27
end
```

但是这样并没有什么用，还需要fallback to any.

**fallback to any，接口默认值实现**

为protocal定义fall_back_to_any为true

```
defprotocol Blank do
  @fallback_to_any true
  def blank?(data)
end
```

这样就可以了。

**内建协议**

Enumerable就是一个协议

```
iex> Enum.map [1, 2, 3], fn(x) -> x * 2 end
[2, 4, 6]
iex> Enum.reduce 1..3, 0, fn(x, acc) -> x + acc end
6
```

String.Chars protocol

```
iex> tuple = {1, 2, 3}
{1, 2, 3}
iex> "tuple: #{tuple}"
** (Protocol.UndefinedError) protocol String.Chars not implemented for {1, 2, 3}
```

Inspect protocals：这个协议将所有类型转成可读的字符串。

```
iex> {1, 2, 3}
{1, 2, 3}
iex> %User{}
%User{name: "john", age: 27}
```

**协议合并：consolidation**


在后面的mix中你可以看到如下输出


```
Consolidated String.Chars
Consolidated Collectable
Consolidated List.Chars
Consolidated IEx.Info
Consolidated Enumerable
Consolidated Inspect

```

具体不详。



