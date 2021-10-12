

# SQL🐋

## 什么是SQL

SQL（Structured Query Language）指结构化查询语言，其实就是定义了操作所有关系型数据库的规则。

每一种数据库操作的方式存在不一样的地方，称为“方言”。



## 注释

```sql
-- 注释方式一
#  注释方式二
/* 注释方式三*/
```



## collations

collations：字符集排序规则

_ci：Case Insensitive，即大小写不敏感

_cs：Case Sensitive，即大小写敏感的

```sql
show collation  -- 查看所有mysql支持的collate
```

参考：[COLLATE详解](https://www.jianshu.com/p/f8707b8461d3)



## SQL的执行顺序

手写：

```sql
SELECT DISTINCT <select_list>
FROM  <left_table> <join_type>
JOIN  <right_table> ON <join_condition>
WHERE  <where_condition>
GROUP BY  <group_by_list>
HAVING <having_condition>
ORDER BY <order_by_condition>
LIMIT <limit_number>
```



机读：

```sql
FROM  <left_table>
ON <join_condition>
<join_type> JOIN  <right_table> 
WHERE  <where_condition>
GROUP BY  <group_by_list>
HAVING <having_condition>
SELECT
DISTINCT <select_list>
ORDER BY <order_by_condition>
LIMIT <limit_number>
```



## 建表语句

```sql
CREATE DATABASE shop CHARSET = utf8;

CREATE TABLE Product
(
    product_id     CHAR(4)      NOT NULL,
    product_name   VARCHAR(100) NOT NULL,
    product_type   VARCHAR(32)  NOT NULL,
    sale_price     INTEGER,
    purchase_price INTEGER,
    regist_date    DATE,
    PRIMARY KEY (product_id)
);

INSERT INTO Product
VALUES ('0001', 'T恤衫', '衣服', 1000, 500, '2009-09-20');
INSERT INTO Product
VALUES ('0002', '打孔器', '办公用品', 500, 320, '2009-09-11');
INSERT INTO Product
VALUES ('0003', '运动T恤 ', '衣服', 4000, 2800, NULL);
INSERT INTO Product
VALUES ('0004', '菜刀', '厨房用具', 3000, 2800, '2009-09-20');
INSERT INTO Product
VALUES ('0005', '高压锅', '厨房用具', 6800, 5000, '2009-01-15');
INSERT INTO Product
VALUES ('0006', '叉子', '厨房用具', 500, NULL, '2009-09-20');
INSERT INTO Product
VALUES ('0007', '擦菜板', '厨房用具', 880, 790, '2008-04-28');
INSERT INTO Product
VALUES ('0008', '圆珠笔', '办公用品', 100, NULL, '2009-11-11');


CREATE TABLE Product2
(
    product_id     CHAR(4)      NOT NULL,
    product_name   VARCHAR(100) NOT NULL,
    product_type   VARCHAR(32)  NOT NULL,
    sale_price     INTEGER,
    purchase_price INTEGER,
    regist_date    DATE,
    PRIMARY KEY (product_id)
);
INSERT INTO Product2
VALUES ('0001', 'T恤衫', '衣服', 1000, 500, '2008-09-20');
INSERT INTO Product2
VALUES ('0002', '打孔器 ', '办公用品', 500, 320, '2009-09-11');
INSERT INTO Product2
VALUES ('0003', '运动T恤 ', '衣服', 4000, 2800, NULL);
INSERT INTO Product2
VALUES ('0009', '手套', '衣服', 800, 500, NULL);
INSERT INTO Product2
VALUES ('0010', '水壶', '厨房用具 ', 2000, 1700, '2009-09-20');

CREATE TABLE ShopProduct
(
    shop_id    CHAR(4)      NOT NULL,

    shop_name  VARCHAR(200) NOT NULL,
    product_id CHAR(4)      NOT NULL,
    quantity   INTEGER      NOT NULL,
    PRIMARY KEY (shop_id, product_id)
);

INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000A', '东京', '0001', 30);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000A', '东京', '0002', 50);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000A', '东京', '0003', 15);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000B', '名古屋', '0002', 30);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000B', '名古屋', '0003', 120);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000B', '名古屋', '0004', 20);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000B', '名古屋', '0006', 10);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000B', '名古屋', '0007', 40);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000C', '大阪', '0003', 20);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000C', '大阪', '0004', 50);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000C', '大阪', '0006', 90);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000C', '大阪', '0007', 70);
INSERT INTO ShopProduct (shop_id, shop_name, product_id, quantity)
VALUES ('000D', '福冈', '0001', 100);
```



# MySQL🐋

## 文件目录

如果使用 apt 命令安装 mysql，则安装后目录如下：

可执行文件：`/usr/bin/`

安装后的软件位置：`/usr/share/mysql`



## 安装

👉 Windows

1. 解压  `mysql-5.7.21-winx64.zip`  到指定目录（C:\Program Files\mysql-5.7.21-winx64）

2. 配置环境变量：`C:\Program Files\mysql-5.7.21-winx64\bin`

3. 在 `C:\Program Files\mysql-5.7.21-winx64` 目录下新建 `my.ini`

   ```sh
   [mysql]
   # 设置mysql客户端默认字符集
   default-character-set=utf8 
   [mysqld]
   # 设置3306端口
   port = 3306 
   # 设置mysql的安装目录
   basedir=C:\Program Files\mysql-5.7.21-winx64
   # 设置mysql数据库的数据的存放目录
   datadir=C:\Program Files\mysql-5.7.21-winx64\data
   # 允许最大连接数
   max_connections=200
   # 设置mysql服务端默认字符集
   character-set-server=utf8
   # 创建新表时将使用的默认存储引擎
   default-storage-engine=INNODB 
   ```

4. 初始化mysql： `mysqld --initialize --user=mysql --console`，记住mysql提供的临时密码

5. 安装mysql服务：`mysqld install MySQL --defaults-file="C:\Program Files\mysql-5.7.21-winx64\my.ini"`

6. 进入mysql：`mysql -u root -p"xxxxxxxx"`

7. 修改密码：`set password = password("new_password");`

> 参考：https://blog.csdn.net/we_are_the_world_123/article/details/79230537





👉  Linux

1. 安装并初始化 mysql

   ```sh
   sudo apt install mysql-server
   
   # 初始化mysql
   sudo mysql_secure_installation  
   ```



2. 登录 mysql 并进行必要配置

   ```sh
   sudo mysql -uroot -p
   ```

   如果只有 sudo 才能登录 mysql，可以用以下方式解决：

   查看 root 用户的权限，将 `auth_sock` 改为 `mysql_native_password`

   ```mysql
   use mysql;
   select user, host, plugin from user; 
   update user set plugin='mysql_native_password'
   ```

   操作完成后，修改 root 用户密码

   <img src="http://store.secretcamp.cn/uPic/image-20210518104050002202105181040501621305650h1mL36h1mL36.png" alt="image-20210518104050002" style="zoom:50%;" />

   

   创建新用户：

   ```mysql
   # 创建一个新用户并拥有全部的权限
   grant all on *.* to superkuang@"%" identified by "kzqkzq";
   
   # 刷新权限
   FLUSH PRIVILEGES;
   
   # 查看用户是否创建成功
   select user, host, plugin from user; 
   ```

   

3. 暴露 mysql

   将配置文件中的 `bind-address` 修改为 `0.0.0.0`

   ```sh
   # 复制配置文件
   sudo cp /etc/mysql/mysql.conf.d/mysqld.cnf /etc/mysql/my.cnf
   
   #  修改配置文件
   sudo vim /etc/mysql/my.cnf
   
   # 重启mysql
   sudo /etc/init.d/mysql restart  
   ```

   

>  https://blog.csdn.net/weixx3/article/details/80782479
>  https://www.cnblogs.com/QingXiaxu/p/9415379.html



👉  Mac OS

```shell
# 安装mysql
brew install mysql@5.7

# 启动mysql
brew services start mysql@5.7  # 后台自动运行mysql
/usr/local/opt/mysql@5.7/bin/mysql.server start # 仅当前运行，重启不自动运行

mysql_secure_installation  # 初始化mysql

export LDFLAGS="-L/usr/local/opt/mysql@5.7/lib"
export CPPFLAGS="-I/usr/local/opt/mysql@5.7/include"
```







## 卸载

👉  Linux

1. 首先在终端中查看MySQL的依赖项：`dpkg --list|grep mysql`
2. 卸载： `sudo apt remove mysql-common`
3. 卸载：`sudo apt autoremove --purge mysql-server-5.7`
4. 清除残留数据：`dpkg -l|grep ^rc|awk '{print$2}'|sudo xargs dpkg -P`
5. 再次查看 MySQL 的剩余依赖项：`dpkg --list|grep mysql`
6. 继续删除剩余依赖项，如：`sudo apt autoremove --purge mysql-apt-config`



👉 Mac OS

```sh
brew uninstall mysql

# 以下文件或目录删干净
rm -rf /usr/local/var/mysql
mr -rf /usr/local/etc/my.cnf
```



## 启停服务

```sh
service mysql start
service mysql status
service mysql stop
```



## 登陆

```mysql
# 远程连接mysql
mysql -h106.75.81.25 -usuperkuangzero -p199794kzq  
```





## shell命令

```shell
mysql -h {{database_host}} {{database_name}}
```



# 数据库操作🐋

DDL（Data Definition Language）：数据定义语言，用来定义数据库对象：数据库，表，列等。

关键字：create， drop，alter 等   



## 创建数据库 CREATE

```mysql
 create database my_db charset=utf8;
```



## 删除数据库 DROP

```sql
 drop database 数据库名称; -- 删除数据库
 drop database if exists 数据库名称; -- 判断数据库存在，存在再删除
```



## 切换数据库 USE

```sql
select database(); -- 查询当前正在使用的数据库名称
use 数据库名称; -- 使用数据库
```



## 修改数据库 ALTER

```sql
alter database 数据库名称 character set 字符集名称; -- 修改数据库的字符集
```



添加主键约束

```sql
alter table Course
	add constraint Course_pk
		primary key (c_id);
```



修改表名

```

```







## 显示数据库相关信息 SHOW

```sql
# 查询所有数据库的名称
show databases; 

# 查询 mysql 数据库当前连接的用户及会话信息
show full processlist

# 查询隔离级别
show variables like '%transaction_isolation';

# 查看支持的存储引擎
show engines

# 显示mysql数据保存路径
show variables like 'datadir'
```









# 表操作🐋

## 创建表 CREATE

创建表，设置主键

```sql
CREATE TABLE customers
(
    cust_id      int      NOT NULL AUTO_INCREMENT,
    cust_name    char(32) NOT NULL,
    cust_address CHAR(64) NULL,
    cust_city    CHAR(16) NULL,
    primary key (cust_id)
) ENGINE = InnoDB;
```



创建表的同时通过关键字 `KEY` 或者 `INDEX` 设置索引，

```sql
CREATE TABLE customers
(
    cust_id      int      NOT NULL AUTO_INCREMENT,
    cust_name    char(32) NOT NULL,
    cust_address CHAR(64) NULL,
    cust_city    CHAR(16) NULL,
    primary key (cust_id),
    key (cust_id, cust_name)  
) ENGINE = InnoDB;
```



## 删除表 DELETE

```sql
delete from 表名 [where 条件]
```





## 插入数据 INSERT

```sql
insert into 表名(列名1, 列名2, ... 列名n) values(值1, 值2, ... 值n);
```





## 更新数据 UPDATE

```sql
update 表名 set 列名1 = 值1, 列名2 = 值2, ... [where 条件];

delete from 表名; -- 删除所有记录，有多少条记录就会执行多少次删除操作
TRUNCATE TABLE 表名; -- 删除所有记录，效率更高，先删除表，然后再创建一张一样的表
```



## 查询数据 SELECT

```sql
select * from 表名;

/* 语法: */ 
	select: 字段列表
	from: 表名列表
	where: 条件列表
	group by: 分组字段
	having: 分组之后的条件
	order by: 排序
	limit: 分页限定
```



### 检索不同行 DISTINCT

```sql
SELECT DISTINCT sex FROM student;
```



### UNIQUE



### ALIAS



### 返回条数限制 LIMIT

```sql
LIMIT 限制的条数
LIMIT 开始的索引, 限制的条数

SELECT * FROM student LIMIT 2, 3;
```



### 偏移量 OFFSET

```sql
OFFSET 偏移的条数

-- 等价
LIMIT 4 OFFSET 3
LIMIT 3, 4
```



## 显示表相关信息 SHOW

```sql
-- 显示全列名
SHOW FULL COLUMNS FROM table_name
-- 显示创建语句
show create table tablename
-- 显示表状态
show table status
```





# 数据过滤🐋

## WHERE



## IN

IN 是把外表和内表做 hash 连接，先查询内表，再把内表结果与外表匹配，对外表使用索引（外表效率高，可用大表），而内表多大都需要查询，不可避免，故外表大的使用 IN，可加快效率。



## EXISTS

EXISTS 语法可以理解为：将主查询的数据，放到子查询中做条件验证，根据验证结果（TRUE 或 FALSE）来决定主查询的数据结果是否得以保留。

```sql
SELECT … FROM table WHERE EXISTS (subquery)
```



## NOT



## LIKE



## LIMIT&OFFSET

以下两种写法含义相同

```sql
SELECT * FROM Student LIMIT 3,1;

SELECT * FROM Student LIMIT 1 OFFSET 3;
```



## BETWEEN

BETWEEN ... AND 会选取介于两个值之间的数据范围，包括两端。



## CASE表达式





# 聚合与排序🐋

## 聚合函数

聚合函数：将一列数据作为一个整体，进行纵向的计算。

聚合函数的计算会排除 NULL 值

- count：计算个数
- MAX：计算最大值
- MIN：计算最小值
- SUM：计算和
- AVG：计算平均值





## DISTINCT

*DISTINCT* 关键字可以计算出不重复的数据

```sql
SELECT COUNT(DISTINCT product_type) from Product;
```



## GROUP BY

```sql
group by 分组字段;
```



## HAVING

HAVING 可以为分组结果指定条件

```sql
SELECT < 列名1>, < 列名2>, < 列名3>, ……
FROM <表名>
GROUP BY <列名1>,< 列名2>, < 列名3>,…… 
HAVING <分组结果对应的条件>;
```



```sql
SELECT product_type, COUNT(*) from Product group by product_type HAVING COUNT(*) >= 2;
```



## ORDER BY

- ASC：升序
- DESC：降序

```sql
SELECT < 列名1>, < 列名2>, < 列名3>, ……
FROM <表名>
GROUP BY <列名1>,< 列名2>, < 列名3>,…… ;
```



```sql
SELECT * FROM Product ORDER BY sale_price DESC;
```



# 用户管理🐋

## 添加用户 CREATE

host：指定该用户在哪个主机上可以登陆，如果是本地用户可用 localhost，如果想让该用户可以从任意远程主机登陆，可以使用通配符 `%`

```sql
CREATE USER 'username'@'host' IDENTIFIED BY '密码';
```



## 删除用户 DROP

```sql
DROP USER 'username'@'host';
```



## 修改用户密码 UPDATE

```sql
UPDATE USER SET PASSWORD = PASSWORD('新密码') WHERE USER = '用户名';

SET PASSWORD FOR 'username'@'host' = PASSWORD('新密码');
```



## 查询用户 SELECT

```sql
USE mysql;
SELECT * FROM USER;
```



# 权限管理🐋

## 查询权限 SHOW

```sql
SHOW GRANTS FOR 'username'@'host';
```



## 授予权限 GRANT

host 如果是 "%"，则表示所有 ip 地址

```sql
GRANT 权限列表 ON 数据库名.表名 TO 'username'@'host';
GRANT ALL ON *.* TO 'kzq'@'localhost';
```



## 撤销权限 REVOKE

```sql
REVOKE 权限列表 ON 数据库名.表名 FROM 'username'@'host';
REVOKE UPDATE ON db.`account` FROM 'kzq'@'%';
```



## 刷新权限 FLUSH

```sql
 flush privileges;  -- 更新权限
```





# 约束 CONSTRIANT🐋

约束：对表中的数据进行限定，保证数据的正确性、有效性和完整性。	

约束的分类：

1. 主键约束：primary key
2.  非空约束：not null
3. 唯一约束：unique
4. 外键约束：foreign key



## 主键约束

主键的定义：

1. 主键非空且唯一
2. 一张表只能有一个字段为主键
3. 主键就是表中记录的唯一标识
```sql
1. 在创建表时，添加主键约束
    create table stu(
        id int primary key,-- 给id添加主键约束
        name varchar(20)
    );

2. 删除主键
    -- 错误 alter table stu modify id int ;
    ALTER TABLE stu DROP PRIMARY KEY;

3. 创建完表后，添加主键
    ALTER TABLE stu MODIFY id INT PRIMARY KEY;

4. 自动增长：
    -- 概念：如果某一列是数值类型的，使用 auto_increment 可以来完成值得自动增长

    -- 在创建表时，添加主键约束，并且完成主键自增长
    create table stu(
        id int primary key auto_increment, -- 给id添加主键约束
        name varchar(20)
    );
```



## 非空约束

非空约束：not null，即值不能为null

```sql
1. 创建表时添加约束
    CREATE TABLE stu(
        id INT,
        NAME VARCHAR(20) NOT NULL -- name为非空
    );
2. 创建表完后，添加非空约束
    ALTER TABLE stu MODIFY NAME VARCHAR(20) NOT NULL;

3. 删除name的非空约束
    ALTER TABLE stu MODIFY NAME VARCHAR(20);
```



## 唯一约束

唯一约束：unique，值不能重复

```sql
1. 创建表时，添加唯一约束
    CREATE TABLE stu(
        id INT,
        phone_number VARCHAR(20) UNIQUE -- 添加唯一约束
    );
    -- 注意mysql中，唯一约束限定的列的值可以有多个null

2. 删除唯一约束	
    ALTER TABLE stu DROP INDEX phone_number;

3. 在创建表后，添加唯一约束
    ALTER TABLE stu MODIFY phone_number VARCHAR(20) UNIQUE;
```



## 外键约束

外键约束：foreign key，让表于表产生关系，从而保证数据的正确性。









# 复杂查询🐋

## 视图

从 SQL 的角度来看视图就是一张表，但两者的区别在于表中保存的是实际的数据，而视图中保存的是 SELECT 语句，视图本身并不存储数据。

视图的优点：

- 视图无需保存数据，因此可以节省存储设备的容量。
- 可以将频繁使用的 SELECT 语句保存成视图，这样 就不用每次都重新书写了。



### 创建视图

```sql
CREATE VIEW 视图名称 (<视图列名1>, <视图列名2>, …… ) 
AS
<SELECT 语句 >
```



## 子查询

子查询就是将一个查询的结果作为另一个查询（主查询）的数据来源或判断条件的查询。

ProductSum  就是子查询的名称，但由于该名称是一次性的，因此不会像视图那样保存在存储介质之中， 在 SELECT 语句执行之后就消失了。

```sql
# 子查询
SELECT product_type, product_type, COUNT(*) cnt_product
FROM Product
GROUP BY product_type;

# 主查询
SELECT product_type, cnt_product
FROM (SELECT product_id, product_type, COUNT(*) cnt_product FROM Product GROUP BY product_type) ProductSum;
```



## 标量子查询

标量子查询有一个特殊的限制，那就是必须而且只能返回 1 行 1 列的结果。

对于 “查询出销售单价高于平均销售单价的商品” 这样的需求，可能会写出如下 SQL：

```sql
# 错误SQL
SELECT product_id, product_name, sale_price
FROM Product
WHERE sale_price > AVG(sale_price);
```

但实际上是错误的，因为 WHERE 子句中不能使用聚合函数。

而标量子查询可以作为一个标量使用，也就可以作为 WHERE 语句中的判断条件：

```sql
SELECT product_id, product_name, sale_price
FROM Product
WHERE sale_price > (SELECT AVG(sale_price) FROM Product);
```



SQL 中任何使用单一值的地方都可以替换成标量子查询



## 关联子查询

对于 “选取出各商品种类中高于该商品种类的平均销售单价的商品” 这样的需求，用以下查询会出错：

错误的原因是大于号后面的的子查询会返回三条结果

```sql
# 错误SQL
SELECT product_id, product_name, sale_price
FROM Product
WHERE sale_price > (SELECT AVG(sale_price) FROM Product GROUP BY product_type);
```



可以在上面的 SQL 中加入关联条件：

起到关键作用的就是在子查询中添加的 WHERE 子句的条件 `p1.product_type = p2.product_type` ， 该条件的是， 在同一商品种类中对各商品的销售单价和平均单价进行比较。

```sql
SELECT product_type, product_name, sale_price
FROM Product p1
WHERE sale_price >
      (SELECT AVG(sale_price) FROM Product p2 WHERE p1.product_type = p2.product_type 
       GROUP BY p1.product_type);
```



关联子查询执行时，数据库内部执行情况：

<img src="http://store.secretcamp.cn/uPic/image-202108201101160252021082011011616294284765FJqQk5FJqQk.png" alt="image-20210820110116025" style="zoom:50%;" />





# 函数🐋

## 算术函数

## 字符串函数

## 日期函数

## 转换函数



# 集合运算🐋

集合运算必须遵守的规则：

- 作为运算对象的记录的列数必须相同
- 作为运算对象的记录中列的类型必须一致
- 可以使用任何 SELECT 语句，但 ORDER BY 子句只能在最后使用一次



## UNION

```sql
SELECT * FROM Product UNION SELECT * FROM Product2;
```



## UNION ALL

在 UNION 的结果中保留重复行，只需要在 UNION 后面添加 ALL 关键字

```sql
SELECT * FROM Product UNION ALL SELECT * FROM Product2;
```



## INTERSECT

选取两个记录集合中公共部分

MySQL 中没有该运算符！

```sql
SELECT * FROM Product INTERSECT SELECT * FROM Product2;
```



## EXCEPT

减法运算

MySQL 中没有该运算符！





# 联结查询🐋

联结（ JOIN ）运算就是将其他表中的列添加过来



## 内联结

内联结只能选取出同时存在于两张表中的数据

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.sale_price
FROM ShopProduct SP
         INNER JOIN Product P ON SP.product_id = P.product_id
WHERE SP.shop_id = '000A';
```



## 外联结

### 左联结

LEFT JOIN = LEFT OUT JOIN

使用 LEFT 时 FROM 子句中写在左侧的表是主表

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.sale_price
FROM ShopProduct SP
         LEFT OUTER JOIN Product P ON SP.product_id = P.product_id;
```



### 右联结

RIGHT JOIN = RIGHT OUT JOIN

使用 RIGHT 时右侧的表是主表

```SQL
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.sale_price
FROM ShopProduct SP
         RIGHT OUTER JOIN Product P ON SP.product_id = P.product_id;
```





## 交叉联结

交叉连接是其他所有联结的基础，运算符是 CROSS JOIN（笛卡儿积），了解即可。

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name
FROM ShopProduct AS SP
         CROSS JOIN Product AS P;
```





# 索引🐋

## 索引命名规范

主键索引名为 `pk_字段名`

唯一索引名为 `uk_字段名`

普通索引名则 `idx_字段名`



## 创建索引

创建索引可以通过 CREATE 和 ALTER 语句

```sql
# 普通索引
CREATE INDEX index_name ON person (column_name);
ALTER TABLE person add INDEX index_name (column_name);

# 主键索引
ALTER TABLE person add PRIMARY KEY index_name (column_name);

# 唯一索引
CREATE UNIQUE INDEX index_name ON person (column_name);
ALTER TABLE person add UNIQUE index_name (column_name);

# 多列索引
CREATE INDEX index_name ON person (column_name);
ALTER TABLE person add INDEX index_name (column_1, column2, column3);
```



## 删除索引

创建索引可以通过 DROP 和 ALTER 语句

```sql
# 删除索引
DROP INDEX index_name ON `table_name`
ALTER TABLE table_name DROP INDEX index_name

# 删除主键索引
ALTER TABLE table_name DROP PRIMARY KEY
```



## 建表时创建索引

```sql
create table person
(
    id      int auto_increment,
    name    varchar(16)   not null,
    age     int           not null,
    sex     int default 0 not null,
    phone   varchar(11)   not null,
    ssn     varchar(18)   not null,
    company varchar(128)  not null,
    job     varchar(64)   not null,
    # 主键索引
    primary key pk_id_age (id, age),
    # 普通索引
    index idx_phone (phone asc),
    # 唯一索引
    unique index uk_ssn (ssn asc)
);
```



## 查看索引

```sql
SHOW INDEX FROM `table`;
SHOW KEYS FROM `table`;
```



# 事务🐋

## 查询隔离级别

```sql
select @@tx_isolation; --  数据库查询隔离级别

set global transaction isolation level  级别字符串;  -- 数据库设置隔离级别 
set global transaction isolation level read uncommitted;  -- 设置隔离级别为 读已提交
```



## 事务操作

```sql
start transaction;  -- 开启事务
rollback;  -- 回滚
commit; -- 提交
```









# 50道数据库题🐋

建表语句：

```sql
# 创建db
CREATE DATABASE school CHARSET=utf8;


# 学生表
CREATE TABLE student(
  id VARCHAR(10),
  name VARCHAR(10),
  age DATETIME,
  sex VARCHAR(10)
);

insert into student values('01' , '赵雷' , '1990-01-01' , '男');
insert into student values('02' , '钱电' , '1990-12-21' , '男');
insert into student values('03' , '孙风' , '1990-05-20' , '男');
insert into student values('04' , '李云' , '1990-08-06' , '男');
insert into student values('05' , '周梅' , '1991-12-01' , '女');
insert into student values('06' , '吴兰' , '1992-03-01' , '女');
insert into student values('07' , '郑竹' , '1989-07-01' , '女');
insert into student values('09' , '张三' , '2017-12-20' , '女');
insert into student values('10' , '李四' , '2017-12-25' , '女');
insert into student values('11' , '李四' , '2017-12-30' , '女');
insert into student values('12' , '赵六' , '2017-01-01' , '女');
insert into student values('13' , '孙七' , '2018-01-01' , '女');

# 课程表
create table course(
  id varchar(10),
  name varchar(10),
  teacher_id varchar(10)
);

insert into course values('01' , '语文' , '02');
insert into course values('02' , '数学' , '01');
insert into course values('03' , '英语' , '03');

create table teacher(
  id varchar(10),
  name varchar(10)
);

insert into teacher values('01' , '张三');
insert into teacher values('02' , '李四');
insert into teacher values('03' , '王五');

# 分数表
create table score(
  student_id varchar(10),
  cource_id varchar(10),
  score decimal(18,1)
);

insert into score values('01' , '01' , 80);
insert into score values('01' , '02' , 90);
insert into score values('01' , '03' , 99);
insert into score values('02' , '01' , 70);
insert into score values('02' , '02' , 60);
insert into score values('02' , '03' , 80);
insert into score values('03' , '01' , 80);
insert into score values('03' , '02' , 80);
insert into score values('03' , '03' , 80);
insert into score values('04' , '01' , 50);
insert into score values('04' , '02' , 30);
insert into score values('04' , '03' , 20);
insert into score values('05' , '01' , 76);
insert into score values('05' , '02' , 87);
insert into score values('06' , '01' , 31);
insert into score values('06' , '03' , 34);
insert into score values('07' , '02' , 89);
insert into score values('07' , '03' , 98);
```



1. 查询 01 课程比 02 课程成绩高的学生的信息及课程分数

   ```sql
   SELECT t1.s_id id, t1.score course01_score, t2.score course02_score
   FROM (SELECT s_id, score FROM Score WHERE c_id = '01') t1,
        (SELECT s_id, score FROM Score WHERE c_id = '02') t2
   WHERE t1.score > t2.score
     AND t1.s_id = t2.s_id;
   ```

   



2. 查询同时存在 01 课程和 02 课程的学生情况

   ```sql
   SELECT t1.s_id id, t1.score score1, t2.score score2
   FROM (SELECT * FROM Score WHERE c_id = '01') t1,
        (SELECT * FROM Score WHERE c_id = '02') t2
   WHERE t1.s_id = t2.s_id;
   ```



3. 查询存在 01 课程但可能不存在 02 课程的学生 id 以及分数，不存在时显示为 NULL

   ```sql
   SELECT t1.s_id id, t1.score score1, t2.score score2
   FROM (SELECT * FROM Score WHERE c_id = '01') t1
            LEFT JOIN (SELECT * FROM Score WHERE c_id = '02') t2
                      ON t1.s_id = t2.s_id;
   ```



4. 查询不存在 01 课程但存在 02 课程的学生 id

   ```sql
   SELECT t2.s_id id
   FROM (SELECT * FROM Score WHERE c_id = '01') t1
            RIGHT JOIN (SELECT * FROM Score WHERE c_id = '02') t2
                       ON t1.s_id = t2.s_id
   WHERE t1.s_id IS NULL;
   ```



5. 查询平均成绩大于等于 60 分的同学的学生编号、学生姓名和平均成绩

   ```sql
   # 最后再用WHERE过滤
   SELECT t2.name, t2.id, t1.avg avg_score
   FROM (SELECT s_id, AVG(score) avg FROM Score GROUP BY s_id) t1
            INNER JOIN Student t2
                       ON t1.s_id = t2.id
   WHERE t1.avg > 60;
   ```

   ```sql
   # 提前用HAVING过滤
   SELECT t2.id, t2.name, t1.avg_score
   FROM (SELECT s_id, AVG(score) avg_score FROM Score GROUP BY s_id HAVING avg_score > 60) t1
            INNER JOIN Student t2 ON t1.s_id = t2.id;
   ```

   

6. 查询在 SC 表存在成绩的学生信息

   ```sql
   SELECT * FROM Student t1 INNER JOIN (SELECT DISTINCT s_id FROM Score) t2 ON t1.id = t2.s_id;
   ```



7. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩（没成绩的显示为 null）

   ```sql
   SELECT s.id, s.name, sc.count, sc.sum
   FROM student s
            LEFT JOIN
        (SELECT s_id id, COUNT(s_id) count, SUM(score) sum FROM score GROUP BY s_id) sc
        ON s.id = sc.id;
   ```



8. 查有成绩的学生信息

   ```sql
   SELECT * FROM Student t1 RIGHT JOIN (SELECT DISTINCT s_id FROM Score) t2 ON t1.id = t2.s_id;
   
   SELECT * FROM Student WHERE id in (SELECT DISTINCT s_id FROM Score);
   
   SELECT * FROM Student t1 WHERE EXISTS(SELECT 1 FROM score t2 WHERE t1.id = t2.s_id);
   ```



9. 

```sql

```





