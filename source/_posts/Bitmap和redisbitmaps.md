---
title: Bitmap和redisbitmaps
date: 2018-10-27 22:31:17
tags: redis
categories: redis
---
> 原文链接：https://blog.getspool.com/2011/11/29/fast-easy-realtime-metrics-using-redis-bitmaps/

# **Bitmap**（又名：bitset）
bitmap或是bitset是一个由0和1构成的数组，在bitset中一个bit被设置为0或1，数组中的每个位置被称为`offset`。操作符包括`AND`,`OR`,`XOR`，等,其他按位操作是Bitmaps的公平游戏。

# **population count**
bitmap的population count是设置为1的位数。有用于计算population count的有效算法。 例如，在MacBook Pro上，包含10亿位的90％填充的bitset的population count为21.1 ms。 SSE4中甚至有一个硬件指令用于整数的总体计数。

![](https://leanote.com/api/file/getImage?fileId=5bdefa63ab64414bb400c0c7)

# **redis中的bitmap**
Redis允许二进制key和二进制value。 bitmap只不过是二进制值。 setbit（key，offset，value）操作占用O(1)时间，在给定key的指定偏移量offset处将位的value设置为0或1。

## **一个简单的例子：日活跃用户**
为了统计今天登录的唯一用户，我们设置了一个bitmap，其中每个用户都由一个offset标识。 当用户访问页面或执行保证计数的操作时，请在表示用户ID的offset将该位设置为1。 位图的键是执行的操作用户的名称和时间戳的函数。
![](https://leanote.com/api/file/getImage?fileId=5bdefa63ab64414bb400c0c6)

在这个简单的示例中，每次用户登录时，我们都会执行redis.setbit（daily_active_users，user_id，1）。 这会将daily_active_users的bitmap中的相应offset翻转为1.这是一个O（1）操作。 对此进行人口统计会导致今天登录的9个唯一身份用户。 键是daily_active_users，值为1011110100100101。

当然，由于每日活跃用户每天都会改变，我们需要一种方法来每天创建一个新的bitmap。 我们只需将日期附加到位图键即可。 例如，如果我们想要计算在给定日期内在音乐应用中播放至少1首歌曲的每日唯一身份用户，我们可以将关键名称设置为播放：yyyy-mm-dd。 如果我们想要计算每小时播放一首歌曲的唯一用户数量，我们可以命名关键名称将播放：yyyy-mm-dd-hh。 对于剩下的讨论，我们将坚持每天播放歌曲的独特用户。 为了收集每日指标，只要用户播放歌曲，我们就会在play：yyyy-mm-dd键中将用户位设置为1。 这是O（1）操作。
```
redis.setbit(play:yyyy-mm-dd, user_id, 1)
```
今天播放歌曲的唯一用户是存储为播放值的bitmap的population count：yyyy-mm-dd键。要计算每周或每月指标，我们可以简单地计算所有每日bitmap的联合（bitop）。 周或月，然后计算结果位图的总体数。

![](https://leanote.com/api/file/getImage?fileId=5bdefa63ab64414bb400c0c8)

您还可以非常轻松地提取更复杂的指标。 例如，11月播放歌曲的高级账户持有者将是：
```
(play:2011-11-01 ∪ play:2011-11-02 ∪...∪play:2011-11-30) ∩ premium:2011-11
```

## **使用1.28亿用户进行性能比较**
下表显示了针对1.28亿用户在1天，7天和30天内计算的每日唯一操作计算的比较。 通过组合每日bitmap计算7和30指标。
|PERIOD	|TIME (MS)|
|:--:|:--:|
|Daily	|50.2|
|Weekly	|392.0|
|Monthly|1624.8|

## 占用空间
```
128000000 bit / 8 = 16000000 byte
16000000 byte / 1024 = 15625 kb
15625 kb / 1024 = 15.285 mb   
```
## **使用 bitmap 实现用户上线次数统计（redis官网）**

Bitmap 对于一些特定类型的计算非常有效。

假设现在我们希望记录自己网站上的用户的上线频率，比如说，计算用户 A 上线了多少天，用户 B 上线了多少天，诸如此类，以此作为数据，从而决定让哪些用户参加 beta 测试等活动 —— 这个模式可以使用 SETBIT 和 BITCOUNT 来实现。

比如说，每当用户在某一天上线的时候，我们就使用 SETBIT ，以用户名作为 key ，将那天所代表的网站的上线日作为 offset 参数，并将这个 offset 上的为设置为 1 。

举个例子，如果今天是网站上线的第 100 天，而用户 peter 在今天阅览过网站，那么执行命令 SETBIT peter 100 1 ；如果明天 peter 也继续阅览网站，那么执行命令 SETBIT peter 101 1 ，以此类推。

当要计算 peter 总共以来的上线次数时，就使用 BITCOUNT 命令：执行 BITCOUNT peter ，得出的结果就是 peter 上线的总天数。


## **性能**
前面的上线次数统计例子，即使运行 10 年，占用的空间也只是每个用户 10*365 比特位(bit)，也即是每个用户 456 字节。对于这种大小的数据来说， BITCOUNT 的处理速度就像 GET 和 INCR 这种 O(1) 复杂度的操作一样快。

如果你的 bitmap 数据非常大，那么可以考虑使用以下两种方法：

- 将一个大的 bitmap 分散到不同的 key 中，作为小的 bitmap 来处理。使用 Lua 脚本可以很方便地完成这一工作。
- 使用 BITCOUNT 的 start 和 end 参数，每次只对所需的部分位进行计算，将位的累积工作(accumulating)放到客户端进行，并且对结果进行缓存 (caching)。
