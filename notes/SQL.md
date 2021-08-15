

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



## NOT





## LIKE





# 聚合与排序🐋

## 聚合函数

聚合函数：将一列数据作为一个整体，进行纵向的计算。

- count：计算个数
- max：计算最大值
- min：计算最小值
- sum：计算和
- avg：计算平均值

> 聚合函数的计算，排除null值







## 分组 GROUP BY

```sql
group by 分组字段;
```



## 排序 ORDER BY

- ASC：升序
- DESC：降序

```sql
order by 排序字段1 排序方式1, 排序字段2 排序方式2...
```







# 用户管理🐋

## 添加用户 CREATE

host：指定该用户在哪个主机上可以登陆，如果是本地用户可用 `localhost`，如果想让该用户可以从任意远程主机登陆，可以使用通配符 `%`

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

## 子查询

## 关联子查询



# 多表查询🐋

## 集合运算

### UNION

### INTERSECT

### EXCEPT

## 左联结 LEFT JOIN



## 右联结 RIGHT JOIN



## 内联结 INNER JOIN



## 外联结 OUTER JOIN

## 交叉联结 CROSS JOIN



# 索引🐋

## 命名规范

主键索引名为 pk_字段名

唯一索引名为 uk_字段名

普通索引名则为a idx_字段名



## 创建索引

```sql
CREATE INDEX userindex ON user (uid)
```



## 查看索引

```sql
SHOW INDEX FROM `table`;
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









