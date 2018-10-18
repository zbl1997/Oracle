Oracle 实验一
===
1.查询语句一
---

```
SELECT d.department_name ,count(e.job_id)as "部门总人数" ,
avg(e.salary)as "平均工资"
from hr.departments d ,hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT' ,'Sales')
GROUP BY department_name;
```

### 查询结果

> ![](https://github.com/zbl1997/Oracle/blob/master/Test1/Images/test1.png)

### 优化指导

> 为数据库创建一个或多个索引来改进当前语句的执行计划。

2.查询语句二
---
```
SELECT d.department_name ,count(e.job_id)as "部门总人数" ,
avg(e.salary)as "平均工资"
FROM hr.departments d ,hr.employees e
WHERE d.department_id = e.department_id
GROUP BY department_name
HAVING d.department_name in ('IT' ,'Sales');
```
### 查询结果
> ![](https://github.com/zbl1997/Oracle/blob/master/Test1/Images/laji1.png)
### 优化指导
> 无

3.自定义语句
---
```
SELECT d.department_name ,count(e.job_id)as "部门总人数" ,
avg(e.salary)as "平均工资"
FROM hr.departments d, hr.employees e
WHERE d.department_id = e.department_id
and d.department_name = 'IT' or d.department_name = 'Sales'
GROUP BY department_name 
```
### 查询结果
> ![](https://github.com/zbl1997/Oracle/blob/master/Test1/Images/test1.png)

优化指导
---
> 减少where语句处大量的笛卡尔积操作

总结
---
> 查询语句二最优，主要是减少语句查询时产生的大量的笛卡尔积。  
