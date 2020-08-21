# 自连接的用法

## 可重排列、排列、组合

生成有序对：下面通过交叉连接生成笛卡尔积，得到有序对

```sql
SELECT P1.name AS name_1, P2.name AS name_2
FROM Products P1, Products P2;
```

加上条件：

```sql
SELECT P1.name AS name_1, P2.name AS name_2
FROM Products P1, Products P2
WHERE P1.name <> P2.name;
```

这次处理结果依然是有序对

再更改条件：

```sql
SELECT P1.name AS name_1, P2.name AS name_2
FROM Products P1, Products P2
WHERE P1.name > P2.name;
```

这样就得到了 无序对，对于多个元素的组合：

```sql
SELECT P1.name AS name_1, P2.name AS name_2
FROM Products P1, Products P2，Products P3
WHERE P1.name > P2.name AND P2.name > P3.name;
```

这种方式称为“非等值自连接”，可以获取列的组合。

## 删除重复行

```sql
DELETE FROM Products P1
WHERE rowid <( SELECT MAX(P2.rowid) FROM Products P2
                WHERE P1.name = P2.name
                AND P1.price = P2.price);
```

```sql
DELETE FROM Products P1
  WHERE EXISTS(SELECT * FROM Products P2
                WHERE P1.name = P2.name
                AND P1.price = P2.price
                AND P1.rowid < P2.rowid);
```

## 查找局部不一致列
