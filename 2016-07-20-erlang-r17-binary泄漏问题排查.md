遇到一个奇怪的问题，操作系统的top显示ejabberd暂用了17G内存，但是erlang:memory()却只有11G，那么还有6G去哪里了？

> 首先:erlang:memory() 并不会统计内存池中的预先分配的内存。
> 其次:内存分配问题可以用recon来查。


内存池总分配

```
(ejabberd)7> recon_alloc:memory(allocated).
13216327024

```

内存池使用率

```
recon_alloc:memory(usage).
0.24950176855636533
```

各个内存池占用内存

```
recon_alloc:memory(allocated_types).
[{binary_alloc,9 432 237 616},
{driver_alloc,152602160},
{eheap_alloc,3907614256},
{ets_alloc,219973168},
{fix_alloc,113280560},
{ll_alloc,45876712},
{sl_alloc,17598000},
{std_alloc,17335856},
{temp_alloc,1180728}]
```

二进制分配器的内存碎片程度:

```
rp(recon_alloc:fragmentation(current)).

(ejabberd)16> rp(recon_alloc:fragmentation(current)).
[{{binary_alloc,4},
[{sbcs_usage,1.0},
{mbcs_usage,0.016481312461743083},
{sbcs_block_size,0},
{sbcs_carriers_size,0},
{mbcs_block_size,38038024},
{mbcs_carriers_size,2307948720}]},
{{binary_alloc,2},
[{sbcs_usage,1.0},
{mbcs_usage,0.04190342465224416},
{sbcs_block_size,0},
{sbcs_carriers_size,0},
{mbcs_block_size,70226768},
{mbcs_carriers_size,1675919536}]},
...
```

可以看出来binary的内存使用率极低。启动时关闭binary内存分配池就好了。

```
+MBacul 0
```
