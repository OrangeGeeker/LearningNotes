# 集合运算

## 表的加减法

- 集合运算回除去重复的记录（有特例）
- 作为运算对象的记录的列数必须相同
- 作为运算对象的记录中列的类型必须一致
- 可以使用任何 `SELECT` 语句，但是 `ORDER BY` 子句只能在最后使用一次
- 集合运算符中使用 `ALL`，可以保留重复行

### UNION 并集

```sql
SELECT product_id, product_name
    FROM Product
UNION
SELECT product_id, product_name
    FROM Product2;
```

```sql
SELECT product_id, product_name
    FROM Product
UNION ALL
SELECT product_id, product_name
    FROM Product2;
```

### INTERSECT （MySQL无法使用）

应用于两张表，选取出他们当中的公共记录

```sql
SELECT product_id, product_name
    FROM Product
INTERSECT
SELECT product_id, product_name
    FROM Product2
ORDER BY product_id;
```

### EXCEPT （MySQL无法使用）

```sql
SELECT product_id, product_name
    FROM Product
EXCEPT
SELECT product_id, product_name
    FROM Product2
ORDER BY product_id;
```

## 联结 JOIN

`UNION` 是以行为单位进行操作，`JOIN` 是以列为单位进行操作

### 内联结 INNER JOIN

- 进行联结时 `FROM` 子句使用多张表
- 进行内联结时必须使用 `ON` 子句，书写在 `FROM` 和 `WHERE` 之间
- 使用联结时 `SELECT` 子句中的列需要按照 别名.列名 的格式进行书写
- 会选取出同时存在于两张表中的数据

将联结之后的结果当作新创建出来的一张表，对这张表使用 `WHERE` 子句等工具

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price
    FROM ShopProduct AS SP
    INNER JOIN Product AS P
        ON SP.product_id = P.product_id
    WHERE SP.shop_id = '000A';
```

### 外联结 OUTER JOIN

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price
    FROM ShopProduct AS SP
    RIGHT OUTER JOIN Product AS P
        ON SP.product_id = P.product_id
    WHERE SP.shop_id = '000A';
```

- 只要数据存在于某一张表中，就能够读取出来
- 指定主表的关键字是 `LEFT` `RIGHT`，最终结果会包含主表内所有的数据

三张表以上

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name, P.sale_price, IP.inventory_quantity
    FROM ShopProduct AS SP INNER JOIN Product AS P
        ON SP.product_id = P.product_id
        INNER JOIN InventoryuProduct AS IP
            ON SP.product_id = IP.product_id
    WHERE SP.shop_id = '000A';
```

### 交叉联结 CROSS JOIN

笛卡尔积

```sql
SELECT SP.shop_id, SP.shop_name, SP.product_id, P.product_name
    FROM ShopProduct AS SP
    CROSS JOIN Product AS P;
```

集合运算中的乘法就是交叉联结，将 A 表中的记录与 B 表中的记录进行交叉组合，结果记录数时两张表中记录数的乘积。

### 表的除法
