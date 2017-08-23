---
layout: post
title:  "数据库操作(2):SQL2.md"
date:   2016-12-03
excerpt: "自己通过数据库参考书以及视频内容总结数据库操作以及基本知识"
tag:
- mDNS
comments: true
---

## SQL语言之DML部分@数据库操作语言【搬运数据】
### 6.常用操作:增[insert] 删[delete] 改[update] 查[select]

**1.INSERT:**

insert into table_name (col1, col2,....) values (value1, value2,....)---**"插入值"与"列"要一一对应**

**2.DELETE**

deletefrom表名where条件【不加条件删除整个表】--对于关系型数据库:”增"和"删"都是相对整个一行数据来说的

**3.UPDATE**

update 表名set列1=新值1,列2=新值2...where条件---修改指定列(修改所有就不用加where)

**4.★★★SELECT★★★**

select(列1,列2,列3,....)from表名where条件 limit 0,100;[时间函数：select uid,userid,username,email,FROM_UNIXTIME(addtime,'%Y年%m月%d') from members]

【更新和删除操作要注意:where条件记得要加，除非对生活心灰意冷了否则还是加上比较好--不加影响的将是整个表的数据】
- select的5种子句:
- where子句；--条件查询
- groupby子句；--分组查询
- having 子句；--筛选查询
- order by子句；--排序查询
- limit 子句；--范围查询

**==5种子句写的时候要有严格的顺序：where | group by | having | order by | limit==**

## 7.SELECT条件查询模型深入理解【重点】
==== 列是"变量"===== 变量就可以计算 =====

select uid, name, age+1 from user;--从user表中查找所有uid, name,age三列，并给age列所在值+1
==where是"表达式"==值为真【true】假【false】==
select * from user where id=5;--从user表中查找所有列，当id为5
- 【判断所在行id=5?==>返回true则输出】
- select * from user where 1;--从user表中查找所有列，当条件恒真--【输出所有】
- select * from user where 0;--从user表中查找所有列，当条件恒假--【返回Empty】
- select 语句还可以配合算数运算符、逻辑运算符和位运算符以及相关函数写出更高效率的查询语句
- 【当然要注意运算符的优先级】
查询的实质：对磁盘上的数据文件进行查询得到结果集，并将结果集存放到内存中，其余就是对内存结果集的操作

# 查询练习:

## 查询出第4和第11列的信息:
select goods_id, goods_name, shop_price from goods where goods_id =4 or goods_id=11;
select goods_id, goods_name, shop_price from goods where goods_id in(4,11);
查询出第4到第11列间的信息:
select goods_id, goods_name, shop_price from goods where goods_id>4 and goods_id < 11;
select goods_id, goods_name, shop_price from goods where goods_id between 4 and 11;
模糊查询(like)--%通配任意字符; _ 通配单一字符

取出名字以"诺基亚"开头的商品
select goods_id,cat_id,goods_name,shop_price from e cs_goods where goods_name like '诺基亚%';
取出名字为"诺基亚Nxx"的手机
select goods_id,cat_id,goods_name,shop_price from ecs_goods where goods_name like '诺基亚N__';
取出名字不以"诺基亚"开头的商品
select goods_id,cat_id,goods_name,shop_price from ecs_goos where goods_name not like '诺基亚%';
当涉及到多重条件查询需要用到运算符,and , or ,not,...之类的来修饰条件时候：

- 1)一定要先弄清楚条件之间的分类
- 2)使用( ) 将其分类--避免因为优先级问题

### 奇怪的NULL查询

对于NULL=NULL==>返回假;==>NULL是什么都没有,所以不能比较！使用is null 才能查询
select * from user where name is not null --查询出user表中name字段不为空的信息
【对于数据表中，null不利于数据表优化操作，所以数据表中一般都对字段设置not null】
### GOUP BY分组与统计函数

group by -- 当出现group by分组中不能配对的情况，该字段取查询时候第一次出现的值
统计函数：
max()--最大值；
min()--取最小值；
avg()--求平均值;	
sum()--求和；
count()--计算行数/条数；	
distinct()--求有多少种不同解；
 

### 【时间是以时间戳的形式存放的，是int型，max() --最新商品; min() -- 最旧商品】
### having筛选结果集

- 1.查询goods表中商品比市场价低出多少？
select goods_id, goods_name,(market_price-shop_price) from goods 
- 2.查询goods表中商品比市场价低出至少200的商品？
select goods_id, goods_name,(market_price-shop_price) from goods where (market_price - shop_price) > 200;
error：查询goods表中商品比市场价低出至少200的商品？
select goods_id, goods_name,(market_price-shop_price) as 'min' from goods where min > 200;
报错:不识别min这个列！
- 【where子句针对的对象是磁盘上的数据表文件去select的，而select出来后的数据是存放在内存中的一个零时"结果集"】

--因此：当使用where min >200 ；去筛选结果集的时候是不能识别出min字段的
having--针对的对象是内存表结构中的"结果集"
- 3.查询goods表中商品比市场价低出至少200的商品？
select goods_id, goods_name,(market_price-shop_price) as '节省' from goods where 1 having '节省' >200;
- 【如果同时写了where和having子句，where子句肯定要写在having子句前面，因为having子句是针对where子句查询出来的结果集来操作的】

