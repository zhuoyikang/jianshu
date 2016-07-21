ecto的确好用，但是release之后就不行了，肿么破？有办法。 [参考](http://blog.plataformatec.com.br/2016/04/running-migration-in-an-exrm-release/)


Exrm 1.0.3增加了一个command task可以用来完成这个工作。

+ [如何使用Exrm](http://www.jianshu.com/p/1fa5ecffc300)

例子：

```
rel/my_app/bin/my_app command <Module> <function> <args>
```


定义一个自定义的migrate模块

```
defmodule Release.Tasks do
  def migrate do
    {:ok, _} = Application.ensure_all_started(:my_app)

    path = Application.app_dir(:my_app, "priv/repo/migrations")

    Ecto.Migrator.run(MyApp.Repo, path, :up, all: true)

    :init.stop()
  end
end
```


release之后调用

```
rel/my_app/bin/my_app command Elixir.Release.Tasks migrate
```


搞定.
