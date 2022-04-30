## 20220411：大的国家

**题目描述**

World 表：

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| name        | varchar |
| continent   | varchar |
| area        | int     |
| population  | int     |
| gdp         | int     |
+-------------+---------+
name 是这张表的主键。
这张表的每一行提供：国家名称、所属大陆、面积、人口和 GDP 值。
```

如果一个国家满足下述两个条件之一，则认为该国是 大国 ：

    面积至少为 300 平方公里（即，3000000 km2），或者
    人口至少为 2500 万（即 25000000）

编写一个 SQL 查询以报告 大国 的国家名称、人口和面积。

**示例**

输入：

```
World 表：
+-------------+-----------+---------+------------+--------------+
| name        | continent | area    | population | gdp          |
+-------------+-----------+---------+------------+--------------+
| Afghanistan | Asia      | 652230  | 25500100   | 20343000000  |
| Albania     | Europe    | 28748   | 2831741    | 12960000000  |
| Algeria     | Africa    | 2381741 | 37100000   | 188681000000 |
| Andorra     | Europe    | 468     | 78115      | 3712000000   |
| Angola      | Africa    | 1246700 | 20609294   | 100990000000 |
+-------------+-----------+---------+------------+--------------+
```

输出：

```
+-------------+------------+---------+
| name        | population | area    |
+-------------+------------+---------+
| Afghanistan | 25500100   | 652230  |
| Algeria     | 37100000   | 2381741 |
+-------------+------------+---------+
```

**思路分析**

无

**代码**

```sql
SELECT World.name, World.population, World.area
FROM World
WHERE World.area >= 3000000 OR World.population >= 25000000;
```

## 20220411：可回收且低脂的产品

**题目描述**

```
表：Products

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| low_fats    | enum    |
| recyclable  | enum    |
+-------------+---------+
product_id 是这个表的主键。
low_fats 是枚举类型，取值为以下两种 ('Y', 'N')，其中 'Y' 表示该产品是低脂产品，'N' 表示不是低脂产品。
recyclable 是枚举类型，取值为以下两种 ('Y', 'N')，其中 'Y' 表示该产品可回收，而 'N' 表示不可回收。
```

写出 SQL 语句，查找既是低脂又是可回收的产品编号。

返回结果 无顺序要求 。

查询结果格式如下例所示：

```
Products 表：
+-------------+----------+------------+
| product_id  | low_fats | recyclable |
+-------------+----------+------------+
| 0           | Y        | N          |
| 1           | Y        | Y          |
| 2           | N        | Y          |
| 3           | Y        | Y          |
| 4           | N        | N          |
+-------------+----------+------------+
Result 表：
+-------------+
| product_id  |
+-------------+
| 1           |
| 3           |
+-------------+
只有产品 id 为 1 和 3 的产品，既是低脂又是可回收的产品。
```

**思路分析**

无

**代码**

```sql
SELECT Products.product_id
FROM Products
WHERE Products.low_fats = 'Y' AND Products.recyclable = 'Y';
```

## 20220411：寻找用户推荐人

**题目描述**

给定表 customer ，里面保存了所有客户信息和他们的推荐人。

```
+------+------+-----------+
| id   | name | referee_id|
+------+------+-----------+
|    1 | Will |      NULL |
|    2 | Jane |      NULL |
|    3 | Alex |         2 |
|    4 | Bill |      NULL |
|    5 | Zack |         1 |
|    6 | Mark |         2 |
+------+------+-----------+
```

写一个查询语句，返回一个客户列表，列表中客户的推荐人的编号都 不是 2。

对于上面的示例数据，结果为：

```
+------+
| name |
+------+
| Will |
| Jane |
| Bill |
| Zack |
+------+
```

**思路分析**

**MySQL 使用三值逻辑 —— TRUE, FALSE 和 UNKNOWN。任何与 NULL 值进行的比较都会与第三种值 UNKNOWN 做比较。这个“任何值”包括 NULL 本身！这就是为什么 MySQL 提供 IS NULL 和 IS NOT NULL 两种操作来对 NULL 特殊判断。**
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

不是2的推荐人编号，除了数字不为2以外，还有NULL

**代码**

```sql
SELECT customer.name 
FROM customer
WHERE customer.referee_id != 2 OR customer.referee_id IS NULL;
```

## 20220411：从不订购的用户

**题目描述**

某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

Customers 表：

```
+----+-------+
| Id | Name  |
+----+-------+
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |
+----+-------+
```

Orders 表：

```
+----+------------+
| Id | CustomerId |
+----+------------+
| 1  | 3          |
| 2  | 1          |
+----+------------+
```

**示例**

输出：

```
+-----------+
| Customers |
+-----------+
| Henry     |
| Max       |
+-----------+
```

**思路分析**

思路一：嵌套查询

思路二：左连接

**代码**

思路一：

```sql
SELECT Customers.Name AS Customers 
FROM Customers
WHERE Customers.Id NOT IN 
(
    SELECT Orders.CustomerId FROM Orders
);
```

思路二：

```sql
SELECT Customers.Name AS Customers 
FROM Customers LEFT JOIN Orders ON Customers.Id = Orders.CustomerId
WHERE Orders.CustomerId IS NULL;
```

## 20220412：计算特殊奖金

**题目描述**

表: Employees

```
+-------------+---------+
| 列名        | 类型     |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
| salary      | int     |
+-------------+---------+
```

employee_id 是这个表的主键。
此表的每一行给出了雇员id ，名字和薪水。

写出一个SQL 查询语句，计算每个雇员的奖金。如果一个雇员的id是奇数并且他的名字不是以'M'开头，那么他的奖金是他工资的100%，否则奖金为0。

Return the result table ordered by employee_id.

返回的结果集请按照employee_id排序。

查询结果格式如下面的例子所示。

**示例**

输入：

```
Employees 表:
+-------------+---------+--------+
| employee_id | name    | salary |
+-------------+---------+--------+
| 2           | Meir    | 3000   |
| 3           | Michael | 3800   |
| 7           | Addilyn | 7400   |
| 8           | Juan    | 6100   |
| 9           | Kannon  | 7700   |
+-------------+---------+--------+
```

输出：

```
+-------------+-------+
| employee_id | bonus |
+-------------+-------+
| 2           | 0     |
| 3           | 0     |
| 7           | 7400  |
| 8           | 0     |
| 9           | 7700  |
+-------------+-------+
```

**思路分析**

注意LIKE的用法

思路一：使用IF()

思路二：使用CASE WHEN THEN ELSE END

**代码**

思路一：

```sql
SELECT Employees.employee_id, IF (Employees.employee_id % 2 = 1 AND Employees.name NOT LIKE 'M%', Employees.salary, 0) as bonus
FROM Employees;
```

思路二：

```sql
SELECT Employees.employee_id, (
    CASE WHEN Employees.employee_id % 2 = 1 AND Employees.name NOT LIKE 'M%' THEN Employees.salary
    ELSE 0 END
) AS bonus
FROM Employees
```

## 20220412：变更性别

**题目描述**

Salary 表：

```
+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| id          | int      |
| name        | varchar  |
| sex         | ENUM     |
| salary      | int      |
+-------------+----------+
```

id 是这个表的主键。
sex 这一列的值是 ENUM 类型，只能从 ('m', 'f') 中取。
本表包含公司雇员的信息。

 

请你编写一个 SQL 查询来交换所有的 'f' 和 'm' （即，将所有 'f' 变为 'm' ，反之亦然），仅使用 单个 update 语句 ，且不产生中间临时表。

注意，你必须仅使用一条 update 语句，且 不能 使用 select 语句。

查询结果如下例所示。

**示例**

输入：

```
Salary 表：
+----+------+-----+--------+
| id | name | sex | salary |
+----+------+-----+--------+
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |
+----+------+-----+--------+
```

输出：

```
+----+------+-----+--------+
| id | name | sex | salary |
+----+------+-----+--------+
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |
+----+------+-----+--------+
```

**思路分析**

思路一：使用IF()

思路二：使用CASE WHEN THEN ELSE END 

**代码**

思路一：

```sql
UPDATE Salary
SET Salary.sex = IF(Salary.sex = 'm', 'f', 'm');
```

思路二：

```sql
UPDATE Salary
SET Salary.sex = (
    CASE Salary.sex WHEN 'f' THEN 'm'
    ELSE 'f' END
);
```

## 20220412：删除重复的电子邮件

**题目描述**

表: Person

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| email       | varchar |
+-------------+---------+
```

