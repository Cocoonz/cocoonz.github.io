---
title: 《数据库系统概念（第6版）》学习笔记
tags: concepts
category: 学习
---

## Chapter 1 导论

- （1.5）关系数据库基于关系模型，使用表的集合来表示数据以及数据之间的联系
- （2.1）在关系模型的术语中，**关系**（relation）用来指代表，**元组**（tuple）用来指代行，**属性**（attribute）指代表中的列，关系中的每个属性都存在一个允许取值的集合，称为该属性的**域**（domain），域中元素被看作是不可再分的单元，也就是域是**原子的**（atomic）。
- （2.1）**空**（null）值表示值未知或不存在。
- （2.2 / 2.7）**数据库模式**（database schema）是数据库的逻辑设计，包括属性，属性类型和关系上的约束，**数据库实例**（database instance）是给定时刻数据库中数据的一个快照。
- （2.2）关系的概念对应于程序设计语言中变量的概念，而**关系模式**（relation schema）的概念对应于类型定义的概念。关系模式由属性序列及个属性对应域组成。
- （2.2）**关系模式**（relation schema）



## Chapter 3 SQL

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



## Chapter 4 中级 SQL

- （4.1.3）连接类型和连接条件

### 视图

- 不是逻辑模式的一部分，但作为“虚关系”对用户可见的关系称为<font color="blue">视图</font>，视图提供了向用户隐藏特定数据的机制
- <font color="blue">视图定义会导致一个**表达式**被存储</font>
- 视图上可以进行的操作
  - 用视图**定义其他视图**
  - 视图更新，视图更新是有条件的：
    - `from`子句中只有一个数据库关系
    - `select`子句中只包含关系的属性名，不包含任何表达式、聚集或`distinct`声明
    - 任何没有出现在`select`子句中的属性可以取空值，这些属性上没有`not null`约束，也不构成主码的一部分
    - 查询中不含有`group by`或`having`子句

- <font color="blue">物化视图</font>：定义视图的实际关系改变，视图也跟着修改

### 完整性约束

<font color="blue">完整性约束</font>防止对数据的意外破坏，它保证授权用户对数据库所做的修改<font color="blue">不会破坏数据的一致性</font>

- `not null`
- `unique`
- `check` 子句
  - `check(P)` 使得关系中的每个元组都必须满足谓词 P，P 可以是包括子查询在内的任意谓词，但实现开销较大
- <font color="blue">参照完整性（子集依赖）</font>：保证一个关系中给定属性集上的取值在另一关系的特定属性集的取值中出现
- **外码可以为空（默认满足约束），候选码可以为空**

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



## Chapter 6 关系代数

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





## Chapter 7 数据库设计和 E-R 模型

### 数据库设计阶段

设计数据库模式、设计访问更新数据库的程序以及设计控制数据访问的安全模式

- 概念设计阶段：构建实体-联系图
- 逻辑设计阶段：将实体-联系图映射到关系模式
- 物理设计阶段：指明数据库文件组织格式和索引结构

### 实体-联系模型

实体-联系（E-R）模型是一种<font color="blue">语义</font>模型，在将现实世界事物的<font color="blue">含义</font>和相互<font color="blue">关联</font>映射到概念模式方面非常有用

一个数据库可以被建模为：实体的集合、实体间的联系

- 三个基本概念：<font color="blue">实体集、联系集、属性</font>
  - 实体集
    - <font color="blue">实体</font>：现实世界可<font color="blue">区别于</font>所有其他对象的一个“事物”或“对象”
    - <font color="blue">实体集</font>：相同类型的实体集合
    - 实体集的<font color="blue">外延</font>：属于实体集的集体的实际集合
    - 实体集的<font color="blue">属性</font>：实体集中每个成员所拥有的描述性性质，**是将实体集映射到域的函数**
  - 联系集
    - <font color="blue">联系</font>：多个实体间的相互关联
    - <font color="blue">联系集</font>：想同类型联系的集合
    - 联系集也可以有属性，🌰 instructor 和 student 之间的联系集 advisor 可以有属性 date 以表示教师成为学生的导师的日期
    - 实体的<font color="blue">角色</font>：实体在联系中扮演的功能（隐含，通常不指定）
    - 当同样的实体集参与一个联系集多于一次，这类联系集称作是<font color="blue">自环的</font>
    - <font color="blue">度</font>：参与联系集的实体集的数目
      - 事实上涉及两个实体集以上的联系是很少的
      - <font color="blue">二元联系集</font>：度为 2，数据库系统中大多数联系集都是二元的
  - 属性
    - 实体通过一组属性来表示，属性是实体集中每个成员所拥有的描述性性质
    - <font color="blue">域</font>：每个属性可取值的集合，也叫值域
    - 类型
      - 简单、复合
      - 单值、多值
      - 派生属性（不存储，需要时计算出来）、基属性

### 约束

- 映射基数约束

  - <font color="blue">映射基数</font>（基数比率）：一个实体通过一个联系集能关联的实体的个数

    描述二元联系集时取值为下列之一：

    - 一对一
    - 一对多
    - 多对一
    - 多对多

- 参与约束

  - 全部（total）参与：实体集 $E$ 中的每个实体都参与到联系集 $R$ 的至少一个联系中
  - 部分（partial）参与

