题目描述： 
> 编写一个 SQL 查询来实现分数排名。        
如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。          
+----+-------+       
| Id | Score |       
+----+-------+      
| 1  | 3.50  |      
| 2  | 3.65  |        
| 3  | 4.00  |        
| 4  | 3.85  |             
| 5  | 4.00  |        
| 6  | 3.65  |       
+----+-------+         
例如，根据上述给定的 Scores 表，你的查询应该返回（按分数从高到低排列）：      
+-------+------+                  
| Score | Rank |       
+-------+------+       
| 4.00  | 1    |       
| 4.00  | 1    |        
| 3.85  | 2    |      
| 3.65  | 3    |        
| 3.65  | 3    |      
| 3.50  | 4    |       
+-------+------+      
来源：力扣（LeetCode）       
链接：https://leetcode-cn.com/problems/rank-scores       
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。     

第一步比较简单，只是将score进行一个逆序排序即可，用到ORDER BY和DESC
第二个则比较难，不知道如何每行返回一个相应的树枝（注意同分数相同，并且不累加，就是1、1、2而不是1、1、3）

看一下网友题解：
> 最后的结果包含两个部分，第一部分是降序排列的分数，第二部分是每个分数对应的排名。    
第一部分不难写：    
```
select a.Score as Score
from Scores a
order by a.Score DESC
```
> 比较难的是第二部分。假设现在给你一个分数X，如何算出它的排名Rank呢？       
我们可以先提取出大于等于X的所有分数集合H，将H去重后的元素个数就是X的排名。比如你考了99分，但最高的就只有99分，那么去重之后集合H里就只有99一个元素，个数为1，因此你的Rank为1。      
先提取集合H：       
```
select b.Score from Scores b where b.Score >= X;
```
> 我们要的是集合H去重之后的元素个数，因此升级为：       
```
select count(distinct b.Score) from Scores b where b.Score >= X as Rank;
```
> 而从结果的角度来看，第二部分的Rank是对应第一部分的分数来的，所以这里的X就是上面的a.Score，把两部分结合在一起为：        
```
select a.Score as Score,
(select count(distinct b.Score) from Scores b where b.Score >= a.Score) as Rank
from Scores a
order by a.Score DESC
```

我的复现：
```
SELECT a.Score AS Score,
       (SELECT COUNT(DISTINCT b.Score) 
        FROM Scores AS b
        WHERE b.Score >= a.Score) AS 'Rank'
FROM Scores AS a
ORDER BY Score DESC;
```

注意RANK是一个关键词，所以不能直接作为别名，必须加上单引号
然后重点在于条件WHERE b.Score >= a.Score和聚集函数COUNT(DISTINCT b.Score) 的配合

相关知识点：
《MySQL必知必会》       
**使用WHERE子句**                 
只检索所需数据需要 指定搜索条件(search criteria)，搜索条件也称为过滤条件(filter condition)。       
在SELECT语句中，数据根据WHERE子句中指定的搜索条件进行过滤。 WHERE子句在表名(FROM子句)之后给出         
WHERE子句的位置：在同时使用ORDERBY和WHERE子句时，应 该让ORDER BY位于WHERE之后，否则将会产生错误          
WHERE子句的等于是 = 而不是 ==      

**COUNT()函数**         
COUNT()函数进行计数。可利用COUNT()确定表中行的数目或符合特定条件的行的数目。            
COUNT()函数有两种使用方式。          
  使用COUNT(\*)对表中行的数目进行计数，不管表列中包含的是空 值(NULL)还是非空值。       
  使用COUNT(column)对特定列中具有值的行进行计数，忽略 NULL值。         
NULL值：如果指定列名，则指定列的值为空的行被COUNT() 函数忽略，但如果COUNT()函数中用的是星号(\*)，则不忽 略。     
