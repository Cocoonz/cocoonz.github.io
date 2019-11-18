---
title: 《数据库系统概念（第6版）》学习笔记
tags: 'computer science'
categories: 学习
---





# 数据库（2019 Fall）期中复习

### 概念部分

- （1.5）关系数据库基于关系模型，使用表的集合来表示数据以及数据之间的联系
- （2.1）在关系模型的术语中，**关系**（relation）用来指代表，**元组**（tuple）用来指代行，**属性**（attribute）指代表中的列，关系中的每个属性都存在一个允许取值的集合，称为该属性的**域**（domain），域中元素被看作是不可再分的单元，也就是域是**原子的**（atomic）。
- （2.1）**空**（null）值表示值未知或不存在。
- （2.2 / 2.7）**数据库模式**（database schema）是数据库的逻辑设计，包括属性，属性类型和关系上的约束，**数据库实例**（database instance）是给定时刻数据库中数据的一个快照。
- （2.2）关系的概念对应于程序设计语言中变量的概念，而**关系模式**（relation schema）的概念对应于类型定义的概念。关系模式由属性序列及个属性对应域组成。
- （2.2）**关系模式**（relation schema）

### Chapter 3 SQL

- （3.3.2）`select-from-where`语句的执行顺序：`from`→`where`→`select`

- （3.6）`null = null`返回`unknown`

- （3.7）五个聚集函数中，`avg`和`sum`的输入必须是数字集，其他的可以是字符串等

- （3.7.2）聚集函数中，没有出现在`group by`语句中的属性必须出现在聚集函数中

- （3.8.2）`= some`等价于`in`，`<> all`等价于`not in`

- （3.8.3）来自外层查询的一个相关名称可以用在内层查询中（**相关子查询**，correlated subquery）

  例如：内层查询使用了外层查询的相关名称 S

  ```sql
  select course_id
  from section as S
  where semester = 'Fall' and year = 2009 and
  	exists (select *
  		   from section as T
  		   where semester = 'Spring' and year 2010 and S.course_id = T.course_id)
  ```

- （3.8.3）`exists`空关系测试，参数子查询结果非空时返回`true`值

  错误示例：

  ```sql
  select dept_name, ID, sum(salary) /* dept_name出现在group by语句中，可以直接选择；但是ID没有出现在group by中，不能直接选择 */
  from instructor
  group by dept_name
  ```

- （3.8.3）“关系 A 包含关系 B”写作`not exists (B except A)`
- （3.7.3）含有`having`语句的执行顺序：`from`→`where`→`group by`→`having`→`select`

- ⭐习题：3.9、3.16、3.21

### Chapter 4 中级 SQL

- （4.1.3）连接类型和连接条件

- （4.2.1）试图关系在概念上包含查询结果中的元组，但不进行预计算和存储，**数据库系统存储与视图关系相关联的查询表达式**。当视图关系被访问时，其中的元组是通过计算查询结果而被创建出来的。

- （4.2.4）SQL 试图可更新的条件

  - `from`子句中只有一个数据库关系
  - `select`子句中只包含关系的属性名，不包含任何表达式、聚集或`distinct`声明
  - 任何没有出现在`select`子句中的属性可以取空值，这些属性上没有`not null`约束，也不构成主码的一部分
  - 查询中不含有`group by`或`having`子句

- （4.6.1）授权

  - 权限的授予和收回：`grant`和`revoke`

    ```sql
    grant <权限>
    on <关系>
    to <角色，用户>
    ```

  - SQL 可以对整个关系或一个关系的指定属性授予权限，但不允许对一个关系的指定元组授权

  -  用户 $\rm \pmb {public}$ 指系统的所有当前用户和将来的用户

  - （4.6.2）**角色**（role）：一个用户标识，用于区分一组用户，角色的授予也用`grant`语句

    ```sql
    grant dean to Amit /* 授予Amit角色dean */
    grant dean to instructor /* 把角色dean授予instructor，即instructor现在继承了dean的全部权限 */
    ```

    角色可以授予给用户，也可以授予给其他角色，因此，一个用户或一个角色的权限包括：

    - 所有直接授予用户/角色的权限
    - 所有授予给用户/角色所拥有角色的权限（角色链）

    基于角色的授权概念并没有在 SQL 中指定，但在很多共享应用中被广泛应用于存取控制

  - （4.6.5）权限的转移：`with grant option`

  - （4.6.6）权限的收回：默认情况下是级联收回（`cascade`），如果需要防止级联收回需要使用`restrict`（存在级联收回时事务回滚并返回错误）

    考虑权限由角色而非特定用户授予的情况，`granted by`语句



### Chapter 6 关系代数