- 码

  - 实体集的码
    - <font color="blue">超码</font>：一个或多个能唯一地标识实体的属性（集）
    - <font color="blue">候选码</font>：最小的超码 
    - <font color="blue">主码</font>：被选择用来区分实体的候选码，**主码只有一个，候选码可以有多个**
  - 联系集的码（看 PPT 写不清楚）
    - 相关的实体集的主码的集合形成了联系集的超码

### E-R 图（看 PPT）

- 矩形 -> 实体集，属性在实体矩阵中列出，构成主码的属性用下划线标注，菱形 -> 联系集

- 全部参与 -> 两条线

- 联系集的属性 -> 矩形，用虚线连接到联系集上

- 基数约束：从联系集指向实体集的箭头 -> 一，一条线段 -> 多（**注意多个包括 0 个**）

- 基数约束也可以用参与约束表示

   <img src="https://s2.ax1x.com/2020/01/06/lykcXn.png" alt="微信截图 20200106194910" style="zoom:67%;" /> 

- 复合、多值、派生属性的表示（PPT 上也没有？）

- <font color="blue">弱实体集</font>：一个没有足够的属性形成主码的实体集

  - 弱实体集依赖于标识实体集
  - 将弱实体集与其标识实体集相连的联系称为<font color="blue">标识性联系</font>（用双边框的菱形表示）
  - <font color="blue">部分码（分辨符）</font>：区分弱实体集中实体的属性集合（用虚下划线表示）
  - <font color="blue">弱实体集的主码</font>由<font color="blue">标识实体集的主码</font>和该弱实体集的部分码共同组成
  - 弱实体集可能与不止一个标识实体集关联

- 表示方法总结

   <img src="https://s2.ax1x.com/2020/01/06/lyV7cQ.png" alt="微信截图 20200106201125" style="zoom:67%;" />

   <img src="https://s2.ax1x.com/2020/01/06/lyVT1g.png" alt="微信截图 20200106194910" style="zoom:67%;" />  

  

### :star:转换为关系模式

- 能表示成 E-R 模式的一定能表示成关系模式
- 从强实体集转换而来的模式与强实体集具有**相同属性和主码**
- 从弱实体集转换而来的模式包含**弱实体集的属性和标识强实体集的主码**（需要添加相应的外码约束和完整性约束）
- （8.2）E-R 模型允许出现多值属性（一个人可以拥有多个电话号码）以及组合属性（地址分为城市、街道、门牌号），在转换为关系模式的时候，要消除这种子结构。对于多值属性，为每个值创建一个新的元组，对于组合属性，让每个子属性成为一个独立的属性。

### 扩展的 E-R 特性

- <font color="blue">自顶向下</font>的设计过程：从初始实体集到一系列不同层次的实体子集的细化
  - <font color="blue">特化</font>：在实体集内部进行分组的过程，用空心箭头表示
- <font color="blue">自底向上</font>设计过程：多个实体集根据共同具有的特征综合成一个较高层的实体集
  - <font color="blue">概化</font>：高层实体集与一个或多个低层实体集间的包含关系，在 E-R 图中的表示与特化相同，二者互为逆过程
- 高层和低层实体集可以形成<font color="blue">超类-子类</font>



## Chapter 8 关系数据库设计

### 基本概念

- （习题 8.16）**主属性**（prime attribute）：至少在一个候选码中出现的属性

- （8.4.3）<font color="blue">**无关属性**</font>（extraneous attribute）：如果去除函数依赖集中的一个属性不改变该函数依赖集的闭包，则这个属性是无关属性

- （8.3）字母表示法：

    - 小写希腊字母表示属性集（sets of attributes），如 $ \alpha$

    - 形如 $r(R)$ 表示属性集为 $R$ 的关系模式 $r$，小写字母表示关系模式（relation schema），大写字母表示关系模式包含的属性集（a set of attributes）

        $R$ 与小写希腊字母的区别是：**并不是所有属性集都能构成关系模式，小写希腊字母表示的范围更广。**

    - $K$ 表示超码

- （8.2）如果一个域的元组都是不可再分的，称这个域是**原子的**（atomic）。

### 核心概念

说明： $R$ 表示关系模式，$f,F$ 表示关系模式上的函数依赖集，$\alpha,\beta,\gamma,\delta$ 表示 $R$ 中的属性集，$A,B,C$ 表示单个属性

