从[程序人生-elixir：灵丹妙药？or 徒有其名？](http://mp.weixin.qq.com/s?__biz=MzA3NDM0ODQwMw==&mid=206087231&idx=1&sn=f6e7ed7f7ae80904171af1de8d60865b#rd)上看到这个库，那就从这里开始学习elixir吧。

[github地址](https://github.com/tyrchen/chinese_translation)

# 说明

-------------------------------------------------------------------------------

这个库干了一件暴力的事情，把wikipedia的最新词库导入，使用macro在编译时生成近10,000个按词进行正向最大匹配的递归函数，这样来完成简繁体转换，编译一下要40分钟左右，但是性能超好：

```
$ mix bench
Settings:
  duration:      1.0 s
  mem stats:     false
  sys mem stats: false

[20:49:22] 1/8: ChineseTranslationBench.translate a character t->s
[20:49:25] 2/8: ChineseTranslationBench.translate 158-character chinese to pinyin
[20:49:28] 3/8: ChineseTranslationBench.translate a 158-character sentence s->t
[20:49:30] 4/8: ChineseTranslationBench.slugify pinyin with tone
[20:49:32] 5/8: ChineseTranslationBench.slugify pinyin
[20:49:36] 6/8: ChineseTranslationBench.translate a character s->t
[20:49:39] 7/8: ChineseTranslationBench.translate a 158-character sentence t->s
[20:49:41] 8/8: ChineseTranslationBench.slugify a short sentence
Finished in 23.25 seconds

ChineseTranslationBench.translate a character t->s:                  10000000   0.28 µs/op
ChineseTranslationBench.translate a character s->t:                  10000000   0.29 µs/op
ChineseTranslationBench.translate a 158-character sentence t->s:       100000   16.78 µs/op
ChineseTranslationBench.translate a 158-character sentence s->t:       100000   16.87 µs/op
ChineseTranslationBench.slugify pinyin with tone:                       50000   32.59 µs/op
```

好吧，开始阅读代码玩一玩。






