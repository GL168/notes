#### MySQL监控参数

##### 1.查看xx库所有表信息

###### 参数详解

1. Name(表名称)
2. Engine(存储引擎)
3. Version(版本)
4. Row_format(行格式。对于MyISAM引擎，这可能是Dynamic，Fixed或Compressed。动态行的行长度可变
5. 例如Varchar或Blob类型字段。固定行是指行长度不变，例如Char和Integer类型字段。)
6. Rows(表中的行数。对于非事务性表，这个值是精确的，对于事务性引擎，这个值通常是估算的。)
7. Avg_row_length(平均每行字节数)
8. Data_length(整个表数据量)
9. Max_data_length(表示容纳最大数据量)
10. index_length(索引占用磁盘的空间大小)
11. data_free(对于MyISAM引擎，标识已分配，但现在未使用的空间，并且包含了已被删除行的空间。)
12. auto_increment(下一个Auto_increment的值)
13. create_time(表的创建时间)
14. update_time(表最近更新时间)
15. check_time(使用 check table 或myisamchk工具检查表的最近时间)
16. collation(默认字符和排序规则)
17. checksum(如果启用，则对整个表的内容计算时的校验和)
18. create_options(指表创建时的其他所有选项)
19. comment(注释)

###### 演示SQL

1. show table status from xx;
2. show table status from xx LIKE 'xx';

##### 2.查看xx表索引信息

###### 参数详解

1. Table	表示创建索引的数据表名，这里是 tb_stu_info2 数据表。
2. Non_unique	表示该索引是否是唯一索引。若不是唯一索引，则该列的值为 1；若是唯一索引，则该列的值为 0。
3. Key_name	表示索引的名称。
4. Seq_in_index	表示该列在索引中的位置，如果索引是单列的，则该列的值为 1；如果索引是组合索引，则该列的值为每列在索引定义中的顺序。
5. Column_name	表示定义索引的列字段。
6. Collation	表示列以何种顺序存储在索引中。在 MySQL 中，升序显示值“A”（升序），若显示为 NULL，则表示无分类。
7. Cardinality	索引中唯一值数目的估计值。基数根据被存储为整数的统计数据计数，所以即使对于小型表，该值也没有必要是精确的。基数越大，当进行联合时，MySQL 使用该索引的机会就越大。
8. Sub_part	表示列中被编入索引的字符的数量。若列只是部分被编入索引，则该列的值为被编入索引的字符的数目；若整列被编入索引，则该列的值为 NULL。
9. Packed	指示关键字如何被压缩。若没有被压缩，值为 NULL。Null	用于显示索引列中是否包含 NULL。若列含有 NULL，该列的值为 YES。若没有，则该列的值为 NO。
10. Index_type	显示索引使用的类型和方法（BTREE(类似二叉树)、FULLTEXT(全文索引,MyISAM引擎支持)、HASH、RTREE(较少使用,仅geometry支持)）。
11. Comment	显示评注。

###### 演示SQL

1. SHOW INDEX FROM xx;

##### 3.查看explain执行计划

###### 参数详解

1. id 序号

   id相同时，执行顺序由上至下

   如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行

   id如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id值越大，优先级越高，越先执行

2.  select_type 查询子句类型

SIMPLE(简单SELECT,不使用UNION或子查询等)

PRIMARY(查询中若包含任何复杂的子部分,最外层的select被标记为PRIMARY)

UNION(UNION中的第二个或后面的SELECT语句)

DEPENDENT UNION(UNION中的第二个或后面的SELECT语句，取决于外面的查询)

UNION RESULT(UNION的结果)

SUBQUERY(子查询中的第一个SELECT)

DEPENDENT SUBQUERY(子查询中的第一个SELECT，取决于外面的查询)

DERIVED(派生表的SELECT, FROM子句的子查询)

UNCACHEABLE SUBQUERY(一个子查询的结果不能被缓存，必须重新评估外链接的第一行)

3. table 显示这一行的数据是关于哪张表的，有时不是真实的表名字,看到的是derivedx(x是个数字)

4. type

   常用的类型有： **ALL, index, range, ref, eq_ref, const, system, NULL（从左到右，性能从差到好）**

   ALL：Full Table Scan， MySQL将遍历全表以找到匹配的行

   index: Full Index Scan，index与ALL区别为index类型只遍历索引树

   range:只检索给定范围的行，使用一个索引来选择行

   ref: 表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值

   eq_ref: 类似ref，区别就在使用的索引是唯一索引，对于每个索引键值，表中只有一条记录匹配，简单来说，就是多表连接中使用primary key或者 unique key作为关联条件

   const、system: 当MySQL对查询某部分进行优化，并转换为一个常量时，使用这些类型访问。如将主键置于where列表中，MySQL就能将该查询转换为一个常量,system是const类型的特例，当查询的表只有一行的情况下，使用system

   NULL: MySQL在优化过程中分解语句，执行时甚至不用访问表或索引，例如从一个索引列里选取最小值可以通过单独索引查找完成。

5. possible_keys

   指出MySQL能使用哪个索引在表中找到记录，查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询使用

   该列完全独立于EXPLAIN输出所示的表的次序。这意味着在possible_keys中的某些键实际上不能按生成的表次序使用。
   如果该列是NULL，则没有相关的索引。在这种情况下，可以通过检查WHERE子句看是否它引用某些列或适合索引的列来提高你的查询性能。如果是这样，创造一个适当的索引并且再次用EXPLAIN检查查询

6. key 实际使用键的索引

   如果没有选择索引，键是NULL。要想强制MySQL使用或忽视possible_keys列中的索引，在查询中使用FORCE INDEX、USE INDEX或者IGNORE INDEX。

7. key_len 根据表定义计算的字节数

   不损失精确性的情况下，长度越短越好

8. ref  连接匹配条件，即哪些列或常量被用于查找索引列上的值

9. rows  表示MySQL根据表统计信息及索引选用情况，估算的找到所需的记录所需要读取的行数

10. extra 包含MySQL解决查询的详细信息

    Using where:列数据是从仅仅使用了索引中的信息而没有读取实际的行动的表返回的，这发生在对表的全部的请求列都是同一个索引的部分的时候，表示mysql服务器将在存储引擎检索行后再进行过滤

    Using temporary：表示MySQL需要使用临时表来存储结果集，常见于排序和分组查询

    Using filesort：MySQL中无法利用索引完成的排序操作称为“文件排序”

    Using join buffer：改值强调了在获取连接条件时没有使用索引，并且需要连接缓冲区来存储中间结果。如果出现了这个值，那应该注意，根据查询的具体情况可能需要添加索引来改进能。

    Impossible where：这个值强调了where语句会导致没有符合条件的行。

    Select tables optimized away：这个值意味着仅通过使用索引，优化器可能仅从聚合函数结果中返回一行

###### 演示SQL

explain select * from t_order_outbound t where t.tracking_number='124545';
进一步查询优化，使用show warnings 返回查询语句

explain extended select * from t_order_outbound where id = 1;
show warnings;

###### 其他：

EXPLAIN不会告诉你关于触发器、存储过程的信息或用户自定义函数对查询的影响情况。
EXPLAIN不考虑各种Cache。
EXPLAIN不能显示MySQL在执行查询时所作的优化工作，部分统计信息是估算的，并非精确值。
EXPALIN只能解释SELECT操作，其他操作要重写为SELECT后查看执行计划。


<center><a href="../db_menu.md">返回</a></center>