- 函数依赖

    - <font color="blue">**函数依赖**</font>（functional dependency），也称为相等产生依赖（equality-generating dependency）

        - 设 $r(R)$ 是一个关系模式，$\alpha \subseteq R$，$\beta \subseteq R$，模式 $R$ 上的函数依赖 $\alpha \to \beta$ 成立的条件是：**对于任意关系实例 $r$ 中任意两个元组 $t_1$ 和 $t_2$，$t_1[\alpha]=t_2[\alpha] \Rightarrow t_1[\beta]=t_2[\beta]$**
        - 如果**每个 $\alpha$ 的特定值至多对应一个 $\beta$**，称作 $\alpha$ 函数确定 $\beta$，或者 $\beta$ 函数依赖于 $\alpha$，记作 $\alpha \rightarrow \beta$ 
        - <font color="blue">部分函数依赖</font>：存在 $\alpha$ 的真子集 $\gamma$ 使得 $\gamma \to \beta$，则 $\alpha \rightarrow \beta$ 是部分依赖
        - 传递函数依赖：令 $\alpha$ 和 $\beta$ 为属性集，使得 $\alpha \rightarrow \beta$ 成立而 $\beta \rightarrow \alpha$ 不成立。令 $A$ 为一个属性，它既不属于 $\alpha$ 也不属于 $\beta$，并且 $\beta \rightarrow A$ 成立。则 $A$ **传递依赖**（transitive dependency）于 $\alpha$
        - 函数依赖是“码"的概化（generalization），能表达超码不能表达的约束
    - （8.3.1）如果一个函数依赖在所有关系上都满足，那么它是**<font color="blue">平凡的</font>**（trivial），**如果 $\beta \subset \alpha$，那么形如 $\alpha \rightarrow \beta$ 的函数依赖是平凡的**
    
- 码
    
        - <font color="blue">超码</font>：若一个或多个属性的集合 $\{A_1,A_2,\dots,A_n\}$ 函数确定该关系中其他全部属性，则称该属性为该关系的超码，即记 $K=\{A_1,A_2,\dots,A_n\}$，$K\to R$，则 $K$ 是关系模式 $R$ 的超码
    -  候选码、外码
    
- **闭包**（closure）
    
    - （8.3.1）**<font color="blue">函数依赖集的闭包</font>**：给定函数依赖集 $F$，我们使用 $F^+$ 表示**由 $F$ 推导出的所有函数依赖的集合**，称为 $F$ 的闭包
    
        - $F^+$ 是 $F$ 的超集
    
          🌰 $F = \{ A \rightarrow B, B \rightarrow C \}$, 我们可以推导出 $A \rightarrow C$
    
    - （8.4.2）**<font color="blue">属性集的闭包</font>**：函数依赖集 $F$ 下**被 $\alpha$ 函数确定的所有属性的集合**称为 $F$ 下 $\alpha$ 的闭包，记作 $\alpha^+$（$\alpha^+_F$，在下标指明函数依赖集）
    
        用途：
    
            - <font color="blue">判断超码</font>：若 $\alpha^+ = R$，则 $\alpha$ 是 $R$ 的一个超码
            - <font color="blue">验证函数依赖</font>：若 $\beta \subseteq \alpha^+$，则 $\alpha \rightarrow \beta$
        - <font color="blue">计算 $F$ 的闭包</font>：计算 $F^+$：对 $\forall \gamma \subseteq R$，找出 $\gamma^+$；对 $\forall S \subseteq \gamma^+$，函数依赖 $\gamma \rightarrow S$ 属于 $F^+$
    
    - （8.6.1）**函数依赖和多值依赖集的闭包**：函数依赖和多值依赖 $D$ 的闭包 $D^+$ 是由 $D$ 逻辑蕴含的所有函数依赖和多值依赖的集合
    
- （8.4.3）**正则覆盖**（canonical cover）：
    
        - $F$ 的正则覆盖 $F_c$ 是一个函数依赖集，并且：
            - $F$ 逻辑蕴含 $F_c$ 中的所有依赖
            - $F_c$ 逻辑蕴含 $F$ 中的所有函数依赖
            - $F_c$ 中任何函数依赖都**不含无关属性**
            - $F_c$ 中函数依赖的**左半部都是不同的**
        - <font color="blue">正则覆盖去除了冗余依赖</font>
    - <font color="blue">正则覆盖 $F_c$ 和 $F$ 具有相同的函数依赖集闭包</font>
    
- ⭐⭐**<font color="blue">Armstrong 公理</font>**（Armstrong's axiom）：用于寻找逻辑蕴含（logically imply）的函数依赖，反复应用 Armstrong 公理可以求函数依赖集 $F$ 的闭包 $F^+$
    
        Armstrong 公理是<font color="blue">有效的、完备的</font>
        
        - 自反律：若 $\beta \subseteq \alpha$，则 $\alpha \rightarrow \beta$
    - 增补律：若 $\alpha \rightarrow \beta$，则 $\gamma \alpha \rightarrow \gamma \beta$
    - 传递律：若 $\alpha \rightarrow \beta$ 且 $\beta \rightarrow \gamma$，则 $\alpha \rightarrow \gamma$
    
        其他一些规则：
        
        - 合并律：若 $\alpha \rightarrow \beta$ 且 $\alpha \rightarrow \gamma$，则 $\alpha \rightarrow \beta \gamma$
        - 分解律：若 $\alpha \rightarrow \beta \gamma$，则 $\alpha \rightarrow \beta$ 且 $\alpha \rightarrow \gamma$
        - 伪传递率：若 $\alpha \rightarrow \beta$ 且 $\gamma \beta \rightarrow \delta$，则 $\gamma \alpha \rightarrow \delta$
        
    - （8.6.1）**多值依赖**（multivalued dependency），也称为元组产生依赖（tuple generating dependency）
    
    - 记作 $\alpha \rightarrow\rightarrow \beta$
        - 多值依赖是说 $\alpha$ 和 $\beta$ 之间的联系独立于 $\alpha$ 和 $R-\beta$ 之间的联系
    
    - 性质：
            - 若 $\alpha \rightarrow \beta$，则 $\alpha \rightarrow\rightarrow \beta$，即每一个函数依赖都是一个多值依赖
            - 若 $\alpha \rightarrow \beta$，则 $\alpha \rightarrow\rightarrow R - \alpha - \beta$
        
    - （8.4.1）**<font color="blue">逻辑蕴含</font>**（logically imply）：如果每一个满足函数依赖集 $f$ 的实例都满足函数依赖集 $F$，则称 $f$ 被 $F$ 逻辑蕴含


