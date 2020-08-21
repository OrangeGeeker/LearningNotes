# CASE 表达式

```sql
CASE WHEN <求值表达式> THEN <表达式>
     WHEN <求值表达式> THEN <表达式>
     ...
     ELSE <表达式>
END
```

```sql
CASE <表达式>
    WHEN <表达式> THEN <表达式>
    WHEN <表达式> THEN <表达式>
    ...
    ELSE <表达式>
END
```

## 注意事项

- 统一各分支返回的数据类型
- 必须写 `END`
- 养成写 `ELSE` 的习惯

## 将已有编号方式转换为新的方式并统计

```sql
SELECT  CASE pref_name
            WHEN '刀' THEN '1',
            WHEN '刀' THEN '1',
            WHEN '刀' THEN '2',
            WHEN '刀' THEN '2',
            WHEN '刀' THEN '2',
        ELSE '其他' END AS district,
        SUM(populations)
    FROM PopTbl
    GROUP BY CASE pref_name
            WHEN '刀' THEN '1',
            WHEN '刀' THEN '1',
            WHEN '刀' THEN '2',
            WHEN '刀' THEN '2',
            WHEN '刀' THEN '2',
        ELSE '其他' END；
```

关键点是将 SELECT 子句中的 CASE 表达式复制到 GROUP BY 子句里。

同样也可以将数值按照适当的级别进行分类统计，例如按照人口级别进行统计等。

可以在 GROUP BY 中使用别名，部分数据库支持 PostgreSQL 和 MySQL 支持。这些数据库在执行查询语句时会先对 SELECT 子句中的列表进行扫描计算等。但这是违反标准的写法，虽然可读性和维护性好。

## 用一条 SQL 语句进行不同条件的统计

新手用 WHERE 子句进行条件分支，高手用 SELECT 子句进行条件分支。

```sql
SELECT pref_name,
    -- 男性人口
    SUM(CASE WHEN sex='1' THEN populations ELSE 0 END) AS cnt_m,
    -- 女性人口
    SUM(CASE WHEN sex='2' THEN populations ELSE 0 END) AS cnt_f,
    FROM PopTbl2
    GROUP BY pref_name;
```

## 用 CHECK 约束定义多个列的条件关系

假设 女性员工工资必须在20万以下

```sql
CONSTRAINT check_salary CHECK
    (CASE WHEN sec='2'
        THEN CASE WHEN salary <= 200000
                THEN 1 ELSE 0 END
            ELSE 1 END = 1)
```

CASE 表达式嵌入到 CHECK 约束里，这种称为蕴含式逻辑表达式，与逻辑表达式的区别是

```sql
CONSTRAINT check_salary CHECK
    (sex='2' AND salary <= 200000)
```

蕴含式相比逻辑与约束更加宽松

## 在 UPDATE 语句里进行条件分支

```sql
UPDATE Salaries
    SET salary = CASE WHEN salary >= 300000
        THEN salary * 0.9
        WHEN salary >= 250000 AND salary < 280000
        THEN salary * 1.2
        ELSE salary END;
```

注意 ELSE 一定要写

这个技巧可以完成主键值调换这种繁重的工作。唯一键也可以使用。

CASE 表达式的条件分支进行更新操作是一气呵成的，可以避免主键重复导致的错误

```sql
UPDATE SomeTable
    SET p_key = CASE WHEN p_key = 'a'
        THEN 'b'
        WHEN p_key = 'b'
        THEN 'a'
        ELSE p_key END
    WHERE p_key IN('a', 'b');
```

## 表之间的数据匹配

CASE 表达式的一大优势是能够判断表达式，在 CASE 表达式里可以使用 BETWEEN LIKE < > 等谓词，以及能嵌套子查询的 IN EXIST 谓词。

```sql
SELECT CM.course_name,
    CASE WHEN EXISTS
        (SELECT course_id FROM OpenCourses OC WHERE month =  200806
            AND OC.course_id = CM.course_id)
            THEN 'O'
            ELSE 'X'
            END
        AS "6月"
        ..
FROM CourseMaster CM;
```

## CASE 表达式中使用聚合函数

```sql
SELECT std_id,
    CASE WHEN COUNT(*) =1
        THEN MAX(club_id)
        ELSE MAX(CASE WHEN main_club_flg = 'Y'
                    THEN club_id
                    ELSE NULL END)
        END AS main_club
    FROM StudentClub
    GROUP BY std_id;
```