id是该表的主键列。
该表的每一行包含一封电子邮件。电子邮件将不包含大写字母。

 

编写一个SQL查询来 删除 所有重复的电子邮件，只保留一个id最小的唯一电子邮件。

以 任意顺序 返回结果表。

查询结果格式如下所示。

**示例**

输入：

```
Person 表:
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
+----+------------------+
```

输出：

```
+----+------------------+
| id | email            |
+----+------------------+
| 1  | john@example.com |
| 2  | bob@example.com  |
+----+------------------+
```

**思路分析**

思路一：将Person自己以name连接起来，删除id较大的

思路二：我们做条件删除，删除的条件是

思路一：

```sql
DELETE p1 
FROM Person p1, Person p2
WHERE p1.email = p2.email AND p1.id > p2.id;
```

思路二：

```sql
DELETE FROM Person
WHERE Person.email IN (
    SELECT * FROM (
        SELECT Person.email
        FROM Person
        GROUP BY Person.email
        HAVING COUNT(Person.email) > 1
    ) a
) AND Person.id NOT IN (
    SELECT * FROM (
        SELECT MIN(Person.id)
        FROM Person
        GROUP BY Person.email
        HAVING COUNT(Person.email) > 1
    ) a
);
```

## 20220413：修改表中的名字

**题目描述**

表： Users

```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| name           | varchar |
+----------------+---------+
```

user_id 是该表的主键。
该表包含用户的 ID 和名字。名字仅由小写和大写字符组成。

编写一个 SQL 查询来修复名字，使得只有第一个字符是大写的，其余都是小写的。

返回按 user_id 排序的结果表。

查询结果格式示例如下。

**示例**

输入：

```
Users table:
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | aLice |
| 2       | bOB   |
+---------+-------+
```

输出：

```
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | Alice |
| 2       | Bob   |
+---------+-------+
```

**思路分析**

使用CONCAT进行字符串拼接，使用LEFT和RIGHT取得字段中字符串的子串

**代码**

```sql
SELECT Users.user_id, CONCAT(
    UPPER(LEFT(Users.name, 1)), LOWER(RIGHT(Users.name, LENGTH(Users.name) - 1))
) AS name
FROM Users
ORDER BY Users.user_id;
```

## 20220413：按日期分组销售商品

**题目描述**

表 Activities：

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| sell_date   | date    |
| product     | varchar |
+-------------+---------+
```

此表没有主键，它可能包含重复项。
此表的每一行都包含产品名称和在市场上销售的日期。



编写一个 SQL 查询来查找每个日期、销售的不同产品的数量及其名称。
每个日期的销售产品名称应按词典序排列。
返回按 sell_date 排序的结果表。
查询结果格式如下例所示。

**示例**

输入：

```
Activities 表：
+------------+-------------+
| sell_date  | product     |
+------------+-------------+
| 2020-05-30 | Headphone   |
| 2020-06-01 | Pencil      |
| 2020-06-02 | Mask        |
| 2020-05-30 | Basketball  |
| 2020-06-01 | Bible       |
| 2020-06-02 | Mask        |
| 2020-05-30 | T-Shirt     |
+------------+-------------+
```

输出：

```
+------------+----------+------------------------------+
| sell_date  | num_sold | products                     |
+------------+----------+------------------------------+
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |
+------------+----------+------------------------------+
```

**思路分析**

GROUP BY按时间分组后，COUNT统计数量，GROUP_CONCAT进行连接

**代码**

```sql
SELECT Activities.sell_date, COUNT(DISTINCT Activities.product) AS num_sold, GROUP_CONCAT(DISTINCT Activities.product) AS products
FROM Activities
GROUP BY Activities.sell_date
ORDER BY Activities.sell_date;
```

## 20220413：患某种疾病的患者

**题目描述**

患者信息表： Patients

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| patient_id   | int     |
| patient_name | varchar |
| conditions   | varchar |
+--------------+---------+
```

patient_id （患者 ID）是该表的主键。
'conditions' （疾病）包含 0 个或以上的疾病代码，以空格分隔。
这个表包含医院中患者的信息。

 

写一条 SQL 语句，查询患有 I 类糖尿病的患者 ID （patient_id）、患者姓名（patient_name）以及其患有的所有疾病代码（conditions）。I 类糖尿病的代码总是包含前缀 DIAB1 。