- 规范化
  - 无数据冗余（符合一定范式）
  - 分解是无损连接分解
  - 最好分解是保持依赖的

- ⭐⭐范式（Normal Form）

    - 各范式之间的关系：
$$
        1NF \supset 2NF \supset 3NF \supset BCNF \supset 4NF \supset 5NF
$$

- （8.2）**第一范式**（1NF，First Normal Format）：所有属性的域都是<font color="blue">原子</font>的关系模式
  
        - 非原子的值会造成<font color="blue">复杂存储</font>和<font color="blue">数据冗余</font>
    
    - （习题 8.17）**<font color="blue">第二范式</font>**（2NF）：若关系模式 $R \in 1NF$，且在 $F^+$ 中<font color="blue">每一个非主属性**完全**函数依赖于候选码</font>，则 $R \in 2NF$  （**消除了 1NF 中的部分函数依赖**）
    
    - **<font color="blue">第三范式</font>**（3NF）

        - 若关系模式 $R \in 2NF$，且不存在属性函数依赖于其他非主属性（**消除了 2NF 中的传递函数依赖**）
    - （8.3.4）具有函数依赖集 $F$ 的关系模式 $R$ 属于 3NF 的条件是，对于 $F^+$ 中**所有**形如 $\alpha \rightarrow \beta$ 的函数依赖，以下至少有一项成立：
            - $\alpha \rightarrow \beta$ 是平凡的函数依赖（即 $\beta \subseteq \alpha$）
            - $\alpha$ 是模式的一个超码
            - $\beta - \alpha$ 中的每个属性 $A$ 都包含于 $R$ 的**某个**候选码中（对 BCNF 的放宽，**允许存在主属性对候选码的传递依赖和部分依赖**）
        - （习题 8.16）使用传递依赖重新定义 3NF：
            - **如果 $R$ 中不存在非主属性 $A$ 传递依赖或部分依赖于 $R$ 的一个候选码，则 $R$ 满足 3NF**

    - **Boyce-Codd 范式（BCNF）**
    
        - 具有函数依赖集 $F$ 的关系模式 $R$ 属于 BCNF 的条件是，对于 $F^+$ 中所有形如 $\alpha \rightarrow \beta$ 的函数依赖，以下至少有一项成立：
    
          - $\alpha \rightarrow \beta$ 是平凡的函数依赖（即 $\beta \subseteq \alpha$）
          - $\alpha$ 是模式的一个超码
    
        - BCNF 要求所有非平凡函数依赖都形如 $\alpha \rightarrow \beta$，其中 $\alpha$ 是超码
    
        - 任意**只有两个属性的关系模式都属于 BCNF**
    
        - 关系模式 $R$ 不属于 BCNF 的条件是，$F^+$ 中至少存在一个非平凡的形如 $\alpha \rightarrow \beta$ 的函数依赖，其中 $\alpha$ 不是 $R$ 的超码
    
        - <font color="blue">BCNF 排除了任何属性（包括主属性和非主属性）对候选码的**部分依赖和传递依赖**，也排除了主属性之间的**传递依赖**</font>
    
    - （8.6.2）第四范式（4NF）
    
        - 定义：对 $D^+$ 中所有形如 $\alpha \rightarrow\rightarrow \beta$ 的多值依赖，以下至少一项成立：
                - $\alpha \rightarrow\rightarrow \beta$ 是平凡的多值依赖
        - $\alpha$ 是一个超码
        - TBC
    
- （8.3.1）码和函数依赖

    假定下文讨论的关系均为 $r(R)$，并且 $\alpha \subset R$，$\beta \subset R$

    - （8.3.1）超码：如果函数依赖 $K \rightarrow R$ 在 $R$ 上成立，那么 $K$ 是 $r(R)$ 的超码

    - （PPT 11）候选码：如果 $K \rightarrow R$ 且不存在 $\alpha \subset K$，满足 $\alpha \rightarrow R$，那么 $K$ 是 $r(R)$ 的候选码

    - **函数依赖在关系模式上成立**：

        考虑一个关系模式 $r(R)$，$\alpha \subset R$，$\beta \subset R$

        - 给定 $r(R)$ 的一个实例，如果对于实例中的所有元组对 $t_1$，$t_2$，若 $t_1[\alpha]=t_2[\alpha]$，就有 $t_1[\beta]=t_2[\beta]$，那么这个实例满足函数依赖 $\alpha \rightarrow \beta$
        - 如果 $r(R)$ 的每个合法实例都满足函数依赖 $\alpha \rightarrow \beta$，那么函数依赖 $\alpha \rightarrow \beta$ 在关系模式 $r(R)$ 上成立
    
    
    
