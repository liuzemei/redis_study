### redis 常用命令

```shell
keys * # 查看当前库所有的key
exists key # 判断某个 key 是否存在
type key # 查看你的 key 是什么类型
del key # 删除指定的 key 数据
unlink key # 根据 value 选择非阻塞删除
expire key 10 # 为给定的 key 设定过期时间（单位为秒）
ttl key # 查看还有多少秒过期，-1 表示永不过期 -2 表示已过去

select # 切换数据库
dbsize # 查看当前数据库的 key 数量
flushdb # 清空当前库
flushall # 清空全部库
```

### redis 支持数据类型

redis 支持 5 种数据类型

> String List Set Hash Zset

#### 1. Redis 字符串（String)

> 1. String 类型是二进制安全的。
> 2. value 最多可以是 512M 的。

#### 操作

1. 基本操作

```shell
set k1 v1 # 设置 k1 的值为 v1，覆盖之前的值
setnx k1 v2 # 若返回 0 则说明存在，没有设置成功。返回 1 则说明设置成功。


get k1 # 获取 k1 的值

append k1 abc # 在 k1 的后边添加 abc，若没有 k1 则创建 k1 值为 abc
strlen k1 # 获取 k1 的值的长度

# 如果值为数字，
set k2 100
incr k2 # k2=101
decr k2 # k2=100

incrby k2 10 # k2=110
decrby k2 10 # k2=100
```

> incr 具有原子性。是原子操作。由于 Redis 是单线程的，所以这个操作是不会被打断的。
> PS：不会被其他进程或者线程打断的操作，就称之为原子操作。

2. 批量操作

```shell
# 批量设置值 和 读取值
mset k1 v1 k2 v2 k3 v3
mget k1 k2 k3

msetnx k1 v11 k2 v22 # msetnx 具有原子性，必须所有的 key 都不存在时，才会设值成功，否则，全部失败。
```

3. 局部操作

```shell
getrange k1 0 3 # 获取字符串截断的 [0,3] 的部分。 后边也包含
setrange k1 3 abc # 从指定 key 的指定 索引位置 覆盖写入数据
```

4. 高级操作

```shell
setex k4 20 v4 # 设值 key 值为 value 的同时， 设值过期时间（单位为s）
getset k4 30 # 获取旧的值，并用新的值替换旧的值。
```

#### 2. Redis 列表（List）

Redis 列表是简单的字符串列表，底层是一个双向链表，对两端的操作性能很高，通过索引下表的操作中间的节点性能会比较差。

1. 常用命令

```shell
lpush/rpush k1 v1 v2 v3 # 从 左边/右边 插入一个或多个值。 如果不是空且不是 list 则会报错。
lpop/rpop k1 # 从 左边/右边 取出一个值。值再键在，值光键亡。


lrange k1 0 3 # 从左边 拿出 key 值为 0-3 索引的值 索引 0 -1 就表示所有的值
lindex k1 2 # 获取下标为 2 的值
len # 获取列表的长度
```

2. 高级操作

```shell
rpoplpush k1 k2 # 从 k1 列表右边取出一个值，查到 k2 列表左边。
linsert k1 before/after v1 v2 # 在 v1 的 前面/后面 插入 v2
lrem k1 10 v1 # 从左边删除 10 个 v1
lset k1 1 v3 # 设置下标为 1 的值为
```

#### 3. Redis 集合（Set）

Set 的底层实现是采用的`HashMap`

1. 基本操作

```shell
# 增
sadd k1 v1 v2 v3 # 将一个或多个 member 元素加入到 集合 key 中，已存在的 member 元素将被忽略

# 查
smembers k1 # 取出指定 集合 的所有值
srandmember k1 10 # 随机从 指定集合 中取出 n 个值，不会从集合删除。
sismember k1 v1 # 判断集合 k1 中是否含有 v1 ，有1 无0
scard k1 # 获取集合的元素个数
spop k1 # 随机的从该集合中吐出一个值。

# 删
srem k1  v1 v2 # 删除集合中的某个元素
```

2. 高级操作

```shell
smove k1 k2 v1 # 吧集合中的一个值从一个集合移动到另一个集合
sinter k1 k2 # 返回两个集合的交集元素
sunion k1 k2 # 返回两个集合的并集元素
sdiff k1 k2 # 返回两个集合的差集元素（k1有，k2 没有）

```
