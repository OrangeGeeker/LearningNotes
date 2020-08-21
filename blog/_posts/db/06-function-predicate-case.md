# 函数、谓词、CASE表达式

## 函数

- 算术函数
- 字符串函数
- 日期函数
- 转换函数
- 聚合函数

### 算术函数

- `NUMERIC(全体位数, 小数位数) 数据类型`

- `ABS(数值)` 参数为 NULL 时，结果也是 NULL

```sql
SELECT m, ABS(m) AS abs_col
FROM SampleMath;
```

- 大多数函数对于 NULL 的返回值都是 NULL
- `MOD(被除数，除数)` 余数。整数类型的的列使用

```sql
SELECT n, p, MOD(n, P) AS mod_col
FROM SampleMath;
```

- `ROUND(对象数值，保留小数位数)`

```sql
SELECT m, n, ROUND(m, n) AS round_col
FROM SampleMath;
```

### 字符串函数

- `字符串1||字符串2` 拼接

SQL Server 和 MySQL 无使用，MySQL 使用 `CONCAT()` 函数。

```sql
SELECT str1, str2, str1 || str2 AS str_concat
FROM SmapleStr;

SELECT str1, str2, CONCAT(str1, str2) AS str_concat
FROM SmapleStr;
```

如果包含 NULL，结果也是 NULL。三个以上的字符串也可以拼接。

- `LENGTH(字符串)` MySQL 中返回字节数
- `LOWER(字符串)` 只能针对英文字母使用
- `UPPER(字符串)`
- `REPLACE(对象字符串，替换前字符串，替换后字符串)`
- `SUBSTRING(对象字符串 FROM 起始位置 FOR 字符数)` 起始位置从字符串的最左侧开始计算

### 日期函数

- `CURRENT_DATE` 当前日期 `SELECT CURRENT_DATE;`
- `CURRENT_TIME` 当前时间 `SELECT CURRENT_TIME;`
- `CURRENT_TIMESTAMP` 当前日期和时间 `SELECT CURRENT_TIMESTAMP;`
- `EXTRACT(日期元素 FROM 日期)` 截取日期和时间 `SELECT CURRENT_TIMESTAMP, EXTRACT(YEAR FROM CURRENT_TIMESTAMP) AS YEAR;`
  - MONTH DAY HOUR MINUTE SECOND

### 转换函数

- `CAST` 类型转换

```sql
CAST(转换前的值 AS 转换的数据类型)

SELECT CAST('0001' AS SIGNED INTEGER) AS int_col;
```

- `COALESCE` 将 NULL 转换为其他值

返回参数中第一个不是 NULL 的值

```sql
COALESCE(数据1, 数据2, 数据3....)

SELECT COALESCE(str2, 'NULL') FROM SampleStr;
```

## 谓词 predicate

通俗来讲，谓词是函数的一种，返回值是真值(TRUE/FALSE/UNKNOW)的函数。

### LIKE

字符串部分一致查询时使用

- `%`代表0字符以上的任意个字符
- `_`代表任意1个字符

```sql
SELECT * FROM SampleLike WHERE strcol LIKE '%ddd%';
```

### BETWEEN

会包含临界值

```sql
SELECT product_name, sale_price
FROM Product
WHERE sale_price BETWEEN 100 AND 1000;
```

### IS NULL、IS NOT NULL

选取 NULL 列或者不是 NULL 列的数据，不能使用 =

```sql
SELECT product_name, purchase_price
FROM Product
WHERE purchase_price IS NOT NULL;
```

### IN、NOT IN

- 无法选取出 NULL 的数据
- 可以使用子查询作为其参数（表、视图）

```sql
SELECT product_name, purchase_price
FROM Product
WHERE purchase_price IN(320, 5000, 10000);

SELECT product_name, sale_price
FROM Product
WHERE product_id IN(SELECT product_id
                    FROM ShopProduct
                    WHERE shop_id='000C');
```

### EXIST

- `EXIST` 的参数通常是一个关联子查询
- `EXIST` 的参数子查询中，通常使用 `SELECT *`

判断是否存在满足某种条件的记录，如果存在返回 TRUE，不存在返回 FALSE

```sql
SELECT product_name, sale_price
FROM Product AS P
WHERE EXIST (SELECT * FROM ShopProduct AS SP
            WHERE SP.shop_id='000C'
            AND SP.product.id=P.product_id);
```

## CASE 表达式

- 求值表达式是返回值为真值的表达式
- `ELSE` 最好不要省略，`END` 不能省略
- 对 SELECT 语句的结果进行编辑时，CASE 表达式能够发挥较大作用
- 各项分支返回的数据类型必须相同

### 搜索 `CASE` 表达式

包含了简单 `CASE` 表达式的全部功能

```sql
CASE WHEN <求值表达式> THEN <表达式>
     WHEN <求值表达式> THEN <表达式>
     ...
     ELSE <表达式>
END
```

例子

```sql
SELECT product_name,
    CASE WHEN product_type = '衣服'
        THEN 'A:' || product_type
        WHEN product_type = '办公'
        THEN 'B:' || product_type
    ELSE NULL
    END AS abc_product_type
```

### 简单 CASE 表达式

```sql
CASE <表达式>
    WHEN <表达式> THEN <表达式>
    WHEN <表达式> THEN <表达式>
    ...
    ELSE <表达式>
END
```

例子

```sql
SELECT product_name,
    CASE product_name
        WHEN '衣服'
        THEN 'A:' || product_type
        WHEN '办公'
        THEN 'B:' || product_type
        ELSE NULL
    END AS abc_product_type
```