- **分解**

    - <font color="blue">有损分解</font>：无法通过**自然连接**重建原始关系元组的分解

    - （8.4.4）**无损分解**（lossless decomposition）：可以通过**自然连接**重建原始关系元组的分解。

        对于关系模式 $r(R)$，函数依赖集为 $F$。令 $R_1,R_2$ 为 $R$ 的分解。如果用 $r_1(R_1)$ 和 $r_2(R_2)$ 代替 $r(R)$ 没有信息损失，则这个分解是无损的。

        - 用关系代数说明无损分解：$\prod_{R_1}(r) \Join \prod_{R_2}(r) = r$
    
    - 用函数依赖说明无损分解：以下两个函数依赖至少有一个属于 $F^+$
    
        - $R_1 \cap R_2 \rightarrow R_1$
            -  $R_1 \cap R_2 \rightarrow R_2$

            换句话说，如果 $R_1 \cap R_2$ 是 $R_1$ 或者 $R_2$ 的超码，则这是一个无损分解。
    
    - （8.5.1）**BCNF 分解**：分解非 BCNF 的关系 $R$ 的一般规则是用以下两个模式取代 $R$
    
        - $(\alpha \cup \beta)$
        - $(R - (\beta - \alpha))$
    
        可能需要经过多次分解，**BCNF 分解是无损的，但通常不能保持依赖**（可能引入新的函数依赖关系）
    
    
    - （8.5.2）**3NF 分解**：将 $F$ 的正则覆盖 $F_c$ 中的每个式子转换成一个子关系模式，如果并未包含 $R$ 中所有的属性，再引入一个候选码（习题 8.29）**3NF 分解是无损、保持依赖的**



## Chapter 10 数据存取

- 文件组织

  - 数据库是以<font color="blue">一系列文件</font>的形式存储的。每个文件在逻辑上组织成为<font color="blue">记录的一个序列</font>

    - 每个文件分为定长的存储单元，称为<font color="blue">块</font>
    - **块是<font color="blue">存储分配</font>和<font color="blue">数据传输</font>的基本单元**，大小一般为 4-8 KB

  - :star:**如何在文件（块）中存储一条记录**？

    - <font color="blue">定长记录</font>
      - 空闲列表
    - <font color="blue">变长记录</font>
      - 应用场景
        - 多种记录类型存储在一个文件中
        - 允许一个或多个<font color="blue">字段是变长</font>的记录类型
        - 允许可重复字段的记录类型
      - 属性按顺序存储
      - 需要在记录首部以固定大小表示可变长度的属性（偏移量、长度）
      - 记录末尾需要<font color="blue">记录终止符</font>

  - :star:**文件中记录的组织**

    - <font color="blue">堆文件组织</font>：一个记录可以放在文件的任何地方，只要那个地方有空间存放这条记录
    - <font color="blue">顺序文件组织</font>：记录根据“<font color="blue">搜索码</font>”的值顺序存储
    - <font color="blue">散列文件组织</font>：在每条记录的某些属性上计算一个散列函数，散列函数的结果确定记录应放到文件的哪个块中

  - 存储访问

    - 数据库系统的一个主要目标就是<font color="blue">减少磁盘和存储器之间传输的块数</font>。

    - <font color="blue">缓冲区</font>：主存储器中用于存储磁盘块的副本的哪一部分

    - <font color="blue">缓冲区管理器</font>：负责缓冲区空间分配的子系统

      程序需要磁盘上的块时，可以向缓冲区管理器发出请求，

      - 如果这个块已经在缓冲区中，缓冲区管理器将这个块在主存储器中的地址传给请求者
      - 如果这个块不在缓冲区中，缓冲区管理器需要在缓冲区中为这个块分配空间（可能需要根据替换策略将其它块移出主存储器），再把这个块从磁盘读入缓冲区，并将这个块在主存储器中的地址传给请求者
      
    - 缓冲区替换策略
    
      - <font color="blue">最近最少使用策略（least recently used，LRU）</font>：根据过去使用块的模式进行将来访问模式的预测（在涉及对<font color="blue">对数据重复扫描的访问模式</font>时，LRU 是一个糟糕的策略）
  
  - 索引（看 PPT）
  
    - 概念
  
      - 索引机制用来加速对所需数据的访问
      - <font color="blue">搜索码</font>：用于在文件中查找记录的<font color="blue">属性或属性集</font>
      - 一个索引文件包含很多<font color="blue">索引项</font>（搜索码值 + 记录指针）
      - 索引文件一般比原文件小很多
  
    - <font color="blue">顺序索引</font>：按顺序存储搜索码的值，并将每个搜索码与包含该搜索码的记录关联起来
  
      - <font color="blue">主索引（聚集索引）</font>：顺序文件中，索引的搜索码指定了文件中记录的顺序
  
      - <font color="blue">辅助索引（非聚集索引）</font>：搜索码指定的顺序与文件中记录的物理顺序不同的索引（**必须是稠密的**）
  
      - <font color="blue">稠密索引</font>：文件中的**每个**搜索码值有一个索引记录
  
      - <font color="blue">稀疏索引</font>：只为搜索码的**某些值**建立索引记录
  
        稠密索引和系数索引对比
  
        - 稀疏索引插入和删除时所需的空间及维护开销较小
        - 系数索引定位一条记录的速度较慢
  
        折中方法：为每个块建立一个索引项（<font color="blue">块起始搜索码</font>）的稀疏索引
  
      - <font color="blue">多级索引</font>：将主索引以顺序文件的形式存放于磁盘
  
    - <font color="blue">散列索引</font>：使用散列函数将搜索码平均分布到若干散列桶中
      - <font color="blue">桶</font>是能存储一条或多条记录的一个存储单元（磁盘块）
      - 在散列文件组织中，通过使用散列函数直接从搜索码中获得包含该记录的桶
      - 散列函数 $h$ 是一个从 $K$ 到 $B$ 的函数，$K$ 表示所有搜索码值的集合，$B$ 表示所有桶地址的集合
      - 具有不同搜索码值的记录<font color="blue">可能映射到同一个桶</font>，因此整个桶要被顺序搜索来定位记录
      - 一个理想的散列函数是<font color="blue">均匀、随机</font>的，即每个桶分配<font color="blue">相同数量</font>的搜索码值
      - 最坏情况时散列函数把所有搜索码映射到同一桶中，此时<font color="blue">访问时间 $\propto$ 搜索码数量</font>
      - 通常散列函数依据<font color="blue">搜索码字符的二进制码和</font>来计算



