题目描述：
> 编写一个 SQL 查询，获取 Employee 表中第二高的薪水（Salary） 。       
+----+--------+       
| Id | Salary |     
+----+--------+      
| 1  | 100    |      
| 2  | 200    |     
| 3  | 300    |       
+----+--------+           
例如上述 Employee 表，SQL查询应该返回 200 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 null。       
+---------------------+        
| SecondHighestSalary |      
+---------------------+      
| 200                 |       
+---------------------+        
来源：力扣（LeetCode）          
链接：https://leetcode-cn.com/problems/second-highest-salary            
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。     

主要是下面几个点
1.列别名， AS SecondHighestSalary
2. 降序排序，使用ORDER BY +DESC
3. 限制行数  LIMIT 1,1，注意行数从0开始，第一行为0
4. 去重， 使用DISTINCT
5. 返回NULL，使用子查询

```
SELECT (SELECT DISTINCT Salary 
        FROM Employee
        ORDER BY Salary DESC
        LIMIT 1,1) AS SecondHighestSalary;
```

官方题解：
> 方法一：使用子查询和 LIMIT 子句
算法
将不同的薪资按降序排序，然后使用 LIMIT 子句获得第二高的薪资。
```
MySQL
SELECT DISTINCT
    Salary AS SecondHighestSalary
FROM
    Employee
ORDER BY Salary DESC
LIMIT 1 OFFSET 1
```
> 然而，如果没有这样的第二最高工资，这个解决方案将被判断为 “错误答案”，因为本表可能只有一项记录。为了克服这个问题，我们可以将其作为临时表。
```
MySQL
SELECT
    (SELECT DISTINCT
            Salary
        FROM
            Employee
        ORDER BY Salary DESC
        LIMIT 1 OFFSET 1) AS SecondHighestSalary
;
```
> 方法二：使用 IFNULL 和 LIMIT 子句
解决 “NULL” 问题的另一种方法是使用 “IFNULL” 函数，如下所示。
```
MySQL
SELECT
    IFNULL(
      (SELECT DISTINCT Salary
       FROM Employee
       ORDER BY Salary DESC
        LIMIT 1 OFFSET 1),
    NULL) AS SecondHighestSalary
```


《MySQL知识点》
**排序检索数据**
&emsp;&emsp;检索出的数据并不是以纯粹的随机顺序显示的。如果不排 序，数据一般将以它在底层表中出现的顺序显示。这可以是数据最初添加到表中的顺序。但是，如果数据后来进行过更新或删除，则此顺 序将会受到MySQL重用回收存储空间的影响。因此，如果不明确控 制的话，不能(也不应该)依赖该排序顺序。关系数据库设计理论认 为，如果不明确规定排序顺序，则不应该假定检索出的数据的顺序有 意义。
&emsp;&emsp;为了明确地排序用SELECT语句检索出的数据，可使用ORDER BY子句。 ORDER BY子句取一个或多个列的名字，据此对输出进行排序
&emsp;&emsp;通常，ORDERBY子句中使用的列将 是为显示所选择的列。但是，实际上并不一定要这样，用非 检索的列排序数据是完全合法的。
&emsp;&emsp;在按多个列排序时，排序完全按所规定的顺序进行。
&emsp;&emsp;数据排序不限于升序排序(从A到Z)。这只是默认的排序顺序，还可 以使用ORDER BY子句以降序(从Z到A)顺序排序。为了进行降序排序， 必须指定DESC关键字。
&emsp;&emsp;DESC关键字只应用到直接位于其前面的列名。如果想在多个列上进行降序排序，必须 对每个列指定DESC关键字。

**LIMIT关键字**
&emsp;&emsp;使用ORDER BY和LIMIT的组合，能够找出一个列中最高或最低的值。
&emsp;&emsp;SELECT语句返回所有匹配的行，它们可能是指定表中的每个行。为了返回第一行或前几行，可使用LIMIT子句
&emsp;&emsp;带一个值的LIMIT总是从第一行开始，给出的数为返回的行数。 带两个值的LIMIT可以指定从行号为第一个值的位置开始。
&emsp;&emsp;检索出来的第一行为行0而不是行1。因此，LIMIT1,1 将检索出第二行而不是第一行。
&emsp;&emsp;在行数不够时 LIMIT中指定要检索的行数为检索的最大行 数。如果没有足够的行(例如，给出LIMIT 10, 5，但只有13 行)，MySQL将只返回它能返回的那么多行。
&emsp;&emsp;MySQL 5的LIMIT语法 LIMIT 3, 4的含义是从行4开始的3 行还是从行3开始的4行?如前所述，它的意思是从行3开始的4 行，这容易把人搞糊涂。
&emsp;&emsp;由于这个原因，MySQL 5支持LIMIT的另一种替代语法。LIMIT 4 OFFSET 3意为从行3开始取4行，就像LIMIT 3, 4一样。

**DISTINCT关键字**
&emsp;&emsp;使用DISTINCT关键字检索出有不同值的列表，此关键字指示MySQL 只返回不同的值。
&emsp;&emsp;不能部分使用DISTINCT DISTINCT关键字应用于所有列而 不仅是前置它的列。如果给出SELECT DISTINCT vend_id, prod_price，除非指定的两个列都不同，否则所有行都将被 检索出来。
