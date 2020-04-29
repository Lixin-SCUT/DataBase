题目描述：    
> 表1: Person       
+-------------+---------+     
| 列名         | 类型     |       
+-------------+---------+     
| PersonId    | int     |            
| FirstName   | varchar |      
| LastName    | varchar |           
+-------------+---------+      
PersonId 是上表主键       
表2: Address       
+-------------+---------+       
| 列名         | 类型    |     
+-------------+---------+      
| AddressId   | int     |      
| PersonId    | int     |       
| City        | varchar |     
| State       | varchar |      
+-------------+---------+         
AddressId 是上表主键        
 编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：        
FirstName, LastName, City, State             
来源：力扣（LeetCode）        
链接：https://leetcode-cn.com/problems/combine-two-tables      
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。   

这道题比较简单，重点在于“无论 person 是否有地址信息”，主要就是默认的inner join和outer join的区别，并且还有on和where的区别

```
SELECT FirstName, LastName, City, State
FROM Person LEFT OUTER JOIN Address
ON Person.PersonId = Address.PersonId
```

官方题解：
> 方法：使用 outer join
算法
因为表 Address 中的 personId 是表 Person 的外关键字，所以我们可以连接这两个表来获取一个人的地址信息。
考虑到可能不是每个人都有地址信息，我们应该使用 outer join 而不是默认的 inner join。
```
MySQL
select FirstName, LastName, City, State
from Person left join Address
on Person.PersonId = Address.PersonId
;
```
**注意：如果没有某个人的地址信息，使用 where 子句过滤记录将失败，因为它不会显示姓名信息。**

**《MySQL必知必会》相关知识点**
&emsp;&emsp;MySQL的联结表默认为内部联结或等值联结(equijoin)的简单联结，另外三种联结分别是自联结、自然联结和外部联结。

**自联结**
&emsp;&emsp;自联结就是同一个表联结起来，需要配合表别名使用，使用表别名的主要原因之一是能在单条SELECT语句中不止一次引用相同的表

&emsp;&emsp;自联结可以解决子查询的问题： 自联结通常作为外部语句用来替代 从相同表中检索数据时使用的子查询语句。虽然最终的结果是 相同的，但有时候处理联结远比处理子查询快得多。应该试一 下两种方法，以确定哪一种的性能更好
 
**自然联结**
&emsp;&emsp;标准的联结(前一章中介绍的内部联结)返回所有数据，甚 至相同的列多次出现。自然联结排除多次出现，使每个列只返回一次。
&emsp;&emsp;但是系统不完成这项工作，由你自己完成它。自然联结是这样一种联结，其中你只能选择那些唯一的列。这一 般是通过对表使用通配符(SELECT * )，对所有其他表的列使用明确的子 集来完成的。

&emsp;&emsp;事实上，迄今为止我们默认建立的每个内部联结都是自然联结，很可能我们永远都不会用到不是自然联结的内部联结。

**外部联结**
&emsp;&emsp;联结包含了那些在相关表中没有关联行的行。这种 类型的联结称为外部联结。
&emsp;&emsp;许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行。
&emsp;&emsp;在使用OUTER JOIN语法时，必须使用RIGHT或LEFT关键字 指定包括其所有行的表(RIGHT指出的是OUTER JOIN右边的表，而LEFT 指出的是OUTER JOIN左边的表)。

**外部联结的类型** 
&emsp;&emsp;存在两种基本的外部联结形式:左外部联结 和右外部联结。它们之间的唯一差别是所关联的表的顺序不 同。换句话说，左外部联结可通过颠倒FROM或WHERE子句中表的顺序转换为右外部联结。因此，两种类型的外部联结可互 换使用，而究竟使用哪一种纯粹是根据方便而定。

&emsp;&emsp;使用LEFT OUTER JOIN从FROM子句的左边表中选择所有行。为了从右边的表中选择所有行，应该使用RIGHT OUTER JOIN

注意
&emsp;&emsp;没有`*=`操作符 MySQL不支持简化字符`*=`和`=*`的使用，这两 种操作符在其他DBMS中是很流行的。

**使用带聚集函数的联结**
&emsp;&emsp;聚集函数用来汇总数据。虽然至今为止聚集函数，但这些函数也可以与联结一起使用。
&emsp;&emsp;聚集函数也可以方便地与其他联结一起使用

**使用联结和联结条件**
- **注意所使用的联结类型。一般我们使用内部联结，但使用外部联结也是有效的。**
- 保证使用正确的联结条件，否则将返回不正确的数据。
- **应该总是提供联结条件，否则会得出笛卡儿积。**
- 在一个联结中可以包含多个表，甚至对于每个联结可以采用不同的联结类型。虽然这样做是合法的，一般也很有用，但应该在一 起测试它们前，分别测试每个联结。这将使故障排除更为简单。



