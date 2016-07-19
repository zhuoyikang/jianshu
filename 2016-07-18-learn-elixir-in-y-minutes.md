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

x1
x2


