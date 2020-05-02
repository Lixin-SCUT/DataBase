题目描述：
> 编写一个 SQL 查询，获取 Employee 表中第 n 高的薪水（Salary）。        
+----+--------+       
| Id | Salary |         
+----+--------+        
| 1  | 100    |      
| 2  | 200    |      
| 3  | 300    |      
+----+--------+       
例如上述 Employee 表，n = 2 时，应返回第二高的薪水 200。如果不存在第 n 高的薪水，那么查询应返回 null。      
+------------------------+      
| getNthHighestSalary(2) |        
+------------------------+      
| 200                    |        
+------------------------+         
来源：力扣（LeetCode）       
链接：https://leetcode-cn.com/problems/nth-highest-salary       
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。       

这一题和上一题绝大部分都相同，主要用于复习
新的点在于
1. SET设置变量，不能使用 LIMIT N-1，N-1是非法的
2. a OFFSET b和 b，a 刚好相反，注意区分

```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
SET n = N-1;
  RETURN (
      # Write your MySQL query statement below.
      SELECT (
          SELECT DISTINCT Salary
          FROM Employee
          ORDER BY Salary DESC
          LIMIT 1 OFFSET n
      )
  );
END
```

网友题解
第一个方案，基于前面一道题，只要调整下limit的起始点即可，但是此方法不是很符合题意，因为给的不是一句sql，而是1个变量定义+1个sql
```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  SET n = N-1;
  RETURN (     
  SELECT DISTINCT Salary FROM Employee ORDER BY Salary DESC LIMIT n,1
  );
END
```
第二个方案，按符合题意的思路来，只用一个sql，那么要先查出前N薪水，然后取最小就好了，注意可能总数不够前N，count一下比较即可
```
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  RETURN (     
  SELECT  IF(count<N,NULL,min) 
  FROM
    (SELECT MIN(Salary) AS min, COUNT(1) AS count
    FROM
      (SELECT DISTINCT Salary
      FROM Employee ORDER BY Salary DESC LIMIT N) AS a
    ) as b
  );
END
```

相关知识点：
《MySQL必知必会》
SET：
SET命令用来将新值赋给被更新的列。

MIN：
MIN()的功能与MAX()功能相反，它返回指定列的最小值。与MAX()一样，MIN()要求指定列名
对非数值数据使用MIN() MIN()函数与MAX()函数类似， MySQL允许将它用来返回任意列中的最小值，包括返回文本 列中的最小值。在用于文本数据时，如果数据按相应的列排序，则MIN()返回最前面的行。
NULL值 MIN()函数忽略列值为NULL的行。

COUNT()：
COUNT()函数进行计数。可利用COUNT()确定表中行的数目或符合特定条件的行的数目。 COUNT()函数有两种使用方式。
 使用COUNT(\*)对表中行的数目进行计数，不管表列中包含的是空值(NULL)还是非空值。
 使用COUNT(column)对特定列中具有值的行进行计数，忽略 NULL值。
如果指定列名，则指定列的值为空的行被COUNT() 函数忽略，但如果COUNT()函数中用的是星号(\*)，则不忽略。

IF语句
如果条件为真，返回前者，否则返回后者
