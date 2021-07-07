





### 三、SQL优化：主要优化索引

索引：index是帮助mysql高效获取数据的数据结构，索引是数据结构（B树、hash树）

**索引的弊端**：

1、索引本身很大，可以存放在内存、硬盘（通常为硬盘）

2、索引不是所有情况都适用（a:少量数据  b:频繁更新字段 c:很少使用的字段）

3、索引会降低增删改的效率

**索引的优势**：

1、提高查询效率

2、降低CPU使用率（排序的时候已经排好（B树，左边小，右边大））

**创建索引**

方式一：create 

create 索引类型 索引名 on 表 （字段）

create  index   dept_index on tb(dept)   单值索引

create unique index  name  name_index on tb(name) 唯一索引

create index  dept_name_index on tb(dept,name)  复合索引

方式二：alter table 

格式：alter table 表名 add 索引类型 索引名（字段）

alter table tb add index dept_index(dept)   单值索引

alter table tb add unique index   name_index (name) 唯一索引

alter table tb add  index dept_name_index (dept,name)  复合索引

**删除索引**

drop index索引名  on  表名

drop index name_index on tb

**查询索引**

show  index  form 表名

show index from tb;

### 四、SQL性能问题

a、分析SQL的执行计划：explain ，可以模拟SQL优化器执行sql 语句

b、Mysql 查询优化器会干扰我们的优化

优化方法：官网介绍https://dev.mysql.com/doc/refman/8.0/en/optimization.html

![image-20210426190023632](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210426190023632.png)

每次explain select  *   from  table （都会出现）

![image-20210426190841007](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210426190841007.png)

**type**:索引类型、类型

system>const>ep_ref>ref>range>index>all ,要对type优化的前提是有索引

system>const  是理想情况，一般达不到， 实际能达到就是ref>range

system:只有一条数据的系统表，或衍生表只有一条数据的主查询

eg:explain select * from (select * from test01) t where tid = 1;

const : 仅仅能查到一条数据的SQL，用户主键或唯一索引

eg:explain select tid from test01 where tid = 1;

ep_ref :唯一索引：对于每个索引建的查询，返回匹配唯一行数据（有且只有一个，不能多，不能0）

select —————form ————where i = __ 常见于唯一索引和主键索引

ref ：非唯一索引

range : 查找指定范围的行，where后面是一个between in > <  >=  （in有时候会失效）

index :查询索引中的数据

all：查询所有数据

**避免filesort**    Extra

单索引：where 哪些字段，就order by 哪些字段

复合索引：不能跨列，where 和order by 按照复合索引的顺序使用，不要跨列或无序使用

using filesort 和 using temporary：代表性能消耗大

using filesort 一般出现在order by 中

 using temporary  创建临时表，一般出现group by 中，已经有表了，但不使用.group by不是同一张表

from--- on ---join----- where ----group by----- having----- select dinstinct ------order by limit-------

如何避免using temporary ？ 

### 五、单表优化查询

博客园文章：罗昊

https://www.cnblogs.com/sweet-ice/articles/10507397.html



小结：a:索引不能跨列使用（最佳前缀）

b:索引逐步优化

c:将IN放到where  条件的最后，防止失效

USing where 需要会原表，Using index 不需要回原表

**双表优化**

双表查询的时候，关联字段应该小表驱动大表，所以应该给小表的字段加《索引》

也就是左外连接给左表加索引，右外连接给右表加索引

一般双表查询的where的条件 也会加索引



**三表优化**

同上：小表驱动大表，索引建立在经常查询的字段

**避免索引失效的一些原则**

（1）复合索引

a:复合索引，不要跨列或无序使用（最佳左前缀）

b:复合索引尽量使用全索引匹配

（2）不要在索引上进行任何操作（计算，函数，类型转换），否则索引失效in

（3）复合索引不等于用（！=  <> )或者is null(is not null) ,否则自身以及右侧全部失效（最佳左前缀）

（4）SQL优化是个概率层面，不是100%，因为本身SQL有优化器，会改变；需要用explain 查看

下图是复合索引中，第一个条件是<1 和=2 ，第一个失效，第二个有效

第一个条件是<4 和=2 ，第一个失效，第二个失效

![image-20210428214926986](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210428214926986.png)