## Chapter 12 事务管理

- 事务
  - 概念：<font color="blue">事务是构成单一逻辑工作单元的操作集合</font>
  - 事务由事务开始（`begin transaction`）与事务结束（`end transaction`）之间操作的全体操作组成
  - 事务管理主要处理两个主要问题：故障恢复、并发执行
  - SQL 标准规定事务的开始是<font color="blue">隐式</font>的
- ⭐**事务的 ACID 特性**
  - A（Atomicity，**原子性**）：事务的所有操作在数据库中要么全部反映出来，要么完全不反映
  - C（Consistency，**一致性**）：事务隔离执行时（即在没有其他事务并发执行的情况下）保持数据库的一致性
  - I（Isolation，**隔离性**）：尽管多个事务可能并发执行，但是每个事务都感觉不到系统中有其他事务在并发地执行
    - 事务处理系统通常允许多个事务并发执行，其优点是
      - <font color="blue">提高吞吐量和资源利用率</font>
      - <font color="blue">减少等待时间</font>
    - <font color="blue">并发控制机制</font>是实现事务隔离性的机制，它通过控制并发执行的事务之间的相互作用，来避免他们破坏数据库的一致性
  - D（Durability，**持久性**）：一个事务成功完成后，它对数据库的改变必须是永久的，即使系统出现故障时也是如此
