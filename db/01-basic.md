# 基础

## 概念

- DB：数据库，将大量数据保存起来，通过计算机加工而成的，可以进行高校访问的数据集合
- DBMS：数据库管理系统，用来管理数据库的计算机系统
- HDB：层次数据库
- RDB：关系数据库，由行和列组成二维表来管理，使用 SQL 对数据进行操作
- RDBMS：关系数据库管理系统，Oracle、SQL Server、DB2、PostgreSQL、MySQL
- OODB：面向对象数据库
- XMLDB：XML 数据库
- KVS：键值存储系统
- 列：字段
- 行：记录
- 关系数据库以行为单位进行读写数据
- 一个单元格只能输入一个数据
- 学会标准 SQL 就能在各种 RDBMS 中书写 SQL 语句了
- DDL（数据定义语言）：CREATE、DROP、ALTER
- DML（数据操作语言）：SELECT、INSERT、UPDATE、DELETE
- DCL（数据控制语言）：COMMIT、ROLLBACK、GRANT、REVOKE
- 使用最多的是 DML
- SQL 语句以分号`;`结尾
- 关键字不区分大小写
- 字符串、日期常数，需要使用单引号 `'` 括起来，数字常数无需单引号
  - 标准 SQL 不存在双引号
- 单词之间使用空格或者换行符进行分隔

## 创建表

- 约束可以在定义列的时候进行，也可以在语句末尾进行设置
- NOT NULL 约束只能以列为单位进行设置
- 字母、数字、下划线作为数据库名、表名
- 必须以字母开头
- 名称不能重复

```sql
CREATE DATABASE <数据库名称>;

CREATE TABLE <表名> (
    <列名> <数据类型> <该列约束>,
    <列名> <数据类型> <该列约束>,
    <列名> <数据类型> <该列约束>,
    ...
    <表约束1>,<表约束2>,...
    );

```

## 数据类型

- 所有的列都必须指定数据类型
- INTEGER：数字整数
- CHAR(x)：字符，定长。不足长度以空格补全
- VARCHAR(x)：字符，可变长
- DATE：日期类型

## 约束

- NOT NULL
- PRIMARY KEY：可以在列名后定义，也可以在 CREATE TABLE 后面 PRIMARY KEY(列名)

## 表修改

- 删除的表无法恢复
- 表定义更改后无法恢复

```sql
删除表
DROP TABLE <表名>;

改名
RENAME TABLE <A> TO <B>; //MySQL
ALTER TABLE xxx RENAME TO xxx; //PostgreSQL

添加列
ALTER TABLE <表名> ADD COLUMN <列的定义>;

删除列
ALTER TABLE <表名> DROP COLUMN <列名>;

插入
INSERT INTO <表名>(列1,列2,...) VALUES(值1,值2,...);

INSERT INTO <表名> VALUES (xx,xx,xx,xx),(xx,xx,xx,xx),...;

 //列清单可以省略，但是需要设置全部的列的 VALUES
 //可以指定 NULL DEFAULT


事务
START TRANSACTION; //MySQL
BEGIN TRANSACTION; //PostgreSQL
COMMIT;
```