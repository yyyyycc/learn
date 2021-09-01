## Redis

> Redis部署方式

+ 单节点模式

  单节点模式只有一个节点，一般用来测试

  缺点：通过持久化功能，Redis保证了即使在服务器重启的情况下也不会丢失（或少量丢失）数据，因为持久化会把内存中数据保存到硬盘上，重启会从硬盘上加载数据。 `但是由于数据是存储在一台服务器上的，如果这台服务器出现硬盘故障等问题，也会导致数据丢失`。

+ 主从模式

  主从模式包括一个主节点和多个从节点，一般来说，`主节点用来读写操作，从节点用户读操作，主节点的数据可以同步到从节点，所以从节点即便支持写操作也没有意义`。

+ 哨兵模式

  + 哨兵模式是基于主从模式的，哨兵模式为了实现主从模式的高可用，监控主从节点的状态，当sentinel发现master节点挂了以后，sentinel就会从slave中重新选举一个master。
  + 一般来说，通过sentinel集群可以管理多个主从Redis，sentinel最好不要和Redis部署在同一台机器，不然Redis的服务器挂了以后，sentinel也挂了。使用sentinel集群也是为了保证Redis的高可用，避免哨兵节点挂了之后影响Redis的使用。
  + 当使用sentinel模式的时候，客户端就不要直接连接Redis，而是连接sentinel的ip和port，由sentinel来提供具体的可提供服务的Redis实现，这样当master节点挂掉以后，sentinel就会感知并将新的master节点提供给使用者。
  + sentinel模式基本可以满足一般生产的需求，具备高可用性。但是当数据量过大到一台服务器存放不下的情况时，主从模式或sentinel模式就不能满足需求了，这个时候需要` 对存储的数据进行分片，将数据存储到多个Redis实例中 ` 。

+ 集群模式

  + cluster的出现是为了解决单机Redis容量有限的问题，将Redis的数据根据一定的规则分配到多台机器。

  + 采用去中心化的思想，没有中心节点的说法，它使用hash slot方式将16348个hash slot覆盖到所有节点上，对于存储的每个key值，使用CRC16（KEY）&16348=slot得到他对应的hash slot，并在访问key的时候就去找他的hash slot在哪一个节点上，然后由当前访问节点从实际被分配了这个hash slot的节点去取数据，节点之间使用轻量协议通信 减少带宽占用 性能很高
  + 其内部中也需要配置主从，并且内部也是采用哨兵模式，如果有半数节点发现某个异常节点，共同决定更改异常节点的状态，如果异常节点是主节点，则对应的从节点自动顶替为主节点，当原先的主节点上线后，则会变为从节点。

> Redis数据结构（RedisObject）

```c
/*
 * Redis 对象
 */
typedef struct redisObject {

    // 类型
    unsigned type:4;

    // 对齐位
    unsigned notused:2;

    // 编码方式
    unsigned encoding:4;

    // LRU 时间（相对于 server.lruclock）
    unsigned lru:22;

    // 引用计数
    int refcount;

    // 指向对象的值
    void *ptr;

} robj;

/*
 * 对象类型
 */
#define REDIS_STRING 0  // 字符串
#define REDIS_LIST 1    // 列表
#define REDIS_SET 2     // 集合
#define REDIS_ZSET 3    // 有序集
#define REDIS_HASH 4    // 哈希表

/*
 * 对象编码
 */
#define REDIS_ENCODING_RAW 0            // 编码为字符串
#define REDIS_ENCODING_INT 1            // 编码为整数
#define REDIS_ENCODING_HT 2             // 编码为哈希表
#define REDIS_ENCODING_LINKEDLIST 4     // 编码为双端链表
#define REDIS_ENCODING_ZIPLIST 5        // 编码为压缩列表
#define REDIS_ENCODING_INTSET 6         // 编码为整数集合
#define REDIS_ENCODING_SKIPLIST 7       // 编码为跳跃表

/*
* ptr
* ptr 是一个指针, 指向实际保存值的数据结构, 这个数据结构由 type 属性和 encoding 属性决定.
*/

/*
举个例子, 如果一个 redisObject 的 type 属性为 REDIS_LIST, encoding 属性为 REDIS_ENCODING_LINKEDLIST, 那么这个对象就是一个 Redis 列表, 它的值保存在一个双端链表内, 而 ptr 指针就指向这个双端链表;
*/
```

> RedisObject对应的底层数据结构

![2](.\images\2.png)

> 主从复制原理

+ 第一阶段是建立链接、协商同步
+ 第二阶段是主服务器同步数据给从服务器
+ 第三阶段是主服务器发送新写操作命令给从服务器

![3](.\images\3.png)