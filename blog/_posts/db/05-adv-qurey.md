# 复杂查询

## 视图

- 从 SQL 的角度来看视图就是一张表
- 视图保存的是 `SELECT` 语句，当从视图中读取数据时，视图内部会执行该 `SELECT` 语句并创建出一张临时表
- 视图不会将数据保存到其他地方

- 表中存储的是实际数据，视图中保存的是从表中取出数据所使用的 `SELECT` 语句
- 节省存储设备容量
- 可以将频繁使用的 `SELECT` 语句保存成视图
- 避免在视图的基础上创建视图

```sql
CREATE VIEW <视图名>(<视图列名>,...)
AS
<SELECT 语句>
```

- `SELECT` 写在 `AS` 之后
- `SELECT` 语句中列的排列顺序和视图中列的排列顺序相同
- 避免视图嵌套，多重视图会降低性能
- 定义视图不能使用 `ORDER BY` 子句
- 视图和表需要同时进行更新，因此对于通过汇总聚合得到的视图，无法对该试图进行更新

```sql
DROP VIEW <视图名>(<视图列名>,...)
```

## 子查询

子查询就是一次性试图，子查询在 `SELECT` 语句执行完之后就会消失。

- 子查询作为内层查询会首先执行
- 为子查询设定名称时，使用 `AS` 关键字

### 标量子查询

- 标量子查询是返回单一值的子查询，可以用在 `= <>` 等比较运算符之中
- 能够使用常数或列名的地方，都可以使用标量查询
  - 不仅仅是 `WHERE` 子句，`SELECT` `GROUP BY` `HAVING` `ORDER BY`
- 标量查询一定不能返回多行结果

`SELECT x FROM (SELECT x FROM x) AS xx;`

```sql
SELECT product_id, product_name, sale_price,
    (SELECT AVG(sale_price) FROM Product) AS avg_price
FROM Product;
```

```sql
SELECT product_id, product_name, sale_price
  FROM Product
  WHERE sale_price > (SELECT AVG(sale_price) FROM Product);
```

## 关联子查询

- 在细分的组内进行比较时，需要使用关联子查询
- 在子查询中添加 `WHERE` 子句
- 结合条件一定要写在子查询中

```sql
SELECT product_type, product_name, sale_price
    FROM Product AS P1
    WHERE sale_price > (SELECT AVG(sale_price)
                            FROM Product AS P2
                            WHERE P1.product_type == P2.product_type
                            GROUP BY product_type);
//即使在子查询中不使用 GROUP BY 子句，也能得到正确结果
```
