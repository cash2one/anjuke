建议大家去读以下文档、书籍或者代码：

  * MySQL 官方文档，了解字段定义，使用长度，`explain`的说明等
  * 《高性能MySQL 第三版》讲怎么样设计schema，建立合适的索引，优化查询以及一些常用的技巧，同时理解这些结论得出的原因
  * 《MySQL技术内幕 InnoDB存储引擎》讲的更加深入一些，里面涉及到底层的实现
  * MySQL 源码，看源码比什么其他文档都准确，但是花费时间较多

# 库的选择

选择库（DB）的时候有以下方面需要考虑：

  * 根据不同的级别和功能选择不同的 DB：不能为了一时方便，将不同复制策略、不同用途的表放在同一个库里面，比如线上的 `house_crawler_db` 是为存储爬虫数据用的数据库，它可能只需要一个 master + 一个 slave 就足够。这个时候如果把线上需要的表放在这个 DB 就非常不合适，因为线上有可能根据访问压力增加多个 slave
  * 不同的 DB 的好处是可以方便的做迁移，按照目前我们的框架，可以对 DB 方便更换 IP PORT 等
  * 不同的 DB 带来的坏处是需要多一个数据库连接，这也是个性能损耗

# 建表

## 表名

表名体现里面存取的数据，单词别拼写错误，不要再加`ajk_`这种前缀，这种前缀对我们来说没有任何意义。

## 引擎选择

目前我们都使用的是 InnoDB ，可以了解一下 InnoDB 和 MyISAM 的区别：

### InnoDB

  * 支持事务
  * 行级锁
  * 数据和文件存放在一个文件中
  * 非主键索引，保存的是索引值和主键的id，主键索引是簇集索引，索引的叶子节点即保存了数据
  * 缓存索引和数据

### MyISAM
 
  * 不支持事务
  * 表级锁
  * 数据和文件分开存储
  * 可以通过文件备份、恢复的方法来备份恢复数据库
  * 主键索引和非主键索引保存的方式没有本质区别，里面保存的是索引值和行号
  * 只能缓存索引，数据的缓存交给操作系统


## 性能和容量的考虑

  * 水平拆分考虑
    * 估计一下这个表的数据量会有多大，如果超过百万。且后续会持续增加的，需要考虑做水平拆分
    * 拆分时，有按主键取模拆分，也有按属性拆分，比如不同的城市的数据放在一起，这个需要根据业务需求考虑
    * 当然也可以按照时间来做拆分，总之，需要根据业务的需求来决定

  * 垂直拆分考虑
    * 表里面的字段是否一起使用？如果一个表里面，有很多字段是很少用到的，需要考虑把它移到扩展表里
    * 表里面如果有大字段，不是每次都要使用的，同样需要考虑拆分
    * 表里面某些字段，更新非常频繁，而其他字段几乎不更新，也需要考虑拆分，一方面是为了让更新的表更小，另外一方面是为了方便应用程序做缓存
    * 还有一种可以拆分是建立一个表，它的用途是用来做搜索的，用它来代替索引，好处是可以根据条件建立不同的索引表，如果这个索引不再需求，删除，不再写入数据就行

  * 归档考虑
    * 队列、偏日志用途的表，是否可以考虑按日期归档存储，比如每个月会有一个表，这样利于归档（单单 DELETE 表里面的数据无法立即释放空间，同时会给数据库带来很大的压力）


# 字段选择
  
## 主键

  需要自增主键（我们都是使用 InnoDB），同时，主键一定是数字，这样其他非主键索引保存的索引数据才小，检索索引速度才会快

