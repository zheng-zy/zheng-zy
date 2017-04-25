---
title: mysql单表优化方案
description: 当MySQL单表记录数过大时，增删改查性能都会急剧下降，可以参考以下步骤来优化
tags: MySQL
categories: tech
abbrlink: 32375
date: 2017-01-17 17:19:44
---
除非单表数据未来会一直不断上涨，否则不要一开始就考虑拆分，拆分会带来逻辑、部署、运维的各种复杂度，一般以整型值为主的表在千万级以下，字符串为主的表在五百万以下是没有太大问题的。而事实上很多时候MySQL单表的性能依然有不少优化空间，甚至能正常支撑千万级以上的数据量：

1. 字段

* 尽量使用TINYINT、SMALLINT、MEDIUM_INT作为整数类型而非INT，如果非负则加上UNSIGNED
* VARCHAR的长度只分配真正需要的空间
* 使用枚举或整数代替字符串类型
* 尽量使用TIMESTAMP而非DATETIME，
* 单表不要有太多字段，建议在20以内
* 避免使用NULL字段，很难查询优化且占用额外索引空间
* 用整型来存IP

2. 索引

* 索引并不是越多越好，要根据查询有针对性的创建，考虑在WHERE和ORDER BY命令上涉及的列建立索引，可根据EXPLAIN来查看是否用了索引还是全表扫描
* 应尽量避免在WHERE子句中对字段进行NULL值判断，否则将导致引擎放弃使用索引而进行全表扫描
* 值分布很稀少的字段不适合建索引，例如"性别"这种只有两三个值的字段
* 字符字段只建前缀索引
* 字符字段最好不要做主键
* 不用外键，由程序保证约束
* 尽量不用UNIQUE，由程序保证约束
* 使用多列索引时主意顺序和查询条件保持一致，同时删除不必要的单列索引

3. 查询SQL

* 可通过开启慢查询日志来找出较慢的SQL
* 不做列运算：SELECT id WHERE age + 1 = 10，任何对列的操作都将导致表扫描，它包括数据库教程函数、计算表达式等等，查询时要尽可能将操作移至等号右边
* sql语句尽可能简单：一条sql只能在一个cpu运算；大语句拆小语句，减少锁时间；一条大sql可以堵死整个库
* 不用SELECT *
* OR改写成IN：OR的效率是n级别，IN的效率是log(n)级别，in的个数建议控制在200以内
* 不用函数和触发器，在应用程序实现
* 避免%xxx式查询
* 少用JOIN
* 使用同类型进行比较，比如用'123'和'123'比，123和123比
* 尽量避免在WHERE子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描
* 对于连续数值，使用BETWEEN不用IN：SELECT id FROM t WHERE num BETWEEN 1 AND 5
* 列表数据不要拿全表，要使用LIMIT来分页，每页数量也不要太大
* 连表查询采用`left join on`，而不用`where =`，因为on是经过mysql底层优化 
* 最左前缀匹配原则，非常重要的原则，MySQL会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如`a = 1 and b = 2 and c > 3 and d = 4` 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。

