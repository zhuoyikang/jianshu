mix 是 elixir 工程的构建工具，利用 mix，可以快速方便的创建 elixir 工程，写单元测试，管理 elixir 包的依赖管理等等。
我觉得刚开始学习 elixir 的时候，先简单看看 mix 的用法，也方便学习中写些 elixir 的小例子。

# 工程生成

-------------------------------------------------------------------------------

    # 新建一个工程
    $ mix new sample
    creating README.md
    creating .gitignore
    creating mix.exs
    creating config
    creating config/config.exs
    creating lib
    creating lib/sample.ex
    creating test
    creating test/test_helper.exs
    creating test/sample_test.exs
    
    Your Mix project was created successfully.
    You can use "mix" to compile it, test it, and more:
    
    cd sample
    mix test
    
    Run "mix help" for more commands.

    .
    ├── README.md               # 默认的工程说明文件，可以根据工程添加自己的内容
    ├── config                  # 工程中使用的各个package的配置，具体说明可以看 config.exs 中的注释说明
    │   └── config.exs
    ├── lib                     # 真正实现功能或者试验的代码放在这个文件夹中，默认生成一个空的示例
    │   └── sample.ex
    ├── mix.exs                 # 整个工程的配置文件，里面包含工程名称，版本，依赖关系等等。mix 工具就是依靠此文件来编译工程的
    └── test                    # 单元测试代码放在此文件夹中，默认生成一个空的测试文件
        ├── sample_test.exs     # 文件名称对应 lib 中的代码文件
        └── test_helper.exs     # 默认只有一行启动测试的代码，可以根据需要扩充（比如加一些共通的测试前处理，后处理等）

# 编译，运行工程

-------------------------------------------------------------------------------

工程创建了之后，就可以开始写 elixir 的代码，体验 elixir 的特别之处了。
为了试验 mix 的编译，首先在 lib/sample.ex 中加入一段简单的代码：

    defmodule Sample do
      def hello do
        IO.puts "hello Elixir"
      end
    end

编译：mix compile

编译的结果就在 \_build 文件夹中。

运行工程也很简单

    $ iex -S mix
    Erlang/OTP 18 [erts-7.2.1] [source] [64-bit] [smp:4:4] [async-threads:10] [hipe] [kernel-poll:false] [dtrace]
    
    Interactive Elixir (1.2.1) - press Ctrl+C to exit (type h() ENTER for help)
    iex(1)> Sample.hello
    hello Elixir
    :ok

# 单元测试

-------------------------------------------------------------------------------

单元测试是确保代码质量的重要手段，mix 创建的工程也规定了单元测试的规则。
针对上面的简单代码，我们可以在 test 目录中对应的测试文件 samle<sub>test.exs</sub> 中添加测试代码：

    defmodule SampleTest do
      use ExUnit.Case
      doctest Sample
    
      test "case01" do
        assert :ok == Sample.hello
      end
    
      test "case02" do
        assert :ng == Sample.hello
      end
    end

运行单元测试非常简单：

    $ mix test
    hello Elixir
    .hello Elixir
    
    
      1) test case02 (SampleTest)
         test/sample_test.exs:9
         Assertion with == failed
         code: :ng == Sample.hello()
         lhs:  :ng
         rhs:  :ok
         stacktrace:
           test/sample_test.exs:10
    
    
    
    Finished in 0.07 seconds (0.07s on load, 0.00s on tests)
    2 tests, 1 failure
    
    Randomized with seed 537919

# 编译二进制文件

-------------------------------------------------------------------------------

上面的示例中，是通过 iex -S mix 来运行工程的，这种方式一般用于运行一个库。
我们也可以将工程编译成一个二进制文件，可以分发到所有安装 OTP 环境的系统中运行。
这时，我们需要一个 main 函数。
修改 lib/sample.ex 中的代码如下

    defmodule Sample do
      def main(args \\ []) do
        IO.puts "hello "
        IO.puts args
      end
    end

为了编译二进制，设置 mix.exs 如下

    def project do
      [app: :sample,
       version: "0.0.1",
       elixir: "~> 1.2",
       escript: escript,                            # 追加 escript 配置
       build_embedded: Mix.env == :prod,
       start_permanent: Mix.env == :prod,
       deps: deps]
    end
    
    def escript do                                  # 追加 escript 函数，其中设置 main_module
      [main_module: Sample]
    end

编译二进制文件

    $ mix escript.build
    Compiled lib/sample.ex
    Generated sample app
    Consolidated List.Chars
    Consolidated Collectable
    Consolidated String.Chars
    Consolidated Enumerable
    Consolidated IEx.Info
    Consolidated Inspect
    Generated escript sample with MIX_ENV=dev
    
    $ ls -l
    total 4824
    -rw-r--r--  1 xxxxxx  staff      399  2 17 22:08 README.md
    drwxr-xr-x  4 xxxxxx  staff      136  2 17 23:08 _build
    drwxr-xr-x  3 xxxxxx  staff      102  2 17 22:08 config
    drwxr-xr-x  3 xxxxxx  staff      102  2 17 23:31 lib
    -rw-r--r--  1 xxxxxx  staff      738  2 17 23:28 mix.exs
    -rwxr-xr-x  1 xxxxxx  staff  2460249  2 17 23:28 sample          # 此为二进制文件
    drwxr-xr-x  4 xxxxxx  staff      136  2 17 23:09 test
    
    $ ./sample
    hello
    
    $ ./sample "Elixir"
    hello
    Elixir


# 环境的系统中运行

-------------------------------------------------------------------------------

总共有三：

+ :dev - the one in which Mix tasks (like compile) run by default
+ :test - used by mix test
+ :prod - the one you will use to run your project in production


与之相关:

```
  def project do
    [app: :sample,
     version: "0.0.1",
     elixir: "~> 1.2",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps]
  end
```


也可以这样

```
$ MIX_ENV=prod mix compile
```


# 探索

-------------------------------------------------------------------------------

```
mix help
```
