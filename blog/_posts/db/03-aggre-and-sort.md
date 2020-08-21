# 聚合和排序

## 聚合函数

用于汇总的函数称为聚合函数，将多行汇总为一行数据。

- `COUNT` 计算表中行数
- `SUM` 计算列中数据的和
- `AVG` 计算列中数据的平均值
- `MAX` 计算列中数据的最大值
- `MIN` 计算列中数据的最小值

- `COUNT` 函数根据参数不同结果不同，`COUNT(*)` 包含 NULL 的行，`COUNT(<列名>)` 不包含 NULL 的行
- 聚合函数会将 NULL 排除在外，`COUNT(*)` 不会排除 NULL
- `MAX/MIN` 几乎适用于所有数据类型的列，`SUM/AVG`只适用于数值类型的列

`SELECT COUNT(DISTINCT product_type) FROM Product;`

- 想要计算值的种类时，可以在 `COUNT` 函数的 **参数** 中使用 `DISTINCT`。
- 所有函数都可以使用 `DISTINCT`，聚合函数中使用 `DISTINCT` 可以删除重复数据

## GROUP BY 分组

```sql
SELECT <列名>,...
FROM <表名>
GROUP BY <列名>,...
```

- `GROUP BY` 子句：不使用时，是将表中所有的数据作为一组来对待的；使用时，将表中数据分为多个组进行处理
- `GROUP BY` 中指定的列称为 **聚合键** 或 **分组列**
- 写在 `FROM / WHERE` 之后，SQL 子句顺序不能改变，也不能替换
- 聚合键包含 NULL 时，结果中会以空行的形式表现出来

```sql
SELECT <列名>,...
FROM <表名>
WHERE
GROUP BY <列名>,...
```

- 先根据 `WHERE` 子句进行过滤，再进行汇总。`FROM -> WHERE -> GROUP BY -> SELECT`
- 使用 `GROUP BY` 子句时，`SELECT` 子句中不能出现聚合键之外的列名
- 使用 `COUNT` 这样的聚合函数时，`SELECT` 子句只能存在 **常数**，**聚合函数**，**GROUP BY 子句中指定的列名**
- `GROUP BY` 子句中，不能使用 `SELECT` 中定义的别名
- `GOURP BY` 结果是无序的
- 只有 `SELECT` 子句和 `HAVING` 子句中能够使用聚合函数，`WHERE` 子句中不可以

## HAVING 指定条件

```sql
SELECT <列名>,...
FROM <表名>
GROUP BY <列名>,...
HAVING <分组结果对应条件>
```

- `HAVING` 写在 `GROUP BY` 之后
- `HAVING` 子句中能够使用常数、聚合函数、`GROUP BY` 子句指定的列名（聚合键）
- 聚合键所对应的条件不应该写在 `HAVING` 子句中，应该写在 `WHERE` 子句当中（组合排序前对数据进行过滤，并且可以添加索引）

`WHERE` = 指定行所对应的条件
`HAVING` = 指定组所对应的条件

```sql
SELECT product_type, COUNT(*)
FROM product
GROUP BY product_type
HAVING COUNT(*)=2;
```

## ORDER BY 排序

```sql
SELECT <列名>,...
FROM <表名>
ORDER BY <排序基准列>,...
```

- `ORDER BY` 子句通常写在 `SELECT` 语句的末尾
- `ORDER BY` 子句中书写的列名成为排序键
- 列名后跟 `ASC DESC` 指定排序方式，默认为 `ASC`
- 可以同时指定多个列的排序方式
- 排序中包含 NULL 时，会在开头或者结尾显示
- 排序键可以使用 `SELECT` 子句中定义的别名
- 可以使用 `SELECT` 子句中未使用的列和聚合函数
- 不要使用列编号
