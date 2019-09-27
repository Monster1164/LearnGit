## 实验一：SQL语句的执行计划分析与优化指导

## 1.教材中的查询语句分析

查询结果展示:
---
![查询1结果](./img/msg1.png)

![查询2结果](./img/msg2.png)
---

查询1比查询2的sql语句更优！

分析：having子句和group by一起使用，紧跟在group by的后边，having子句可以使用结果集中的列，也可以使用聚合函数(max、min、count、sum、svg)。having的作用是对分组后的结果进行过滤。where是先筛选之后再进行排序。所以在这个查询中 where的效率比having的效率更高！

- 优化指导：

![查询1结果](./img/youhua1.png)

![查询2结果](./img/youhua2.png)


- 查询1

    建议：考虑运行可以改进物理方案设计的访问指导或者创建推荐的索引。

    原理：创建推荐的索引可以显著地改进此语句的执行计划。但是, 使用典型的 SQL 工作量运行 "访问指导" 可能比单个语句更可取。通过这种方法可以获得全面的索引建议案,包括计算索引维护的开销和附加的空间消耗。

- 查询2无优化指导

### 自己的查询代码

```SQL
SELECT d.department_name,count(e.job_id)as "部门总人数",
avg(e.salary)as "平均工资"
from hr.departments d,hr.employees e
where d.department_id = e.department_id
and 
exists( 
select 1 from hr.departments where d.department_name='IT'
union all 
select 1 from hr.departments where d.department_name='Sales')
GROUP BY d.department_name;
```

- 查询结果：

![查询结果](./img/myself.png)


- 分析：通过使用EXISTS，Oracle会首先检查主查询，然后运行子查询直到它找到第一个匹配项，这就节省了时间。Oracle在执行IN子查询时，首先执行子查询，并将获得的结果列表存放在一个加了索引的临时表中。在执行子查询之前，系统先将主查询挂起，待子查询执行完毕，存放在临时表中以后再执行主查询。这也就是使用EXISTS比使用IN通常查询速度快的原因