### order by排序查询【在内存中排序】 与 limit范围查询【--经典应用:分页类】

- 1:按价格由高到低排序
select goods_id,goods_name,shop_price from goods order by shop_price desc;
- 2:按发布时间由早到晚排序
select goods_id,goods_name,add_time from goods order by add_time;
- 3:接栏目由低到高排序,栏目内部按价格由高到低排序【有冲突时，顺序决定优先】
select goods_id,cat_id,goods_name,shop_price from goods order by cat_id ,shop_price desc;
- 4:取出价格最高的前三名商品
select goods_id,goods_name,shop_price from goods order by shop_price desc limit 3;
- 5:取出点击量前三名到前5名的商品
select goods_id,goods_name,click_count from goods order by click_count desc limit 2,3;
子句的查询陷阱

如何：查询goods表中，每个栏目(cat_id) 下最新(goods_id最大)的那件商品？
错误示范:【正确答案见下】
思路：
- 1)最新的商品 -- max(good_id)
- 2)每个栏目--group by cat_id

mysql> select max(goods_id), goods_name, cat_id, shop_price from goods group by cat_id;

+---------------+-----------------------+--------+------------+
| max(goods_id) | goods_name | cat_id | shop_price |
+---------------+-----------------------+--------+------------+
| 16 | 恒基伟业g101 | 2 | 823.33 |
| 32 | 飞利浦9@9v | 3 | 399.00 |
除了数据goods_id对了其他 ✘
| 18 | kd876 | 4 | 1388.00 |
| 23 | 诺基亚n96 | 5 | 3700.00 |
| 7 | 诺基亚n85原装充电器 | 8 | 58.00 |
| 6 | 索爱原装m2卡读卡器 | 11 | 20.00 |
| 26 | 小灵通/固话50元充值卡 | 13 | 48.00 |
| 30 | 移动100元充值卡 | 14 | 90.00 |
| 28 | 联通100元充值卡 | 15 | 95.00 |
+---------------+-----------------------+--------+------------+

这里错在：“先查询在排序”==>group by cat_id ,但goods_name, shop_price，我们应该取谁的呢？--解决思路:用到“子查询”/连接查询==>先排序再查询
子查询 之 where子查询[以内层查询结果作为外层的比较条件]

- 1:查找出goods表中最新的那件商品信息?
思考问题：1.如何保证每次更新商品后，取得都是最新的呢？☛ 涉及到了"变量"☛"列"就是变量
- 2.查询的条件可以是个表达式☛但是表示得到的要是一个“明确”的量才可以查询
- 3.数据库查询☛"投影式"查询[要那列查那列,查的那列和其他列没关系]
--第3点典型错误: select max(goods_id), goods_name, shop_price from goods;--除了goods_id对,其余都是错的！这是个有语义缺陷的语句
子语句查询:select goods_id,goods_name,shop_price from goods where goods_id = 
( select max(goods_id) from goods );
以查询select max( goods_id ) from user;的返回结果【存放在内存中，且无论如何该结果都是一个"定值"】作为对前方查询语句的条件
### 2.如何：查询goods表中，每个栏目(cat_id) 下最新(goods_id最大)的那件商品？
思路整理:(从上面的错误范例已可以得到正确思路==>先"排序" 再"查询")
#### 1.排序==>有题目可知,排序的变量应该是cat_id字段,通过排序找到每一个cat_id下中goods_id最大的那个商品ID号
#### 2.查询==>用排序得到的那个最大ID号作为条件表达式的对比条件，查找出商品信息
- 1.先"排序:"mysql> select max(goods_id), cat_id, shop_price from goods group by cat_id;
+---------------+--------+------------+
| max(goods_id) | cat_id | shop_price |
+---------------+--------+------------+
| 16 | 2 | 823.33 |
| 32 | 3 | 399.00 |
| 18 | 4 | 1388.00 |
| 23 | 5 | 3700.00 |
| 7 | 8 | 58.00 |
| 6 | 11 | 20.00 |
| 26 | 13 | 48.00 |
| 30 | 14 | 90.00 |
| 28 | 15 | 95.00 |
+---------------+--------+------------+
9 rows in set (0.00 sec)
- 2.再"查询":mysql> select good_id, goods_name, shop_price from goods where goods_id in (select max(goods_id) from goods group by cat_id);
- 
+----------+------------------------------+------------+
| goods_id | goods_name | shop_price |
+----------+------------------------------+------------+
| 6 | 胜创kingmax内存卡 | 42.00 |
| 7 | 诺基亚n85原装立体声耳机hs-82 | 100.00 |
| 16 | 恒基伟业g101 | 823.33 |
| 18 | 夏新t5 | 2878.00 |
| 23 | 诺基亚n96 | 3700.00 |
| 26 | 小灵通/固话20元充值卡 | 19.00 |
| 28 | 联通50元充值卡 | 45.00 |
| 30 | 移动20元充值卡 | 18.00 |
| 32 | 诺基亚n85 | 3010.00 |
+----------+------------------------------+------------+
2.查询出编号为19的商品的栏目名称[栏目名称放在category表中](用左连接查询和子查询分别)
#### WHERE型子查询：
1.先找出外层条件的内层结果--goods表中第19号商品的cat_id：select cat_id from goods where goods_id = 19; 
2.查询:select cat_name from category where cat_id = ( select cat_id from goods where goods_id = 19 );
子查询 之 from子查询【将查询出来的结果集当成一个新"表"来操作】