按 任意顺序 返回结果表。

查询结果格式如下示例所示。

**示例**

输入：

```
Patients表：
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 1          | Daniel       | YFEV COUGH   |
| 2          | Alice        |              |
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 |
| 5          | Alain        | DIAB201      |
+------------+--------------+--------------+
```

输出：

```
+------------+--------------+--------------+
| patient_id | patient_name | conditions   |
+------------+--------------+--------------+
| 3          | Bob          | DIAB100 MYOP |
| 4          | George       | ACNE DIAB100 | 
+------------+--------------+--------------+
```

**思路分析**

正则匹配

**代码**

```sql
SELECT Patients.patient_id, Patients.patient_name, Patients.conditions
FROM Patients
WHERE Patients.conditions LIKE '% DIAB1%' OR Patients.conditions LIKE 'DIAB1%';
```

## 20220414：丢失信息的雇员

**题目描述**

表: Employees

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| name        | varchar |
+-------------+---------+
```

employee_id 是这个表的主键。
每一行表示雇员的id 和他的姓名。

表: Salaries

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| employee_id | int     |
| salary      | int     |
+-------------+---------+
```

employee_id is 这个表的主键。
每一行表示雇员的id 和他的薪水。

写出一个查询语句，找到所有 丢失信息 的雇员id。当满足下面一个条件时，就被认为是雇员的信息丢失：

    雇员的 姓名 丢失了，或者
    雇员的 薪水信息 丢失了，或者

返回这些雇员的id  employee_id ， 从小到大排序 。

查询结果格式如下面的例子所示。

**示例**

输入：

```
Employees table:
+-------------+----------+
| employee_id | name     |
+-------------+----------+
| 2           | Crew     |
| 4           | Haven    |
| 5           | Kristian |
+-------------+----------+
Salaries table:
+-------------+--------+
| employee_id | salary |
+-------------+--------+
| 5           | 76071  |
| 1           | 22517  |
| 4           | 63539  |
+-------------+--------+
```

输出：

```
+-------------+
| employee_id |
+-------------+
| 1           |
| 2           |
+-------------+
```

**思路分析**

使用左连接将两个表连接起来，选择出表中salary为null的人，同时使用右连接将两个表连接起来，选择出表中name为null的人，将两个结果合并起来即可。

**代码**

```sql
SELECT Employees.employee_id
FROM Employees LEFT JOIN Salaries ON Employees.employee_id = Salaries.employee_id
WHERE Salaries.salary IS NULL
UNION
SELECT Salaries.employee_id
FROM Employees RIGHT JOIN Salaries ON Employees.employee_id = Salaries.employee_id
WHERE Employees.name IS NULL
ORDER BY employee_id;
```

## 20220414：每个产品在不同商店的价格

**题目描述**

表：Products

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_id  | int     |
| store1      | int     |
| store2      | int     |
| store3      | int     |
+-------------+---------+
```

这张表的主键是product_id（产品Id）。
每行存储了这一产品在不同商店store1, store2, store3的价格。
如果这一产品在商店里没有出售，则值将为null。

 

请你重构 Products 表，查询每个产品在不同商店的价格，使得输出的格式变为(product_id, store, price) 。如果这一产品在商店里没有出售，则不输出这一行。

输出结果表中的 顺序不作要求 。

查询输出格式请参考下面示例。

**示例**

输入：

```
Products table:
+------------+--------+--------+--------+
| product_id | store1 | store2 | store3 |
+------------+--------+--------+--------+
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |
+------------+--------+--------+--------+

```

输出：

```
+------------+--------+-------+
| product_id | store  | price |
+------------+--------+-------+
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |
+------------+--------+-------+
```

**思路分析**

分别查询出三段，再用UNION进行连接即可。

- UNION ALL效率比UNION更高，但是不会对结果进行去重。

**代码**

```sql
SELECT Products.product_id, 'store1' AS store, Products.store1 AS price
FROM Products
WHERE Products.store1 IS NOT NULL
UNION ALL
SELECT Products.product_id, 'store2' AS store, Products.store2 AS price
FROM Products
WHERE Products.store2 IS NOT NULL
UNION ALL
SELECT Products.product_id, 'store3' AS store, Products.store3 AS price
FROM Products
WHERE Products.store3 IS NOT NULL;
```

## 20220414：树节点

**题目描述**

给定一个表 tree，id 是树节点的编号， p_id 是它父节点的 id 。

```
+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
```

树中每个节点属于以下三种类型之一：

    叶子：如果这个节点没有任何孩子节点。
    根：如果这个节点是整棵树的根，即没有父节点。
    内部节点：如果这个节点既不是叶子节点也不是根节点。


写一个查询语句，输出所有节点的编号和节点的类型，并将结果按照节点编号排序。上面样例的结果为：

```
 +----+------+
| id | Type |
+----+------+
| 1  | Root |
| 2  | Inner|
| 3  | Leaf |
| 4  | Leaf |
| 5  | Leaf |
+----+------+
```

**思路分析**

当id对应的pid为null时，为根结点，当id出现在pid的集合中，为内部结点，否则为外部结点。

**代码**

```sql
SELECT tree.id, (
    CASE WHEN tree.p_id IS NULL THEN 'Root'
    WHEN tree.id IN (SELECT DISTINCT tree.p_id FROM tree) THEN 'Inner'
    ELSE 'Leaf' END
) AS Type
FROM tree;
```

## 20220414：第二高薪水

**题目描述**

Employee 表：

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+
```

id 是这个表的主键。
表的每一行包含员工的工资信息。

 

编写一个 SQL 查询，获取并返回 Employee 表中第二高的薪水 。如果不存在第二高的薪水，查询应该返回 null 。

查询结果如下例所示。                                                                                                  

**示例**

输入：

```
Employee 表：
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+
```

输出：

```
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+
```

**思路分析**

思路一：嵌套查询，找到比最大值小的最大值即可。

思路二：使用limit和offset，limit表示查询显示多少条数据，offset表示跳过几条。加上IFNULL函数判空。

**代码**

思路一：

```sql
SELECT MAX(Employee.salary) AS SecondHighestSalary
FROM Employee
WHERE Employee.salary < (
    SELECT MAX(Employee.salary)
    FROM Employee
);
```

思路二：

