# 更新数据

## 插入

```sql
INSERT INTO <表名>(列名,....) VALUES(值,...);
```

列清单、值清单

```sql
//多行插入
INSERT INTO 表名 VALUES (),(),...;
```

- 原则上，执行一次 `INSERT` 会插入一行数据
- 对表进行全列 `INSERT` 时，列清单可以省略
- 需要插入 NULL 直接写 NULL，并且列没有 NOT NULL 约束
- 默认值可以通过写 `DEFAULT` 插入。也可以不使用 `DEFAULT` 关键字，此时列清单不能省略，列清单列出需要插入的列。
- 如果省略了没有默认值的列，那么会被插入 NULL，如果该列为 NOT NULL 约束，那么会出错。

## 复制

```sql
INSERT INTO 表名(列名) SELECT 列名 FROM 表名;
```

该语句中的 `SELECT` 子句也可以使用  `WHERE` `GROUP BY` 等任何 SQL 语法。

## 删除

```sql
DROP TABLE <表名>;
DELETE FROM <表名>;
```

- `DELETE` 语句删除的对象不是表或者列，而是记录（行）。
- `DELETE` 可以加上 `WHERE` 子句来指定条件，删除部分数据。

`TRUNCATE <表名>` 删除表中的全部数据，不能通过 `WHERE` 子句指定条件来删除部分数据。其处理速度比 `DELETE` 快。

## 更新

```sql
UPDATE <表名>
SET <列名> = <表达式>, ...;

WHERE <条件>;
```

表达式可以是值，也可以是包含列的表达式。也可以将列更新为 NULL。

## 事务

事务时需要在同一个处理单元中执行一系列更新处理的集合。

```sql
START TRANSACTION;

COMMIT;
ROLLBACK;
```

DBMS 事务都遵循 ACID 特性：

原子性
一致性
隔离性
持久性
