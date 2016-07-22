Erlangy由于社区落后，这么多年连个像样的ORM都没有，每次遇到DB数据管理我都抓瞎，知道遇到了Elixir/Ecto，但是Ecto只有Mix项目才能集成，那么问题来了：

> 我是老牌Erlang项目能不能用？

答案是，先试一试吧。[Ecto](https://hex.pm/packages/ecto)

# Ecto也是OTP应用

-------------------------------------------------------------------------------

Erlang的的应用程序都是按otp应用组织的，不管是Elixir还是Erlang，那么Ecto也是一个Otp应用，虽然我不能通过源码的形式集成到项目，那么我能不能直接引用其beam/app来使用呢？