```sql
SELECT (
    IFNULL((
        SELECT DISTINCT Employee.salary
        FROM Employee
        ORDER BY Employee.salary DESC
        LIMIT 1, 1), NULL)
) AS SecondHighestSalary
```

## 20220415：组合两个表

**题目描述**

表: Person

```
+-------------+---------+
| 列名         | 类型     |
+-------------+---------+
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |
+-------------+---------+
```

personId是该表的主键列。
该表包含一些人的ID和他们的姓和名的信息。

表: Address

```
+-------------+---------+
| 列名         | 类型    |
+-------------+---------+
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |
+-------------+---------+
```

addressId是该表的主键列。
该表的每一行都包含一个ID = PersonId的人的城市和州的信息。

 

编写一个SQL查询来报告 Person 表中每个人的姓、名、城市和状态。如果 personId 的地址不在 Address 表中，则报告为空  null 。

以 任意顺序 返回结果表。

查询结果格式如下所示。

**示例**

输入：

```
Person表:
+----------+----------+-----------+
| personId | lastName | firstName |
+----------+----------+-----------+
| 1        | Wang     | Allen     |
| 2        | Alice    | Bob       |
+----------+----------+-----------+
Address表:
+-----------+----------+---------------+------------+
| addressId | personId | city          | state      |
+-----------+----------+---------------+------------+
| 1         | 2        | New York City | New York   |
| 2         | 3        | Leetcode      | California |
+-----------+----------+---------------+------------+
```

输出：

```
+-----------+----------+---------------+----------+
| firstName | lastName | city          | state    |
+-----------+----------+---------------+----------+
| Allen     | Wang     | Null          | Null     |
| Bob       | Alice    | New York City | New York |
+-----------+----------+---------------+----------+
```

**思路分析**

左连接

**代码**

```sql
SELECT Person.FirstName AS firstName, Person.LastName AS lastName, Address.city, Address.state
FROM Person LEFT JOIN Address ON Person.personId = Address.personId;
```

## 20220415：进店却未进行交易的顾客

**题目描述**

表：Visits

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| visit_id    | int     |
| customer_id | int     |
+-------------+---------+
```

visit_id 是该表的主键。
该表包含有关光临过购物中心的顾客的信息。

 

表：Transactions

```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| transaction_id | int     |
| visit_id       | int     |
| amount         | int     |
+----------------+---------+
```

transaction_id 是此表的主键。
此表包含 visit_id 期间进行的交易的信息。

 

有一些顾客可能光顾了购物中心但没有进行交易。请你编写一个 SQL 查询，来查找这些顾客的 ID ，以及他们只光顾不交易的次数。

返回以 任何顺序 排序的结果表。

查询结果格式如下例所示。

输入：

```
Visits
+----------+-------------+
| visit_id | customer_id |
+----------+-------------+
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |
+----------+-------------+
Transactions
+----------------+----------+--------+
| transaction_id | visit_id | amount |
+----------------+----------+--------+
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 5        | 200    |
| 12             | 1        | 910    |
| 13             | 2        | 970    |
+----------------+----------+--------+
```

输出：

```
+-------------+----------------+
| customer_id | count_no_trans |
+-------------+----------------+
| 54          | 2              |
| 30          | 1              |
| 96          | 1              |
+-------------+----------------+
```

**思路分析**

左连接后transactionid取为null，则为未消费的顾客id，再分组求count即可。

**代码**

```sql
SELECT Visits.customer_id, COUNT(Visits.visit_id) AS count_no_trans
FROM Visits LEFT JOIN Transactions ON Visits.visit_id = Transactions.visit_id
WHERE Transactions.transaction_id IS NULL
GROUP BY Visits.customer_id;
```

## 20220415：文章浏览I

**题目描述**

Views 表：

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| article_id    | int     |
| author_id     | int     |
| viewer_id     | int     |
| view_date     | date    |
+---------------+---------+
```

此表无主键，因此可能会存在重复行。
此表的每一行都表示某人在某天浏览了某位作者的某篇文章。
请注意，同一人的 author_id 和 viewer_id 是相同的。

**示例**

输出：

```
Views 表：
+------------+-----------+-----------+------------+
| article_id | author_id | viewer_id | view_date  |
+------------+-----------+-----------+------------+
| 1          | 3         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |
+------------+-----------+-----------+------------+
```

**思路分析**

判断

**代码**

```sql
SELECT DISTINCT Views.author_id AS id
FROM Views
WHERE Views.author_id = Views.viewer_id
ORDER BY Views.viewer_id;
```

## 20220416：上升的温度

**题目描述**

表： Weather

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
```

id 是这个表的主键
该表包含特定日期的温度信息



编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 id 。

返回结果 不要求顺序 。

查询结果格式如下例。

**示例**

输入：

```
Weather 表：
+----+------------+-------------+
| id | recordDate | Temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |
+----+------------+-------------+
```

输出：

```
+----+
| id |
+----+
| 2  |
| 4  |
+----+
```

**思路分析**

使用DATE_ADD函数得到每一天的昨天的温度，再进行比较得到答案。

**代码**

```sql
SELECT a.id
FROM Weather a, (SELECT DATE_ADD(Weather.recordDate, INTERVAL 1 DAY) as nextDate, Temperature FROM Weather) b 
WHERE a.recordDate = b.nextDate AND a.Temperature > b.Temperature;
```

## 20220416：销售员

**题目描述**

表: SalesPerson

```
+-----------------+---------+
| Column Name     | Type    |
+-----------------+---------+
| sales_id        | int     |
| name            | varchar |
| salary          | int     |
| commission_rate | int     |
| hire_date       | date    |
+-----------------+---------+
```

Sales_id是该表的主键列。
该表的每一行都显示了销售人员的姓名和ID，以及他们的工资、佣金率和雇佣日期。

表: Company

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| com_id      | int     |
| name        | varchar |
| city        | varchar |
+-------------+---------+
```