* 尽量选择区分度高的列作为索引,区分度的公式是count(distinct col)/count(*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录。

* 尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会 逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。

* 索引列不能参与计算，保持列“干净”，比如`from_unixtime(create_time) ='2014-05-29'`就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成`create_time = unix_timestamp('2014-05-29')`;应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用。

* 索引而进行全表扫描，如：

`select id from t where num is null`可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：

`select id from t where num=0`

* 应尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：

`select id from t where num=10 or num=20`可以这样查询：

`select id from t where num=10 union all select id from t where num=20`

* 下面的查询也将导致全表扫描（不能前置百分号）：

`select id from t where name like '%abc%'`

若要提高效率，可以考虑全文检索。

* in 和 not in 也要慎用，否则会导致全表扫描，如：

`select id from t where num in(1,2,3)`对于连续的数值，能用 between 就不要用 in 了：

`select id from t where num between 1 and 3`

4. 引擎

目前广泛使用的是MyISAM和InnoDB两种引擎：

MyISAM

MyISAM引擎是MySQL 5.1及之前版本的默认引擎，它的特点是：

* 不支持行锁，读取时对需要读到的所有表加锁，写入时则对表加排它锁
* 不支持事务
* 不支持外键
* 不支持崩溃后的安全恢复
* 在表有读取查询的同时，支持往表中插入新纪录
* 支持BLOB和TEXT的前500个字符索引，支持全文索引
* 支持延迟更新索引，极大提升写入性能
* 对于不会进行修改的表，支持压缩表，极大减少磁盘空间占用

InnoDB

InnoDB在MySQL 5.5后成为默认索引，它的特点是：

* 支持行锁，采用MVCC来支持高并发
* 支持事务
* 支持外键
* 支持崩溃后的安全恢复
* 不支持全文索引

总体来讲，MyISAM适合SELECT密集型的表，而InnoDB适合INSERT和UPDATE密集型的表

5. 系统调优参数

可以使用下面几个工具来做基准测试：

* sysbench：一个模块化，跨平台以及多线程的性能测试工具
* iibench-mysql：基于 Java 的 MySQL/Percona/MariaDB 索引进行插入性能测试工具
* tpcc-mysql：Percona开发的TPC-C测试工具

具体的调优参数内容较多，具体可参考官方文档，这里介绍一些比较重要的参数：

* back_log：back_log值指出在MySQL暂时停止回答新请求之前的短时间内多少个请求可以被存在堆栈中。也就是说，如果MySql的连接数据达到max_connections时，新来的请求将会被存在堆栈中，以等待某一连接释放资源，该堆栈的数量即back_log，如果等待连接的数量超过back_log，将不被授予连接资源。可以从默认的50升至500
* wait_timeout：数据库连接闲置时间，闲置连接会占用内存资源。可以从默认的8小时减到半小时
* max_user_connection: 最大连接数，默认为0无上限，最好设一个合理上限
* thread_concurrency：并发线程数，设为CPU核数的两倍
* skip_name_resolve：禁止对外部连接进行DNS解析，消除DNS解析时间，但需要所有远程主机用IP访问
* key_buffer_size：索引块的缓存大小，增加会提升索引处理速度，对MyISAM表性能影响最大。对于内存4G左右，可设为256M或384M，通过查询show status like 'key_read%'，保证key_reads / key_read_requests在0.1%以下最好
* innodb_buffer_pool_size：缓存数据块和索引块，对InnoDB表性能影响最大。通过查询show status like 'Innodb_buffer_pool_read%'，保证 (Innodb_buffer_pool_read_requests – Innodb_buffer_pool_reads) / Innodb_buffer_pool_read_requests越高越好
* innodb_additional_mem_pool_size：InnoDB存储引擎用来存放数据字典信息以及一些内部数据结构的内存空间大小，当数据库对象非常多的时候，适当调整该参数的大小以确保所有数据都能存放在内存中提高访问效率，当过小的时候，MySQL会记录Warning信息到数据库的错误日志中，这时就需要该调整这个参数大小
* innodb_log_buffer_size：InnoDB存储引擎的事务日志所使用的缓冲区，一般来说不建议超过32MB
* query_cache_size：缓存MySQL中的ResultSet，也就是一条SQL语句执行的结果集，所以仅仅只能针对select语句。当某个表的数据有任何任何变化，都会导致所有引用了该表的select语句在Query Cache中的缓存数据失效。所以，当我们的数据变化非常频繁的情况下，使用Query Cache可能会得不偿失。根据命中率(Qcache_hits/(Qcache_hits+Qcache_inserts)*100))进行调整，一般不建议太大，256MB可能已经差不多了，大型的配置型静态数据可适当调大.
* 可以通过命令show status like 'Qcache_%'查看目前系统Query catch使用大小
* read_buffer_size：MySql读入缓冲区大小。对表进行顺序扫描的请求将分配一个读入缓冲区，MySql会为它分配一段内存缓冲区。如果对表的顺序扫描请求非常频繁，可以通过增加该变量值以及内存缓冲区大小提高其性能
* sort_buffer_size：MySql执行排序使用的缓冲大小。如果想要增加ORDER BY的速度，首先看是否可以让MySQL使用索引而不是额外的排序阶段。如果不能，可以尝试增加sort_buffer_size变量的大小
* read_rnd_buffer_size：MySql的随机读缓冲区大小。当按任意顺序读取行时(例如，按照排序顺序)，将分配一个随机读缓存区。进行排序查询时，MySql会首先扫描一遍该缓冲，以避免磁盘搜索，提高查询速度，如果需要排序大量数据，可适当调高该值。但MySql会为每个客户连接发放该缓冲空间，所以应尽量适当设置该值，以避免内存开销过大。
* record_buffer：每个进行一个顺序扫描的线程为其扫描的每张表分配这个大小的一个缓冲区。如果你做很多顺序扫描，可能想要增加该值
* thread_cache_size：保存当前没有与连接关联但是准备为后面新的连接服务的线程，可以快速响应连接的线程请求而无需创建新的
* table_cache：类似于thread_cache_size，但用来缓存表文件，对InnoDB效果不大，主要用于MyISAM

6. 升级硬件

Scale up，这个不多说了，根据MySQL是CPU密集型还是I/O密集型，通过提升CPU和内存、使用SSD，都能显著提升MySQL性能