本章介绍的是 SQL 所基于的形式化模型（同时它也是其他关系查询语言的基础）

#### 关系代数（过程化）

（6.1.1）**关系运算的结果自身也是一个关系**，所以我们可以把多个关系代数运算组合成一个关系代数表达式（relational-algebra expression）

基本运算：选择、投影、并、集合差、笛卡尔积、更名

附加运算：集合交、自然连接、赋值（可以用基本运算来改写）

- （6.1.1）基本运算

  - **选择**： $\sigma_{P} (E)$
  - **投影**：$\prod_{P}(E)$，$P$：投影列表，$E$：关系名

  - **并**：$\cup$

    并运算 $r \cup s$ 的条件：关系 $r$ 和 $s$ 必须是同元的，即属性（属性名、域）必须完全相同

  - **集合差**：$-$

  - **笛卡尔积**（Cartesian-product）：$\times$

    如果关系 $r$ 有 $n_1$ 个元组，关系 $s$ 有 $n_2$ 个元组，则 $r \times s$ 有 $n_1 \times n_2$ 个元组

  - **更名**：$\rho_x(E)$ 返回关系代数表达式 $E$ 的结果，并把名字 $x$ 赋给了它

    🌰：

    （6.1.1）查找大学里最高工资

    $\prod_{salary}(instructor)- \prod_{instructor.salary}(\sigma_{instructor.salary<d.salary}(instructor \times \rho_d(instructor)))$

    这里对 $instructor$ 关系使用了更名，第二个投影运算得出的关系是“找出所有非最高工资”

- （6.1.3）附加运算

  - **集合交**（intersection）：$\cap$

    $r\cap s= r - (r-s)$

  - （6.1.3）**自然连接**：$\Join$

    自然连接运算首先形成两个参数的笛卡尔积，然后基于两个关系模式都出现的属性上的相等性进行选择，最后还要去除重复属性

    ![image-20191027153414085](C:%5CUsers%5Caqyjz%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191027153414085.png)

    其中 $R \cap S = \{A_1,A_2,A_3,\dots,A_n\}$

    - 如果 $r$ 和 $s$ 不含任何相同属性，则自然连接结果等价于笛卡尔积结果，$r \Join s = r \times s$

    - 自然连接是可结合的，$(r \Join s) \Join t$ 和 $r \Join (s \Join t)$ 是等价的

  - **赋值**（assignment）：$\leftarrow$

  - **外连接**：⟕、⟖、⟗

  （6.1.4）扩展运算

  - 广义投影：允许在投影列表中使用算术运算和字符串函数

  - **聚集函数**（aggregate function）：𝒢

    一般形式：$_{G_1,G_2,\ldots,G_n}𝒢_{F_1(A_1),\ldots,F_m(A_m)}(E)$

    ​	其中：$G_1,G_2,\ldots,G_n$ 是分组依据的一组属性（`group by`的参数，分组的依据是同一组的所有元组在这组属性上取值相同，不同组的元组在这组属性上取值不同）

    ​			   $F_1(A_1),\ldots,F_m(A_m)$ 是一组聚集函数

      			$E$ 是关系

    - 去重，将 $distinct$ 加在函数名后面，如 $count-distinct$

      🌰：找出在2010年春天教课的教师总数

      $𝒢_{count-distinct(ID)}(\sigma_{semester='Spring',year=2010}(teaches))$

    - 分组聚集，模拟`group by`

      🌰：求出每个系的平均工资

      $ _{dept\_name}𝒢_{avg(salary)}(instructor) $

- 可以用 \$1，\$2，… 分别指代关系中的第一个属性、第二个属性等

#### 元组关系演算（非过程化）

一般形式：$ \{t \space | \space P(t) \}$$，其中 $ $t$ 是元组，$P$ 是公式，元组演算的公式由**原子**构成，这是一个递归的定义

![image-20191027164306446](C:%5CUsers%5Caqyjz%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20191027164306446.png)

因为是集合运算，结果中不包含重复元组

🌰：找出选了生物系所有课程的学生

$\{t \space | \space \exist r \in student (t[ID] = s[ID]) \and \\\forall u \in course(u[dept\_name] = 'Biology') \Rightarrow \\ \exist s \in takes(s[ID] = t[ID] \and s[course\_id] = u[course\_id]) \}$



#### 域关系演算（domain relational calculus）

非过程化

#### ⭐习题

6.1、6.4



### 完整性约束

- （1.4.2）**域约束**（见名词解释），最基本形式

- `primary key`：主码，非空且唯一

- `foreign key`