Com_id是该表的主键列。
该表的每一行都表示公司的名称和ID，以及公司所在的城市。

 表: Orders

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| order_id    | int  |
| order_date  | date |
| com_id      | int  |
| sales_id    | int  |
| amount      | int  |
+-------------+------+
```

Order_id是该表的主键列。
com_id是Company表中com_id的外键。
sales_id是来自销售员表com_id的外键。
该表的每一行包含一个订单的信息。这包括公司的ID、销售人员的ID、订单日期和支付的金额。

 编写一个SQL查询，报告没有任何与名为 “RED” 的公司相关的订单的所有销售人员的姓名。

以 任意顺序 返回结果表。

查询结果格式如下所示。

**示例**

输入：

```
SalesPerson 表:
+----------+------+--------+-----------------+------------+
| sales_id | name | salary | commission_rate | hire_date  |
+----------+------+--------+-----------------+------------+
| 1        | John | 100000 | 6               | 4/1/2006   |
| 2        | Amy  | 12000  | 5               | 5/1/2010   |
| 3        | Mark | 65000  | 12              | 12/25/2008 |
| 4        | Pam  | 25000  | 25              | 1/1/2005   |
| 5        | Alex | 5000   | 10              | 2/3/2007   |
+----------+------+--------+-----------------+------------+
Company 表:
+--------+--------+----------+
| com_id | name   | city     |
+--------+--------+----------+
| 1      | RED    | Boston   |
| 2      | ORANGE | New York |
| 3      | YELLOW | Boston   |
| 4      | GREEN  | Austin   |
+--------+--------+----------+
Orders 表:
+----------+------------+--------+----------+--------+
| order_id | order_date | com_id | sales_id | amount |
+----------+------------+--------+----------+--------+
| 1        | 1/1/2014   | 3      | 4        | 10000  |
| 2        | 2/1/2014   | 4      | 5        | 5000   |
| 3        | 3/1/2014   | 1      | 1        | 50000  |
| 4        | 4/1/2014   | 1      | 4        | 25000  |
+----------+------------+--------+----------+--------+
```

输出：

```
+------+
| name |
+------+
| Amy  |
| Mark |
| Alex |
+------+
```

**思路分析**

嵌套查询

**代码**

```sql
SELECT SalesPerson.name
FROM SalesPerson
WHERE SalesPerson.sales_id NOT IN (
    SELECT Orders.sales_id
    FROM Orders LEFT JOIN Company ON Orders.com_id = Company.com_id
    WHERE Company.name = 'RED'
);
```

## 20220417：查询近30天活跃用户数

**题目描述**

活动记录表：Activity

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| session_id    | int     |
| activity_date | date    |
| activity_type | enum    |
+---------------+---------+
```

该表是用户在社交网站的活动记录。
该表没有主键，可能包含重复数据。
activity_type 字段为以下四种值 ('open_session', 'end_session', 'scroll_down', 'send_message')。
每个 session_id 只属于一个用户。

 

请写SQL查询出截至 2019-07-27（包含2019-07-27），近 30 天的每日活跃用户数（当天只要有一条活动记录，即为活跃用户）。

以 任意顺序 返回结果表。

查询结果示例如下。

**示例**

输入：

```
Activity table:
+---------+------------+---------------+---------------+
| user_id | session_id | activity_date | activity_type |
+---------+------------+---------------+---------------+
| 1       | 1          | 2019-07-20    | open_session  |
| 1       | 1          | 2019-07-20    | scroll_down   |
| 1       | 1          | 2019-07-20    | end_session   |
| 2       | 4          | 2019-07-20    | open_session  |
| 2       | 4          | 2019-07-21    | send_message  |
| 2       | 4          | 2019-07-21    | end_session   |
| 3       | 2          | 2019-07-21    | open_session  |
| 3       | 2          | 2019-07-21    | send_message  |
| 3       | 2          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |
+---------+------------+---------------+---------------+
```

输出：

```
+------------+--------------+ 
| day        | active_users |
+------------+--------------+ 
| 2019-07-20 | 2            |
| 2019-07-21 | 2            |
+------------+--------------+ 
```

**思路分析**

使用DATEDIFF判断日期，注意先后顺序！

**代码**

```sql
SELECT Activity.activity_date AS day, COUNT(DISTINCT Activity.user_id) AS active_users
FROM Activity
WHERE DATEDIFF('2019-07-27', Activity.activity_date) < 30
GROUP BY Activity.activity_date;
```

## 20220417：每天的领导和合伙人

**题目描述**

表：DailySales

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| date_id     | date    |
| make_name   | varchar |
| lead_id     | int     |
| partner_id  | int     |
+-------------+---------+
```

该表没有主键。
该表包含日期、产品的名称，以及售给的领导和合伙人的编号。
名称只包含小写英文字母。

 

写一条 SQL 语句，使得对于每一个 date_id 和 make_name，返回不同的 lead_id 以及不同的 partner_id 的数量。

按 任意顺序 返回结果表。

查询结果格式如下示例所示。

**示例**

输入：

```
DailySales 表：
+-----------+-----------+---------+------------+
| date_id   | make_name | lead_id | partner_id |
+-----------+-----------+---------+------------+
| 2020-12-8 | toyota    | 0       | 1          |
| 2020-12-8 | toyota    | 1       | 0          |
| 2020-12-8 | toyota    | 1       | 2          |
| 2020-12-7 | toyota    | 0       | 2          |
| 2020-12-7 | toyota    | 0       | 1          |
| 2020-12-8 | honda     | 1       | 2          |
| 2020-12-8 | honda     | 2       | 1          |
| 2020-12-7 | honda     | 0       | 1          |
| 2020-12-7 | honda     | 1       | 2          |
| 2020-12-7 | honda     | 2       | 1          |
+-----------+-----------+---------+------------+
```

输出：

```
+-----------+-----------+--------------+-----------------+
| date_id   | make_name | unique_leads | unique_partners |
+-----------+-----------+--------------+-----------------+
| 2020-12-8 | toyota    | 2            | 3               |
| 2020-12-7 | toyota    | 1            | 2               |
| 2020-12-8 | honda     | 2            | 2               |
| 2020-12-7 | honda     | 3            | 2               |
+-----------+-----------+--------------+-----------------+
```

**思路分析**

直接分组+计数

**代码**

```sql
SELECT DailySales.date_id, DailySales.make_name, COUNT(DISTINCT DailySales.lead_id) AS unique_leads, COUNT(DISTINCT DailySales.partner_id) AS unique_partners
FROM DailySales
GROUP BY DailySales.date_id, DailySales.make_name;
```

## 20220417：求关注者的数量

**题目描述**

表： Followers

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| follower_id | int  |
+-------------+------+
```

(user_id, follower_id) 是这个表的主键。
该表包含一个关注关系中关注者和用户的编号，其中关注者关注用户。

写出 SQL 语句，对于每一个用户，返回该用户的关注者数量。

按 user_id 的顺序返回结果表。