## 字段类型

  基本的原则都是能选择存储空间小的就选择小的，这基本上是计算机系统里面的基本法则吧。

  * 清楚每个类型都有很多选择，尽量选择占用存储空间小的

    * 整数有很多选择：从 TINYINT 到 BIGINT
    * 字符串也有很多选择：从 CHAR, VARCHAR 到 LONGTEXT
    * 时间也有很多选择： YEAR, TIMESTAMP DATETIME 等

  * 理解字段定义

    * VARCHAR(num) 里面的num是字符的数量，并不是字节的数量（MySQL 5.0 以后）
    * INT(num) 后面的num只用于客户端显示，与实际存储无关
    * 整数类型的 UNSIGNED 可以使保存的正数翻倍
    * CHAR 和 VARCHAR 的选择时，如果存储的字符串的所占用的字节数是固定的，最好选择定长的 CHAR，比如保存 MD5 值；当要 CHAR 列要保存中英文混存的字符（即选择的是多字节字符集，比如 utf8 gbk）时，事实上，它所占用的长度也是变化的。同样的 VARCHAR 比 CHAR 多存储一至两个字节，用于保存长度

  * 根据各种字段的差异，选择合适的字段

    * 时间字段的选择，如果是记录1970年以后且2038年以前，可以使用 TIMESTAMP（不用选择 INT，占用字节一样，还不如 TIMESTAMP 方便），如果要记录更早或者更晚的时间才会考虑用 DATETIME；TIMESTAMP 占用4个字节，而 DATETIME 占用8个字节
    * IP 地址可以使用 INT UNSIGNED 来保存，如果使用 VARCHAR(15) 则会占用更多的空间
    * DOUBLE FLOAT 和 DECIMAL 都是有区别的，DECIMAL 是在要求精确计算时使用


## 其他考虑
  
  * 字段名不要是 MySQL 关键字。
  * 和其他表关联的考虑：关联其他表的主键时，请确保字段类型和其他表主键的类型一致，这样才不会出现溢出的情形
  * 为了后续更新、导入导出数据等考虑：要有最后更新时间，方便做增量更新以及DW数据导出
  * 通常情况下尽量避免 NOT NULL ，原因是查询中如果保存 NULL 的列，MySQL 更难优化，索引、索引统计以及比较会更加复杂，但 NULL 改成 NOT NULL 带来的性能提升较小，故原有的表可以不改
  * 谨慎的使用 ENUM 这种类型，它的好处是在客户端好看，限制可能的值，带来的不便也很明显，就是后续扩展会不方便，需要修改表的定义；当可以确定这个字段的值可能的取值范围时，比如性别，使用 ENUM 则会比较好

# 索引选择

## 基本原则

  * 合理的索引是可以过滤掉大量的数据，通过确定的值获得少量的数据
  * 不好的索引，MySQL 会放弃使用它，转而全表扫描等
  * 索引也是有代价的，会带来另外的磁盘 IO 
    * 显然，需要多写一份数据
    * 再次，先通过索引找到具体的主键id（行号），然后再去读数据；如果通过索引无法很快的找到需要的记录，那还不如直接扫描表里面的数据
  * 能在建表时确定的索引要事先建立好，建好表，有数据后建立的开销大；简单来说，MySQL 会重新建立一个一样的临时表，然后重命名过来

## 选择哪些字段建立索引

  * 审视自己的可能的查询，找出所有的 WHERE ORDER BY 语句
  * 确定这些字段的选择性，如 user_id 这种字段一般来说筛选数据的能力较强，而 sex 用户性别这种筛选数据能力较弱，故，在 sex 这种字段上面建索引没有什么意义
  * 主键和 UNIQUE KEY 本身也是索引

## 理解复合索引

复合索引里面按照定义的字段次序排列好的。

例如，有一个复合索引（a, b），可以简单的理解按以下顺序组织的：先按a字段升序，再按b字段升序的顺序组织，知道就能理解什么时候能够使用到复合索引

依据上面的说明，会有以下结论：

  * 复合索引里面的字段顺序会对使用查询有影响
  * 建立好以某个字段开头的复合索引，就无须再建立这个字段的单个索引，当然，如果是要做唯一索引，那另外考虑
  * 单独查询b字段等于某个值，很明显，需要在索引上面扫描，不是简单的找到一个起始点，然后连续读取，这时候，MySQL 一般会选择不使用这个索引