补救：尽量使用索引覆盖（using index）也就是查什么，索引就是什么，select name ,name就是索引，后面name也作为条件

（5）like 尽量以常量开头，不要以‘%aa%’,否则索引失效

（6）类型转换，索引失效

（7）or 索引失效，左边的也失效；之前都是自身以及自身以后的失效（最佳左前缀）



### 六、优化方法

exist和in

主查询的数据集大，则使用in；非要用exist 也可以，不过这种情况in 效率高，下面同理

子查询的数据集大，则使用exist；

order by 优化

using filesort





数据库引擎：

![image-20210502113004003](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210502113004003.png)

MyISAM不支持事务，而InnoDB支持事务；
MyISAM支持表级锁，InnoDB支持行级锁；
MyISAM支持全文索引，而InnoDB不支持全文索引；
MyISAM支持管理非事务表，提高了全文检索的能力，如果业务上需要大量的select 请求，可以考虑使用MyISAM ，而InnoDB支持事务操作，适用于大量的update和insert操作。

![image-20210502113438794](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210502113438794.png)

![image-20210502113450273](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210502113450273.png)

![image-20210502113504834](C:\Users\CS_Chan\AppData\Roaming\Typora\typora-user-images\image-20210502113504834.png)



视图和存储过程

```sql
-- 创建视图
CREATE VIEW JJJM AS 
SELECT FIRST_NAME,SALARY FROM t_employees 
-- 使用视图
SELECT FIRST_NAME FROM JJJM;

-- 创建存储过程(无参数)
CREATE PROCEDURE ccs()
BEGIN 
		SELECT Avg(SALARY) as avgSalary FROM t_employees;
		END;
		
-- 	使用存储过程	
CALL ccs();

--使用有参数的存储过程
call ccs(@SALARYhight,
        @SALARYaverage,
        @SALARYlow);

creat procedure Productpricing(out SALARYhight decimal(8,2),
                              out SALARYaverage decimal(8,2),
                              out SALARYlow decimal(8,2))
begin 
	select Min(salary) into SALARYhight from t_employees;
	select Max(salary) into SALARYaverage from t_employees;
	select Avg(salary) into SALARYlow from t_employees;
End;
--使用有参数
select @SALARYhight; --查询SALARYhight
select @SALARYaverage;--查询SALARYaverage
select @SALARYlow;--查询SALARYlow
```

为什么使用存储过程？

（1）通过把处理封装在容易使用的的单元中，简化复杂的操作

（2）保证数据的完整性；如果所有开发人员和应用程序都使用同一（试验和测试）存储过程，则所使用的代码都是相同的。为了防止错误，保证数据的一致性。

（3）简化对变动的管理；

通过存储过程限制对基础数据的访问减少了数据讹误（无意识的或别的原因所导致的数据讹误）的机会

（4）提高性能。存储过程要比普通SQL快

总而言之：简单、安全、高性能

缺点：

（1）存储过程的编写比基本SQL语句复杂，编写存储过程

需要更高的技能，更丰富的经验

（2）你可能没有创建存储过程的安全访问权限。许多数据库管理员限制存储过程的创建权限，允许用户使用存储过程，但不允许他们创建存储过程



使用游标  

存储过程、游标、逐行处理以及存储过程调用其他存储过程的工作样例：

```sql
creat procedure processorders()
begin
	declare done boolean default 0;
	declare o int;
	
	declare ordernumbers cursor 
	for
	select salary from e_employees;
	
	declare continue handler for sqlstate '0200' set done = 1;
	open ordernumbers;
	
	repeat
		fetch ordernumbers into o;
	until done end repeat
	close ordernumbers;
end;
			
```



### 七、触发器

**1、创建触发器：**

唯一的触发器名；

触发器关联的表；

触发器应该响应的活动（DELETE、INSERT或UPDATE）；

触发器何时执行（处理之前或之后）

### 八、事务

rollback 回滚

```sql
-- 完整的事务
	SELECT * FROM t_employees;
	START TRANSACTION;
	DELETE FROM t_employees where EMPLOYEE_ID='207';
	SELECT * FROM t_employees;
	ROLLBACK;
	SELECT * FROM t_employees;
	commit;
```

commit 提交

