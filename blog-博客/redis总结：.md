# redis总结：

#### 一，什么是redis？

     Redis 是 Remote Dictionary Server 的缩写，是一个开源的、基于内存的键值存储数据库。它不仅支持简单的键值对，还支持丰富的数据结构，如字符串、哈希、列表、集合、位图、HyperLogLogs等。同时，Redis 也支持持久化，能够将内存中的数据异步保存到磁盘上，是内存数据库中最具代表性的产品之一。

      与MySQL数据库不同的是，Redis的数据是存在**内存**中的。它的读写速度非常快，每秒可以处理超过10万次读写操作。因此redis被**广泛应用于缓存**，另外，Redis也经常用来做分布式锁。除此之外，Redis支持事务、持久化、[LUA 脚本](https://zhida.zhihu.com/search?content_id=235651583&content_type=Article&match_order=1&q=LUA+%E8%84%9A%E6%9C%AC&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTgyMDAxMDYsInEiOiJMVUEg6ISa5pysIiwiemhpZGFfc291cmNlIjoiZW50aXR5IiwiY29udGVudF9pZCI6MjM1NjUxNTgzLCJjb250ZW50X3R5cGUiOiJBcnRpY2xlIiwibWF0Y2hfb3JkZXIiOjEsInpkX3Rva2VuIjpudWxsfQ.NUt6kPAEpaNGFb91H_jJeB2m9FIPNmLqG-TGdTV3wX8&zhida_source=entity)、LRU 驱动事件、多种集群方案

****

#### 二，redis的常用应用场景：

- **缓存**：利用其高速读写能力，Redis常用于缓存频繁访问的数据，提高应用程序的响应速度。

- **会话存储**：Redis可用于存储和管理用户会话数据，处理会话信息。

- **消息队列**：利用发布订阅功能和列表数据结构，Redis可以实现消息队列的功能。

- **实时排行榜**：有序集合数据结构非常适合实现实时排行榜功能。

****

**三，redis的数据类型 :**

 1.基本数据类型：

- String（字符串）
- Hash（哈希）
- List（列表）
- Set（集合）
- zset（有序集合）

 2.特殊的数据结构类型 :

- Geospatial
- Hyperloglog
- Bitmap

****

##### 四， redis常用操作命令：

- **启动 Redis 服务**：

` redis-server `

- **连接 Redis 客户端**：

` redis-cli `

- **停止 Redis 服务**：
  
  `redis-cli shutdown ``

- **测试连通性**：

`redis-cli ping `

****

##### Key 操作命令 :

- **获取所有键**：

`keys *`

- **获取键总数**：

`dbsize`

- **判断键是否存在**：

`exists key`

- **删除键**：

`del key`

- **设置键的过期时间**：

`expire key seconds`

- **查询键的生命周期**：

`ttl key`

- **重命名键**：

`rename key newkey`

- **清除当前数据库**：

`flushdb`

- **清除所有数据库**：

`flushall`

****

##### 数据类型操作命令 :

**字符串类型 (String) :**

**简介:** String是Redis最基础的数据结构类型，它是二进制[安全](https://link.zhihu.com/?target=https%3A//activity.huaweicloud.com/free_test/index.html%3Futm_source%3Dhwc-csdn%26utm_medium%3Dshare-op%26utm_campaign%3D%26utm_content%3D%26utm_term%3D%26utm_adplace%3DAdPlace070851)的，可以[存储](https://link.zhihu.com/?target=https%3A//activity.huaweicloud.com/free_test/index.html%3Futm_source%3Dhwc-csdn%26utm_medium%3Dshare-op%26utm_campaign%3D%26utm_content%3D%26utm_term%3D%26utm_adplace%3DAdPlace070851)图片或者序列化的对象，值最大[存储](https://link.zhihu.com/?target=https%3A//activity.huaweicloud.com/free_test/index.html%3Futm_source%3Dhwc-csdn%26utm_medium%3Dshare-op%26utm_campaign%3D%26utm_content%3D%26utm_term%3D%26utm_adplace%3DAdPlace070851)为512M

- **设置值**：

`set key value`

- **获取值**：

`get key`

- **删除值**：

`del key`

- **递增值**：

`incr key`

- **递减值**：

`decr key`

- **追加值**：

`append key value`

- **获取值的长度**：

`strlen key`

****

**哈希类型 (Hash) :**

**简介:** 在Redis中，哈希类型是指v（值）本身又是一个键值对（k-v）结构

**内部编码:**  `ziplist(压缩列表)`，`hashtable(哈希表)`

- **设置字段值**：

`hset hash field value`

- **获取字段值**：

`hget hash field`

- **删除字段**：

`hdel hash field`

- **获取所有字段和值**：

`hgetall hash`

<mark>注意点：</mark>如果开发使用hgetall，哈希元素比较多的话，可能导致Redis阻塞，可以使用hscan。而如果只是获取部分field，建议使用hmget。

****

**列表类型 (List) :**

**简介:** 列表（list）类型是用来[存储](https://link.zhihu.com/?target=https%3A//activity.huaweicloud.com/free_test/index.html%3Futm_source%3Dhwc-csdn%26utm_medium%3Dshare-op%26utm_campaign%3D%26utm_content%3D%26utm_term%3D%26utm_adplace%3DAdPlace070851)多个有序的字符串，一个列表最多可以[存储](https://link.zhihu.com/?target=https%3A//activity.huaweicloud.com/free_test/index.html%3Futm_source%3Dhwc-csdn%26utm_medium%3Dshare-op%26utm_campaign%3D%26utm_content%3D%26utm_term%3D%26utm_adplace%3DAdPlace070851)2^32-1个元素。

**内部编码：** `ziplist（压缩列表）`、`linkedlist（链表）`

- **从左边添加元素**：

`lpush list value`

- **从右边添加元素**：

`rpush list value`

- **获取列表长度**：

`llen list`

- **获取指定范围的元素**：

`lrange list start end`

- **删除最左边的元素**：

`lpop list`

- **删除最右边的元素**：

`rpop list`

****

**集合类型 (Set) :**

**简介:** 集合（set）类型也是用来保存多个的字符串元素，但是不允许重复元素

**内部编码：**`intset（整数集合）`、`hashtable（哈希表）`

- **添加元素**：

`sadd set value`

- **获取所有元素**：

`smembers set`

- **删除元素**：

`srem set value`

- **获取集合大小**：

`scard set`

****

**有序集合类型 (Sorted Set) :**

**简介：** 已排序的字符串集合，同时元素不能重复

**底层内部编码:** `ziplist(压缩列表)`，`skiplist(跳跃表)`

- **添加元素及其分数**：

`zadd zset score value`

- **获取所有元素**：

`zrange zset start end`

- **删除元素**：

`zrem zset value`

- **获取集合大小**：

`zcard zset`