查询结果的格式如下示例所示。

**示例**

输入：

```
Followers 表：
+---------+-------------+
| user_id | follower_id |
+---------+-------------+
| 0       | 1           |
| 1       | 0           |
| 2       | 0           |
| 2       | 1           |
+---------+-------------+
```

输出：

```
+---------+----------------+
| user_id | followers_count|
+---------+----------------+
| 0       | 1              |
| 1       | 1              |
| 2       | 2              |
+---------+----------------+
```

**思路分析**

分组+计数

**代码**

```sql
SELECT Followers.user_id, COUNT(DISTINCT Followers.follower_id) AS followers_count
FROM Followers
GROUP BY Followers.user_id;
```

## 20220418：订单最多的客户

**题目描述**

表: Orders

```
+-----------------+----------+
| Column Name     | Type     |
+-----------------+----------+
| order_number    | int      |
| customer_number | int      |
+-----------------+----------+
```

Order_number是该表的主键。
此表包含关于订单ID和客户ID的信息。

 

编写一个SQL查询，为下了 最多订单 的客户查找 customer_number 。

测试用例生成后， 恰好有一个客户 比任何其他客户下了更多的订单。

查询结果格式如下所示。

**示例**

输入：

```
Orders 表:
+--------------+-----------------+
| order_number | customer_number |
+--------------+-----------------+
| 1            | 1               |
| 2            | 2               |
| 3            | 3               |
| 4            | 3               |
+--------------+-----------------+
```

输出：

```
+-----------------+
| customer_number |
+-----------------+
| 3               |
+-----------------+
```

**思路分析**

分组之后按count降序排列LIMIT取1

**代码**

```sql
SELECT customer_number
FROM Orders
GROUP BY Orders.customer_number
ORDER BY COUNT(*) DESC
LIMIT 1;
```

## 20220418：游戏玩法分析I

**题目描述**

活动表 Activity：

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
```

表的主键是 (player_id, event_date)。
这张表展示了一些游戏玩家在游戏平台上的行为活动。
每行数据记录了一名玩家在退出平台之前，当天使用同一台设备登录平台后打开的游戏的数目（可能是 0 个）。

**示例**

输出：

```
Activity 表：
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result 表：
+-----------+-------------+
| player_id | first_login |
+-----------+-------------+
| 1         | 2016-03-01  |
| 2         | 2017-06-25  |
| 3         | 2016-03-02  |
+-----------+-------------+
```

**思路分析**

分组取最小

**代码**

```sql
SELECT Activity.player_id, MIN(Activity.event_date) AS first_login
FROM Activity
GROUP BY Activity.player_id;
```

## 20220418：2020年的最后一次登录

**题目描述**

表: Logins

```
+----------------+----------+
| 列名           | 类型      |
+----------------+----------+
| user_id        | int      |
| time_stamp     | datetime |
+----------------+----------+
```

(user_id, time_stamp) 是这个表的主键。
每一行包含的信息是user_id 这个用户的登录时间。

 

编写一个 SQL 查询，该查询可以获取在 2020 年登录过的所有用户的本年度 最后一次 登录时间。结果集 不 包含 2020 年没有登录过的用户。

返回的结果集可以按 任意顺序 排列。

查询结果格式如下例。

**示例**

输入：

```
Logins 表:
+---------+---------------------+
| user_id | time_stamp          |
+---------+---------------------+
| 6       | 2020-06-30 15:06:07 |
| 6       | 2021-04-21 14:06:06 |
| 6       | 2019-03-07 00:18:15 |
| 8       | 2020-02-01 05:10:53 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
| 2       | 2019-08-25 07:59:08 |
| 14      | 2019-07-14 09:00:00 |
| 14      | 2021-01-06 11:59:59 |
+---------+---------------------+
```

输出：

```
+---------+---------------------+
| user_id | last_stamp          |
+---------+---------------------+
| 6       | 2020-06-30 15:06:07 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
+---------+---------------------+
```

**思路分析**

分组+判断+取最大值

**代码**

```sql
SELECT Logins.user_id, MAX(Logins.time_stamp) AS last_stamp
FROM Logins
WHERE Logins.time_stamp BETWEEN '2020-01-01 00:00:00' AND '2020-12-31 23:59:59'
GROUP BY Logins.user_id;
```

## 20220418：查询每个员工花费的总时间

**题目描述**

表: Employees

```
+-------------+------+
| Column Name | Type |
+-------------+------+
| emp_id      | int  |
| event_day   | date |
| in_time     | int  |
| out_time    | int  |
+-------------+------+
```

(emp_id, event_day, in_time) 是这个表的主键。
该表显示了员工在办公室的出入情况。
event_day 是此事件发生的日期，in_time 是员工进入办公室的时间，而 out_time 是他们离开办公室的时间。
in_time 和 out_time 的取值在1到1440之间。
题目保证同一天没有两个事件在时间上是相交的，并且保证 in_time 小于 out_time。

 

编写一个SQL查询以计算每位员工每天在办公室花费的总时间（以分钟为单位）。 请注意，在一天之内，同一员工是可以多次进入和离开办公室的。 在办公室里一次进出所花费的时间为out_time 减去 in_time。

返回结果表单的顺序无要求。

**示例**

输出：

```
Employees table:
+--------+------------+---------+----------+
| emp_id | event_day  | in_time | out_time |
+--------+------------+---------+----------+
| 1      | 2020-11-28 | 4       | 32       |
| 1      | 2020-11-28 | 55      | 200      |
| 1      | 2020-12-03 | 1       | 42       |
| 2      | 2020-11-28 | 3       | 33       |
| 2      | 2020-12-09 | 47      | 74       |
+--------+------------+---------+----------+
Result table:
+------------+--------+------------+
| day        | emp_id | total_time |
+------------+--------+------------+
| 2020-11-28 | 1      | 173        |
| 2020-11-28 | 2      | 30         |
| 2020-12-03 | 1      | 41         |
| 2020-12-09 | 2      | 27         |
+------------+--------+------------+
```

**思路分析**

分组+求和

**代码**

```sql
SELECT Employees.event_day AS day, Employees.emp_id, SUM(Employees.out_time) - SUM(Employees.in_time) AS total_time
FROM Employees
GROUP BY Employees.event_day, Employees.emp_id;
```

## 20220419：股票中的资本损益

**题目描述**

Stocks 表：

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| stock_name    | varchar |
| operation     | enum    |
| operation_day | int     |
| price         | int     |
+---------------+---------+
```

