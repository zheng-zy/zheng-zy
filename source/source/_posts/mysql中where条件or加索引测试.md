---
title: mysql中where条件or加索引测试
description: mysql中where条件or加索引测试
tags: MySQL
categories: 技术
abbrlink: 8961
date: 2016-10-14 10:32:05
---
<!-- more -->
### 测试表建表语句
```
CREATE DATABASE /*!32312 IF NOT EXISTS*/`test` /*!40100 DEFAULT CHARACTER SET utf8 */;

USE `test`;

/*Table structure for table `user` */

DROP TABLE IF EXISTS `user`;

CREATE TABLE `user` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `uname` varchar(20) DEFAULT NULL,
  `ucreatetime` datetime DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `uuid` varchar(50) DEFAULT NULL,
  UNIQUE KEY `id` (`id`),
  KEY `idx_uuid` (`uuid`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
<!-- more -->
### 插入千万数据存储过程
```
DELIMITER $$

USE `test`$$

DROP PROCEDURE IF EXISTS `test1`$$

CREATE DEFINER=`root`@`localhost` PROCEDURE `test1`()
BEGIN
DECLARE v_cnt DECIMAL (10)  DEFAULT 0 ;
dd:LOOP 
          INSERT  INTO usertb VALUES
        (NULL,'用户1','2010-01-01 00:00:00',20,UUID()),
        (NULL,'用户2','2010-01-01 00:00:00',20,UUID()),
        (NULL,'用户3','2010-01-01 00:00:00',20,UUID()),
        (NULL,'用户4','2010-01-01 00:00:00',20,UUID()),
        (NULL,'用户5','2011-01-01 00:00:00',20,UUID()),
        (NULL,'用户6','2011-01-01 00:00:00',20,UUID()),
        (NULL,'用户7','2011-01-01 00:00:00',20,UUID()),
        (NULL,'用户8','2012-01-01 00:00:00',20,UUID()),
        (NULL,'用户9','2012-01-01 00:00:00',20,UUID()),
        (NULL,'用户0','2012-01-01 00:00:00',20,UUID())
            ;
                  COMMIT;
                    SET v_cnt = v_cnt+10 ;
                           IF  v_cnt = 20000000 THEN LEAVE dd;
                          END IF;
         END LOOP dd ;
END$$

DELIMITER ;
```

### 运行
```
CALL test1;
```

### 分析
```
EXPLAIN
SELECT * FROM usertb a WHERE a.`id`=1 OR a.`uuid` = '4cbdd0da-91b1-11e6-84cc-f48e38a25f50' OR a.`uuid` = '4cbd86a0-91b1-11e6-84cc-f48e38a25f50';
```
### 结果
```
id	select_type	table	partitions	type	possible_keys	key	key_len	ref	rows	filtered	Extra
1	SIMPLE	a	\N	index_merge	id,idx_uuid	idx_uuid,id	153,8	\N	3	100.00	Using sort_union(idx_uuid,id); Using where
```

OR查询会让你的所有优化都白做，索引也用不上，在这里好像还可以使用，需要再实践一下。

[mysql分析说明](http://dev.mysql.com/doc/refman/5.7/en/index-merge-optimization.html)