- 可串行化
  - **调度**：指定并发执行事务的指令的<font color="blue">执行顺序</font>
    - 必须<font color="blue">保持指令在各个事务中出现的顺序</font>
    - 如果一个调度等价于一个串行调度，那么这个调度就是可串行化的
  - **冲突可串行化**
    - 调度：指定并发执行事务的指令的<font color="blue">执行顺序</font>
    - <font color="blue">冲突</font>：两条连续指令访问同一个数据项，且至少有一条指令执行了 `write` 操作
      - 两条指令之间的冲突迫使它们之间有一个逻辑时间顺序
      - 如果调度 $\rm{S}$ 可以通过一系列非冲突指令交换转换为 $\rm S'$，称 $\rm{S}$ 和 $\rm S'$ 是<font color="blue">冲突等价</font>的
      - 若一个调度 $\rm{S}$ 与一个可串行调度冲突等价，则称调度 $\rm{S'}$ 是<font color="blue">冲突可串行化</font>的 
  - 视图可串行化
- 可恢复性
  - **可恢复调度**：如果 $T_j$ 读取了先前由 $T_i$ 所写的数据项，则 $T_i$ 必须在 $T_j$ 之前提交。
  - **级联回滚**：因单个事务故障导致一系列事务回滚。级联回滚会导致撤销大量工作。
  - **无级联调度**：  对于每对事务 $T_i$ 和 $T_j$ ，如果 $T_j$ 读取了先前由 $T_i$ 所写的数据项，则 $T_i$ 必须在 $T_j$ 这一<font color="blue">读取操作前</font>提交。无级联调度不会发生级联回滚，也是可恢复调度。

- **并发控制**

  - 数据库必须保证所有的调度是<font color="blue">冲突可串行化</font>并且<font color="blue">可恢复</font>（最好是无级联）

  - SQL-92 中的所有并发级别都<font color="blue">不允许脏写</font>

  - :star:基于锁的协议

    锁是指加在数据项上的约束

    - **排他锁**（X）：对数据项既可写又可读，`lock-X`
    - **共享锁**（S）：对数据项只能读，`lock-S`
    - 释放锁：`unlock`
    - 发送<font color="blue">申请</font>锁请求给并发控制管理器，并发控制管理器<font color="blue">授予</font>事务所需的锁；如果一个锁不能被授予，那么请求该锁的事务必须<font color="blue">等待</font>直到该数据项上的其他不相容锁全部释放。
    - 锁相容性矩阵
      - 一个数据项可以同时拥有多个共享锁
      - 如果一个事务在某个数据项上拥有排他锁，那么其他事务不能再在这个数据项上加任何锁
    - 指令之间的冲突对应于锁类型之间的<font color="blue">不相容性</font>
    - 问题
      - 死锁（deadlock）处理：回滚并释放锁
      - 饿死（starved）

- ⭐封锁协议：<font color="blue">$T_i \to T_j$ 称为 $T_i$ 先于 $T_j$</font>，表示在任何等价的串行调度中，$T_i$ 必须出现在 $T_j$ 之前

- ⭐两阶段封锁协议

  - <font color="blue">增长阶段</font>：事务可以获得锁，不能释放锁
  - <font color="blue">缩减阶段</font>：事务可以释放锁，不能获得新锁
  - <font color="blue">封锁点</font>：在调度中该事务获得其最后加锁的位置（增长阶段结束点）
  - ⭐两阶段封锁协议可以<font color="blue">保证可串行化</font>（事务可以按照封锁点来串行化），但是<font color="blue">不能保证不发生死锁</font>，<font color="blue">很有可能发生级联回滚</font>
    - 解决：严格两阶段封锁协议
  - **严格两阶段封锁协议**：未提交事务所写的任何数据在该事务提交之前均以<font color="blue">排他方式</font>加锁。
  - **强两阶段封锁协议**：要求事务提交之前不得释放任何锁。本协议下事务可以按其提交的顺序串行化。

- 多粒度

  - <font color="blue">将多个数据项聚成一组</font>，作为同步单元，无需再单独对单个数据项进行加锁。
  - 允许各种大小的数据项，并定义数据粒度的<font color="blue">层次结构</font>，可以图形化地表示为树。
  - 如果一个事务显式地对树中的某个节点加了锁，那么它也给所有统一模式下的该节点的子节点<font color="blue">隐式</font>地加了锁。
  - 锁的粒度：
    - 细粒度（树的底层）：高并发，开销大（必须从根开始遍历到该节点）
    - 粗粒度（树的高层）：低并发，开销小

- :star:基于时间戳的协议：

  - 时间戳：一个事务 $T_i$ 的时间戳可理解为该事务进入系统的时间，可记作 $TS(T_i)$。一般使用<font color="blue">系统时钟/逻辑计数器</font>作为时间戳。
  - <font color="blue">事务的时间戳决定了串行化顺序</font>。
  - `W-timestamp(Q)` 表示成功执行 `write(Q)` 的所有事务的最大时间戳，`R-timestamp(Q)` 表示成功执行 `read(Q)` 的所有事务的最大时间戳
  - :star:时间戳排序协议保证任何有冲突的 `read` 或 `write` 操作按时间顺序执行，<font color="blue">保证冲突可串行化</font>、<font color="blue">无死锁</font>，但是<font color="blue">可能出现饿死和不可恢复的调度</font>（事务的可恢复性与事务提交顺序有关）

- :star:基于有效性检查的协议

  - 有效性检查协议要求每个事务在其生命周期中按两个或三个阶段<font color="blue">顺序</font>执行：

    - 读阶段
    - 有效性检查阶段（只有通过有效性检查阶段的事务才能进入下一阶段）
    - 写阶段（只读事务不进入此阶段）

    并发执行的事务的三个阶段可以是<font color="blue">交叉执行</font>的。

  - 并且每个事务都有三个不同的时间戳：
    - $\rm Start(T_i)$：事务开始执行的时间。
    - $\rm Validation(T_i)$：事务完成读阶段并开始有效性检查的时间，利用本时间戳的值可以通过时间戳排序技术决定可串行性顺序，以增加并发性，$\rm TS(T_i)=Validation(T_i)$。
    - $\rm Finish(T_i)$：事务完成写阶段的时间。
  - :star:事务的有效性检查：对于任何满足 $\rm TS(T_k)<TS(T_i)$  的事务 $\rm T_k$ 必须满足：
    - $\rm{Finish}(T_k) < Start(T_i)$
    - $\rm Start(T_i) < Finish(T_k) < Validation(T_i)$，并且<font color="blue"> $\rm T_k$ 所写的数据项集与 $\rm T_i$ 所写的数据项集不相交</font> 
    - <font color="blue">可以预防级联回滚，保证无死锁</font>

|              |     两阶段封锁     |                  时间戳                  |     有效性检查     |
| :----------: | :----------------: | :--------------------------------------: | :----------------: |
| 保证可串行化 | :heavy_check_mark: |            :heavy_check_mark:            |                    |
| 预防级联回滚 |        :x:         |                                          | :heavy_check_mark: |
|  保证无死锁  |        :x:         |            :heavy_check_mark:            | :heavy_check_mark: |
|     其他     |                    | 可能有事务饿死<br>可能产生不可恢复的调度 |                    |

- 恢复系统

  - 故障分类
    - 事务故障
    - 系统崩溃：硬件故障、数据库软件或操作系统的漏洞
    - 磁盘故障
  - 存储器类型：
    - <font color="blue">易失性存储器（volatile storage）</font>：主存、Cache
    - <font color="blue">非易失性存储器（nonvolatile storage）</font>：磁盘、磁带（<font color="blue">仍有可能丢失数据</font>）
    - <font color="blue">稳定存储器</font>
  - 数据访问
    - 概念
      - <font color="blue">物理块</font>是位于磁盘上的块
      - <font color="blue">缓冲块</font>是临时位于<font color="blue">主存</font>的块
      - 磁盘和主存间的块移动：$\rm input(B)$ 传送物理块 B 到主存，$\rm output(B)$ 传送缓冲块 B 到磁盘，并替换磁盘上对应的物理块
    - 过程
      - 每个事务 $\rm T_i$ 有一个<font color="blue">私有工作区</font>，用于保存 $\rm T_i$ 所访问及更新的所有数据项的拷贝，工作区中的每一个数据项记为 $\rm x_i$ 
      - 数据在工作区和系统缓冲区之间传递依赖两个操作：
        - $\rm read(X)$ 将数据项 $\rm X$ 的值赋予局部变量 $\rm x_i$
        - $\rm write(X)$ 将数据项 $\rm x_i$ 的值赋予局部变量 $\rm X$

  - 恢复算法：保证数据库一致性以及事务原子性的算法
    - 在<font color="blue">正常事务处理</font>时采取措施，保证有足够的信息可用于故障恢复（备份）
    - <font color="blue">故障发生后</font>采取措施，将数据库内容恢复到某个保证数据库一致性、事务原子性及持久性的状态（恢复）

  - :star:基于日志的恢复机制

    - <font color="blue">日志</font>是日志记录的序列，记录数据库中的所有更新活动，保存于稳定存储器中。
      - 事务开始：$\rm <T_i \ start>$
      - 事务执行 $\rm write(X)$ 操作：$\rm <T_i, \ X, \ V_1,\ V_2>$，其中 $\rm V_1$ 是旧值，$\rm V_2$ 是新值
      - 事务中止：$\rm <T_i \  abort>$
      - 事务提交：$\rm <T_i \ commit>$
    - 使用日志的两种方法：
      - 延迟的数据库修改：允许在事务提交前，将未提交的事务更新到缓冲区或磁盘
      - 立即的数据库修改：直到事务提交时都没有更新到缓冲区/磁盘

    - <font color="blue">日志记录的更新必须在数据项被 `write` 之前完成</font>

    - ⭐⭐$\rm Undo$ 和 $\rm Redo$

      - $\rm Undo$ 
        - 什么时候用？有日志 $\rm <T_i\ start>$ ，没有 $\rm <T_i\ commit>$ 和 $\rm <T_i\ abort>$
        - 完成后，日志记录 $\rm <T_i \ abort>$ 被写入
      - $\rm Redo$
        - 什么时候用？有日志 $\rm <T_i\ start>$，有 $\rm<T_i\ commit>$ 或 $\rm <T_i\ abort>$ 
        - <font color="blue">无需更新日志</font>
        - 已经将输出更新到数据库的事务没有必要 $\rm redo$
      - 一种特殊情况：如果事务 $\rm T_i$ 之前执行了 $\rm undo$ 操作，$\rm <T_i\ abort>$ 被写入到日志，接着故障发生。为了恢复，$\rm  T_i$ 要执行 $\rm redo$  操作。这样的话，重新执行了原先的所有操作，包括重新存储旧值。这样的情况称为<font color="blue">重复历史</font>。

    - :star::star:检查点

      - 流线型恢复过程周期性地执行<font color="blue">检查点</font>：
        - 将当前位于主存的所有日志记录输出到稳定存储器上
        - <font color="blue">将所有修改了的缓冲块输出到磁盘上</font>
        - 将一个日志记录 $\rm <checkpoint\ L>$ 输出到稳定存储器
      - 执行检查点时，<font color="blue">所有数据更新都停止</font>

      🌰

       <img src="https://s2.ax1x.com/2020/01/04/lwB9s0.png" alt="image 20200104094814445" style="zoom: 50%;" /> 

  -  :star:并发控制和恢复

    - 并发事务中，**所有事务共享一个磁盘缓冲区和日志**
    - 假如一个事务 $\rm T_i$ 修改了一个数据项，那么在 $\rm T_i$ 提交前，其他事务不能修改同一个数据项（即<font color="blue">不允许脏写</font>）



## Chapter X 大数据

- **5V 特性**：Variety、Velocity、Volume、Value、Veracity（真实性）

- Spark
- MapReduce：用于大数据分析的编程模型
- GFS (Google File System)：适用于大数据存储的分布式文件系统

技术上，MapReduce 基于 GFS 之上



## 概念押题

1. 什么是完整性约束？
2. 事务管理主要处理两个主要问题：故障恢复、并发执行 
3. 数据库的并发控制保证所有的调度是冲突可串行化、可恢复的
4. 检查点的作用是什么？
5. :star:5V 特性
6. :star:事务的 ACID 特性是原子性、持久性、一致性、隔离性
7. 块是 <u>存储分配</u> 和 <u>数据传输</u> 的基本单位
8. 数据库系统的一个主要目标是 <u>减少磁盘和存储器之间传输的块数（减少磁盘访问）</u>
9. 非原子的值会造成<u>复杂存储</u>和<u>数据冗余</u>
10. Armstrong 公理是<u>有效的</u>、<u>完备的</u>



<font color="blue"></font>