(stock_name, day) 是这张表的主键
operation 列使用的是一种枚举类型，包括：('Sell','Buy')
此表的每一行代表了名为 stock_name 的某支股票在 operation_day 这一天的操作价格。
保证股票的每次'Sell'操作前，都有相应的'Buy'操作。

 

编写一个SQL查询来报告每支股票的资本损益。

股票的资本损益是一次或多次买卖股票后的全部收益或损失。

以任意顺序返回结果即可。

**示例**

输入：

```
+---------------+-----------+---------------+--------+
| stock_name    | operation | operation_day | price  |
+---------------+-----------+---------------+--------+
| Leetcode      | Buy       | 1             | 1000   |
| Corona Masks  | Buy       | 2             | 10     |
| Leetcode      | Sell      | 5             | 9000   |
| Handbags      | Buy       | 17            | 30000  |
| Corona Masks  | Sell      | 3             | 1010   |
| Corona Masks  | Buy       | 4             | 1000   |
| Corona Masks  | Sell      | 5             | 500    |
| Corona Masks  | Buy       | 6             | 1000   |
| Handbags      | Sell      | 29            | 7000   |
| Corona Masks  | Sell      | 10            | 10000  |
+---------------+-----------+---------------+--------+
```

输出：

```
+---------------+-------------------+
| stock_name    | capital_gain_loss |
+---------------+-------------------+
| Corona Masks  | 9500              |
| Leetcode      | 8000              |
| Handbags      | -23000            |
+---------------+-------------------+
```

**思路分析**

使用CASE WHEN语句，当operation为bug的时候变成负的，再求和即可。

**代码**

```sql
SELECT Stocks.stock_name, SUM(
    CASE WHEN Stocks.operation = 'Buy' THEN - Stocks.price
    ELSE Stocks.price END
) AS capital_gain_loss
FROM Stocks
GROUP BY Stocks.stock_name;
```

## 20220419：排名靠前的旅行者

**题目描述**

表：Users

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+
```

id 是该表单主键。
name 是用户名字。

表：Rides

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| user_id       | int     |
| distance      | int     |
+---------------+---------+
```

id 是该表单主键。
user_id 是本次行程的用户的 id, 而该用户此次行程距离为 distance 。

 

写一段 SQL , 报告每个用户的旅行距离。

返回的结果表单，以 travelled_distance 降序排列 ，如果有两个或者更多的用户旅行了相同的距离, 那么再以 name 升序排列 。

查询结果格式如下例所示。

**示例**

输入：

```
Users 表：
+------+-----------+
| id   | name      |
+------+-----------+
| 1    | Alice     |
| 2    | Bob       |
| 3    | Alex      |
| 4    | Donald    |
| 7    | Lee       |
| 13   | Jonathan  |
| 19   | Elvis     |
+------+-----------+

Rides 表：
+------+----------+----------+
| id   | user_id  | distance |
+------+----------+----------+
| 1    | 1        | 120      |
| 2    | 2        | 317      |
| 3    | 3        | 222      |
| 4    | 7        | 100      |
| 5    | 13       | 312      |
| 6    | 19       | 50       |
| 7    | 7        | 120      |
| 8    | 19       | 400      |
| 9    | 7        | 230      |
+------+----------+----------+
```

输出：

```
+----------+--------------------+
| name     | travelled_distance |
+----------+--------------------+
| Elvis    | 450                |
| Lee      | 450                |
| Bob      | 317                |
| Jonathan | 312                |
| Alex     | 222                |
| Alice    | 120                |
| Donald   | 0                  |
+----------+--------------------+
```

**思路分析**

注意空值变成0

**代码**

```sql
SELECT Users.name, IFNULL(SUM(Rides.distance), 0) AS travelled_distance 
FROM Users LEFT JOIN Rides ON Users.id = Rides.user_id 
GROUP BY Rides.user_id 
ORDER BY travelled_distance DESC, Users.name
```

## 20220419：市场分析I

**题目描述**

Table: Users

```
+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| user_id        | int     |
| join_date      | date    |
| favorite_brand | varchar |
+----------------+---------+
```

此表主键是 user_id。
表中描述了购物网站的用户信息，用户可以在此网站上进行商品买卖。

 

Table: Orders

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| order_date    | date    |
| item_id       | int     |
| buyer_id      | int     |
| seller_id     | int     |
+---------------+---------+
```

此表主键是 order_id。
外键是 item_id 和（buyer_id，seller_id）。

 

Table: Items

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| item_id       | int     |
| item_brand    | varchar |
+---------------+---------+
```

此表主键是 item_id。

 

请写出一条SQL语句以查询每个用户的注册日期和在 2019 年作为买家的订单总数。

以 任意顺序 返回结果表。

查询结果格式如下。

**示例**

输入：

```
Users 表:
+---------+------------+----------------+
| user_id | join_date  | favorite_brand |
+---------+------------+----------------+
| 1       | 2018-01-01 | Lenovo         |
| 2       | 2018-02-09 | Samsung        |
| 3       | 2018-01-19 | LG             |
| 4       | 2018-05-21 | HP             |
+---------+------------+----------------+
Orders 表:
+----------+------------+---------+----------+-----------+
| order_id | order_date | item_id | buyer_id | seller_id |
+----------+------------+---------+----------+-----------+
| 1        | 2019-08-01 | 4       | 1        | 2         |
| 2        | 2018-08-02 | 2       | 1        | 3         |
| 3        | 2019-08-03 | 3       | 2        | 3         |
| 4        | 2018-08-04 | 1       | 4        | 2         |
| 5        | 2018-08-04 | 1       | 3        | 4         |
| 6        | 2019-08-05 | 2       | 2        | 4         |
+----------+------------+---------+----------+-----------+
Items 表:
+---------+------------+
| item_id | item_brand |
+---------+------------+
| 1       | Samsung    |
| 2       | Lenovo     |
| 3       | LG         |
| 4       | HP         |
+---------+------------+
```

输出：

```
+-----------+------------+----------------+
| buyer_id  | join_date  | orders_in_2019 |
+-----------+------------+----------------+
| 1         | 2018-01-01 | 1              |
| 2         | 2018-02-09 | 2              |
| 3         | 2018-01-19 | 0              |
| 4         | 2018-05-21 | 0              |
+-----------+------------+----------------+
```