## 其他要注意的

  * 对于我们经常用的 InnoDB 来说，非主键索引保存的是索引列的值加上主键
  * 当对字符串的列做索引的时候，可以考虑只使用前面的一部分（prefix）：
    * 带来的好处是索引的占用空间小了，速度会快
    * 需要考虑的问题是，使用这些 prefix 建立的索引，是否仍然可以很好的筛选数据

# 查询数据库

## SELECT * 还是 SELECT 需要的字段

  * 为了应用层有很好的缓存包装，SELECT * 会比较方便，但需要表的设计合理
  * 在没有应用层缓存包装的情况下，SELECT 需要的字段一般会比较好
  * 如果可以使用到覆盖索引，可以尝试 SELECT 需要的字段

## 尽量避免的

  * JOIN
  * 子查询

JOIN 和 自查询都会导致锁的表增多，同时缓存无法很好的使用上。

## WHERE 条件

  * ` WHERE name = 'a' AND sex = 2` 和 `WHERE sex = 2 AND name = 'a'` 效果是一样的， MySQL优化器会优化，当然唯一可能的带来的影响是第一个条件生成的查询缓存（QueryCache），第二查询虽然语义上是一样，但是无法命中到
  * 谨慎使用MySQL的函数，在索引列上使用缓存会使导致MySQL无法使用索引，那怕是这种人类认为等效的，比如： `WHERE id + 1 = 1000` 不能使用索引，而 `WHERE id = 999` 则可以（假设key_field上已经有索引）
  * 避免使用MySQL函数的另外一个理由是，它无法正常使用查询缓存
  * 不要让MySQL做隐式转换，因为MySQL很有可能无法正常使用到索引
  * 不要让MySQL做隐式转换的另外一个理由是查询的结果和你期望不一致，可能带来安全隐患，执行以下语句可以看到
`SELECT 'abc' = 0`，它的返回结果是 1 ，也就是条件成立的

## 使用 EXPLAIN

EXPLAIN 会给出 MySQL 执行 SQL 语句的计划；重点需要关注以下列：

`select_type` `type` `rows` `extra`

简单来说以下情况需要特别注意：

  * select_type 记不是 SIMPLE，也不是 PRIMARY
  * type 是 ALL
  * rows远远超出返回结果的大小
  * Extra 里面有 Using temporay （意味着MySQL需要创建一个临时表） 或者 Using filesort （意味着需要再排序，数据少的时候可能在内存中能够完成，数据大的时候有可能需要磁盘）

更详细的信息参考官网的文档

## 使用 SHOW PROCESSLIST

使用这个命令可以或者当前连接到服务器的各个查询情况，如果比较慢，可以看到当前什么查询比较慢。

## 其他注意的

  * SELECT 的时候，OFFSET 不能太大，假设你要执行 LIMIT N OFFSET M，事实上需要对从数据库中找出前 N + M 条记录，然后再从里面提取 N 条记录返回给用户，当 M 非常大时，会非常消耗CPU和IO。这个时候的解决办法是使用另外一个条件来限制，使得 OFFSET 变小，比如使用主键来限制
  * 我们线上有master和slave，当立即插入数据库后立即进行查询，有可能无法查到正确的数据，这时候需要考虑使用缓存或者直接读取数据库
 
# 更新数据库

  * 推荐按主键更新数据
  * 当做批量更新、插入数据时，需要计算一下磁盘的压力，简单来说，做一定量的更新、插入后，sleep 一段时间，因为 MySQL 需要把它写入磁盘，slave需要做同步

# 其他

  * 安全：使用规范的PDO等参数绑定的方式来执行SQL，避免拼凑SQL，拼凑SQL会带来安全隐患
  * 扩展性：SQL 中不要使用`库名 . 表名`的写法，这样做会使调整 DB 的名字，对应的 IP PORT 都非常麻烦，需要修改代码
  * 节省资源：数据库在需要查询的时候才打开连接，因为过早打开数据库连接，当中间有费时的逻辑时，导致占用MySQL的连接长期不释放，这样可能导致 MySQL 的 Too many connection 。所以，需要的时候再打开，读取完成后尽量早点关闭

