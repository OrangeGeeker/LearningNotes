# SQL 高级处理

## 窗口函数

窗口函数也称为 **OLAP** 函数。（OnLine Analytical Processing 数据实时分析处理）

目前 MySQL 不支持窗口函数

`<窗口函数> OVER([PARTITION BY <列清单>] ORDER BY<排序用列清单>)`

- 能够作为窗口函数的聚合函数：`SUM、AVG、COUNT、MAX、MIN`
- 专用窗口函数：`RANK、DENSE_RANK、ROW_NUMBER` 等

```sql
SELECT product_name, product_type, sale_price,
    RANK() OVER(PARTITION BY product_type ORDER BY sale_price) AS ranking
    FROM Product;
```

- `PARTITION BY` 能够设定排序对象的范围。
- `ORDER BY` 能够指定按照那一列、何种顺序进行排序。

- 窗口函数间距分组和排序两种功能
- 通过 `PARTITION BY` 分组后的记录集合称为窗口（代表范围）
- `GROUP BY` 会减少记录的行数，`PARTITION BY` 不会
- `PARTITION BY` 不是必须的，将整个表作为一个大窗口使用

- `RANK` 函数，计算排序时，如果存在相同位次的记录，则会跳过之后的位次
- `DENSE_RANK` 函数，即使存在相同位次的记录，也不会跳过之后的位次
- `ROW_NUMBER` 函数，赋予唯一的连续位次
- 由于专用窗口函数无需参数，因此括号中都是空的
- 窗口函数只能在 `SELECT` 子句中使用（`ORDER BY` 和 `UPDATE` 的 `SET` 也可以，但是没有实际使用需要）

窗口函数是对 `WHERE` 子句 或者 `GROUP BY` 子句处理后的结果进行操作。正由于这个原因，在 `SELECT` 子句之外使用窗口函数没有意义。

### 作为窗口函数使用的聚合函数

- 将聚合函数作为窗口函数使用时，会以当前记录为基准来决定汇总对象的记录

所有的聚合函数都能用作窗口函数，语法相同。

```sql
SELECT product_name, product_type, sale_price,
    SUM(sale_price) OVER(ORDER BY product_id) AS current_sum
    FROM Product;
```

这样的结果结果称为 **累计统计**

#### 框架

像这样以当前记录作为基准的统计，是将聚合函数作为窗口函数使用的最大特征。

在窗口中指定更加详细的汇总返回的备选功能，称为 **框架**。需要在 `ORDER BY` 之后只用指定范围的关键字。

```sql
SELECT product_id, product_name, sale_price,
    AVG(sale_price) OVER(ORDER BY product_id ROWS 2 PRECEDING) AS moving_avg
    FROM Product;
```

这里使用了 `ROWS` 和 `PRECEDING` 两个关键字，将框架指定为“截止到之前的x行”，结果就是将作为汇总对象的记录限定为“最靠近的3行”。

框架是根据当前记录来确定的，因此会随着记录的变化而变化。

这样的统计方法称为 **移动平均**。

- 使用 `FOLLOWING` 指定“之后的x行”
- 同时使用 `PREDCEDING` 和 `FOLLOWING` 指定当前行的前后行作为汇总对象

```sql
SELECT product_id, product_name, sale_price,
    AVG(sale_price) OVER(ORDER BY product_id
        ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING) AS moving_avg
    FROM Product;
```

#### 两个 GROUP BY

`OVER` 子句中的 `ORDER BY` 只是用来决定窗口函数按照什么样的顺序进行计算，对结果的排列顺序没有影响。通常在 `SELECT` 语句最后，使用 `ORDER BY` 进行指定排序。

## GROUPING 运算符

目前 PostgreSQL 和 MySQL 不支持 GROUPING 云端夫（MySQL 仅支持 ROLLUP）。

### ROLLUP

```sql
SELECT product_type, SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY ROLLUP(product_type);

//MySQL
SELECT product_type, SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY product_type WITH ROLLUP;
```

语法上讲，将 GROUP BY 的聚合键清单写在 ROLLUP 参数中。

该运算符的作用，就是一次计算出不同聚合键组合的结果，上例中是 GROUP BY() 和 GROUP BY(product_type)。

没有聚合键相当于没有 GROUP BY 子句，该合计行记录称为超级分组记录，默认使用 NULL 作为聚合键。

- ROLLUP 可以同时得出合计和小计

#### 多个聚合键

```sql
SELECT product_type, regist_date, SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY ROLLUP(product_type, regist_date);
```

### GROUPING 函数

- 使用 GROUPING 函数能够简单的分辨出原始数据中的 NULL 和超级分组记录中的 NULL

该函数在其参数列的值为超级分组记录所产生的 NULL 时返回1，其他情况返回0。

```sql
SELECT GROUPING(product_type) AS product_type,
    GROUPING(regist_date) AS regist_date,
    SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY ROLLUP(product_type, regist_date);
```

根据这种特性，当 GROUPING 函数返回 1，指定合计或者小计等字符串，其他情况返回通常的列

```sql
SELECT CASE WHEN GROUPING(product_type) = 1
    THEN '商品种类合计'
    ELSE product_type
    END AS product_type,
    CASE WHEN GROUPING(regist_date) = 1
    THEN '登记日期合计'
    ELSE CAST(regist_date AS VARCHAR(16))
    END AS regist_date,
    SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY ROLLUP(product_type, regist_date);
```

### CUBE 函数

```sql
SELECT CASE WHEN GROUPING(product_type) = 1
    THEN '商品种类合计'
    ELSE product_type
    END AS product_type,
    CASE WHEN GROUPING(regist_date) = 1
    THEN '登记日期合计'
    ELSE CAST(regist_date AS VARCHAR(16))
    END AS regist_date,
    SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY CUBE(product_type, regist_date);
```

与 ROLLUP 相比，CUBE 多了几行记录，多出来的记录就是只把 regist_date 作为聚合键所得到的结果。

CUBE 就是将 GROUP BY 子句中聚合键的所有可能组合的汇总结果集中到一个结果中，因此组合的个数是 2的n次方（n是聚合键个数）。

使用 ROLLUP 时时 n+1，ROLLUP 的结果一定包含在 CUBE 中。

### GROUPING SETS

该运算符可以用于从 ROLLUP 或 CUBE 的结果中取出部分记录。

```sql
SELECT CASE WHEN GROUPING(product_type) = 1
    THEN '商品种类合计'
    ELSE product_type
    END AS product_type,
    CASE WHEN GROUPING(regist_date) = 1
    THEN '登记日期合计'
    ELSE CAST(regist_date AS VARCHAR(16))
    END AS regist_date,
    SUM(sale_price) AS sum_price
    FROM Product
    GROUP BY GROUPING SETS(product_type, regist_date);
```