**思路分析**

思路一：使用嵌套查询

思路二：使用YEAR()函数

**代码**

```sql
SELECT Users.user_id AS buyer_id, Users.join_date, IFNULL(r.orders_in_2019, 0) AS orders_in_2019
FROM Users LEFT JOIN (
    SELECT Orders.buyer_id, COUNT(Orders.order_id) AS orders_in_2019
    FROM Orders
    WHERE Orders.order_date BETWEEN '2019-01-01' AND '2019-12-31'
    GROUP BY Orders.buyer_id
) AS r ON Users.user_id = r.buyer_id;
```

思路二：

```sql
SELECT Users.user_id AS buyer_id, Users.join_date, COUNT(Orders.order_id) AS orders_in_2019
FROM Users LEFT JOIN Orders ON Users.user_id = Orders.buyer_id AND YEAR(Orders.order_date) = '2019'
GROUP BY user_id
```

## 20220420：查找重复的电子邮箱 

**题目描述**

编写一个 SQL 查询，查找 `Person` 表中所有重复的电子邮箱。

**示例**

输入：

```
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+
```

输出：

```
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
```

**思路分析**

分组，计数大于1，也可以使用嵌套查询

**代码**

```sql
SELECT Person.Email
FROM Person
GROUP BY Person.Email
HAVING COUNT(Person.Email) > 1;
```

## 20220420：合作过至少三次的演员和导演

**题目描述**

ActorDirector 表：

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| actor_id    | int     |
| director_id | int     |
| timestamp   | int     |
+-------------+---------+
```

timestamp 是这张表的主键.

**示例**

输入：

```
+-------------+-------------+-------------+
| actor_id    | director_id | timestamp   |
+-------------+-------------+-------------+
| 1           | 1           | 0           |
| 1           | 1           | 1           |
| 1           | 1           | 2           |
| 1           | 2           | 3           |
| 1           | 2           | 4           |
| 2           | 1           | 5           |
| 2           | 1           | 6           |
+-------------+-------------+-------------+
```

输出：

```
+-------------+-------------+
| actor_id    | director_id |
+-------------+-------------+
| 1           | 1           |
+-------------+-------------+
```

**思路分析**

分组计数

**代码**

```sql
SELECT ActorDirector.actor_id, ActorDirector.director_id
FROM ActorDirector
GROUP BY ActorDirector.actor_id, ActorDirector.director_id
HAVING COUNT(ActorDirector.timestamp) >= 3;
```

## 20220420：银行账户概要II

**题目描述**

表: Users

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| account      | int     |
| name         | varchar |
+--------------+---------+
```

account 是该表的主键.
表中的每一行包含银行里中每一个用户的账号.

表: Transactions

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| trans_id      | int     |
| account       | int     |
| amount        | int     |
| transacted_on | date    |
+---------------+---------+
```

trans_id 是该表主键.
该表的每一行包含了所有账户的交易改变情况.
如果用户收到了钱, 那么金额是正的; 如果用户转了钱, 那么金额是负的.
所有账户的起始余额为 0.

写一个 SQL,  报告余额高于 10000 的所有用户的名字和余额. 账户的余额等于包含该账户的所有交易的总和.

返回结果表单没有顺序要求.

查询结果格式如下例所示.

**示例**

输入：

```
Users table:
+------------+--------------+
| account    | name         |
+------------+--------------+
| 900001     | Alice        |
| 900002     | Bob          |
| 900003     | Charlie      |
+------------+--------------+

Transactions table:
+------------+------------+------------+---------------+
| trans_id   | account    | amount     | transacted_on |
+------------+------------+------------+---------------+
| 1          | 900001     | 7000       |  2020-08-01   |
| 2          | 900001     | 7000       |  2020-09-01   |
| 3          | 900001     | -3000      |  2020-09-02   |
| 4          | 900002     | 1000       |  2020-09-12   |
| 5          | 900003     | 6000       |  2020-08-07   |
| 6          | 900003     | 6000       |  2020-09-07   |
| 7          | 900003     | -4000      |  2020-09-11   |
+------------+------------+------------+---------------+
```

输出：

```
+------------+------------+
| name       | balance    |
+------------+------------+
| Alice      | 11000      |
+------------+------------+
```

**思路分析**

分组求和+连接

**代码**

```sql
SELECT Users.name AS NAME, SUM(Transactions.amount) AS BALANCE
FROM Transactions LEFT JOIN Users ON Transactions.account = Users.account
GROUP BY Transactions.account
HAVING SUM(Transactions.amount) > 10000;
```

## 20220420：销售分析III

**题目描述**

Table: Product

```
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
| unit_price   | int     |
+--------------+---------+
```

Product_id是该表的主键。
该表的每一行显示每个产品的名称和价格。

Table: Sales

```
+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| seller_id   | int     |
| product_id  | int     |
| buyer_id    | int     |
| sale_date   | date    |
| quantity    | int     |
| price       | int     |
+------ ------+---------+
```

这个表没有主键，它可以有重复的行。
product_id 是 Product 表的外键。
该表的每一行包含关于一个销售的一些信息。

编写一个SQL查询，报告2019年春季才售出的产品。即仅在2019-01-01至2019-03-31（含）之间出售的商品。

以 任意顺序 返回结果表。

查询结果格式如下所示。

**示例**

输入：

```
Product table:
+------------+--------------+------------+
| product_id | product_name | unit_price |
+------------+--------------+------------+
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
+------------+--------------+------------+
Sales table:
+-----------+------------+----------+------------+----------+-------+
| seller_id | product_id | buyer_id | sale_date  | quantity | price |
+-----------+------------+----------+------------+----------+-------+
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |
+-----------+------------+----------+------------+----------+-------+
```

输出：

```
+-------------+--------------+
| product_id  | product_name |
+-------------+--------------+
| 1           | S8           |
+-------------+--------------+
```

**思路分析**

核心就是判断当前分组内所有的sale_date是否都在2019年春季。

**代码**

```sql
SELECT Sales.product_id, Product.product_name
FROM Sales LEFT JOIN Product ON Sales.product_id = Product.product_id
GROUP BY Sales.product_id
HAVING MIN(Sales.sale_date) >= '2019-01-01' AND MAX(Sales.sale_date) <= '2019-03-31'
```









































































## 20220420：

**题目描述**



**示例**

输入：

```

```

输出：

```

```

**思路分析**



**代码**

```sql

```