- 单个关系上的约束

  - （4.4.2）`not null`，主码不必显式地声明为`not null`
  - （4.4.3）`unique`

  ```SQL
  unique (A1, A2, ..., Am)
  ```

  声明这组属性构成一组候选码（候选码属性可以为$null$），即这组属性取值唯一

  - （4.4.4）`check`子句，理论上可以出现包含子查询的任意谓词，但目前没有被大多数数据库系统所实现

    复杂`check`条件在保持数据完整性上很有用，但是检测开销可能会很大

    例：

    大学数据库，一个约束：每个课程段都需要至少一位教师来讲授，有两种方案实现这个约束：

    1. 声明 $section$ 关系的属性集 $(course\_id, sec\_id, semester, year)$ 为外码，参照 $teaches$ 关系中的相应属性（事实上，这些属性并不是 $teaches$ 关系的主码）

    2. 使用包含子查询的`check`语句强制实现

       ```
       check ((course_id, sec_id, semester, year) =
       		(select course_id, sec_id, semester, year)
       		from teaches)
       ```

- （4.4.5）**参照完整性**（referential integrity）/ **子集依赖**（subset dependency）

  <font color="red">注意：参照完整性存在于两个关系（表）之间</font>
  
  - `foreign key`中可用的参照完整性约束
  
    - （习题4.9）`references`允许指向同一个关系
    
    - `on delete cascade`：级联删除
    
    - `on update cascade`：级联更新
    
      例：
    
      ```
      create table course
      	(...
      	foreign key (dept_name) references department
      		on delete cascade
      		on update cascade,
      	...)
      ```
    
      如果删除 $department$ 中的元组导致了此参照完整性约束被违反，则删除不被系统拒绝，而是对 $course$ 关系作“级联”删除，即删除参照了被删除系的元组。更新同理。
    
    - `set null`
    - `set default`
    
  - **断言**（assertion），域约束和参照完整性约束是断言的特殊形式
  
- ⭐习题4.12



```sql
create view tot_credits as
	(select year, sum(credits)
    from takes natural join course
    group by year)
```

3.21.b

```sql
select distinct m.name
from member m
where not exists
	((select isbn
      from book
      where publisher = 'McGraw-Hill')
     except
     (select isbn
     from borrowed b
     where b.memb_no = m.memb_no))
```



### 名词解释

- （1.3.1）**数据抽象**：数据库中用于表示数据的复杂的数据结构，包括物理层、逻辑层和视图层。
- （1.3.1）**物理数据独立性**：虽然逻辑层的简单结构的实现可能涉及复杂的物理层结构，但逻辑层的用户不必知道这样的复杂性。
- （1.4.2）DDL 的输出放在**数据字典**中，数据字典中包含了**元数据**（metadata）。元数据是关于数据的数据，数据字典可以看作是一种特殊的表，这种表只能由数据库系统本身（非常规用户）来访问和修改，在读取和修改实际的数据前，数据库系统先要参考数据字典。
- （1.4.2）**域约束**：每个属性都必须对应于一个所有可能取值构成的域（例如，整数型、字符型、日期/时间型）。声明一种属性属于某种具体的域就相当于约束它可以取的值。
- （2.3）**超码**（superkey）：一个或多个属性的集合，这些属性上的取值保证可以唯一识别出关系中的元组
- （2.3）**候选码**：最小的超码，它是超码的子集，但它的任何子集都不是超码
- （2.3）**主码**（primary key）：关系中的一个候选码，通常由人为指定
- （2.3）**外码**（foreign key）：一个属性集合，对于参照关系中的每个元组来说，它在外码属性上的取值肯定等于被参照关系中的某个元组在主码上的取值

- （4.2）**视图**（view）：不是逻辑模型的一部分，但作为虚关系对用户可见的关系。

- （4.3）事务（transaction）：由查询和（或）更新语句的序列组成。SQL 标准规定当一条 SQL 语句被执行，就隐式地开始了一个事务，语句`Commit work`和`Rollback work`会结束一个事务。

- （4.4.5）**参照完整性约束**（referential integrity constraint）/ **子集依赖**（subset dependency）：一个关系中的给定属性集上的取值也在另一关系的特定属性集的取值中出现。

- （1.4.2 / 4.4.7）**断言**（assertion）：一个数据库需要时刻满足的一个条件。

- （1.4.2）**授权**（authorization）：对于不同的用户在数据库的不同数据值上允许不同的访问类型。

- （4.6.1）**权限**（privilege）：包括  $\rm \pmb {select}$、 $\rm \pmb {insert}$、 $\rm \pmb {update}$、 $\rm \pmb {delete}$，一个创建了新关系的用户将自动被授予该关系上的所有权限

  

### 阙疑

3.10.e

3.14.a

4.1.a / 4.8.a：`count`，`group by`里面参数可以带多个属性？

6.1.4小节的最后一个🌰？