2.如何：查询goods表中，每个栏目(cat_id) 下最新(goods_id最大)的那件商品？--使用from子查询
同样的思路==>先排序再查询
排序：mysql> select goods_id, goods_name, shop_price from order by cat_id asc, goods_id DESC; 
得到一张优先按照cat_id升序,再goods_id降序的"表"-----同一个cat_id的商品，它在"表"里出现的位置是第一个
排序：mysql> select goods_id, goods_name, shop_price from order by cat_id asc, goods_id DESC; 
查询：mysql> select goods_id, goods_name,shop_price from 
(select goods_id,cat_id, goods_name, shop_price from goods order by cat_id ) as tmp 
group by cat_id;
#### 子查询 之 exists子查询【"存在"】

1.用exists型子查询，查出所有商品的栏目下有商品的栏目
mysql> select * from category where exists (select * from goods where goods.cat_id = category.cat_id);
查找category这个表，如果select * from goods where goods.cat_id = category.cat_id这个"表"中对应的数据存在则查询+--------+-------------------+-----------+
| cat_id | cat_name | parent_id |
+--------+-------------------+-----------+
| 2 | CDMA手机 | 1 |
| 3 | GSM手机 | 1 |
| 4 | 3G手机 | 1 |
| 5 | 双模手机 | 1 |
| 8 | 耳机 | 6 |
| 11 | 读卡器和内存卡 | 6 |
| 13 | 小灵通/固话充值卡 | 12 |
| 14 | 移动手机充值卡 | 12 |
| 15 | 联通手机充值卡 | 12 |
+--------+-------------------+-----------+
9 rows in set (0.00 sec)
内连接查询[inner join]、左连接[left join]、右连接[right join]

【MySQL中没有外连接】
详解：http://www.dedecms.com/knowledge/data-base/sql-server/2012/0709/2872.html
内连接：select xxxx from table1 inner join table2 on table1.xx=table2.xx ☛ 交集
左连接：select xxxx from table1 left join table2 on table1.xx=table2.xx ☛ 左表为基础的查询
右连接：select xxxx from table1 right join table2 on table1.xx=table2.xx ☛ 右表为基础的查询
1.查询价格大于2000元的商品及其栏目名称
思路：
--涉及到两个表；--基础表为goods表,连接表为category表，条件为shop_price > 2000
--goods表cat_id中的和category表中的cat_id对应
mysql > select goods.goods_id, category.cat_name, goods.goods_name, goods.shop price from 
- > goods left join category 
- > on goods.cat_id = category.cat_id 
- > where goods.shop_price > 2000; 
2.取出第4个栏目下的商品的商品名,栏目名,与品牌名
select goods_name,cat_name,shop_price from goods left join category on goods.cat_id=category.cat_id where goods.cat_id = 4

#### union查询:将2条或多条SQL的查询结果合并成1个结果集

注意：
- 1)取的两个表投影查找的字段列数要相同，列名可不一致(默认使用第一个表的列名)否则
- 2)如果碰到完全相同的行，将会被合并【合并是非常耗时的☛使用 union all 就不需要比较字段合并了】
- 3)union查询的内部子句中不用写order by子句，意义不大！但是可以对查询合并后id结果集进行排列
1.同时查询goods表中cat_id为2和4的商品
select goods_id,cat_id,goods_name from goods where cat_id =2 
union
select goods_id, cat_id,goods_name from goods where cat_id = 4 (order by )
union查询面试题

将A、B表中id值相同的两个num值相加
A表: 
+------+------+ 
| id | num | 
+------+------+ 
| a | 5 | 
| b | 10 | 
| c | 15 | 
| d | 10 | 
+------+------+ B表:
+------+------+
| id | num |
+------+------+
| b | 5 |
| c | 15 |
| d | 20 |
| e | 99 |
+------+------+mysql> # 合并 ,注意all的作用
mysql> select * from ta 
        -> union all
        -> select * from tb;
+------+------+
| id | num |
+------+------+
| a | 5 |
| b | 10 |
| c | 15 |
| d | 10 |
| b | 5 |
| c | 15 |
| d | 20 |
| e | 99 |
+------+------+
将上面查询的"结果集"当做是一个新表
参考答案:

mysql> # sum,group求和

mysql> select id,sum(num)

        ->from

        ->(select * from ta union all select * from tb) as tmp

        ->group by id;

+------+----------+

| id | sum(num) |

+------+----------+

| a | 5 |

| b | 15 |

| c | 25 |

| d | 30 |

| e | 99 |

+------+----------+

5 rows in set (0.00 sec)
