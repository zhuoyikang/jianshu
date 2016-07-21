thanks [元气糯米团子的Coding Blog](http://developerworks.github.io/2015/01/05/elixir-release-manager/)

Elixir发布管理器(Elixir Release Manager)是一个Elixir任务模块,用于把一个完整Elixir项目打包为一个.tar.gz文件用于发布和部署.

首先, 需要在mix.exs文件中添加如下依赖:

```
defp deps do
    [{:exrm, "~> 0.14.16"}]
end
```

exrm的版本需要在[hex](https://hex.pm/packages?_utf8=%E2%9C%93&search=exrm&sort=downloads)上查最新版本号。


下载依赖,并编译

```
mix deps.get
mix deps.compile
```

发布项目

```
mix release

....
==> The release for ecto_test-0.0.1 is ready!
==> You can boot a console running your release with `$ rel/ecto_test/bin/ecto_test console`
```

之后就生成在build下可以直接启动了。



