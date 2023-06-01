# JavaWeb 学习笔记!



# 1.基础加强

	1. Junit单元测试
	2. 反射
	3. 注解

## Junit单元测试：

	* 测试分类：
		1. 黑盒测试：不需要写代码，给输入值，看程序是否能够输出期望的值。
		2. 白盒测试：需要写代码的。关注程序具体的执行流程。
	
	* Junit使用：白盒测试
		* 步骤：
			1. 定义一个测试类(测试用例)
				* 建议：
					* 测试类名：被测试的类名Test		CalculatorTest
					* 包名：xxx.xxx.xx.test		cn.itcast.test
	
			2. 定义测试方法：可以独立运行
				* 建议：
					* 方法名：test测试的方法名		testAdd()  
					* 返回值：void
					* 参数列表：空参
	
			3. 给方法加@Test
			4. 导入junit依赖环境
	
		* 判定结果：
			* 红色：失败
			* 绿色：成功
			* 一般我们会使用断言操作来处理结果
				* Assert.assertEquals(期望的结果,运算的结果);
	
		* 补充：
			* @Before:
				* 修饰的方法会在测试方法之前被自动执行
			* @After:
				* 修饰的方法会在测试方法执行之后自动被执行


## 反射：框架设计的灵魂

	* 框架：半成品软件。可以在框架的基础上进行软件开发，简化编码
	* 反射：将类的各个组成部分封装为其他对象，这就是反射机制
		* 好处：
			1. 可以在程序运行过程中，操作这些对象。
			2. 可以解耦，提高程序的可扩展性。


	* 获取Class对象的方式：
		1. Class.forName("全类名")：将字节码文件加载进内存，返回Class对象
			* 多用于配置文件，将类名定义在配置文件中。读取文件，加载类
		2. 类名.class：通过类名的属性class获取
			* 多用于参数的传递
		3. 对象.getClass()：getClass()方法在Object类中定义着。
			* 多用于对象的获取字节码的方式
	
		* 结论：
			同一个字节码文件(*.class)在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个。


	* Class对象功能：
		* 获取功能：
			1. 获取成员变量们
				* Field[] getFields() ：获取所有public修饰的成员变量
				* Field getField(String name)   获取指定名称的 public修饰的成员变量
	
				* Field[] getDeclaredFields()  获取所有的成员变量，不考虑修饰符
				* Field getDeclaredField(String name)  
			2. 获取构造方法们
				* Constructor<?>[] getConstructors()  
				* Constructor<T> getConstructor(类<?>... parameterTypes)  
	
				* Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)  
				* Constructor<?>[] getDeclaredConstructors()  
			3. 获取成员方法们：
				* Method[] getMethods()  
				* Method getMethod(String name, 类<?>... parameterTypes)  
	
				* Method[] getDeclaredMethods()  
				* Method getDeclaredMethod(String name, 类<?>... parameterTypes)  
	
			4. 获取全类名	
				* String getName()  


	* Field：成员变量
		* 操作：
			1. 设置值
				* void set(Object obj, Object value)  
			2. 获取值
				* get(Object obj) 
	
			3. 忽略访问权限修饰符的安全检查
				* setAccessible(true):暴力反射



	* Constructor:构造方法
		* 创建对象：
			* T newInstance(Object... initargs)  
	
			* 如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法


	* Method：方法对象
		* 执行方法：
			* Object invoke(Object obj, Object... args)  
	
		* 获取方法名称：
			* String getName:获取方法名


	* 案例：
		* 需求：写一个"框架"，不能改变该类的任何代码的前提下，可以帮我们创建任意类的对象，并且执行其中任意方法
			* 实现：
				1. 配置文件
				2. 反射
			* 步骤：
				1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中
				2. 在程序中加载读取配置文件
				3. 使用反射技术来加载类文件进内存
				4. 创建对象
				5. 执行方法


## 注解：

	* 概念：说明程序的。给计算机看的
	* 注释：用文字描述程序的。给程序员看的
	
	* 定义：注解（Annotation），也叫元数据。一种代码级别的说明。它是JDK1.5及以后版本引入的一个特性，与类、接口、枚举是在同一个层次。它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明，注释。
	* 概念描述：
		* JDK1.5之后的新特性
		* 说明程序的
		* 使用注解：@注解名称


​	

	* 作用分类：
		①编写文档：通过代码里标识的注解生成文档【生成文档doc文档】
		②代码分析：通过代码里标识的注解对代码进行分析【使用反射】
		③编译检查：通过代码里标识的注解让编译器能够实现基本的编译检查【Override】


	* JDK中预定义的一些注解
		* @Override	：检测被该注解标注的方法是否是继承自父类(接口)的
		* @Deprecated：该注解标注的内容，表示已过时
		* @SuppressWarnings：压制警告
			* 一般传递参数all  @SuppressWarnings("all")
	
	* 自定义注解
		* 格式：
			元注解
			public @interface 注解名称{
				属性列表;
			}
	
		* 本质：注解本质上就是一个接口，该接口默认继承Annotation接口
			* public interface MyAnno extends java.lang.annotation.Annotation {}
	
		* 属性：接口中的抽象方法
			* 要求：
				1. 属性的返回值类型有下列取值
					* 基本数据类型
					* String
					* 枚举
					* 注解
					* 以上类型的数组
	
				2. 定义了属性，在使用时需要给属性赋值
					1. 如果定义属性时，使用default关键字给属性默认初始化值，则使用注解时，可以不进行属性的赋值。
					2. 如果只有一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值即可。
					3. 数组赋值时，值使用{}包裹。如果数组中只有一个值，则{}可以省略
		
		* 元注解：用于描述注解的注解
			* @Target：描述注解能够作用的位置
				* ElementType取值：
					* TYPE：可以作用于类上
					* METHOD：可以作用于方法上
					* FIELD：可以作用于成员变量上
			* @Retention：描述注解被保留的阶段
				* @Retention(RetentionPolicy.RUNTIME)：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
			* @Documented：描述注解是否被抽取到api文档中
			* @Inherited：描述注解是否被子类继承


	* 在程序使用(解析)注解：获取注解中定义的属性值
		1. 获取注解定义的位置的对象  （Class，Method,Field）
		2. 获取指定的注解
			* getAnnotation(Class)
			//其实就是在内存中生成了一个该注解接口的子类实现对象
	
		            public class ProImpl implements Pro{
		                public String className(){
		                    return "cn.itcast.annotation.Demo1";
		                }
		                public String methodName(){
		                    return "show";
		                }
		            }
		3. 调用注解中的抽象方法获取配置的属性值


	* 案例：简单的测试框架
	* 小结：
		1. 以后大多数时候，我们会使用注解，而不是自定义注解
		2. 注解给谁用？
			1. 编译器
			2. 给解析程序用
		3. 注解不是程序的一部分，可以理解为注解就是一个标签



------



# 2.MySQL基础

1. 数据库的基本概念


2. MySQL数据库软件
    1. 安装
    2. 卸载
    3. 配置

3. SQL

## 数据库的基本概念

	1. 数据库的英文单词： DataBase 简称 ： DB
	2. 什么数据库？
		* 用于存储和管理数据的仓库。
	
	3. 数据库的特点：
		1. 持久化存储数据的。其实数据库就是一个文件系统
		2. 方便存储和管理数据
		3. 使用了统一的方式操作数据库 -- SQL


​	

	4. 常见的数据库软件
		* 参见《MySQL基础.pdf》

# MySQL数据库软件

	1. 安装
		* 参见《MySQL基础.pdf》
	2. 卸载
		1. 去mysql的安装目录找到my.ini文件
			* 复制 datadir="C:/ProgramData/MySQL/MySQL Server 5.5/Data/"
		2. 卸载MySQL
		3. 删除C:/ProgramData目录下的MySQL文件夹。
		
	3. 配置
		* MySQL服务启动
			1. 手动。
			2. cmd--> services.msc 打开服务的窗口
			3. 使用管理员打开cmd
				* net start mysql : 启动mysql的服务
				* net stop mysql:关闭mysql服务
		* MySQL登录
			1. mysql -uroot -p密码
			2. mysql -hip -uroot -p连接目标的密码
			3. mysql --host=ip --user=root --password=连接目标的密码
		* MySQL退出
			1. exit
			2. quit
	
		* MySQL目录结构
			1. MySQL安装目录：basedir="D:/develop/MySQL/"
				* 配置文件 my.ini
			2. MySQL数据目录：datadir="C:/ProgramData/MySQL/MySQL Server 5.5/Data/"
				* 几个概念
					* 数据库：文件夹
					* 表：文件
					* 数据：数据



# SQL

	1.什么是SQL？
		Structured Query Language：结构化查询语言
		其实就是定义了操作所有关系型数据库的规则。每一种数据库操作的方式存在不一样的地方，称为“方言”。
		
	2.SQL通用语法
		1. SQL 语句可以单行或多行书写，以分号结尾。
		2. 可使用空格和缩进来增强语句的可读性。
		3. MySQL 数据库的 SQL 语句不区分大小写，关键字建议使用大写。
		4. 3 种注释
			* 单行注释: -- 注释内容 或 # 注释内容(mysql 特有) 
			* 多行注释: /* 注释 */
		
	3. SQL分类
		1) DDL(Data Definition Language)数据定义语言
			用来定义数据库对象：数据库，表，列等。关键字：create, drop,alter 等
		2) DML(Data Manipulation Language)数据操作语言
			用来对数据库中表的数据进行增删改。关键字：insert, delete, update 等
		3) DQL(Data Query Language)数据查询语言
			用来查询数据库中表的记录(数据)。关键字：select, where 等
		4) DCL(Data Control Language)数据控制语言(了解)
			用来定义数据库的访问权限和安全级别，及创建用户。关键字：GRANT， REVOKE 等

## DDL:操作数据库、表

	1. 操作数据库：CRUD
		1. C(Create):创建
			* 创建数据库：
				* create database 数据库名称;
			* 创建数据库，判断不存在，再创建：
				* create database if not exists 数据库名称;
			* 创建数据库，并指定字符集
				* create database 数据库名称 character set 字符集名;
	
			* 练习： 创建db4数据库，判断是否存在，并制定字符集为gbk
				* create database if not exists db4 character set gbk;
		2. R(Retrieve)：查询
			* 查询所有数据库的名称:
				* show databases;
			* 查询某个数据库的字符集:查询某个数据库的创建语句
				* show create database 数据库名称;
		3. U(Update):修改
			* 修改数据库的字符集
				* alter database 数据库名称 character set 字符集名称;
		4. D(Delete):删除
			* 删除数据库
				* drop database 数据库名称;
			* 判断数据库存在，存在再删除
				* drop database if exists 数据库名称;
		5. 使用数据库
			* 查询当前正在使用的数据库名称
				* select database();
			* 使用数据库
				* use 数据库名称;


	2. 操作表
		1. C(Create):创建
			1. 语法：
				create table 表名(
					列名1 数据类型1,
					列名2 数据类型2,
					....
					列名n 数据类型n
				);
				* 注意：最后一列，不需要加逗号（,）
				* 数据库类型：
					1. int：整数类型
						* age int,
					2. double:小数类型
						* score double(5,2)
					3. date:日期，只包含年月日，yyyy-MM-dd
					4. datetime:日期，包含年月日时分秒	 yyyy-MM-dd HH:mm:ss
					5. timestamp:时间错类型	包含年月日时分秒	 yyyy-MM-dd HH:mm:ss	
						* 如果将来不给这个字段赋值，或赋值为null，则默认使用当前的系统时间，来自动赋值
	
					6. varchar：字符串
						* name varchar(20):姓名最大20个字符
						* zhangsan 8个字符  张三 2个字符


			* 创建表
				create table student(
					id int,
					name varchar(32),
					age int ,
					score double(4,1),
					birthday date,
					insert_time timestamp
				);
			* 复制表：
				* create table 表名 like 被复制的表名;	  	
		2. R(Retrieve)：查询
			* 查询某个数据库中所有的表名称
				* show tables;
			* 查询表结构
				* desc 表名;
		3. U(Update):修改
			1. 修改表名
				alter table 表名 rename to 新的表名;
			2. 修改表的字符集
				alter table 表名 character set 字符集名称;
			3. 添加一列
				alter table 表名 add 列名 数据类型;
			4. 修改列名称 类型
				alter table 表名 change 列名 新列别 新数据类型;
				alter table 表名 modify 列名 新数据类型;
			5. 删除列
				alter table 表名 drop 列名;
		4. D(Delete):删除
			* drop table 表名;
			* drop table  if exists 表名 ;

* 客户端图形化工具：SQLYog

## DML：增删改表中数据

	1. 添加数据：
		* 语法：
			* insert into 表名(列名1,列名2,...列名n) values(值1,值2,...值n);
		* 注意：
			1. 列名和值要一一对应。
			2. 如果表名后，不定义列名，则默认给所有列添加值
				insert into 表名 values(值1,值2,...值n);
			3. 除了数字类型，其他类型需要使用引号(单双都可以)引起来
	2. 删除数据：
		* 语法：
			* delete from 表名 [where 条件]
		* 注意：
			1. 如果不加条件，则删除表中所有记录。
			2. 如果要删除所有记录
				1. delete from 表名; -- 不推荐使用。有多少条记录就会执行多少次删除操作
				2. TRUNCATE TABLE 表名; -- 推荐使用，效率更高 先删除表，然后再创建一张一样的表。
	3. 修改数据：
		* 语法：
			* update 表名 set 列名1 = 值1, 列名2 = 值2,... [where 条件];
	
		* 注意：
			1. 如果不加任何条件，则会将表中所有记录全部修改。



## DQL：查询表中的记录

	* select * from 表名;
	
	1. 语法：
		select
			字段列表
		from
			表名列表
		where
			条件列表
		group by
			分组字段
		having
			分组之后的条件
		order by
			排序
		limit
			分页限定


	2. 基础查询
		1. 多个字段的查询
			select 字段名1，字段名2... from 表名；
			* 注意：
				* 如果查询所有字段，则可以使用*来替代字段列表。
		2. 去除重复：
			* distinct
		3. 计算列
			* 一般可以使用四则运算计算一些列的值。（一般只会进行数值型的计算）
			* ifnull(表达式1,表达式2)：null参与的运算，计算结果都为null
				* 表达式1：哪个字段需要判断是否为null
				* 如果该字段为null后的替换值。
		4. 起别名：
			* as：as也可以省略


	3. 条件查询
		1. where子句后跟条件
		2. 运算符
			* > 、< 、<= 、>= 、= 、<>
			* BETWEEN...AND  
			* IN( 集合) 
			* LIKE：模糊查询
				* 占位符：
					* _:单个任意字符
					* %：多个任意字符
			* IS NULL  
			* and  或 &&
			* or  或 || 
			* not  或 !
			
				-- 查询年龄大于20岁
	
				SELECT * FROM student WHERE age > 20;
				
				SELECT * FROM student WHERE age >= 20;
				
				-- 查询年龄等于20岁
				SELECT * FROM student WHERE age = 20;
				
				-- 查询年龄不等于20岁
				SELECT * FROM student WHERE age != 20;
				SELECT * FROM student WHERE age <> 20;
				
				-- 查询年龄大于等于20 小于等于30
				
				SELECT * FROM student WHERE age >= 20 &&  age <=30;
				SELECT * FROM student WHERE age >= 20 AND  age <=30;
				SELECT * FROM student WHERE age BETWEEN 20 AND 30;
				
				-- 查询年龄22岁，18岁，25岁的信息
				SELECT * FROM student WHERE age = 22 OR age = 18 OR age = 25
				SELECT * FROM student WHERE age IN (22,18,25);
				
				-- 查询英语成绩为null
				SELECT * FROM student WHERE english = NULL; -- 不对的。null值不能使用 = （!=） 判断
				
				SELECT * FROM student WHERE english IS NULL;
				
				-- 查询英语成绩不为null
				SELECT * FROM student WHERE english  IS NOT NULL;



				-- 查询姓马的有哪些？ like
				SELECT * FROM student WHERE NAME LIKE '马%';
				-- 查询姓名第二个字是化的人
				
				SELECT * FROM student WHERE NAME LIKE "_化%";
				
				-- 查询姓名是3个字的人
				SELECT * FROM student WHERE NAME LIKE '___';


​				

				-- 查询姓名中包含德的人
				SELECT * FROM student WHERE NAME LIKE '%德%';



​	

------





# 3.MySQL 约束

	1. DQL:查询语句
		1. 排序查询
		2. 聚合函数
		3. 分组查询
		4. 分页查询
	
	2. 约束
	3. 多表之间的关系
	4. 范式
	5. 数据库的备份和还原

# DQL:查询语句

	1. 排序查询
		* 语法：order by 子句
			* order by 排序字段1 排序方式1 ，  排序字段2 排序方式2...
	
		* 排序方式：
			* ASC：升序，默认的。
			* DESC：降序。
	
		* 注意：
			* 如果有多个排序条件，则当前边的条件值一样时，才会判断第二条件。


	2. 聚合函数：将一列数据作为一个整体，进行纵向的计算。
		1. count：计算个数
			1. 一般选择非空的列：主键
			2. count(*)
		2. max：计算最大值
		3. min：计算最小值
		4. sum：计算和
		5. avg：计算平均值


		* 注意：聚合函数的计算，排除null值。
			解决方案：
				1. 选择不包含非空的列进行计算
				2. IFNULL函数
	
	3. 分组查询:
		1. 语法：group by 分组字段；
		2. 注意：
			1. 分组之后查询的字段：分组字段、聚合函数
			2. where 和 having 的区别？
				1. where 在分组之前进行限定，如果不满足条件，则不参与分组。having在分组之后进行限定，如果不满足结果，则不会被查询出来
				2. where 后不可以跟聚合函数，having可以进行聚合函数的判断。
	
			-- 按照性别分组。分别查询男、女同学的平均分
	
			SELECT sex , AVG(math) FROM student GROUP BY sex;
			
			-- 按照性别分组。分别查询男、女同学的平均分,人数
			
			SELECT sex , AVG(math),COUNT(id) FROM student GROUP BY sex;
			
			--  按照性别分组。分别查询男、女同学的平均分,人数 要求：分数低于70分的人，不参与分组
			SELECT sex , AVG(math),COUNT(id) FROM student WHERE math > 70 GROUP BY sex;
			
			--  按照性别分组。分别查询男、女同学的平均分,人数 要求：分数低于70分的人，不参与分组,分组之后。人数要大于2个人
			SELECT sex , AVG(math),COUNT(id) FROM student WHERE math > 70 GROUP BY sex HAVING COUNT(id) > 2;
			
			SELECT sex , AVG(math),COUNT(id) 人数 FROM student WHERE math > 70 GROUP BY sex HAVING 人数 > 2;


​			

	4. 分页查询
		1. 语法：limit 开始的索引,每页查询的条数;
		2. 公式：开始的索引 = （当前的页码 - 1） * 每页显示的条数
			-- 每页显示3条记录 
	
			SELECT * FROM student LIMIT 0,3; -- 第1页
			
			SELECT * FROM student LIMIT 3,3; -- 第2页
			
			SELECT * FROM student LIMIT 6,3; -- 第3页
	
		3. limit 是一个MySQL"方言"


## 约束

	* 概念： 对表中的数据进行限定，保证数据的正确性、有效性和完整性。	
	* 分类：
		1. 主键约束：primary key
		2. 非空约束：not null
		3. 唯一约束：unique
		4. 外键约束：foreign key
	
	* 非空约束：not null，值不能为null
		1. 创建表时添加约束
			CREATE TABLE stu(
				id INT,
				NAME VARCHAR(20) NOT NULL -- name为非空
			);
		2. 创建表完后，添加非空约束
			ALTER TABLE stu MODIFY NAME VARCHAR(20) NOT NULL;
	
		3. 删除name的非空约束
			ALTER TABLE stu MODIFY NAME VARCHAR(20);


​	

	* 唯一约束：unique，值不能重复
		1. 创建表时，添加唯一约束
			CREATE TABLE stu(
				id INT,
				phone_number VARCHAR(20) UNIQUE -- 添加了唯一约束
			
			);
			* 注意mysql中，唯一约束限定的列的值可以有多个null


​		

		2. 删除唯一约束
		
			ALTER TABLE stu DROP INDEX phone_number;
		
		3. 在创建表后，添加唯一约束
			ALTER TABLE stu MODIFY phone_number VARCHAR(20) UNIQUE;
	
	* 主键约束：primary key。
		1. 注意：
			1. 含义：非空且唯一
			2. 一张表只能有一个字段为主键
			3. 主键就是表中记录的唯一标识
	
		2. 在创建表时，添加主键约束
			create table stu(
				id int primary key,-- 给id添加主键约束
				name varchar(20)
			);
	
		3. 删除主键
			-- 错误 alter table stu modify id int ;
			ALTER TABLE stu DROP PRIMARY KEY;
	
		4. 创建完表后，添加主键
			ALTER TABLE stu MODIFY id INT PRIMARY KEY;
	
		5. 自动增长：
			1.  概念：如果某一列是数值类型的，使用 auto_increment 可以来完成值得自动增长
	
			2. 在创建表时，添加主键约束，并且完成主键自增长
			create table stu(
				id int primary key auto_increment,-- 给id添加主键约束
				name varchar(20)
			);


​			

			3. 删除自动增长
			ALTER TABLE stu MODIFY id INT;
			4. 添加自动增长
			ALTER TABLE stu MODIFY id INT AUTO_INCREMENT;


	* 外键约束：foreign key,让表于表产生关系，从而保证数据的正确性。
		1. 在创建表时，可以添加外键
			* 语法：
				create table 表名(
					....
					外键列
					constraint 外键名称 foreign key (外键列名称) references 主表名称(主表列名称)
				);
	
		2. 删除外键
			ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;
	
		3. 创建表之后，添加外键
			ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称);


​		

		4. 级联操作
			1. 添加级联操作
				语法：ALTER TABLE 表名 ADD CONSTRAINT 外键名称 
						FOREIGN KEY (外键字段名称) REFERENCES 主表名称(主表列名称) ON UPDATE CASCADE ON DELETE CASCADE  ;
			2. 分类：
				1. 级联更新：ON UPDATE CASCADE 
				2. 级联删除：ON DELETE CASCADE 


## 数据库的设计

	1. 多表之间的关系
		1. 分类：
			1. 一对一(了解)：
				* 如：人和身份证
				* 分析：一个人只有一个身份证，一个身份证只能对应一个人
			2. 一对多(多对一)：
				* 如：部门和员工
				* 分析：一个部门有多个员工，一个员工只能对应一个部门
			3. 多对多：
				* 如：学生和课程
				* 分析：一个学生可以选择很多门课程，一个课程也可以被很多学生选择
		2. 实现关系：
			1. 一对多(多对一)：
				* 如：部门和员工
				* 实现方式：在多的一方建立外键，指向一的一方的主键。
			2. 多对多：
				* 如：学生和课程
				* 实现方式：多对多关系实现需要借助第三张中间表。中间表至少包含两个字段，这两个字段作为第三张表的外键，分别指向两张表的主键
			3. 一对一(了解)：
				* 如：人和身份证
				* 实现方式：一对一关系实现，可以在任意一方添加唯一外键指向另一方的主键。
	
		3. 案例
			-- 创建旅游线路分类表 tab_category
			-- cid 旅游线路分类主键，自动增长
			-- cname 旅游线路分类名称非空，唯一，字符串 100
			CREATE TABLE tab_category (
				cid INT PRIMARY KEY AUTO_INCREMENT,
				cname VARCHAR(100) NOT NULL UNIQUE
			);
			
			-- 创建旅游线路表 tab_route
			/*
			rid 旅游线路主键，自动增长
			rname 旅游线路名称非空，唯一，字符串 100
			price 价格
			rdate 上架时间，日期类型
			cid 外键，所属分类
			*/
			CREATE TABLE tab_route(
				rid INT PRIMARY KEY AUTO_INCREMENT,
				rname VARCHAR(100) NOT NULL UNIQUE,
				price DOUBLE,
				rdate DATE,
				cid INT,
				FOREIGN KEY (cid) REFERENCES tab_category(cid)
			);
			
			/*创建用户表 tab_user
			uid 用户主键，自增长
			username 用户名长度 100，唯一，非空
			password 密码长度 30，非空
			name 真实姓名长度 100
			birthday 生日
			sex 性别，定长字符串 1
			telephone 手机号，字符串 11
			email 邮箱，字符串长度 100
			*/
			CREATE TABLE tab_user (
				uid INT PRIMARY KEY AUTO_INCREMENT,
				username VARCHAR(100) UNIQUE NOT NULL,
				PASSWORD VARCHAR(30) NOT NULL,
				NAME VARCHAR(100),
				birthday DATE,
				sex CHAR(1) DEFAULT '男',
				telephone VARCHAR(11),
				email VARCHAR(100)
			);
			
			/*
			创建收藏表 tab_favorite
			rid 旅游线路 id，外键
			date 收藏时间
			uid 用户 id，外键
			rid 和 uid 不能重复，设置复合主键，同一个用户不能收藏同一个线路两次
			*/
			CREATE TABLE tab_favorite (
				rid INT, -- 线路id
				DATE DATETIME,
				uid INT, -- 用户id
				-- 创建复合主键
				PRIMARY KEY(rid,uid), -- 联合主键
				FOREIGN KEY (rid) REFERENCES tab_route(rid),
				FOREIGN KEY(uid) REFERENCES tab_user(uid)
			);


​		

	2. 数据库设计的范式
		* 概念：设计数据库时，需要遵循的一些规范。要遵循后边的范式要求，必须先遵循前边的所有范式要求
	
			设计关系数据库时，遵从不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的范式，各种范式呈递次规范，越高的范式数据库冗余越小。
			目前关系数据库有六种范式：第一范式（1NF）、第二范式（2NF）、第三范式（3NF）、巴斯-科德范式（BCNF）、第四范式(4NF）和第五范式（5NF，又称完美范式）。
	
		* 分类：
			1. 第一范式（1NF）：每一列都是不可分割的原子数据项
			2. 第二范式（2NF）：在1NF的基础上，非码属性必须完全依赖于码（在1NF基础上消除非主属性对主码的部分函数依赖）
				* 几个概念：
					1. 函数依赖：A-->B,如果通过A属性(属性组)的值，可以确定唯一B属性的值。则称B依赖于A
						例如：学号-->姓名。  （学号，课程名称） --> 分数
					2. 完全函数依赖：A-->B， 如果A是一个属性组，则B属性值得确定需要依赖于A属性组中所有的属性值。
						例如：（学号，课程名称） --> 分数
					3. 部分函数依赖：A-->B， 如果A是一个属性组，则B属性值得确定只需要依赖于A属性组中某一些值即可。
						例如：（学号，课程名称） -- > 姓名
					4. 传递函数依赖：A-->B, B -- >C . 如果通过A属性(属性组)的值，可以确定唯一B属性的值，在通过B属性（属性组）的值可以确定唯一C属性的值，则称 C 传递函数依赖于A
						例如：学号-->系名，系名-->系主任
					5. 码：如果在一张表中，一个属性或属性组，被其他所有属性所完全依赖，则称这个属性(属性组)为该表的码
						例如：该表中码为：（学号，课程名称）
						* 主属性：码属性组中的所有属性
						* 非主属性：除过码属性组的属性
						
			3. 第三范式（3NF）：在2NF基础上，任何非主属性不依赖于其它非主属性（在2NF基础上消除传递依赖）


## 数据库的备份和还原

	1. 命令行：
		* 语法：
			* 备份： mysqldump -u用户名 -p密码 数据库名称 > 保存的路径
			* 还原：
				1. 登录数据库
				2. 创建数据库
				3. 使用数据库
				4. 执行文件。source 文件路径
	2. 图形化工具：





------







# 4.MySQL 多表&事务

	1. 多表查询
	
	2. 事务
	
	3. DCL


## 多表查询：

	* 查询语法：
		select
			列名列表
		from
			表名列表
		where....
	* 准备sql
		# 创建部门表
		CREATE TABLE dept(
			id INT PRIMARY KEY AUTO_INCREMENT,
			NAME VARCHAR(20)
		);
		INSERT INTO dept (NAME) VALUES ('开发部'),('市场部'),('财务部');
		# 创建员工表
		CREATE TABLE emp (
			id INT PRIMARY KEY AUTO_INCREMENT,
			NAME VARCHAR(10),
			gender CHAR(1), -- 性别
			salary DOUBLE, -- 工资
			join_date DATE, -- 入职日期
			dept_id INT,
			FOREIGN KEY (dept_id) REFERENCES dept(id) -- 外键，关联部门表(部门表的主键)
		);
		INSERT INTO emp(NAME,gender,salary,join_date,dept_id) VALUES('孙悟空','男',7200,'2013-02-24',1);
		INSERT INTO emp(NAME,gender,salary,join_date,dept_id) VALUES('猪八戒','男',3600,'2010-12-02',2);
		INSERT INTO emp(NAME,gender,salary,join_date,dept_id) VALUES('唐僧','男',9000,'2008-08-08',2);
		INSERT INTO emp(NAME,gender,salary,join_date,dept_id) VALUES('白骨精','女',5000,'2015-10-07',3);
		INSERT INTO emp(NAME,gender,salary,join_date,dept_id) VALUES('蜘蛛精','女',4500,'2011-03-14',1);
	* 笛卡尔积：
		* 有两个集合A,B .取这两个集合的所有组成情况。
		* 要完成多表查询，需要消除无用的数据
	* 多表查询的分类：
		1. 内连接查询：
			1. 隐式内连接：使用where条件消除无用数据
				* 例子：
				-- 查询所有员工信息和对应的部门信息
	
				SELECT * FROM emp,dept WHERE emp.`dept_id` = dept.`id`;
				
				-- 查询员工表的名称，性别。部门表的名称
				SELECT emp.name,emp.gender,dept.name FROM emp,dept WHERE emp.`dept_id` = dept.`id`;
				
				SELECT 
					t1.name, -- 员工表的姓名
					t1.gender,-- 员工表的性别
					t2.name -- 部门表的名称
				FROM
					emp t1,
					dept t2
				WHERE 
					t1.`dept_id` = t2.`id`;


​	

			2. 显式内连接：
				* 语法： select 字段列表 from 表名1 [inner] join 表名2 on 条件
				* 例如：
					* SELECT * FROM emp INNER JOIN dept ON emp.`dept_id` = dept.`id`;	
					* SELECT * FROM emp JOIN dept ON emp.`dept_id` = dept.`id`;	
	
			3. 内连接查询：
				1. 从哪些表中查询数据
				2. 条件是什么
				3. 查询哪些字段
		2. 外链接查询：
			1. 左外连接：
				* 语法：select 字段列表 from 表1 left [outer] join 表2 on 条件；
				* 查询的是左表所有数据以及其交集部分。
				* 例子：
					-- 查询所有员工信息，如果员工有部门，则查询部门名称，没有部门，则不显示部门名称
					SELECT 	t1.*,t2.`name` FROM emp t1 LEFT JOIN dept t2 ON t1.`dept_id` = t2.`id`;
			2. 右外连接：
				* 语法：select 字段列表 from 表1 right [outer] join 表2 on 条件；
				* 查询的是右表所有数据以及其交集部分。
				* 例子：
					SELECT 	* FROM dept t2 RIGHT JOIN emp t1 ON t1.`dept_id` = t2.`id`;
		3. 子查询：
			* 概念：查询中嵌套查询，称嵌套查询为子查询。
				-- 查询工资最高的员工信息
				-- 1 查询最高的工资是多少 9000
				SELECT MAX(salary) FROM emp;
				
				-- 2 查询员工信息，并且工资等于9000的
				SELECT * FROM emp WHERE emp.`salary` = 9000;
				
				-- 一条sql就完成这个操作。子查询
				SELECT * FROM emp WHERE emp.`salary` = (SELECT MAX(salary) FROM emp);
	
			* 子查询不同情况
				1. 子查询的结果是单行单列的：
					* 子查询可以作为条件，使用运算符去判断。 运算符： > >= < <= =
					* 
					-- 查询员工工资小于平均工资的人
					SELECT * FROM emp WHERE emp.salary < (SELECT AVG(salary) FROM emp);
				2. 子查询的结果是多行单列的：
					* 子查询可以作为条件，使用运算符in来判断
					-- 查询'财务部'和'市场部'所有的员工信息
					SELECT id FROM dept WHERE NAME = '财务部' OR NAME = '市场部';
					SELECT * FROM emp WHERE dept_id = 3 OR dept_id = 2;
					-- 子查询
					SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept WHERE NAME = '财务部' OR NAME = '市场部');
	
				3. 子查询的结果是多行多列的：
					* 子查询可以作为一张虚拟表参与查询
					-- 查询员工入职日期是2011-11-11日之后的员工信息和部门信息
					-- 子查询
					SELECT * FROM dept t1 ,(SELECT * FROM emp WHERE emp.`join_date` > '2011-11-11') t2
					WHERE t1.id = t2.dept_id;
					
					-- 普通内连接
					SELECT * FROM emp t1,dept t2 WHERE t1.`dept_id` = t2.`id` AND t1.`join_date` >  '2011-11-11'
	
		* 多表查询练习
	
				-- 部门表
				CREATE TABLE dept (
				  id INT PRIMARY KEY PRIMARY KEY, -- 部门id
				  dname VARCHAR(50), -- 部门名称
				  loc VARCHAR(50) -- 部门所在地
				);
				
				-- 添加4个部门
				INSERT INTO dept(id,dname,loc) VALUES 
				(10,'教研部','北京'),
				(20,'学工部','上海'),
				(30,'销售部','广州'),
				(40,'财务部','深圳');


​				
​				

				-- 职务表，职务名称，职务描述
				CREATE TABLE job (
				  id INT PRIMARY KEY,
				  jname VARCHAR(20),
				  description VARCHAR(50)
				);
				
				-- 添加4个职务
				INSERT INTO job (id, jname, description) VALUES
				(1, '董事长', '管理整个公司，接单'),
				(2, '经理', '管理部门员工'),
				(3, '销售员', '向客人推销产品'),
				(4, '文员', '使用办公软件');


​				
​				

				-- 员工表
				CREATE TABLE emp (
				  id INT PRIMARY KEY, -- 员工id
				  ename VARCHAR(50), -- 员工姓名
				  job_id INT, -- 职务id
				  mgr INT , -- 上级领导
				  joindate DATE, -- 入职日期
				  salary DECIMAL(7,2), -- 工资
				  bonus DECIMAL(7,2), -- 奖金
				  dept_id INT, -- 所在部门编号
				  CONSTRAINT emp_jobid_ref_job_id_fk FOREIGN KEY (job_id) REFERENCES job (id),
				  CONSTRAINT emp_deptid_ref_dept_id_fk FOREIGN KEY (dept_id) REFERENCES dept (id)
				);
				
				-- 添加员工
				INSERT INTO emp(id,ename,job_id,mgr,joindate,salary,bonus,dept_id) VALUES 
				(1001,'孙悟空',4,1004,'2000-12-17','8000.00',NULL,20),
				(1002,'卢俊义',3,1006,'2001-02-20','16000.00','3000.00',30),
				(1003,'林冲',3,1006,'2001-02-22','12500.00','5000.00',30),
				(1004,'唐僧',2,1009,'2001-04-02','29750.00',NULL,20),
				(1005,'李逵',4,1006,'2001-09-28','12500.00','14000.00',30),
				(1006,'宋江',2,1009,'2001-05-01','28500.00',NULL,30),
				(1007,'刘备',2,1009,'2001-09-01','24500.00',NULL,10),
				(1008,'猪八戒',4,1004,'2007-04-19','30000.00',NULL,20),
				(1009,'罗贯中',1,NULL,'2001-11-17','50000.00',NULL,10),
				(1010,'吴用',3,1006,'2001-09-08','15000.00','0.00',30),
				(1011,'沙僧',4,1004,'2007-05-23','11000.00',NULL,20),
				(1012,'李逵',4,1006,'2001-12-03','9500.00',NULL,30),
				(1013,'小白龙',4,1004,'2001-12-03','30000.00',NULL,20),
				(1014,'关羽',4,1007,'2002-01-23','13000.00',NULL,10);


​				
​				

				-- 工资等级表
				CREATE TABLE salarygrade (
				  grade INT PRIMARY KEY,   -- 级别
				  losalary INT,  -- 最低工资
				  hisalary INT -- 最高工资
				);
				
				-- 添加5个工资等级
				INSERT INTO salarygrade(grade,losalary,hisalary) VALUES 
				(1,7000,12000),
				(2,12010,14000),
				(3,14010,20000),
				(4,20010,30000),
				(5,30010,99990);
				
				-- 需求：
				
				-- 1.查询所有员工信息。查询员工编号，员工姓名，工资，职务名称，职务描述
				/*
					分析：
						1.员工编号，员工姓名，工资，需要查询emp表  职务名称，职务描述 需要查询job表
						2.查询条件 emp.job_id = job.id
				
				*/
				SELECT 
					t1.`id`, -- 员工编号
					t1.`ename`, -- 员工姓名
					t1.`salary`,-- 工资
					t2.`jname`, -- 职务名称
					t2.`description` -- 职务描述
				FROM 
					emp t1, job t2
				WHERE 
					t1.`job_id` = t2.`id`;


​				
​				

				-- 2.查询员工编号，员工姓名，工资，职务名称，职务描述，部门名称，部门位置
				/*
					分析：
						1. 员工编号，员工姓名，工资 emp  职务名称，职务描述 job  部门名称，部门位置 dept
						2. 条件： emp.job_id = job.id and emp.dept_id = dept.id
				*/
				
				SELECT 
					t1.`id`, -- 员工编号
					t1.`ename`, -- 员工姓名
					t1.`salary`,-- 工资
					t2.`jname`, -- 职务名称
					t2.`description`, -- 职务描述
					t3.`dname`, -- 部门名称
					t3.`loc` -- 部门位置
				FROM 
					emp t1, job t2,dept t3
				WHERE 
					t1.`job_id` = t2.`id` AND t1.`dept_id` = t3.`id`;
				   
				-- 3.查询员工姓名，工资，工资等级
				/*
					分析：
						1.员工姓名，工资 emp  工资等级 salarygrade
						2.条件 emp.salary >= salarygrade.losalary and emp.salary <= salarygrade.hisalary
							emp.salary BETWEEN salarygrade.losalary and salarygrade.hisalary
				*/
				SELECT 
					t1.ename ,
					t1.`salary`,
					t2.*
				FROM emp t1, salarygrade t2
				WHERE t1.`salary` BETWEEN t2.`losalary` AND t2.`hisalary`;


​				
​				

				-- 4.查询员工姓名，工资，职务名称，职务描述，部门名称，部门位置，工资等级
				/*
					分析：
						1. 员工姓名，工资 emp ， 职务名称，职务描述 job 部门名称，部门位置，dept  工资等级 salarygrade
						2. 条件： emp.job_id = job.id and emp.dept_id = dept.id and emp.salary BETWEEN salarygrade.losalary and salarygrade.hisalary
							
				*/
				SELECT 
					t1.`ename`,
					t1.`salary`,
					t2.`jname`,
					t2.`description`,
					t3.`dname`,
					t3.`loc`,
					t4.`grade`
				FROM 
					emp t1,job t2,dept t3,salarygrade t4
				WHERE 
					t1.`job_id` = t2.`id` 
					AND t1.`dept_id` = t3.`id`
					AND t1.`salary` BETWEEN t4.`losalary` AND t4.`hisalary`;


​				
​				

				-- 5.查询出部门编号、部门名称、部门位置、部门人数
				
				/*
					分析：
						1.部门编号、部门名称、部门位置 dept 表。 部门人数 emp表
						2.使用分组查询。按照emp.dept_id完成分组，查询count(id)
						3.使用子查询将第2步的查询结果和dept表进行关联查询
						
				*/
				SELECT 
					t1.`id`,t1.`dname`,t1.`loc` , t2.total
				FROM 
					dept t1,
					(SELECT
						dept_id,COUNT(id) total
					FROM 
						emp
					GROUP BY dept_id) t2
				WHERE t1.`id` = t2.dept_id;


​				

				-- 6.查询所有员工的姓名及其直接上级的姓名,没有领导的员工也需要查询
				
				/*
					分析：
						1.姓名 emp， 直接上级的姓名 emp
							* emp表的id 和 mgr 是自关联
						2.条件 emp.id = emp.mgr
						3.查询左表的所有数据，和 交集数据
							* 使用左外连接查询
					
				*/
				/*
				select
					t1.ename,
					t1.mgr,
					t2.`id`,
					t2.ename
				from emp t1, emp t2
				where t1.mgr = t2.`id`;
				
				*/
				
				SELECT 
					t1.ename,
					t1.mgr,
					t2.`id`,
					t2.`ename`
				FROM emp t1
				LEFT JOIN emp t2
				ON t1.`mgr` = t2.`id`;


## 事务

	1. 事务的基本介绍
		1. 概念：
			*  如果一个包含多个步骤的业务操作，被事务管理，那么这些操作要么同时成功，要么同时失败。
			
		2. 操作：
			1. 开启事务： start transaction;
			2. 回滚：rollback;
			3. 提交：commit;
		3. 例子：
			CREATE TABLE account (
				id INT PRIMARY KEY AUTO_INCREMENT,
				NAME VARCHAR(10),
				balance DOUBLE
			);
			-- 添加数据
			INSERT INTO account (NAME, balance) VALUES ('zhangsan', 1000), ('lisi', 1000);


​			

			SELECT * FROM account;
			UPDATE account SET balance = 1000;
			-- 张三给李四转账 500 元
			
			-- 0. 开启事务
			START TRANSACTION;
			-- 1. 张三账户 -500
			
			UPDATE account SET balance = balance - 500 WHERE NAME = 'zhangsan';
			-- 2. 李四账户 +500
			-- 出错了...
			UPDATE account SET balance = balance + 500 WHERE NAME = 'lisi';
			
			-- 发现执行没有问题，提交事务
			COMMIT;
			
			-- 发现出问题了，回滚事务
			ROLLBACK;
		4. MySQL数据库中事务默认自动提交
			
			* 事务提交的两种方式：
				* 自动提交：
					* mysql就是自动提交的
					* 一条DML(增删改)语句会自动提交一次事务。
				* 手动提交：
					* Oracle 数据库默认是手动提交事务
					* 需要先开启事务，再提交
			* 修改事务的默认提交方式：
				* 查看事务的默认提交方式：SELECT @@autocommit; -- 1 代表自动提交  0 代表手动提交
				* 修改默认提交方式： set @@autocommit = 0;


	2. 事务的四大特征：
		1. 原子性：是不可分割的最小操作单位，要么同时成功，要么同时失败。
		2. 持久性：当事务提交或回滚后，数据库会持久化的保存数据。
		3. 隔离性：多个事务之间。相互独立。
		4. 一致性：事务操作前后，数据总量不变
	3. 事务的隔离级别（了解）
		* 概念：多个事务之间隔离的，相互独立的。但是如果多个事务操作同一批数据，则会引发一些问题，设置不同的隔离级别就可以解决这些问题。
		* 存在问题：
			1. 脏读：一个事务，读取到另一个事务中没有提交的数据
			2. 不可重复读(虚读)：在同一个事务中，两次读取到的数据不一样。
			3. 幻读：一个事务操作(DML)数据表中所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改。
		* 隔离级别：
			1. read uncommitted：读未提交
				* 产生的问题：脏读、不可重复读、幻读
			2. read committed：读已提交 （Oracle）
				* 产生的问题：不可重复读、幻读
			3. repeatable read：可重复读 （MySQL默认）
				* 产生的问题：幻读
			4. serializable：串行化
				* 可以解决所有的问题
	
			* 注意：隔离级别从小到大安全性越来越高，但是效率越来越低
			* 数据库查询隔离级别：
				* select @@tx_isolation;
			* 数据库设置隔离级别：
				* set global transaction isolation level  级别字符串;
	
		* 演示：
			set global transaction isolation level read uncommitted;
			start transaction;
			-- 转账操作
			update account set balance = balance - 500 where id = 1;
			update account set balance = balance + 500 where id = 2;



## DCL：

	* SQL分类：
		1. DDL：操作数据库和表
		2. DML：增删改表中数据
		3. DQL：查询表中数据
		4. DCL：管理用户，授权
	
	* DBA：数据库管理员
	
	* DCL：管理用户，授权
		1. 管理用户
			1. 添加用户：
				* 语法：CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
			2. 删除用户：
				* 语法：DROP USER '用户名'@'主机名';
			3. 修改用户密码：
				
				UPDATE USER SET PASSWORD = PASSWORD('新密码') WHERE USER = '用户名';
				UPDATE USER SET PASSWORD = PASSWORD('abc') WHERE USER = 'lisi';
				
				SET PASSWORD FOR '用户名'@'主机名' = PASSWORD('新密码');
				SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123');
	
				* mysql中忘记了root用户的密码？
					1. cmd -- > net stop mysql 停止mysql服务
						* 需要管理员运行该cmd
	
					2. 使用无验证方式启动mysql服务： mysqld --skip-grant-tables
					3. 打开新的cmd窗口,直接输入mysql命令，敲回车。就可以登录成功
					4. use mysql;
					5. update user set password = password('你的新密码') where user = 'root';
					6. 关闭两个窗口
					7. 打开任务管理器，手动结束mysqld.exe 的进程
					8. 启动mysql服务
					9. 使用新密码登录。
			4. 查询用户：
				-- 1. 切换到mysql数据库
				USE myql;
				-- 2. 查询user表
				SELECT * FROM USER;
				
				* 通配符： % 表示可以在任意主机使用用户登录数据库
	
		2. 权限管理：
			1. 查询权限：
				-- 查询权限
				SHOW GRANTS FOR '用户名'@'主机名';
				SHOW GRANTS FOR 'lisi'@'%';
	
			2. 授予权限：
				-- 授予权限
				grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
				-- 给张三用户授予所有权限，在任意数据库任意表上
				
				GRANT ALL ON *.* TO 'zhangsan'@'localhost';
			3. 撤销权限：
				-- 撤销权限：
				revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';
				REVOKE UPDATE ON db3.`account` FROM 'lisi'@'%';



------





# 5.JDBC 本质

	1. JDBC基本概念
	2. 快速入门
	3. 对JDBC中各个接口和类详解

## JDBC：

	1. 概念：Java DataBase Connectivity  Java 数据库连接， Java语言操作数据库
		* JDBC本质：其实是官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类。
	
	2. 快速入门：
		* 步骤：
			1. 导入驱动jar包 mysql-connector-java-5.1.37-bin.jar
				1.复制mysql-connector-java-5.1.37-bin.jar到项目的libs目录下
				2.右键-->Add As Library
			2. 注册驱动
			3. 获取数据库连接对象 Connection
			4. 定义sql
			5. 获取执行sql语句的对象 Statement
			6. 执行sql，接受返回结果
			7. 处理结果
			8. 释放资源
	
		* 代码实现：
		  	//1. 导入驱动jar包
	        //2.注册驱动
	        Class.forName("com.mysql.jdbc.Driver");
	        //3.获取数据库连接对象
	        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db3", "root", "root");
	        //4.定义sql语句
	        String sql = "update account set balance = 500 where id = 1";
	        //5.获取执行sql的对象 Statement
	        Statement stmt = conn.createStatement();
	        //6.执行sql
	        int count = stmt.executeUpdate(sql);
	        //7.处理结果
	        System.out.println(count);
	        //8.释放资源
	        stmt.close();
	        conn.close();
	
	3. 详解各个对象：
		1. DriverManager：驱动管理对象
			* 功能：
				1. 注册驱动：告诉程序该使用哪一个数据库驱动jar
					static void registerDriver(Driver driver) :注册与给定的驱动程序 DriverManager 。 
					写代码使用：  Class.forName("com.mysql.jdbc.Driver");
					通过查看源码发现：在com.mysql.jdbc.Driver类中存在静态代码块
					 static {
					        try {
					            java.sql.DriverManager.registerDriver(new Driver());
					        } catch (SQLException E) {
					            throw new RuntimeException("Can't register driver!");
					        }
						}
	
					注意：mysql5之后的驱动jar包可以省略注册驱动的步骤。
				2. 获取数据库连接：
					* 方法：static Connection getConnection(String url, String user, String password) 
					* 参数：
						* url：指定连接的路径
							* 语法：jdbc:mysql://ip地址(域名):端口号/数据库名称
							* 例子：jdbc:mysql://localhost:3306/db3
							* 细节：如果连接的是本机mysql服务器，并且mysql服务默认端口是3306，则url可以简写为：jdbc:mysql:///数据库名称
						* user：用户名
						* password：密码 
		2. Connection：数据库连接对象
			1. 功能：
				1. 获取执行sql 的对象
					* Statement createStatement()
					* PreparedStatement prepareStatement(String sql)  
				2. 管理事务：
					* 开启事务：setAutoCommit(boolean autoCommit) ：调用该方法设置参数为false，即开启事务
					* 提交事务：commit() 
					* 回滚事务：rollback() 
		3. Statement：执行sql的对象
			1. 执行sql
				1. boolean execute(String sql) ：可以执行任意的sql 了解 
				2. int executeUpdate(String sql) ：执行DML（insert、update、delete）语句、DDL(create，alter、drop)语句
					* 返回值：影响的行数，可以通过这个影响的行数判断DML语句是否执行成功 返回值>0的则执行成功，反之，则失败。
				3. ResultSet executeQuery(String sql)  ：执行DQL（select)语句
			2. 练习：
				1. account表 添加一条记录
				2. account表 修改记录
				3. account表 删除一条记录
	
				代码：
					Statement stmt = null;
			        Connection conn = null;
			        try {
			            //1. 注册驱动
			            Class.forName("com.mysql.jdbc.Driver");
			            //2. 定义sql
			            String sql = "insert into account values(null,'王五',3000)";
			            //3.获取Connection对象
			            conn = DriverManager.getConnection("jdbc:mysql:///db3", "root", "root");
			            //4.获取执行sql的对象 Statement
			            stmt = conn.createStatement();
			            //5.执行sql
			            int count = stmt.executeUpdate(sql);//影响的行数
			            //6.处理结果
			            System.out.println(count);
			            if(count > 0){
			                System.out.println("添加成功！");
			            }else{
			                System.out.println("添加失败！");
			            }
			
			        } catch (ClassNotFoundException e) {
			            e.printStackTrace();
			        } catch (SQLException e) {
			            e.printStackTrace();
			        }finally {
			            //stmt.close();
			            //7. 释放资源
			            //避免空指针异常
			            if(stmt != null){
			                try {
			                    stmt.close();
			                } catch (SQLException e) {
			                    e.printStackTrace();
			                }
			            }
			
			            if(conn != null){
			                try {
			                    conn.close();
			                } catch (SQLException e) {
			                    e.printStackTrace();
			                }
			            }
			        }
				
		4. ResultSet：结果集对象,封装查询结果
			* boolean next(): 游标向下移动一行，判断当前行是否是最后一行末尾(是否有数据)，如果是，则返回false，如果不是则返回true
			* getXxx(参数):获取数据
				* Xxx：代表数据类型   如： int getInt() ,	String getString()
				* 参数：
					1. int：代表列的编号,从1开始   如： getString(1)
					2. String：代表列名称。 如： getDouble("balance")
			
			* 注意：
				* 使用步骤：
					1. 游标向下移动一行
					2. 判断是否有数据
					3. 获取数据
	
				   //循环判断游标是否是最后一行末尾。
		            while(rs.next()){
		                //获取数据
		                //6.2 获取数据
		                int id = rs.getInt(1);
		                String name = rs.getString("name");
		                double balance = rs.getDouble(3);
		
		                System.out.println(id + "---" + name + "---" + balance);
		            }
	
			* 练习：
				* 定义一个方法，查询emp表的数据将其封装为对象，然后装载集合，返回。
					1. 定义Emp类
					2. 定义方法 public List<Emp> findAll(){}
					3. 实现方法 select * from emp;
						
		5. PreparedStatement：执行sql的对象
			1. SQL注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接。会造成安全性问题
				1. 输入用户随便，输入密码：a' or 'a' = 'a
				2. sql：select * from user where username = 'fhdsjkf' and password = 'a' or 'a' = 'a' 
	
			2. 解决sql注入问题：使用PreparedStatement对象来解决
			3. 预编译的SQL：参数使用?作为占位符
			4. 步骤：
				1. 导入驱动jar包 mysql-connector-java-5.1.37-bin.jar
				2. 注册驱动
				3. 获取数据库连接对象 Connection
				4. 定义sql
					* 注意：sql的参数使用？作为占位符。 如：select * from user where username = ? and password = ?;
				5. 获取执行sql语句的对象 PreparedStatement  Connection.prepareStatement(String sql) 
				6. 给？赋值：
					* 方法： setXxx(参数1,参数2)
						* 参数1：？的位置编号 从1 开始
						* 参数2：？的值
				7. 执行sql，接受返回结果，不需要传递sql语句
				8. 处理结果
				9. 释放资源
	
			5. 注意：后期都会使用PreparedStatement来完成增删改查的所有操作
				1. 可以防止SQL注入
				2. 效率更高

## 抽取JDBC工具类 ： JDBCUtils

	* 目的：简化书写
	* 分析：
		1. 注册驱动也抽取
		2. 抽取一个方法获取连接对象
			* 需求：不想传递参数（麻烦），还得保证工具类的通用性。
			* 解决：配置文件
				jdbc.properties
					url=
					user=
					password=


		3. 抽取一个方法释放资源
	
	* 代码实现：
		public class JDBCUtils {
	    private static String url;
	    private static String user;
	    private static String password;
	    private static String driver;
	    /**
	     * 文件的读取，只需要读取一次即可拿到这些值。使用静态代码块
	     */
	    static{
	        //读取资源文件，获取值。
	
	        try {
	            //1. 创建Properties集合类。
	            Properties pro = new Properties();
	
	            //获取src路径下的文件的方式--->ClassLoader 类加载器
	            ClassLoader classLoader = JDBCUtils.class.getClassLoader();
	            URL res  = classLoader.getResource("jdbc.properties");
	            String path = res.getPath();
	            System.out.println(path);///D:/IdeaProjects/itcast/out/production/day04_jdbc/jdbc.properties
	            //2. 加载文件
	           // pro.load(new FileReader("D:\\IdeaProjects\\itcast\\day04_jdbc\\src\\jdbc.properties"));
	            pro.load(new FileReader(path));
	
	            //3. 获取数据，赋值
	            url = pro.getProperty("url");
	            user = pro.getProperty("user");
	            password = pro.getProperty("password");
	            driver = pro.getProperty("driver");
	            //4. 注册驱动
	            Class.forName(driver);
	        } catch (IOException e) {
	            e.printStackTrace();
	        } catch (ClassNotFoundException e) {
	            e.printStackTrace();
	        }
	    }


​	

	    /**
	     * 获取连接
	     * @return 连接对象
	     */
	    public static Connection getConnection() throws SQLException {
	
	        return DriverManager.getConnection(url, user, password);
	    }
	
	    /**
	     * 释放资源
	     * @param stmt
	     * @param conn
	     */
	    public static void close(Statement stmt,Connection conn){
	        if( stmt != null){
	            try {
	                stmt.close();
	            } catch (SQLException e) {
	                e.printStackTrace();
	            }
	        }
	
	        if( conn != null){
	            try {
	                conn.close();
	            } catch (SQLException e) {
	                e.printStackTrace();
	            }
	        }
	    }

- 使用try-catch-resources结构无论是否抛出异常在try-block执行完毕后都会调用资源的close方法。

- 使用try-catch-resources结构创建多个资源，try-block执行完毕后调用的close方法的顺序与创建资源顺序相反

- 使用try-catch-resources结构，try-block块抛出异常后先执行所有资源（try的（）中声明的）的close方法然后在执行catch里面的代码然后才是finally.

- 只用在try的()中声明的资源的close方法才会被调用，并且当对象销毁的时候close也不会被调用

- 使用try-catch-resources结构，无须显示调用资源释放。

	   
	
	
	   /**
	     * 释放资源
	     * @param stmt
	     * @param conn
	     */
	    public static void close(ResultSet rs,Statement stmt, Connection conn){
	        if( rs != null){
	            try {
	                rs.close();
	            } catch (SQLException e) {
	                e.printStackTrace();
	            }
	        }
	
	        if( stmt != null){
	            try {
	                stmt.close();
	            } catch (SQLException e) {
	                e.printStackTrace();
	            }
	        }
	    
	        if( conn != null){
	            try {
	                conn.close();
	            } catch (SQLException e) {
	                e.printStackTrace();
	            }
	        }
	    }
	
	}
	
	* 练习：
		* 需求：
			1. 通过键盘录入用户名和密码
			2. 判断用户是否登录成功
			
				* select * from user where username = "" and password = "";
		* 如果这个sql有查询结果，则成功，反之，则失败
			
		* 步骤：
			1. 创建数据库表 user
			
				CREATE TABLE USER(
					id INT PRIMARY KEY AUTO_INCREMENT,
					username VARCHAR(32),
					PASSWORD VARCHAR(32)
			
			);
				
			INSERT INTO USER VALUES(NULL,'zhangsan','123');
			INSERT INTO USER VALUES(NULL,'lisi','234');
			
		2. 代码实现：
		
				public class JDBCDemo9 {
				
				    public static void main(String[] args) {
				        //1.键盘录入，接受用户名和密码
				        Scanner sc = new Scanner(System.in);
				        System.out.println("请输入用户名：");
				        String username = sc.nextLine();
				        System.out.println("请输入密码：");
				        String password = sc.nextLine();
				        //2.调用方法
				        boolean flag = new JDBCDemo9().login(username, password);
				        //3.判断结果，输出不同语句
				        if(flag){
				            //登录成功
				            System.out.println("登录成功！");
				        }else{
				            System.out.println("用户名或密码错误！");
				        }


​				

				    }


​				
​				

				    /**
				     * 登录方法
				     */
				    public boolean login(String username ,String password){
				        if(username == null || password == null){
				            return false;
				        }
				        //连接数据库判断是否登录成功
				        Connection conn = null;
				        Statement stmt =  null;
				        ResultSet rs = null;
				        //1.获取连接
				        try {
				            conn =  JDBCUtils.getConnection();
				            //2.定义sql
				            String sql = "select * from user where username = '"+username+"' and password = '"+password+"' ";
				            //3.获取执行sql的对象
				            stmt = conn.createStatement();
				            //4.执行查询
				            rs = stmt.executeQuery(sql);
				            //5.判断
				           /* if(rs.next()){//如果有下一行，则返回true
				                return true;
				            }else{
				                return false;
				            }*/
				           return rs.next();//如果有下一行，则返回true
				
				        } catch (SQLException e) {
				            e.printStackTrace();
				        }finally {
				            JDBCUtils.close(rs,stmt,conn);
				        }


​				

				        return false;
				    }
				}


## JDBC控制事务：

```Java
1. 事务：一个包含多个步骤的业务操作。如果这个业务操作被事务管理，则这多个步骤要么同时成功，要么同时失败。
2. 操作：
	1. 开启事务
	2. 提交事务
	3. 回滚事务
3. 使用Connection对象来管理事务
	* 开启事务：setAutoCommit(boolean autoCommit) ：调用该方法设置参数为false，即开启事务
		* 在执行sql之前开启事务
	* 提交事务：commit() 
		* 当所有sql都执行完提交事务
	* 回滚事务：rollback() 
		* 在catch中回滚事务

4. 代码：
	public class JDBCDemo10 {

	    public static void main(String[] args) {
	        Connection conn = null;
	        PreparedStatement pstmt1 = null;
	        PreparedStatement pstmt2 = null;
	
	        try {
	            //1.获取连接
	            conn = JDBCUtils.getConnection();
	            //开启事务
	            conn.setAutoCommit(false);
	
	            //2.定义sql
	            //2.1 张三 - 500
	            String sql1 = "update account set balance = balance - ? where id = ?";
	            //2.2 李四 + 500
	            String sql2 = "update account set balance = balance + ? where id = ?";
	            //3.获取执行sql对象
	            pstmt1 = conn.prepareStatement(sql1);
	            pstmt2 = conn.prepareStatement(sql2);
	            //4. 设置参数
	            pstmt1.setDouble(1,500);
	            pstmt1.setInt(2,1);
	
	            pstmt2.setDouble(1,500);
	            pstmt2.setInt(2,2);
	            //5.执行sql
	            pstmt1.executeUpdate();
	            // 手动制造异常
	            int i = 3/0;
	
	            pstmt2.executeUpdate();
	            //提交事务
	            conn.commit();
	        } catch (Exception e) {
	            //事务回滚
	            try {
	                if(conn != null) {
	                    conn.rollback();
	                }
	            } catch (SQLException e1) {
	                e1.printStackTrace();
	            }
	            e.printStackTrace();
	        }finally {
	            JDBCUtils.close(pstmt1,conn);
	            JDBCUtils.close(pstmt2,null);
	        }
	 }
	
}
```


​		



​	

------


​		



​	

#  6.JDBC 数据库连接池&JDBC Template

	1. 数据库连接池
	
	2. Spring JDBC : JDBC Template



## 数据库连接池

	1. 概念：其实就是一个容器(集合)，存放数据库连接的容器。
		    当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。
		    数据源(DataSource):制SUN制定的用于获取数据库连接的规范接口。它存在于 javax.sql包中，用来代替 DriverManager 的方式来获取连接。
	
	2. 好处：
		1. 节约资源
		2. 用户访问高效
	
	3. 实现：
		DataSource一般有如下三种实现方式：
	  标准实现 -- 提供最基本的连接，也就是DriverManager的方式
	  连接池的实现 -- 提供了连接池，是一种可以缓存及管理多个
	    数据库连接的“容器”。
	  分布事务的实现 -- 提供了连接池，而且这个池中的连接是
	  支持分布式事务的(Distribute Transaction)
	
		一般来说，DataSource 都会由专业的中间件(MiddleWare)来实现。
		1. 标准接口：DataSource   javax.sql包下的
			1. 方法：
				* 获取连接：getConnection()
				* 归还连接：Connection.close()。如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会再关闭连接了。而是归还连接
	
		2. 一般我们不去实现它，有数据库厂商来实现
			1. C3P0：数据库连接池技术
			2. Druid：数据库连接池实现技术，由阿里巴巴提供的


	4. C3P0：数据库连接池技术
		* 步骤：
			1. 导入jar包 (两个) c3p0-0.9.5.2.jar mchange-commons-java-0.2.12.jar ，
				* 不要忘记导入数据库驱动jar包
			2. 定义配置文件：
				* 名称： c3p0.properties 或者 c3p0-config.xml
				* 路径：直接将文件放在src目录下即可。
	
			3. 创建核心对象 数据库连接池对象 ComboPooledDataSource
			4. 获取连接： getConnection
		* 代码：
			 //1.创建数据库连接池对象
	        DataSource ds  = new ComboPooledDataSource();
	        //2. 获取连接对象
	        Connection conn = ds.getConnection();
	5. Druid：数据库连接池实现技术，由阿里巴巴提供的
		1. 步骤：
			1. 导入jar包 druid-1.0.9.jar
			2. 定义配置文件：
				* 是properties形式的
				* 可以叫任意名称，可以放在任意目录下
			3. 加载配置文件。Properties
			4. 获取数据库连接池对象：通过工厂来来获取  DruidDataSourceFactory
			5. 获取连接：getConnection
		* 代码：
			 //3.加载配置文件
	        Properties pro = new Properties();
	        InputStream is = DruidDemo.class.getClassLoader().getResourceAsStream("druid.properties");
	        pro.load(is);
	        //4.获取连接池对象
	        DataSource ds = DruidDataSourceFactory.createDataSource(pro);
	        //5.获取连接
	        Connection conn = ds.getConnection();
		2. 定义工具类
			1. 定义一个类 JDBCUtils
			2. 提供静态代码块加载配置文件，初始化连接池对象
			3. 提供方法
				1. 获取连接方法：通过数据库连接池获取连接
				2. 释放资源
				3. 获取连接池的方法


![image-20211016082833203](https://i.loli.net/2021/10/16/dV5qvHkUjAeC47i.png)


		* 代码：
			public class JDBCUtils {
	
			    //1.定义成员变量 DataSource
			    private static DataSource ds ;
			
			    static{
			        try {
			            //1.加载配置文件
			            Properties pro = new Properties();
			            pro.load(JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties"));
			            //2.获取DataSource
			            ds = DruidDataSourceFactory.createDataSource(pro);
			        } catch (IOException e) {
			            e.printStackTrace();
			        } catch (Exception e) {
			            e.printStackTrace();
			        }
			    }
			
			    /**
			     * 获取连接
			     */
			    public static Connection getConnection() throws SQLException {
			        return ds.getConnection();
			    }
			
			    /**
			     * 释放资源
			     */
			    public static void close(Statement stmt,Connection conn){
			       /* if(stmt != null){
			            try {
			                stmt.close();
			            } catch (SQLException e) {
			                e.printStackTrace();
			            }
			        }
			
			        if(conn != null){
			            try {
			                conn.close();//归还连接
			            } catch (SQLException e) {
			                e.printStackTrace();
			            }
			        }*/
			
			       close(null,stmt,conn);
			    }


​			

			    public static void close(ResultSet rs , Statement stmt, Connection conn){


​			

			        if(rs != null){
			            try {
			                rs.close();
			            } catch (SQLException e) {
			                e.printStackTrace();
			            }
			        }


​			

			        if(stmt != null){
			            try {
			                stmt.close();
			            } catch (SQLException e) {
			                e.printStackTrace();
			            }
			        }
			
			        if(conn != null){
			            try {
			                conn.close();//归还连接
			            } catch (SQLException e) {
			                e.printStackTrace();
			            }
			        }
			    }
			
			    /**
			     * 获取连接池方法
			     */
			
			    public static DataSource getDataSource(){
			        return  ds;
			    }
			
			}

## Spring JDBC

	* Spring框架对JDBC的简单封装。提供了一个JDBCTemplate对象简化JDBC的开发
	* 步骤：
		1. 导入jar包
		2. 创建JdbcTemplate对象。依赖于数据源DataSource
			* JdbcTemplate template = new JdbcTemplate(ds);
	
		3. 调用JdbcTemplate的方法来完成CRUD的操作
			* update():执行DML语句。增、删、改语句
			* queryForMap():查询结果将结果集封装为map集合，将列名作为key，将值作为value 将这条记录封装为一个map集合
				* 注意：这个方法查询的结果集长度只能是1
			* queryForList():查询结果将结果集封装为list集合
				* 注意：将每一条记录封装为一个Map集合，再将Map集合装载到List集合中
			* query():查询结果，将结果封装为JavaBean对象
				* query的参数：RowMapper
					* 一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean的自动封装
					* new BeanPropertyRowMapper<类型>(类型.class)
			* queryForObject：查询结果，将结果封装为对象
				* 一般用于聚合函数的查询
	
		4. 练习：
			* 需求：
				1. 修改1号数据的 salary 为 10000
				2. 添加一条记录
				3. 删除刚才添加的记录
				4. 查询id为1的记录，将其封装为Map集合
				5. 查询所有记录，将其封装为List
				6. 查询所有记录，将其封装为Emp对象的List集合
				7. 查询总记录数
	
			* 代码：
				
				import cn.itcast.domain.Emp;
				import cn.itcast.utils.JDBCUtils;
				import org.junit.Test;
				import org.springframework.jdbc.core.BeanPropertyRowMapper;
				import org.springframework.jdbc.core.JdbcTemplate;
				import org.springframework.jdbc.core.RowMapper;
				
				import java.sql.Date;
				import java.sql.ResultSet;
				import java.sql.SQLException;
				import java.util.List;
				import java.util.Map;
				
				public class JdbcTemplateDemo2 {
				
				    //Junit单元测试，可以让方法独立执行


​				

				    //1. 获取JDBCTemplate对象
				    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
				    /**
				     * 1. 修改1号数据的 salary 为 10000
				     */
				    @Test
				    public void test1(){
				
				        //2. 定义sql
				        String sql = "update emp set salary = 10000 where id = 1001";
				        //3. 执行sql
				        int count = template.update(sql);
				        System.out.println(count);
				    }
				
				    /**
				     * 2. 添加一条记录
				     */
				    @Test
				    public void test2(){
				        String sql = "insert into emp(id,ename,dept_id) values(?,?,?)";
				        int count = template.update(sql, 1015, "郭靖", 10);
				        System.out.println(count);
				
				    }
				
				    /**
				     * 3.删除刚才添加的记录
				     */
				    @Test
				    public void test3(){
				        String sql = "delete from emp where id = ?";
				        int count = template.update(sql, 1015);
				        System.out.println(count);
				    }
				
				    /**
				     * 4.查询id为1001的记录，将其封装为Map集合
				     * 注意：这个方法查询的结果集长度只能是1
				     */
				    @Test
				    public void test4(){
				        String sql = "select * from emp where id = ? or id = ?";
				        Map<String, Object> map = template.queryForMap(sql, 1001,1002);
				        System.out.println(map);
				        //{id=1001, ename=孙悟空, job_id=4, mgr=1004, joindate=2000-12-17, salary=10000.00, bonus=null, dept_id=20}
				
				    }
				
				    /**
				     * 5. 查询所有记录，将其封装为List
				     */
				    @Test
				    public void test5(){
				        String sql = "select * from emp";
				        List<Map<String, Object>> list = template.queryForList(sql);
				
				        for (Map<String, Object> stringObjectMap : list) {
				            System.out.println(stringObjectMap);
				        }
				    }
				
				    /**
				     * 6. 查询所有记录，将其封装为Emp对象的List集合
				     */
				
				    @Test
				    public void test6(){
				        String sql = "select * from emp";
				        List<Emp> list = template.query(sql, new RowMapper<Emp>() {
				
				            @Override
				            public Emp mapRow(ResultSet rs, int i) throws SQLException {
				                Emp emp = new Emp();
				                int id = rs.getInt("id");
				                String ename = rs.getString("ename");
				                int job_id = rs.getInt("job_id");
				                int mgr = rs.getInt("mgr");
				                Date joindate = rs.getDate("joindate");
				                double salary = rs.getDouble("salary");
				                double bonus = rs.getDouble("bonus");
				                int dept_id = rs.getInt("dept_id");
				
				                emp.setId(id);
				                emp.setEname(ename);
				                emp.setJob_id(job_id);
				                emp.setMgr(mgr);
				                emp.setJoindate(joindate);
				                emp.setSalary(salary);
				                emp.setBonus(bonus);
				                emp.setDept_id(dept_id);
				
				                return emp;
				            }
				        });


​				

				        for (Emp emp : list) {
				            System.out.println(emp);
				        }
				    }
				
				    /**
				     * 6. 查询所有记录，将其封装为Emp对象的List集合
				     */
				
				    @Test
				    public void test6_2(){
				        String sql = "select * from emp";
				        List<Emp> list = template.query(sql, new BeanPropertyRowMapper<Emp>(Emp.class));
				        for (Emp emp : list) {
				            System.out.println(emp);
				        }
				    }
				
				    /**
				     * 7. 查询总记录数
				     */
				
				    @Test
				    public void test7(){
				        String sql = "select count(id) from emp";
				        Long total = template.queryForObject(sql, Long.class);
				        System.out.println(total);
				    }
				
				}





------



# 7.HTML

	1. web概念概述
	2. HTML



## web概念概述

	* JavaWeb：
		* 使用Java语言开发基于互联网的项目
	
	* 软件架构：
		1. C/S: Client/Server 客户端/服务器端
			* 在用户本地有一个客户端程序，在远程有一个服务器端程序
			* 如：QQ，迅雷...
			* 优点：
				1. 用户体验好
			* 缺点：
				1. 开发、安装，部署，维护 麻烦
		2. B/S: Browser/Server 浏览器/服务器端
			* 只需要一个浏览器，用户通过不同的网址(URL)，客户访问不同的服务器端程序
			* 优点：
				1. 开发、安装，部署，维护 简单
			* 缺点：
				1. 如果应用过大，用户的体验可能会受到影响
				2. 对硬件要求过高
	
	* B/S架构详解
		* 资源分类：
			1. 静态资源：
				* 使用静态网页开发技术发布的资源。
				* 特点：
					* 所有用户访问，得到的结果是一样的。
					* 如：文本，图片，音频、视频, HTML,CSS,JavaScript
					* 如果用户请求的是静态资源，那么服务器会直接将静态资源发送给浏览器。浏览器中内置了静态资源的解析引擎，可以展示静态资源
			2. 动态资源：
				* 使用动态网页及时发布的资源。
				* 特点：
					* 所有用户访问，得到的结果可能不一样。
					* 如：jsp/servlet,php,asp...
					* 如果用户请求的是动态资源，那么服务器会执行动态资源，转换为静态资源，再发送给浏览器


		* 我们要学习动态资源，必须先学习静态资源！
	
		* 静态资源：
			* HTML：用于搭建基础网页，展示页面的内容
			* CSS：用于美化页面，布局页面
			* JavaScript：控制页面的元素，让页面有一些动态的效果




## HTML

	1. 概念：是最基础的网页开发语言
		* Hyper Text Markup Language 超文本标记语言
			* 超文本:
				* 超文本是用超链接的方法，将各种不同空间的文字信息组织在一起的网状文本.
			* 标记语言:
				* 由标签构成的语言。<标签名称> 如 html，xml
				* 标记语言不是编程语言
	
	2. 快速入门：
		* 语法：
			1. html文档后缀名 .html 或者 .htm
			2. 标签分为
				1. 围堵标签：有开始标签和结束标签。如 <html> </html>
				2. 自闭和标签：开始标签和结束标签在一起。如 <br/>
	
			3. 标签可以嵌套：
				需要正确嵌套，不能你中有我，我中有你
				错误：<a><b></a></b>
				正确：<a><b></b></a>
	
			4. 在开始标签中可以定义属性。属性是由键值对构成，值需要用引号(单双都可)引起来
			5. html的标签不区分大小写，但是建议使用小写。
	
		* 代码：
			<html>
			
				<head>
					<title>title</title>
				</head>
				
				<body>
					<FONT color='red'>Hello World</font><br/>
					
					<font color='green'>Hello World</font>
				
				</body>
		
			</html>
	
	3. 标签学习：
		1. 文件标签：构成html最基本的标签
			* html:html文档的根标签
			* head：头标签。用于指定html文档的一些属性。引入外部的资源
			* title：标题标签。
			* body：体标签
			* <!DOCTYPE html>：html5中定义该文档是html文档
		2. 文本标签：和文本有关的标签
			* 注释：<!-- 注释内容 -->
			* <h1> to <h6>：标题标签
				* h1~h6:字体大小逐渐递减
			* <p>：段落标签
			* <br>：换行标签
			* <hr>：展示一条水平线
				* 属性：
					* color：颜色
					* width：宽度
					* size：高度
					* align：对其方式
						* center：居中
						* left：左对齐
						* right：右对齐
			* <b>：字体加粗
			* <i>：字体斜体
			* <font>:字体标签
			* <center>:文本居中
				* 属性：
					* color：颜色
					* size：大小
					* face：字体
	
			* 属性定义：
				* color：
					1. 英文单词：red,green,blue
					2. rgb(值1，值2，值3)：值的范围：0~255  如  rgb(0,0,255)
					3. #值1值2值3：值的范围：00~FF之间。如： #FF00FF
				* width：
					1. 数值：width='20' ,数值的单位，默认是 px(像素)
					2. 数值%：占比相对于父元素的比例
	
			* 案例：公司简介
				<!DOCTYPE html>
				<html lang="ch">
				<head>
				    <meta charset="UTF-8">
				    <title>黑马程序员简介</title>
				</head>
				<body>
				
				<h1>
				    公司简介
				</h1>
				<hr color="#ffd700">
				
				<p>
				<font color="#FF0000">"中关村黑马程序员训练营"</font>是由<b><i>传智播客</i></b>联合中关村软件园、CSDN， 并委托传智播客进行教学实施的软件开发高端培训机构，致力于服务各大软件企业，解决当前软件开发技术飞速发展， 而企业招不到优秀人才的困扰。
				</p>
				
				<p>
				目前，“中关村黑马程序员训练营”已成长为行业“学员质量好、课程内容深、企业满意”的移动开发高端训练基地， 并被评为中关村软件园重点扶持人才企业。
				</p>
				
				<p>
				
				黑马程序员的学员多为大学毕业后，有理想、有梦想，想从事IT行业，而没有环境和机遇改变自己命运的年轻人。 黑马程序员的学员筛选制度，远比现在90%以上的企业招聘流程更为严格。任何一名学员想成功入学“黑马程序员”， 必须经历长达2个月的面试流程，这些流程中不仅包括严格的技术测试、自学能力测试，还包括性格测试、压力测试、 品德测试等等测试。毫不夸张地说，黑马程序员训练营所有学员都是精挑细选出来的。百里挑一的残酷筛选制度确 保学员质量，并降低企业的用人风险。
				中关村黑马程序员训练营不仅着重培养学员的基础理论知识，更注重培养项目实施管理能力，并密切关注技术革新， 不断引入先进的技术，研发更新技术课程，确保学员进入企业后不仅能独立从事开发工作，更能给企业带来新的技术体系和理念。
				</p>
				
				<p>
				
				一直以来，黑马程序员以技术视角关注IT产业发展，以深度分享推进产业技术成长，致力于弘扬技术创新，倡导分享、 开放和协作，努力打造高质量的IT人才服务平台。
				</p>
				
				<hr color="#ffd700">
				
				<font color="gray" size="2">
				    <center>
				        江苏传智播客教育科技股份有限公司<br>
				        版权所有Copyright 2006-2018&copy;, All Rights Reserved 苏ICP备16007882
				    </center>
				</font>


​				
​				

				</body>
				</html>
	
		3. 图片标签：
			* img：展示图片
				* 属性：
					* src：指定图片的位置
	
			* 代码：
				 <!--展示一张图片 img-->
	
			    <img src="image/jingxuan_2.jpg" align="right" alt="古镇" width="500" height="500"/>
			
			    <!--
			        相对路径
			            * 以.开头的路径
			                * ./：代表当前目录  ./image/1.jpg
			                * ../:代表上一级目录
			     -->
			
			    <img src="./image/jiangwai_1.jpg">
			
			    <img src="../image/jiangwai_1.jpg">
		4. 列表标签：
			* 有序列表：
				* ol:
				* li:
			* 无序列表：
				* ul:
				* li:
		5. 链接标签：
			* a:定义一个超链接
				* 属性：
					* href：指定访问资源的URL(统一资源定位符)
					* target：指定打开资源的方式
						* _self:默认值，在当前页面打开
						* _blank：在空白页面打开
	
			* 代码：
				 <!--超链接  a-->
	
			    <a href="http://www.itcast.cn">点我</a>
			    <br>
			
			    <a href="http://www.itcast.cn" target="_self">点我</a>
			    <br>
			    <a href="http://www.itcast.cn" target="_blank">点我</a>
			
			    <br>
			
			    <a href="./5_列表标签.html">列表标签</a><br>
			    <a href="mailto:itcast@itcast.cn">联系我们</a>
			
			    <br>
			    <a href="http://www.itcast.cn"><img src="image/jiangwai_1.jpg"></a>
		6. div和span：
			* div:每一个div占满一整行。块级标签
	    	* span：文本信息在一行展示，行内标签 内联标签
	
		7. 语义化标签：html5中为了提高程序的可读性，提供了一些标签。
			1. <header>：页眉
			2. <footer>：页脚


		8. 表格标签：
			* table：定义表格
				* width：宽度
				* border：边框
				* cellpadding：定义内容和单元格的距离
				* cellspacing：定义单元格之间的距离。如果指定为0，则单元格的线会合为一条、
				* bgcolor：背景色
				* align：对齐方式
			* tr：定义行
				* bgcolor：背景色
				* align：对齐方式
			* td：定义单元格
				* colspan：合并列
				* rowspan：合并行
			* th：定义表头单元格
			* <caption>：表格标题
			* <thead>：表示表格的头部分
			* <tbody>：表示表格的体部分
			* <tfoot>：表示表格的脚部分


## 案例：旅游网站首页

	1. 确定使用table来完成布局	
	2. 如果某一行只有一个单元格，则使用<tr><td></td></tr>
	3. 如果某一行有多个单元格，则使用
		<tr>
			<td>
				<table></table>
			</td>
		</tr>
	
	4. 代码实现
	
		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <title>黑马旅游网</title>
		</head>
		<body>
		
		    <!--采用table来完成布局-->
		    <!--最外层的table，用于整个页面的布局-->
		    <table width="100%" align="center">
		       <!-- 第1行 -->
		        <tr>
		            <td>
		                <img src="image/top_banner.jpg" width="100%" alt="">
		            </td>
		        </tr>
		
		        <!-- 第2行 -->
		        <tr>
		            <td>
		                <table width="100%" align="center">
		                    <tr>
		                        <td>
		                            <img src="image/logo.jpg" alt="">
		                        </td>
		                        <td>
		                            <img src="image/search.png" alt="">
		                        </td>
		                        <td>
		                            <img src="image/hotel_tel.png" alt="">
		                        </td>
		                    </tr>
		                </table>
		
		            </td>
		        </tr>
		
		        <!-- 第3行 -->
		        <tr>
		            <td>
		                <table width="100%" align="center">
		                    <tr bgcolor="#ffd700" align="center" height="45" >
		                        <td>
		                            <a href="">首页</a>
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		
		                        <td>
		                            门票
		                        </td>
		                    </tr>
		                </table>
		            </td>
		        </tr>
		
		        <!-- 第4行 轮播图 -->
		        <tr>
		            <td>
		                <img src="image/banner_3.jpg" alt="" width="100%">
		            </td>
		        </tr>
		
		        <!-- 第5行 黑马精选-->
		        <tr>
		            <td>
		                <img src="image/icon_5.jpg" alt="">
		                黑马精选
		                <hr  color="#ffd700" >
		            </td>
		        </tr>
		
		        <!-- 第6行 -->
		        <tr>
		            <td>
		                <table align="center" width="95%">
		                    <tr>
		                        <td>
		
		                            <img src="image/jiangxuan_1.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 899</font>
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_1.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 899</font>
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_1.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 899</font>
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_1.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 899</font>
		                        </td>
		                    </tr>
		                </table>
		            </td>
		        </tr>
		
		        <!-- 第7行 国内游 -->
		        <tr>
		            <td>
		                <img src="image/icon_6.jpg" alt="">
		                国内游
		                <hr  color="#ffd700" >
		            </td>
		        </tr>
		
		        <!-- 第8行 -->
		        <tr>
		            <td>
		                <table align="center" width="95%">
		                    <tr>
		                        <td rowspan="2">
		                            <img src="image/guonei_1.jpg" alt="">
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_2.jpg" alt="" height="100%">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_2.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_2.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		                    </tr>
		
		                    <tr>
		                        <td>
		
		                            <img src="image/jiangxuan_2.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_2.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_2.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>


​		

		                    </tr>
		                </table>
		            </td>
		        </tr>
		
		        <!-- 第9行 境外游 -->
		        <tr>
		            <td>
		                <img src="image/icon_7.jpg" alt="">
		                境外游
		                <hr  color="#ffd700" >
		            </td>
		        </tr>
		
		        <!-- 第10行 -->
		        <tr>
		            <td>
		                <table align="center" width="95%">
		                    <tr>
		                        <td rowspan="2">
		                            <img src="image/jiangwai_1.jpg" alt="">
		                        </td>
		
		                        <td>
		
		                            <img src="image/jiangxuan_3.jpg" alt="" height="100%">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                           <img src="image/jiangxuan_3.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                           <img src="image/jiangxuan_3.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		                    </tr>
		
		                    <tr>
		                        <td>
		
		                           <img src="image/jiangxuan_3.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                           <img src="image/jiangxuan_3.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>
		
		                        <td>
		
		                           <img src="image/jiangxuan_3.jpg" alt="">
		                            <p>上海飞三亚五天4晚自由行(春节销售+亲子+蜜月+自由行)</p>
		                            <font color="red">&yen; 699</font>
		                        </td>


​		

```HTML
	                    </tr>
	                </table>
	            </td>
	        </tr>
	        <!-- 第11行 -->
	        <tr>
	            <td>
	                <img src="image/footer_service.png" alt="" width="100%">
	            </td>
	        </tr>
	
	        <!-- 第12行 -->
	        <tr>
	            <td align="center" bgcolor="#ffd700" height="40">
	                <font color="gray" size="2">
	                江苏传智播客教育科技股份有限公司
	                版权所有Copyright 2006-2018&copy;, All Rights Reserved 苏ICP备16007882
	                </font>
	            </td>
	        </tr>
	        
	    </table>
	    
        </body>
	</html>
```


​		

​	

------





# 8.CSS&HTML

	1. HTML标签：表单标签
	
	2. CSS：


##  HTML标签：表单标签

	* 表单：
		* 概念：用于采集用户输入的数据的。用于和服务器进行交互。
		* form：用于定义表单的。可以定义一个范围，范围代表采集用户数据的范围
	        * 属性：
	            * action：指定提交数据的URL
	            * method:指定提交方式
	                * 分类：一共7种，2种比较常用
	                   * get：
	                        1. 请求参数会在地址栏中显示。会封装到请求行中(HTTP协议后讲解)。
	                        2. 请求参数大小是有限制的。
	                        3. 不太安全。
	                   * post：
	                        2. 请求参数不会再地址栏中显示。会封装在请求体中(HTTP协议后讲解)
	                        2. 请求参数的大小没有限制。
	                        3. 较为安全。
	
	        * 表单项中的数据要想被提交：必须指定其name属性


​		

		* 表单项标签：
			* input：可以通过type属性值，改变元素展示的样式
				* type属性：
					* text：文本输入框，默认值
						* placeholder：指定输入框的提示信息，当输入框的内容发生变化，会自动清空提示信息	
					* password：密码输入框
					* radio:单选框
						* 注意：
							1. 要想让多个单选框实现单选的效果，则多个单选框的name属性值必须一样。
							2. 一般会给每一个单选框提供value属性，指定其被选中后提交的值
							3. checked属性，可以指定默认值
					* checkbox：复选框
						* 注意：
							1. 一般会给每一个单选框提供value属性，指定其被选中后提交的值
							2. checked属性，可以指定默认值
	
					* file：文件选择框
					* hidden：隐藏域，用于提交一些信息。
					* 按钮：
						* submit：提交按钮。可以提交表单
						* button：普通按钮
						* image：图片提交按钮
							* src属性指定图片的路径	
	
			   * label：指定输入项的文字描述信息
				   * 注意：
					   * label的for属性一般会和 input 的 id属性值 对应。如果对应了，则点击label区域，会让input输入框获取焦点。
			* select: 下拉列表
				* 子元素：option，指定列表项
				
			* textarea：文本域
				* cols：指定列数，每一行有多少个字符
				* rows：默认多少行。


## CSS：页面美化和布局控制

	1. 概念： Cascading Style Sheets 层叠样式表
		* 层叠：多个样式可以作用在同一个html的元素上，同时生效
	
	2. 好处：
		1. 功能强大
		2. 将内容展示和样式控制分离
			* 降低耦合度。解耦
			* 让分工协作更容易
			* 提高开发效率


	3. CSS的使用：CSS与html结合方式
		1. 内联样式
			 * 在标签内使用style属性指定css代码
			 * 如：<div style="color:red;">hello css</div>
		2. 内部样式
			* 在head标签内，定义style标签，style标签的标签体内容就是css代码
			* 如：
				<style>
			        div{
			            color:blue;
			        }
			
			    </style>
				<div>hello css</div>
		3. 外部样式
			1. 定义css资源文件。
			2. 在head标签内，定义link标签，引入外部的资源文件
			* 如：
	    		* a.css文件：
					div{
					    color:green;
					}
				<link rel="stylesheet" href="css/a.css">
				<div>hello css</div>
				<div>hello css</div>
	
		* 注意：
			* 1,2,3种方式 css作用范围越来越大
			* 1方式不常用，后期常用2,3
			* 3种格式可以写为：
				<style>
			        @import "css/a.css";
			    </style>
	
	4. css语法：
		* 格式：
			选择器 {
				属性名1:属性值1;
				属性名2:属性值2;
				...
			}
		* 选择器:筛选具有相似特征的元素
		* 注意：
			* 每一对属性需要使用；隔开，最后一对属性可以不加；


	5. 选择器：筛选具有相似特征的元素
		* 分类：
			1. 基础选择器
				1. id选择器：选择具体的id属性值的元素.建议在一个html页面中id值唯一
			        * 语法：#id属性值{}
			    2. 元素选择器：选择具有相同标签名称的元素
			        * 语法： 标签名称{}
			        * 注意：id选择器优先级高于元素选择器
			    3. 类选择器：选择具有相同的class属性值的元素。
			        * 语法：.class属性值{}
			        * 注意：类选择器选择器优先级高于元素选择器
			2. 扩展选择器：
				1. 选择所有元素：
					* 语法： *{}
				2. 并集选择器：
					* 选择器1,选择器2{}
				
				3. 子选择器：筛选选择器1元素下的选择器2元素
					* 语法：  选择器1 选择器2{}
				4. 父选择器：筛选选择器2的父元素选择器1
					* 语法：  选择器1 > 选择器2{}
	
				5. 属性选择器：选择元素名称，属性名=属性值的元素
					* 语法：  元素名称[属性名="属性值"]{}
	
				6. 伪类选择器：选择一些元素具有的状态
					* 语法： 元素:状态{}
					* 如： <a>
						* 状态：
							* link：初始化的状态
							* visited：被访问过的状态
							* active：正在访问状态
							* hover：鼠标悬浮状态
	6. 属性
		1. 字体、文本
			* font-size：字体大小
			* color：文本颜色
			* text-align：对其方式
			* line-height：行高 
		2. 背景
			* background：
		3. 边框
			* border：设置边框，符合属性
		4. 尺寸
			* width：宽度
			* height：高度
		5. 盒子模型：控制布局
			* margin：外边距
			* padding：内边距
				* 默认情况下内边距会影响整个盒子的大小
				* box-sizing: border-box;  设置盒子的属性，让width和height就是最终盒子的大小
	
			* float：浮动
				* left
				* right

## 案例：

		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <title>注册页面</title>
		<style>
		    *{
		        margin: 0px;
		        padding: 0px;
		        box-sizing: border-box;
		    }
		    body{
		        background: url("img/register_bg.png") no-repeat center;
		        padding-top: 25px;
		    }
		
		    .rg_layout{
		        width: 900px;
		        height: 500px;
		        border: 8px solid #EEEEEE;
		        background-color: white;
		        /*让div水平居中*/
		        margin: auto;
		    }
		
		    .rg_left{
		        /*border: 1px solid red;*/
		        float: left;
		        margin: 15px;
		    }
		    .rg_left > p:first-child{
		        color:#FFD026;
		        font-size: 20px;
		    }
		
		    .rg_left > p:last-child{
		        color:#A6A6A6;
		        font-size: 20px;
		
		    }


​		

		    .rg_center{
		        float: left;
		       /* border: 1px solid red;*/
		
		    }
		
		    .rg_right{
		        /*border: 1px solid red;*/
		        float: right;
		        margin: 15px;
		    }
		
		    .rg_right > p:first-child{
		        font-size: 15px;
		
		    }
		    .rg_right p a {
		        color:pink;
		    }
		
		    .td_left{
		        width: 100px;
		        text-align: right;
		        height: 45px;
		    }
		    .td_right{
		        padding-left: 50px ;
		    }
		
		    #username,#password,#email,#name,#tel,#birthday,#checkcode{
		        width: 251px;
		        height: 32px;
		        border: 1px solid #A6A6A6 ;
		        /*设置边框圆角*/
		        border-radius: 5px;
		        padding-left: 10px;
		    }
		    #checkcode{
		        width: 110px;
		    }
		
		    #img_check{
		        height: 32px;
		        vertical-align: middle;
		    }
		
		    #btn_sub{
		        width: 150px;
		        height: 40px;
		        background-color: #FFD026;
		        border: 1px solid #FFD026 ;
		    }
		
		</style>
		
		</head>
		<body>
		
		<div class="rg_layout">
		    <div class="rg_left">
		        <p>新用户注册</p>
		        <p>USER REGISTER</p>
		
		    </div>
		
		    <div class="rg_center">
		        <div class="rg_form">
		            <!--定义表单 form-->
		            <form action="#" method="post">
		                <table>
		                    <tr>
		                        <td class="td_left"><label for="username">用户名</label></td>
		                        <td class="td_right"><input type="text" name="username" id="username" placeholder="请输入用户名"></td>
		                    </tr>
		
		                    <tr>
		                        <td class="td_left"><label for="password">密码</label></td>
		                        <td class="td_right"><input type="password" name="password" id="password" placeholder="请输入密码"></td>
		                    </tr>
		
		                    <tr>
		                        <td class="td_left"><label for="email">Email</label></td>
		                        <td class="td_right"><input type="email" name="email" id="email" placeholder="请输入邮箱"></td>
		                    </tr>
		
		                    <tr>
		                        <td class="td_left"><label for="name">姓名</label></td>
		                        <td class="td_right"><input type="text" name="name" id="name" placeholder="请输入姓名"></td>
		                    </tr>
		
		                    <tr>
		                        <td class="td_left"><label for="tel">手机号</label></td>
		                        <td class="td_right"><input type="text" name="tel" id="tel" placeholder="请输入手机号"></td>
		                    </tr>
		
		                    <tr>
		                        <td class="td_left"><label>性别</label></td>
		                        <td class="td_right">
		                            <input type="radio" name="gender" value="male"> 男
		                            <input type="radio" name="gender" value="female"> 女
		                        </td>
		                    </tr>
		
		                    <tr>
		                        <td class="td_left"><label for="birthday">出生日期</label></td>
		                        <td class="td_right"><input type="date" name="birthday" id="birthday" placeholder="请输入出生日期"></td>
		                    </tr>
		
		                    <tr>
		                        <td class="td_left"><label for="checkcode" >验证码</label></td>
		                        <td class="td_right"><input type="text" name="checkcode" id="checkcode" placeholder="请输入验证码">
		                            <img id="img_check" src="img/verify_code.jpg">
		                        </td>
		                    </tr>


​		

		                    <tr>
		                        <td colspan="2" align="center"><input type="submit" id="btn_sub" value="注册"></td>
		                    </tr>
		                </table>
		
		            </form>


​		

		        </div>
		
		    </div>
		
		    <div class="rg_right">
		        <p>已有账号?<a href="#">立即登录</a></p>
		    </div>


​		

		</div>


​		

		</body>
		</html>



------







# 9. JavaScript基础

	1. JavaScript基础


## JavaScript：

	* 概念：	一门客户端脚本语言
		* 运行在客户端浏览器中的。每一个浏览器都有JavaScript的解析引擎
		* 脚本语言：不需要编译，直接就可以被浏览器解析执行了
	
	* 功能：
		* 可以来增强用户和html页面的交互过程，可以来控制html元素，让页面有一些动态的效果，增强用户的体验。
	
	* JavaScript发展史：
		1. 1992年，Nombase公司，开发出第一门客户端脚本语言，专门用于表单的校验。命名为 ： C--	，后来更名为：ScriptEase
		2. 1995年，Netscape(网景)公司，开发了一门客户端脚本语言：LiveScript。后来，请来SUN公司的专家，修改LiveScript，命名为JavaScript
		3. 1996年，微软抄袭JavaScript开发出JScript语言
		4. 1997年，ECMA(欧洲计算机制造商协会)，制定出客户端脚本语言的标准：ECMAScript，就是统一了所有客户端脚本语言的编码方式。
	
		* JavaScript = ECMAScript + JavaScript自己特有的东西(BOM+DOM)
	
	* ECMAScript：客户端脚本语言的标准
		1. 基本语法：
			1. 与html结合方式
				1. 内部JS：
					* 定义<script>，标签体内容就是js代码
				2. 外部JS：
					* 定义<script>，通过src属性引入外部的js文件
	
				* 注意：
					1. <script>可以定义在html页面的任何地方。但是定义的位置会影响执行顺序。
					2. <script>可以定义多个。
			2. 注释
				1. 单行注释：//注释内容
				2. 多行注释：/*注释内容*/
			3. 数据类型：
				1. 原始数据类型(基本数据类型)：
					1. number：数字。 整数/小数/NaN(not a number 一个不是数字的数字类型)
					2. string：字符串。 字符串  "abc" "a" 'abc'
					3. boolean: true和false
					4. null：一个对象为空的占位符
					5. undefined：未定义。如果一个变量没有给初始化值，则会被默认赋值为undefined
					
				2. 引用数据类型：对象
				
			4. 变量
				* 变量：一小块存储数据的内存空间
				* Java语言是强类型语言，而JavaScript是弱类型语言。
					* 强类型：在开辟变量存储空间时，定义了空间将来存储的数据的数据类型。只能存储固定类型的数据
					* 弱类型：在开辟变量存储空间时，不定义空间将来的存储数据类型，可以存放任意类型的数据。
				* 语法：
					* var 变量名 = 初始化值;
				
				* typeof运算符：获取变量的类型。
					* 注：null运算后得到的是object
			5. 运算符
				1. 一元运算符：只有一个运算数的运算符
					++，-- ， +(正号)  
					* ++ --: 自增(自减)
						* ++(--) 在前，先自增(自减)，再运算
						* ++(--) 在后，先运算，再自增(自减)
					* +(-)：正负号
				    * 注意：在JS中，如果运算数不是运算符所要求的类型，那么js引擎会自动的将运算数进行类型转换
	                    * 其他类型转number：
	                        * string转number：按照字面值转换。如果字面值不是数字，则转为NaN（不是数字的数字）
	                        * boolean转number：true转为1，false转为0
				2. 算数运算符
					+ - * / % ...
	
				3. 赋值运算符
					= += -+....
	
				4. 比较运算符
					> < >= <= == ===(全等于)
					* 比较方式
	                  1. 类型相同：直接比较
	                      * 字符串：按照字典顺序比较。按位逐一比较，直到得出大小为止。
	                  2. 类型不同：先进行类型转换，再比较
	                      * ===：全等于。在比较之前，先判断类型，如果类型不一样，则直接返回false


				5. 逻辑运算符
					&& || !
					* 其他类型转boolean：
	                   1. number：0或NaN为假，其他为真
	                   2. string：除了空字符串("")，其他都是true
	                   3. null&undefined:都是false
	                   4. 对象：所有对象都为true
				
				6. 三元运算符
					? : 表达式
					var a = 3;
			        var b = 4;
			
			        var c = a > b ? 1:0;
					* 语法：
						* 表达式? 值1:值2;
						* 判断表达式的值，如果是true则取值1，如果是false则取值2；
				
			6. 流程控制语句：
				1. if...else...
				2. switch:
					* 在java中，switch语句可以接受的数据类型： byte int shor char,枚举(1.5) ,String(1.7)
						* switch(变量):
							case 值:
					* 在JS中,switch语句可以接受任意的原始数据类型
				3. while
				4. do...while
				5. for
			7. JS特殊语法：
				1. 语句以;结尾，如果一行只有一条语句则 ;可以省略 (不建议)
				2. 变量的定义使用var关键字，也可以不使用
	        		* 用： 定义的变量是局部变量
	                * 不用：定义的变量是全局变量(不建议)
	
			8. 练习：99乘法表
			<!DOCTYPE html>
			<html lang="en">
			<head>
			    <meta charset="UTF-8">
			    <title>99乘法表</title>
			    <style>
			        td{
			            border: 1px solid;
			        }
			
			    </style>
			
			    <script>
			
			        document.write("<table  align='center'>");


​			

			        //1.完成基本的for循环嵌套，展示乘法表
			        for (var i = 1; i <= 9 ; i++) {
			            document.write("<tr>");
			            for (var j = 1; j <=i ; j++) {
			                document.write("<td>");
			
			                //输出  1 * 1 = 1
			                document.write(i + " * " + j + " = " + ( i*j) +"&nbsp;&nbsp;&nbsp;");
			
			                document.write("</td>");
			            }
			            /*//输出换行
			            document.write("<br>");*/
			
			            document.write("</tr>");
			        }
			
			        //2.完成表格嵌套
			        document.write("</table>");
			
			    </script>
			</head>
			<body>
			
			</body>
			</html>
	
		2. 基本对象：
			1. Function：函数(方法)对象
	            1. 创建：
	                1. var fun = new Function(形式参数列表,方法体);  //忘掉吧
	                2. 
	                    function 方法名称(形式参数列表){
	                        方法体
	                    }
	
	                3. 
	                   var 方法名 = function(形式参数列表){
	                        方法体
	                   }
	            2. 方法：
	
	            3. 属性：
	                length:代表形参的个数
	            4. 特点：
	                1. 方法定义是，形参的类型不用写,返回值类型也不写。
	                2. 方法是一个对象，如果定义名称相同的方法，会覆盖
	                3. 在JS中，方法的调用只与方法的名称有关，和参数列表无关
	                4. 在方法声明中有一个隐藏的内置对象（数组），arguments,封装所有的实际参数
	            5. 调用：
	                方法名称(实际参数列表);
			
			2. Array:数组对象
	            1. 创建：
	                1. var arr = new Array(元素列表);
	                2. var arr = new Array(默认长度);
	                3. var arr = [元素列表];
	            2. 方法
	                join(参数):将数组中的元素按照指定的分隔符拼接为字符串
	                push()	向数组的末尾添加一个或更多元素，并返回新的长度。
	            3. 属性
	                length:数组的长度
	            4. 特点：
	                1. JS中，数组元素的类型可变的。
	                2. JS中，数组长度可变的。
			3. Boolean
			4. Date：日期对象
	            1. 创建：
	                var date = new Date();
	
	            2. 方法：
	                toLocaleString()：返回当前date对象对应的时间本地字符串格式
	                getTime():获取毫秒值。返回当前如期对象描述的时间到1970年1月1日零点的毫秒值差
			5. Math：数学对象
	            1. 创建：
	                * 特点：Math对象不用创建，直接使用。  Math.方法名();
	
	            2. 方法：
	                random():返回 0 ~ 1 之间的随机数。 含0不含1
	                ceil(x)：对数进行上舍入。
	                floor(x)：对数进行下舍入。
	                round(x)：把数四舍五入为最接近的整数。
	            3. 属性：
	                PI
			6. Number
			7. String
			8. RegExp：正则表达式对象
				1. 正则表达式：定义字符串的组成规则。
					1. 单个字符:[]
						如： [a] [ab] [a-zA-Z0-9_]
						* 特殊符号代表特殊含义的单个字符:
							\d:单个数字字符 [0-9]
							\w:单个单词字符[a-zA-Z0-9_]
					2. 量词符号：
						?：表示出现0次或1次
						*：表示出现0次或多次
						+：出现1次或多次
						{m,n}:表示 m<= 数量 <= n
							* m如果缺省： {,n}:最多n次
							* n如果缺省：{m,} 最少m次
					3. 开始结束符号
						* ^:开始
						* $:结束
				2. 正则对象：
					1. 创建
						1. var reg = new RegExp("正则表达式");
						2. var reg = /正则表达式/;
					2. 方法	
						1. test(参数):验证指定的字符串是否符合正则定义的规范	
			9. Global
				1. 特点：全局对象，这个Global中封装的方法不需要对象就可以直接调用。  方法名();
				2. 方法：
				    encodeURI():url编码
				    decodeURI():url解码
	
				    encodeURIComponent():url编码,编码的字符更多
				    decodeURIComponent():url解码
	
				    parseInt():将字符串转为数字
				        * 逐一判断每一个字符是否是数字，直到不是数字为止，将前边数字部分转为number
				    isNaN():判断一个值是否是NaN
				        * NaN六亲不认，连自己都不认。NaN参与的==比较全部问false
	
				    eval():讲 JavaScript 字符串，并把它作为脚本代码来执行。
	            3. URL编码
	               传智播客 =  %E4%BC%A0%E6%99%BA%E6%92%AD%E5%AE%A2
	
	* BOM
	
	* DOM





------

​	



# 10.JavaScript高级

	1. JavaScript：
		1. ECMAScript：
		2. BOM：
		3. DOM：
			1. 事件

## DOM简单学习：为了满足案例要求

	* 功能：控制html文档的内容
	* 获取页面标签(元素)对象：Element
		* document.getElementById("id值"):通过元素的id获取元素对象
	
	* 操作Element对象：
		1. 修改属性值：
			1. 明确获取的对象是哪一个？
			2. 查看API文档，找其中有哪些属性可以设置
		2. 修改标签体内容：
			* 属性：innerHTML
			1. 获取元素对象
			2. 使用innerHTML属性修改标签体内容

## 事件简单学习

	* 功能： 某些组件被执行了某些操作后，触发某些代码的执行。
		* 造句：  xxx被xxx,我就xxx
			* 我方水晶被摧毁后，我就责备对友。
			* 敌方水晶被摧毁后，我就夸奖自己。
	
	* 如何绑定事件
		1. 直接在html标签上，指定事件的属性(操作)，属性值就是js代码
			1. 事件：onclick--- 单击事件
	
		2. 通过js获取元素对象，指定事件属性，设置一个函数
	
		* 代码：
			<body>
				<img id="light" src="img/off.gif"  onclick="fun();">
				<img id="light2" src="img/off.gif">
				
				<script>
				    function fun(){
				        alert('我被点了');
				        alert('我又被点了');
				    }
				
				    function fun2(){
				        alert('咋老点我？');
				    }
				
				    //1.获取light2对象
				    var light2 = document.getElementById("light2");
				    //2.绑定事件
				    light2.onclick = fun2;


​				

				</script>
			</body>
	
	* 案例1：电灯开关
		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <title>电灯开关</title>
		
		</head>
		<body>
		
		<img id="light" src="img/off.gif">
		
		<script>
		    /*
		        分析：
		            1.获取图片对象
		            2.绑定单击事件
		            3.每次点击切换图片
		                * 规则：
		                    * 如果灯是开的 on,切换图片为 off
		                    * 如果灯是关的 off,切换图片为 on
		                * 使用标记flag来完成
		
		     */
		
		    //1.获取图片对象
		    var light = document.getElementById("light");
		
		    var flag = false;//代表灯是灭的。 off图片
		
		    //2.绑定单击事件
		    light.onclick = function(){
		        if(flag){//判断如果灯是开的，则灭掉
		            light.src = "img/off.gif";
		            flag = false;
		
		        }else{
		            //如果灯是灭的，则打开
		
		            light.src = "img/on.gif";
		            flag = true;
		        }


​		

		    }
		    
		</script>
		</body>
		</html>


# BOM:

	1. 概念：Browser Object Model 浏览器对象模型
		* 将浏览器的各个组成部分封装成对象。
	
	2. 组成：
		* Window：窗口对象
		* Navigator：浏览器对象
		* Screen：显示器屏幕对象
		* History：历史记录对象
		* Location：地址栏对象
	
	3. Window：窗口对象
	    1. 创建
	    2. 方法
	         1. 与弹出框有关的方法：
	            alert()	显示带有一段消息和一个确认按钮的警告框。
	            confirm()	显示带有一段消息以及确认按钮和取消按钮的对话框。
	                * 如果用户点击确定按钮，则方法返回true
	                * 如果用户点击取消按钮，则方法返回false
	            prompt()	显示可提示用户输入的对话框。
	                * 返回值：获取用户输入的值
	         2. 与打开关闭有关的方法：
	            close()	关闭浏览器窗口。
	                * 谁调用我 ，我关谁
	            open()	打开一个新的浏览器窗口
	                * 返回新的Window对象
	         3. 与定时器有关的方式
	            setTimeout()	在指定的毫秒数后调用函数或计算表达式。
	                * 参数：
	                    1. js代码或者方法对象
	                    2. 毫秒值
	                * 返回值：唯一标识，用于取消定时器
	            clearTimeout()	取消由 setTimeout() 方法设置的 timeout。
	
	            setInterval()	按照指定的周期（以毫秒计）来调用函数或计算表达式。
	            clearInterval()	取消由 setInterval() 设置的 timeout。
	
	    3. 属性：
	        1. 获取其他BOM对象：
	            history
	            location
	            Navigator
	            Screen:
	        2. 获取DOM对象
	            document
	    4. 特点
	        * Window对象不需要创建可以直接使用 window使用。 window.方法名();
	        * window引用可以省略。  方法名();


	4. Location：地址栏对象
		1. 创建(获取)：
			1. window.location
			2. location
	
		2. 方法：
			* reload()	重新加载当前文档。刷新
		3. 属性
			* href	设置或返回完整的 URL。


	5. History：历史记录对象
	    1. 创建(获取)：
	        1. window.history
	        2. history
	
	    2. 方法：
	        * back()	加载 history 列表中的前一个 URL。
	        * forward()	加载 history 列表中的下一个 URL。
	        * go(参数)	加载 history 列表中的某个具体页面。
	            * 参数：
	                * 正数：前进几个历史记录
	                * 负数：后退几个历史记录
	    3. 属性：
	        * length	返回当前窗口历史列表中的 URL 数量。




## DOM：

	* 概念： Document Object Model 文档对象模型
		* 将标记语言文档的各个组成部分，封装为对象。可以使用这些对象，对标记语言文档进行CRUD的动态操作
	
	* W3C DOM 标准被分为 3 个不同的部分：
	
		* 核心 DOM - 针对任何结构化文档的标准模型
			* Document：文档对象
			* Element：元素对象
			* Attribute：属性对象
			* Text：文本对象
			* Comment:注释对象
	
			* Node：节点对象，其他5个的父对象
		* XML DOM - 针对 XML 文档的标准模型
		* HTML DOM - 针对 HTML 文档的标准模型





	* 核心DOM模型：
		* Document：文档对象
			1. 创建(获取)：在html dom模型中可以使用window对象来获取
				1. window.document
				2. document
			2. 方法：
				1. 获取Element对象：
					1. getElementById()	： 根据id属性值获取元素对象。id属性值一般唯一
					2. getElementsByTagName()：根据元素名称获取元素对象们。返回值是一个数组
					3. getElementsByClassName():根据Class属性值获取元素对象们。返回值是一个数组
					4. getElementsByName(): 根据name属性值获取元素对象们。返回值是一个数组
				2. 创建其他DOM对象：
					createAttribute(name)
	            	createComment()
	            	createElement()
	            	createTextNode()
			3. 属性
		* Element：元素对象
			1. 获取/创建：通过document来获取和创建
			2. 方法：
				1. removeAttribute()：删除属性
				2. setAttribute()：设置属性
		* Node：节点对象，其他5个的父对象
			* 特点：所有dom对象都可以被认为是一个节点
			* 方法：
				* CRUD dom树：
					* appendChild()：向节点的子节点列表的结尾添加新的子节点。
					* removeChild()	：删除（并返回）当前节点的指定子节点。
					* replaceChild()：用新节点替换一个子节点。
			* 属性：
				* parentNode 返回节点的父节点。


	* HTML DOM
		1. 标签体的设置和获取：innerHTML
		2. 使用html元素对象的属性
		3. 控制元素样式
			1. 使用元素的style属性来设置
				如：
					 //修改样式方式1
			        div1.style.border = "1px solid red";
			        div1.style.width = "200px";
			        //font-size--> fontSize
			        div1.style.fontSize = "20px";
			2. 提前定义好类选择器的样式，通过元素的className属性来设置其class属性值。


## 事件监听机制：

	* 概念：某些组件被执行了某些操作后，触发某些代码的执行。	
		* 事件：某些操作。如： 单击，双击，键盘按下了，鼠标移动了
		* 事件源：组件。如： 按钮 文本输入框...
		* 监听器：代码。
		* 注册监听：将事件，事件源，监听器结合在一起。 当事件源上发生了某个事件，则触发执行某个监听器代码。


	* 常见的事件：
		1. 点击事件：
			1. onclick：单击事件
			2. ondblclick：双击事件
		2. 焦点事件
			1. onblur：失去焦点
			2. onfocus:元素获得焦点。
	
		3. 加载事件：
			1. onload：一张页面或一幅图像完成加载。
	
		4. 鼠标事件：
			1. onmousedown	鼠标按钮被按下。
			2. onmouseup	鼠标按键被松开。
			3. onmousemove	鼠标被移动。
			4. onmouseover	鼠标移到某元素之上。
			5. onmouseout	鼠标从某元素移开。


​			

		5. 键盘事件：
			1. onkeydown	某个键盘按键被按下。	
			2. onkeyup		某个键盘按键被松开。
			3. onkeypress	某个键盘按键被按下并松开。
	
		6. 选择和改变
			1. onchange	域的内容被改变。
			2. onselect	文本被选中。
	
		7. 表单事件：
			1. onsubmit	确认按钮被点击。
			2. onreset	重置按钮被点击。





------





# 11.BootStrap

	1. Bootstrap




## Bootstrap：

	1. 概念： 一个前端开发的框架，Bootstrap，来自 Twitter，是目前很受欢迎的前端框架。Bootstrap 是基于 HTML、CSS、JavaScript 的，它简洁灵活，使得 Web 开发更加快捷。
		* 框架:一个半成品软件，开发人员可以在框架基础上，在进行开发，简化编码。
		* 好处：
			1. 定义了很多的css样式和js插件。我们开发人员直接可以使用这些样式和插件得到丰富的页面效果。
			2. 响应式布局。
				* 同一套页面可以兼容不同分辨率的设备。


	2. 快速入门
		1. 下载Bootstrap
		2. 在项目中将这三个文件夹复制
		3. 创建html页面，引入必要的资源文件


		<!DOCTYPE html>
		<html lang="zh-CN">
		<head>
		    <meta charset="utf-8">
		    <meta http-equiv="X-UA-Compatible" content="IE=edge">
		    <meta name="viewport" content="width=device-width, initial-scale=1">
		    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
		    <title>Bootstrap HelloWorld</title>
		
		    <!-- Bootstrap -->
		    <link href="css/bootstrap.min.css" rel="stylesheet">


​		

		    <!-- jQuery (Bootstrap 的所有 JavaScript 插件都依赖 jQuery，所以必须放在前边) -->
		    <script src="js/jquery-3.2.1.min.js"></script>
		    <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
		    <script src="js/bootstrap.min.js"></script>
		</head>
		<body>
		<h1>你好，世界！</h1>
		
		</body>
		</html>

## 响应式布局

	* 同一套页面可以兼容不同分辨率的设备。
	* 实现：依赖于栅格系统：将一行平均分成12个格子，可以指定元素占几个格子
	* 步骤：
		1. 定义容器。相当于之前的table、
			* 容器分类：
				1. container：两边留白
				2. container-fluid：每一种设备都是100%宽度
		2. 定义行。相当于之前的tr   样式：row
		3. 定义元素。指定该元素在不同的设备上，所占的格子数目。样式：col-设备代号-格子数目
			* 设备代号：
				1. xs：超小屏幕 手机 (<768px)：col-xs-12
				2. sm：小屏幕 平板 (≥768px)
				3. md：中等屏幕 桌面显示器 (≥992px)
				4. lg：大屏幕 大桌面显示器 (≥1200px)
	
		* 注意：
			1. 一行中如果格子数目超过12，则超出部分自动换行。
			2. 栅格类属性可以向上兼容。栅格类适用于与屏幕宽度大于或等于分界点大小的设备。
			3. 如果真实设备宽度小于了设置栅格类属性的设备代码的最小值，会一个元素沾满一整行。

## CSS样式和JS插件

	1. 全局CSS样式：
		* 按钮：class="btn btn-default"
		* 图片：
			*  class="img-responsive"：图片在任意尺寸都占100%
			*  图片形状
				*  <img src="..." alt="..." class="img-rounded">：方形
				*  <img src="..." alt="..." class="img-circle"> ： 圆形
				*  <img src="..." alt="..." class="img-thumbnail"> ：相框
		* 表格
			* table
			* table-bordered
			* table-hover
		* 表单
			* 给表单项添加：class="form-control" 
	2. 组件：
		* 导航条
		* 分页条
	3. 插件：
		* 轮播图

## 案例

	<!DOCTYPE html>
	<html lang="zh-CN">
	<head>
	    <meta charset="utf-8">
	    <meta http-equiv="X-UA-Compatible" content="IE=edge">
	    <meta name="viewport" content="width=device-width, initial-scale=1">
	    <!-- 上述3个meta标签*必须*放在最前面，任何其他内容都*必须*跟随其后！ -->
	    <title>Bootstrap HelloWorld</title>
	
	    <!-- Bootstrap -->
	    <link href="css/bootstrap.min.css" rel="stylesheet">


​	

	    <!-- jQuery (Bootstrap 的所有 JavaScript 插件都依赖 jQuery，所以必须放在前边) -->
	    <script src="js/jquery-3.2.1.min.js"></script>
	    <!-- 加载 Bootstrap 的所有 JavaScript 插件。你也可以根据需要只加载单个插件。 -->
	    <script src="js/bootstrap.min.js"></script>
	    <style>
	        .paddtop{
	            padding-top: 10px;
	        }
	        .search-btn{
	            float: left;
	            border:1px solid #ffc900;
	            width: 90px;
	            height: 35px;
	            background-color:#ffc900 ;
	            text-align: center;
	            line-height: 35px;
	            margin-top: 15px;
	        }
	
	        .search-input{
	            float: left;
	            border:2px solid #ffc900;
	            width: 400px;
	            height: 35px;
	            padding-left: 5px;
	            margin-top: 15px;
	        }
	        .jx{
	            border-bottom: 2px solid #ffc900;
	            padding: 5px;
	        }
	        .company{
	            height: 40px;
	            background-color: #ffc900;
	            text-align: center;
	            line-height:40px ;
	            font-size: 8px;
	        }
	    </style>
	</head>
	<body>
	
	   <!-- 1.页眉部分-->
	   <header class="container-fluid">
	       <div class="row">
	           <img src="img/top_banner.jpg" class="img-responsive">
	       </div>
	       <div class="row paddtop">
	           <div class="col-md-3">
	               <img src="img/logo.jpg" class="img-responsive">
	           </div>
	           <div class="col-md-5">
	               <input class="search-input" placeholder="请输入线路名称">
	               <a class="search-btn" href="#">搜索</a>
	           </div>
	           <div class="col-md-4">
	               <img src="img/hotel_tel.png" class="img-responsive">
	           </div>
	
	       </div>
	       <!--导航栏-->
	       <div class="row">
	           <nav class="navbar navbar-default">
	               <div class="container-fluid">
	                   <!-- Brand and toggle get grouped for better mobile display -->
	                   <div class="navbar-header">
	                       <!-- 定义汉堡按钮 -->
	                       <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
	                           <span class="sr-only">Toggle navigation</span>
	                           <span class="icon-bar"></span>
	                           <span class="icon-bar"></span>
	                           <span class="icon-bar"></span>
	                       </button>
	                       <a class="navbar-brand" href="#">首页</a>
	                   </div>
	
	                   <!-- Collect the nav links, forms, and other content for toggling -->
	                   <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
	                       <ul class="nav navbar-nav">
	                           <li class="active"><a href="#">Link <span class="sr-only">(current)</span></a></li>
	                           <li><a href="#">Link</a></li>
	                           <li><a href="#">Link</a></li>
	                           <li><a href="#">Link</a></li>
	                           <li><a href="#">Link</a></li>
	                           <li><a href="#">Link</a></li>
	
	                       </ul>
	                   </div><!-- /.navbar-collapse -->
	               </div><!-- /.container-fluid -->
	           </nav>
	
	       </div>
	
	       <!--轮播图-->
	       <div class="row">
	           <div id="carousel-example-generic" class="carousel slide" data-ride="carousel">
	               <!-- Indicators -->
	               <ol class="carousel-indicators">
	                   <li data-target="#carousel-example-generic" data-slide-to="0" class="active"></li>
	                   <li data-target="#carousel-example-generic" data-slide-to="1"></li>
	                   <li data-target="#carousel-example-generic" data-slide-to="2"></li>
	               </ol>
	
	               <!-- Wrapper for slides -->
	               <div class="carousel-inner" role="listbox">
	                   <div class="item active">
	                       <img src="img/banner_1.jpg" alt="...">
	                   </div>
	                   <div class="item">
	                       <img src="img/banner_2.jpg" alt="...">
	                   </div>
	                   <div class="item">
	                       <img src="img/banner_3.jpg" alt="...">
	                   </div>
	
	               </div>
	
	               <!-- Controls -->
	               <a class="left carousel-control" href="#carousel-example-generic" role="button" data-slide="prev">
	                   <span class="glyphicon glyphicon-chevron-left" aria-hidden="true"></span>
	                   <span class="sr-only">Previous</span>
	               </a>
	               <a class="right carousel-control" href="#carousel-example-generic" role="button" data-slide="next">
	                   <span class="glyphicon glyphicon-chevron-right" aria-hidden="true"></span>
	                   <span class="sr-only">Next</span>
	               </a>
	           </div>


​	
​	

	       </div>
	
	   </header>
	   <!-- 2.主体部分-->
	   <div class="container">
	        <div class="row jx">
	            <img src="img/icon_5.jpg">
	            <span>黑马精选</span>
	        </div>
	
	       <div class="row paddtop">
	           <div class="col-md-3">
	                <div class="thumbnail">
	                    <img src="img/jiangxuan_3.jpg" alt="">
	                    <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                    <font color="red">&yen; 699</font>
	                </div>
	           </div>
	           <div class="col-md-3">
	               <div class="thumbnail">
	                   <img src="img/jiangxuan_3.jpg" alt="">
	                   <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                   <font color="red">&yen; 699</font>
	               </div>
	
	           </div>
	           <div class="col-md-3">
	
	               <div class="thumbnail">
	                   <img src="img/jiangxuan_3.jpg" alt="">
	                   <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                   <font color="red">&yen; 699</font>
	               </div>
	           </div>
	           <div class="col-md-3">
	
	               <div class="thumbnail">
	                   <img src="img/jiangxuan_3.jpg" alt="">
	                   <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                   <font color="red">&yen; 699</font>
	               </div>
	           </div>


​	

	       </div>
	       <div class="row jx">
	           <img src="img/icon_6.jpg">
	           <span>国内游</span>
	       </div>
	       <div class="row paddtop">
	           <div class="col-md-4">
	               <img src="img/guonei_1.jpg">
	           </div>
	           <div class="col-md-8">
	               <div class="row">
	                   <div class="col-md-4">
	                       <div class="thumbnail">
	                           <img src="img/jiangxuan_3.jpg" alt="">
	                           <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                           <font color="red">&yen; 699</font>
	                       </div>
	                   </div>
	                   <div class="col-md-4">
	                       <div class="thumbnail">
	                           <img src="img/jiangxuan_3.jpg" alt="">
	                           <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                           <font color="red">&yen; 699</font>
	                       </div>
	
	                   </div>
	                   <div class="col-md-4">
	
	                       <div class="thumbnail">
	                           <img src="img/jiangxuan_3.jpg" alt="">
	                           <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                           <font color="red">&yen; 699</font>
	                       </div>
	                   </div>
	
	               </div>
	               <div class="row">
	                   <div class="col-md-4">
	                       <div class="thumbnail">
	                           <img src="img/jiangxuan_3.jpg" alt="">
	                           <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                           <font color="red">&yen; 699</font>
	                       </div>
	                   </div>
	                   <div class="col-md-4">
	                       <div class="thumbnail">
	                           <img src="img/jiangxuan_3.jpg" alt="">
	                           <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                           <font color="red">&yen; 699</font>
	                       </div>
	
	                   </div>
	                   <div class="col-md-4">
	
	                       <div class="thumbnail">
	                           <img src="img/jiangxuan_3.jpg" alt="">
	                           <p>上海直飞三亚5天4晚自由行(春节预售+亲子/蜜月/休闲游首选+豪华酒店任选+接送机)</p>
	                           <font color="red">&yen; 699</font>
	                       </div>
	                   </div>


​	

	               </div>
	
	           </div>
	
	       </div>
	   </div>
	   <!-- 3.页脚部分-->
	   <footer class="container-fluid">
	       <div class="row">
	           <img src="img/footer_service.png" class="img-responsive">
	       </div>
	       <div class="row company">
	           江苏传智播客教育科技股份有限公司 版权所有Copyright 2006-2018, All Rights Reserved 苏ICP备16007882
	       </div>
	
	   </footer>


​	

	</body>
	</html>



------





# 12.XML笔记

	1. XML
		1. 概念
		2. 语法
		3. 解析

## XML：

```xml
1. 概念：Extensible Markup Language 可扩展标记语言
	* 可扩展：标签都是自定义的。 <user>  <student>

	* 功能
		* 存储数据
			1. 配置文件
			2. 在网络中传输
	* xml与html的区别
		1. xml标签都是自定义的，html标签是预定义。
		2. xml的语法严格，html语法松散
		3. xml是存储数据的，html是展示数据
        始于展示数据,终于存储数据!

	* w3c:万维网联盟

2. 语法：
	* 基本语法：
		1. xml文档的后缀名 .xml
		2. xml第一行必须定义为文档声明
		3. xml文档中有且仅有一个根标签
		4. 属性值必须使用引号(单双都可)引起来
		5. 标签必须正确关闭
		6. xml标签名称区分大小写
	* 快速入门：
		<?xml version='1.0' ?>
		<users>
			<user id='1'>
				<name>zhangsan</name>
				<age>23</age>
				<gender>male</gender>
				<br/>
			</user>
			
			<user id='2'>
				<name>lisi</name>
				<age>24</age>
				<gender>female</gender>
			</user>
		</users>
		
	* 组成部分：
		1. 文档声明
			1. 格式：<?xml 属性列表 ?>
			2. 属性列表：
				* version：版本号，必须的属性
				* encoding：编码方式。告知解析引擎当前文档使用的字符集，默认值：ISO-8859-1
				* standalone：是否独立
					* 取值：
						* yes：不依赖其他文件
						* no：依赖其他文件
		2. 指令(了解)：结合css的
			* <?xml-stylesheet type="text/css" href="a.css" ?>
		3. 标签：标签名称自定义的
			* 规则：
				* 名称可以包含字母、数字以及其他的字符 
				* 名称不能以数字或者标点符号开始 
				* 名称不能以字母 xml（或者 XML、Xml 等等）开始 
				* 名称不能包含空格 

		4. 属性：
			id属性值唯一
		5. 文本：
			* CDATA区：在该区域中的数据会被原样展示
				* 格式：  <![CDATA[ 数据 ]]>
```


​		* 约束：规定xml文档的书写规则
​			* 作为框架的使用者(程序员)：
​				1. 能够在xml中引入约束文档
​				2. 能够简单的读懂约束文档
​			

			* 分类：
				1. DTD:一种简单的约束技术
				2. Schema:一种复杂的约束技术


			* DTD：
				* 引入dtd文档到xml文档中
					* 内部dtd：将约束规则定义在xml文档中
					* 外部dtd：将约束的规则定义在外部的dtd文件中
						* 本地：<!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">
						* 网络：<!DOCTYPE 根标签名 PUBLIC "dtd文件名字" "dtd文件的位置URL">


			* Schema:
				* 引入：
					1.填写xml文档的根元素
					2.引入xsi前缀.  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
					3.引入xsd文件命名空间.  xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd"
					4.为每一个xsd约束声明一个前缀,作为标识  xmlns="http://www.itcast.cn/xml" 
	
				<students   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
					xmlns="http://www.itcast.cn/xml"
					xsi:schemaLocation="http://www.itcast.cn/xml  student.xsd">


​	

	3. 解析：操作xml文档，将文档中的数据读取到内存中
		* 操作xml文档
			1. 解析(读取)：将文档中的数据读取到内存中
			2. 写入：将内存中的数据保存到xml文档中。持久化的存储
	
		* 解析xml的方式：
			1. DOM：将标记语言文档一次性加载进内存，在内存中形成一颗dom树
				* 优点：操作方便，可以对文档进行CRUD的所有操作
				* 缺点：占内存
			2. SAX：逐行读取，基于事件驱动的。
				* 优点：不占内存。
				* 缺点：只能读取，不能增删改


​		* xml常见的解析器：
​			1. JAXP：sun公司提供的解析器，支持dom和sax两种思想
​			2. DOM4J：一款非常优秀的解析器
​			3. Jsoup：jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。
​			4. PULL：Android操作系统内置的解析器，sax方式的。


```java
	* Jsoup：jsoup 是一款Java 的HTML解析器，可直接解析某个URL地址、HTML文本内容。它提供了一套非常省力的API，可通过DOM，CSS以及类似于jQuery的操作方法来取出和操作数据。
		* 快速入门：
			* 步骤：
				1. 导入jar包
				2. 获取Document对象
				3. 获取对应的标签Element对象
				4. 获取数据

		* 代码：
			 //2.1获取student.xml的path
	        String path = JsoupDemo1.class.getClassLoader().getResource("student.xml").getPath();
	        //2.2解析xml文档，加载文档进内存，获取dom树--->Document
	        Document document = Jsoup.parse(new File(path), "utf-8");
	        //3.获取元素对象 Element
	        Elements elements = document.getElementsByTag("name");
	
	        System.out.println(elements.size());
	        //3.1获取第一个name的Element对象
	        Element element = elements.get(0);
	        //3.2获取数据
	        String name = element.text();
	        System.out.println(name);

	* 对象的使用：
		1. Jsoup：工具类，可以解析html或xml文档，返回Document
			* parse：解析html或xml文档，返回Document
				* parse​(File in, String charsetName)：解析xml或html文件的。
				* parse​(String html)：解析xml或html字符串
				* parse​(URL url, int timeoutMillis)：通过网络路径获取指定的html或xml的文档对象
		2. Document：文档对象。代表内存中的dom树
			* 获取Element对象
				* getElementById​(String id)：根据id属性值获取唯一的element对象
				* getElementsByTag​(String tagName)：根据标签名称获取元素对象集合
				* getElementsByAttribute​(String key)：根据属性名称获取元素对象集合
				* getElementsByAttributeValue​(String key, String value)：根据对应的属性名和属性值获取元素对象集合
		3. Elements：元素Element对象的集合。可以当做 ArrayList<Element>来使用
		4. Element：元素对象
			1. 获取子元素对象
				* getElementById​(String id)：根据id属性值获取唯一的element对象
				* getElementsByTag​(String tagName)：根据标签名称获取元素对象集合
				* getElementsByAttribute​(String key)：根据属性名称获取元素对象集合
				* getElementsByAttributeValue​(String key, String value)：根据对应的属性名和属性值获取元素对象集合

			2. 获取属性值
				* String attr(String key)：根据属性名称获取属性值
			3. 获取文本内容
				* String text():获取文本内容
				* String html():获取标签体的所有内容(包括字标签的字符串内容)
		5. Node：节点对象
			* 是Document和Element的父类
```


​			

```java
	* 快捷查询方式：
		1. selector:选择器
			* 使用的方法：Elements	select​(String cssQuery)
				* 语法：参考Selector类中定义的语法
		2. XPath：XPath即为XML路径语言，它是一种用来确定XML（标准通用标记语言的子集）文档中某部分位置的语言
			* 使用Jsoup的Xpath需要额外导入jar包。
			* 查询w3cshool参考手册，使用xpath的语法完成查询
			* 代码：
				//1.获取student.xml的path
		        String path = JsoupDemo6.class.getClassLoader().getResource("student.xml").getPath();
		        //2.获取Document对象
		        Document document = Jsoup.parse(new File(path), "utf-8");
		
		        //3.根据document对象，创建JXDocument对象
		        JXDocument jxDocument = new JXDocument(document);
		
		        //4.结合xpath语法查询
		        //4.1查询所有student标签
		        List<JXNode> jxNodes = jxDocument.selN("//student");
		        for (JXNode jxNode : jxNodes) {
		            System.out.println(jxNode);
		        }
		
		        System.out.println("--------------------");
		
		        //4.2查询所有student标签下的name标签
		        List<JXNode> jxNodes2 = jxDocument.selN("//student/name");
		        for (JXNode jxNode : jxNodes2) {
		            System.out.println(jxNode);
		        }
		
		        System.out.println("--------------------");
		
		        //4.3查询student标签下带有id属性的name标签
		        List<JXNode> jxNodes3 = jxDocument.selN("//student/name[@id]");
		        for (JXNode jxNode : jxNodes3) {
		            System.out.println(jxNode);
		        }
		        System.out.println("--------------------");
		        //4.4查询student标签下带有id属性的name标签 并且id属性值为itcast
		
		        List<JXNode> jxNodes4 = jxDocument.selN("//student/name[@id='itcast']");
		        for (JXNode jxNode : jxNodes4) {
		            System.out.println(jxNode);
		        }
```





------





# 13.Tomcat&Servlet

	1. web相关概念回顾
	2. web服务器软件：Tomcat
	3. Servlet入门学习




## web相关概念回顾

```xml
1. 软件架构
	1. C/S：客户端/服务器端
	2. B/S：浏览器/服务器端

2. 资源分类
	1. 静态资源：所有用户访问后，得到的结果都是一样的，称为静态资源.静态资源可以直接被浏览器解析
		* 如： html,css,JavaScript
	2. 动态资源:每个用户访问相同资源后，得到的结果可能不一样。称为动态资源。动态资源被访问后，需要先转换为静态资源，在返回给浏览器
		* 如：servlet/jsp,php,asp....
```




	3. 网络通信三要素
		1. IP：电子设备(计算机)在网络中的唯一标识。
		2. 端口：应用程序在计算机中的唯一标识。 0~65536 , 尽量不要使用1024 之内的端口.
		3. 传输协议：规定了数据传输的规则
			1. 基础协议：
				1. tcp:安全协议，三次握手。 速度稍慢
				2. udp：不安全协议。 速度快

## web服务器软件：

	* 服务器：安装了服务器软件的计算机
	* 服务器软件：接收用户的请求，处理请求，做出响应
	* web服务器软件：接收用户的请求，处理请求，做出响应。
		* 在web服务器软件中，可以部署web项目，让用户通过浏览器来访问这些项目
		* web容器


	* 常见的java相关的web服务器软件：
		* webLogic：oracle公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
		* webSphere：IBM公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
		* JBOSS：JBOSS公司的，大型的JavaEE服务器，支持所有的JavaEE规范，收费的。
		* Tomcat：Apache基金组织，中小型的JavaEE服务器，仅仅支持少量的JavaEE规范servlet/jsp。开源的，免费的。


	* JavaEE：Java语言在企业级开发中使用的技术规范的总和，一共规定了13项大的规范
	
	* Tomcat：web服务器软件
		1. 下载：http://tomcat.apache.org/
		2. 安装：解压压缩包即可。
			* 注意：安装目录建议不要有中文和空格
		3. 卸载：删除目录就行了
		4. 启动：
			* bin/startup.bat ,双击运行该文件即可
			* 访问：浏览器输入：http://localhost:8080 回车访问自己
							  http://别人的ip:8080 访问别人
			
			* 可能遇到的问题：
				1. 黑窗口一闪而过：
					* 原因： 没有正确配置JAVA_HOME环境变量
					* 解决方案：正确配置JAVA_HOME环境变量
	
				2. 启动报错：
					1. 暴力：找到占用的端口号，并且找到对应的进程，杀死该进程
						* netstat -ano
					2. 温柔：修改自身的端口号
						* conf/server.xml
						* <Connector port="8888" protocol="HTTP/1.1"
			               connectionTimeout="20000"
			               redirectPort="8445" />
						* 一般会将tomcat的默认端口号修改为80。80端口号是http协议的默认端口号。
							* 好处：在访问时，就不用输入端口号
		5. 关闭：
			1. 正常关闭：
				* bin/shutdown.bat
				* ctrl+c
			2. 强制关闭：
				* 点击启动窗口的×
		6. 配置:
			* 部署项目的方式：
				1. 直接将项目放到webapps目录下即可。
					* /hello：项目的访问路径-->虚拟目录
					* 简化部署：将项目打成一个war包，再将war包放置到webapps目录下。
						* war包会自动解压缩
	
				2. 配置conf/server.xml文件
					在<Host>标签体中配置
					<Context docBase="D:\hello" path="/hehe" />
					* docBase:项目存放的路径
					* path：虚拟目录
	
				3. 在conf\Catalina\localhost创建任意名称的xml文件。在文件中编写
					<Context docBase="D:\hello" />
					* 虚拟目录：xml文件的名称
			
			* 静态项目和动态项目：
				* 目录结构
					* java动态项目的目录结构：
						-- 项目的根目录
							-- WEB-INF目录：
								-- web.xml：web项目的核心配置文件
								-- classes目录：放置字节码文件的目录
								-- lib目录：放置依赖的jar包


			* 将Tomcat集成到IDEA中，并且创建JavaEE的项目，部署项目。





## Servlet：  server applet

	* 概念：运行在服务器端的小程序
		* Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则。
		* 将来我们自定义一个类，实现Servlet接口，复写方法。


	* 快速入门：
		1. 创建JavaEE项目
		2. 定义一个类，实现Servlet接口
			* public class ServletDemo1 implements Servlet
		3. 实现接口中的抽象方法
		4. 配置Servlet
			 在web.xml中配置：
		    <!--配置Servlet -->
		    <servlet>
		        <servlet-name>demo1</servlet-name>
		        <servlet-class>cn.itcast.web.servlet.ServletDemo1</servlet-class>
		    </servlet>
		
		    <servlet-mapping>
		        <servlet-name>demo1</servlet-name>
		        <url-pattern>/demo1</url-pattern>
		    </servlet-mapping>
	
	* 执行原理：
		1. 当服务器接受到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径
		2. 查找web.xml文件，是否有对应的<url-pattern>标签体内容。
		3. 如果有，则在找到对应的<servlet-class>全类名
		4. tomcat会将字节码文件加载进内存，并且创建其对象 // Class.forName() , Class.getNewInstance() 获取实例对象
		5. 调用其方法  		// Class.Service() 
	
	* Servlet中的生命周期方法：
		1. 被创建：执行init方法，只执行一次
			* Servlet什么时候被创建？
				* 默认情况下，第一次被访问时，Servlet被创建
				* 可以配置执行Servlet的创建时机。
					* 在<servlet>标签下配置
						1. 第一次被访问时，创建
	                		* <load-on-startup>的值为负数
			            2. 在服务器启动时，创建
			                * <load-on-startup>的值为0或正整数


			* Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的
				* 多个用户同时访问时，可能存在线程安全问题。
				* 解决：尽量不要在Servlet中定义成员变量。即使定义了成员变量，也不要对修改值
	
		2. 提供服务：执行service方法，执行多次
			* 每次访问Servlet时，Service方法都会被调用一次。
		3. 被销毁：执行destroy方法，只执行一次
			* Servlet被销毁时执行。服务器关闭时，Servlet被销毁
			* 只有服务器正常关闭时，才会执行destroy方法。
			* destroy方法在Servlet被销毁之前执行，一般用于释放资源
	
	* Servlet3.0：
		* 好处：
			* 支持注解配置。可以不需要web.xml了。
	
		* 步骤：
			1. 创建JavaEE项目，选择Servlet的版本3.0以上，可以不创建web.xml
			2. 定义一个类，实现Servlet接口
			3. 复写方法
			4. 在类上使用@WebServlet注解，进行配置
				* @WebServlet("资源路径")          //只需要配置这一个值 urlPatterns={"" , "" ,"" ...} 属性值就够了


				@Target({ElementType.TYPE})
				@Retention(RetentionPolicy.RUNTIME)
				@Documented
				public @interface WebServlet {
				    String name() default "";//相当于<Servlet-name>
				
				    String[] value() default {};//代表urlPatterns()属性配置
				
				    String[] urlPatterns() default {};//相当于<url-pattern>
				
				    int loadOnStartup() default -1;//相当于<load-on-startup>
				
				    WebInitParam[] initParams() default {};
				
				    boolean asyncSupported() default false;
				
				    String smallIcon() default "";
				
				    String largeIcon() default "";
				
				    String description() default "";
				
				    String displayName() default "";
				}



## IDEA与tomcat的相关配置

	1. IDEA会为每一个tomcat部署的项目单独建立一份配置文件
		* 查看控制台的log：Using CATALINA_BASE:   "C:\Users\fqy\.IntelliJIdea2018.1\system\tomcat\_itcast"
	
	2. 工作空间项目    和     tomcat部署的web项目
		* tomcat真正访问的是“tomcat部署的web项目”，"tomcat部署的web项目"对应着"工作空间项目" 的web目录下的所有资源
		* WEB-INF目录下的资源不能被浏览器直接访问。
	3. 断点调试：使用"小虫子"启动 dubug 启动





------





# 14.Servlet&HTTP&Request：

	1. Servlet
	2. HTTP协议
	3. Request

​	

## Servlet：

	1. 概念
	2. 步骤
	3. 执行原理
	4. 生命周期
	5. Servlet3.0 注解配置
	6. Servlet的体系结构	
		Servlet -- 接口
			|
		GenericServlet -- 抽象类
			|
		HttpServlet  -- 抽象类
	
		* GenericServlet：将Servlet接口中其他的方法做了默认空实现，只将service()方法作为抽象方法
			* 将来定义Servlet类时，可以继承GenericServlet，实现service()方法即可
	
		* HttpServlet：对http协议的*一种封装，简化操作
			1. 定义类继承HttpServlet 接口
			2. 复写doGet/doPost方法 : 在HTTPServlet 接口父类中的Service() 方法会根据请求方式来调用对应的doGet() 或者 doPost() 方法
	
	7. Servlet相关配置
		1. urlpartten:Servlet访问路径
			1. 一个Servlet可以定义多个访问路径 ： @WebServlet({"/d4","/dd4","/ddd4"})
			2. 路径定义规则：
				1. /xxx：路径匹配
				2. /xxx/xxx:多层路径，目录结构
				3. *.do：扩展名匹配

## HTTP：

	* 概念：Hyper Text Transfer Protocol 超文本传输协议
		* 传输协议：定义了，客户端和服务器端通信时，发送数据的格式
		* 特点：
			1. 基于TCP/IP的高级协议
			2. 默认端口号:80
			3. 基于请求/响应模型的:一次请求对应一次响应
			4. 无状态的：每次请求之间相互独立，不能交互数据
	
		* 历史版本：
			* 1.0：每一次请求响应都会建立新的连接
			* 1.1：复用连接: 使用完连接后并不会立即销毁Resquest 请求, 会进行等待.
	
	* 请求消息数据格式
		1. 请求行
			请求方式 请求url 请求协议/版本
			GET /login.html	HTTP/1.1
	
			* 请求方式：
				* HTTP协议有7中请求方式，常用的有2种
					* GET：
						1. 请求参数在请求行中，在url后。
						2. 请求的url长度有限制的
						3. 不太安全
					* POST：
						1. 请求参数在请求体中
						2. 请求的url长度没有限制的
						3. 相对安全
		2. 请求头：客户端浏览器告诉服务器一些信息
			请求头名称: 请求头值
			* 常见的请求头：
				1. User-Agent：浏览器告诉服务器，我访问你使用的浏览器版本信息
					* 可以在服务器端获取该头的信息，解决浏览器的兼容性问题
	
				2. Referer：http://localhost/login.html
					* 告诉服务器，我(当前请求)从哪里来？
						* 作用：
							1. 防盗链：
							2. 统计工作：
		3. 请求空行
			空行，就是用于分割POST请求的请求头，和请求体的。
		4. 请求体(正文)：
			* 封装POST请求消息的请求参数的
	
		* 字符串格式：
			POST /login.html	HTTP/1.1
			Host: localhost
			User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:60.0) Gecko/20100101 Firefox/60.0
			Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
			Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
			Accept-Encoding: gzip, deflate
			Referer: http://localhost/login.html
			Connection: keep-alive
			Upgrade-Insecure-Requests: 1
			
			username=zhangsan	


	* 响应消息数据格式



## Request：

	1. request00对象和response对象的原理
		1. request和response对象是由服务器创建的。我们来使用它们
		2. request对象是来获取请求消息，response对象是来设置响应消息
	
	2. request对象继承体系结构：	
		ServletRequest		--	接口
			|	继承
		HttpServletRequest	-- 接口
			|	实现
		org.apache.catalina.connector.RequestFacade 类(tomcat)
	
	3. request功能：
		1. 获取请求消息数据
			1. 获取请求行数据
				* GET /day14/demo1?name=zhangsan HTTP/1.1
				* 方法：
					1. 获取请求方式 ：GET
						* String getMethod()  
					2. (*)获取虚拟目录：/day14
						* String getContextPath()
					3. 获取Servlet路径: /demo1
						* String getServletPath()
					4. 获取get方式请求参数：name=zhangsan
						* String getQueryString()
					5. (*)获取请求URI：/day14/demo1
						* String getRequestURI():		/day14/demo1
						* StringBuffer getRequestURL()  :http://localhost/day14/demo1
	
						* URL:统一资源定位符 ： http://localhost/day14/demo1	中华人民共和国
						* URI：统一资源标识符 : /day14/demo1					共和国
					
					6. 获取协议及版本：HTTP/1.1
						* String getProtocol()
	
					7. 获取客户机的IP地址：
						* String getRemoteAddr()
					
			2. 获取请求头数据
				* 方法：
					* (*)String getHeader(String name):通过请求头的名称获取请求头的值
					* Enumeration<String> getHeaderNames():获取所有的请求头名称
				
			3. 获取请求体数据:
				* 请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数
				* 步骤：
					1. 获取流对象
						*  BufferedReader getReader()：获取字符输入流，只能操作字符数据
						*  ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据
							* 在文件上传知识点后讲解
	
					2. 再从流对象中拿数据

​				









		2. 其他功能：
			1. 获取请求参数通用方式：不论get还是post请求方式都可以使用下列方法来获取请求参数
				1. String getParameter(String name):根据参数名称获取参数值    username=zs&password=123
				2. String[] getParameterValues(String name):根据参数名称获取参数值的数组  hobby=xx&hobby=game
				3. Enumeration<String> getParameterNames():获取所有请求的参数名称
				4. Map<String,String[]> getParameterMap():获取所有参数的map集合
	
				* 中文乱码问题：
					* get方式：tomcat 8 已经将get方式乱码问题解决了
					* post方式：会乱码
						* 解决：在获取参数前，设置request的编码request.setCharacterEncoding("utf-8");


​					

			2. 请求转发：一种在服务器内部的资源跳转方式
				1. 步骤：
					1. 通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)
					2. 使用RequestDispatcher对象来进行转发：forward(ServletRequest request, ServletResponse response) 
	
				2. 特点：
					1. 浏览器地址栏路径不发生变化
					2. 只能转发到当前服务器内部资源中。不能访问服务器外部资源
					3. 转发是一次请求 : 这种请求转发的方式是 相当于将请求源的Request 和 Response 对象传递给一个 目标Servlet , 并且调用 service(req , resp) 函数, 所以相当与是在浏览器的单独的访问一次目标Servlet , 所以 req 和 resp 对象的来源不是源Servlet 而是 目标Servlet 自己的 


			3. 共享数据：
				* 域对象：一个有作用范围的对象，可以在范围内共享数据
				* request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据
				* 方法：
					1. void setAttribute(String name,Object obj):存储数据
					2. Object getAttitude(String name):通过键获取值
					3. void removeAttribute(String name):通过键移除键值对
	
			4. 获取ServletContext：
				* ServletContext getServletContext()



## 案例：用户登录

	* 用户登录案例需求：
		1.编写login.html登录页面
			username & password 两个输入框
		2.使用Druid数据库连接池技术,操作mysql，day14数据库中user表
		3.使用JdbcTemplate技术封装JDBC
		4.登录成功跳转到SuccessServlet展示：登录成功！用户名,欢迎您
		5.登录失败跳转到FailServlet展示：登录失败，用户名或密码错误


	* 分析
	
	* 开发步骤
		1. 创建项目，导入html页面，配置文件，jar包
		2. 创建数据库环境
			CREATE DATABASE day14;
			USE day14;
			CREATE TABLE USER(
			
				id INT PRIMARY KEY AUTO_INCREMENT,
				username VARCHAR(32) UNIQUE NOT NULL,
				PASSWORD VARCHAR(32) NOT NULL
			);
	
		3. 创建包cn.itcast.domain,创建类User
			package cn.itcast.domain;
			/**
			 * 用户的实体类
			 */
			public class User {
			
			    private int id;
			    private String username;
			    private String password;


​			

			    public int getId() {
			        return id;
			    }
			
			    public void setId(int id) {
			        this.id = id;
			    }
			
			    public String getUsername() {
			        return username;
			    }
			
			    public void setUsername(String username) {
			        this.username = username;
			    }
			
			    public String getPassword() {
			        return password;
			    }
			
			    public void setPassword(String password) {
			        this.password = password;
			    }
			
			    @Override
			    public String toString() {
			        return "User{" +
			                "id=" + id +
			                ", username='" + username + '\'' +
			                ", password='" + password + '\'' +
			                '}';
			    }
			}
		4. 创建包cn.itcast.util,编写工具类JDBCUtils
			package cn.itcast.util;
	
			import com.alibaba.druid.pool.DruidDataSourceFactory;
			
			import javax.sql.DataSource;
			import javax.xml.crypto.Data;
			import java.io.IOException;
			import java.io.InputStream;
			import java.sql.Connection;
			import java.sql.SQLException;
			import java.util.Properties;
			
			/**
			 * JDBC工具类 使用Durid连接池
			 */
			public class JDBCUtils {
			
			    private static DataSource ds ;
			
			    static {
			
			        try {
			            //1.加载配置文件
			            Properties pro = new Properties();
			            //使用ClassLoader加载配置文件，获取字节输入流
			            InputStream is = JDBCUtils.class.getClassLoader().getResourceAsStream("druid.properties");
			            pro.load(is);
			
			            //2.初始化连接池对象
			            ds = DruidDataSourceFactory.createDataSource(pro);
			
			        } catch (IOException e) {
			            e.printStackTrace();
			        } catch (Exception e) {
			            e.printStackTrace();
			        }
			    }
			
			    /**
			     * 获取连接池对象
			     */
			    public static DataSource getDataSource(){
			        return ds;
			    }


​			

			    /**
			     * 获取连接Connection对象
			     */
			    public static Connection getConnection() throws SQLException {
			        return  ds.getConnection();
			    }
			}
		5. 创建包cn.itcast.dao,创建类UserDao,提供login方法
			
			package cn.itcast.dao;
	
			import cn.itcast.domain.User;
			import cn.itcast.util.JDBCUtils;
			import org.springframework.dao.DataAccessException;
			import org.springframework.jdbc.core.BeanPropertyRowMapper;
			import org.springframework.jdbc.core.JdbcTemplate;
			
			/**
			 * 操作数据库中User表的类
			 */
			public class UserDao {
			
			    //声明JDBCTemplate对象共用
			    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());
			
			    /**
			     * 登录方法
			     * @param loginUser 只有用户名和密码
			     * @return user包含用户全部数据,没有查询到，返回null
			     */
			    public User login(User loginUser){
			        try {
			            //1.编写sql
			            String sql = "select * from user where username = ? and password = ?";
			            //2.调用query方法
			            User user = template.queryForObject(sql,
			                    new BeanPropertyRowMapper<User>(User.class),
			                    loginUser.getUsername(), loginUser.getPassword());


​			

			            return user;
			        } catch (DataAccessException e) {
			            e.printStackTrace();//记录日志
			            return null;
			        }
			    }
			}
		
		6. 编写cn.itcast.web.servlet.LoginServlet类
			package cn.itcast.web.servlet;
	
			import cn.itcast.dao.UserDao;
			import cn.itcast.domain.User;
			
			import javax.servlet.ServletException;
			import javax.servlet.annotation.WebServlet;
			import javax.servlet.http.HttpServlet;
			import javax.servlet.http.HttpServletRequest;
			import javax.servlet.http.HttpServletResponse;
			import java.io.IOException;


​			

			@WebServlet("/loginServlet")
			public class LoginServlet extends HttpServlet {


​			

			    @Override
			    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			        //1.设置编码
			        req.setCharacterEncoding("utf-8");
			        //2.获取请求参数
			        String username = req.getParameter("username");
			        String password = req.getParameter("password");
			        //3.封装user对象
			        User loginUser = new User();
			        loginUser.setUsername(username);
			        loginUser.setPassword(password);
			
			        //4.调用UserDao的login方法
			        UserDao dao = new UserDao();
			        User user = dao.login(loginUser);
			
			        //5.判断user
			        if(user == null){
			            //登录失败
			            req.getRequestDispatcher("/failServlet").forward(req,resp);
			        }else{
			            //登录成功
			            //存储数据
			            req.setAttribute("user",user);
			            //转发
			            req.getRequestDispatcher("/successServlet").forward(req,resp);
			        }
			
			    }
			
			    @Override
			    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			        this.doGet(req,resp);
			    }
			}
	
		7. 编写FailServlet和SuccessServlet类
			@WebServlet("/successServlet")
			public class SuccessServlet extends HttpServlet {
			    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			        //获取request域中共享的user对象
			        User user = (User) request.getAttribute("user");
			
			        if(user != null){
			            //给页面写一句话
			
			            //设置编码
			            response.setContentType("text/html;charset=utf-8");
			            //输出
			            response.getWriter().write("登录成功！"+user.getUsername()+",欢迎您");
			        }


​			

			    }		


			@WebServlet("/failServlet")
			public class FailServlet extends HttpServlet {
			    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			        //给页面写一句话
			
			        //设置编码
			        response.setContentType("text/html;charset=utf-8");
			        //输出
			        response.getWriter().write("登录失败，用户名或密码错误");
			
			    }
			
			    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
			        this.doPost(request,response);
			    }
			}



		8. login.html中form表单的action路径的写法
			* 虚拟目录+Servlet的资源路径
	
		9. BeanUtils工具类，简化数据封装
			* 用于封装JavaBean的
			1. JavaBean：标准的Java类 , domain 包下存放javabean 实体类
				1. 要求：
					1. 类必须被public修饰
					2. 必须提供空参的构造器
					3. 成员变量必须使用private修饰
					4. 提供公共setter和getter方法
				2. 功能：封装数据


			2. 概念：
				成员变量：
				属性：setter和getter方法截取后的产物
					例如：getUsername() --> Username--> username


			3. 方法：
				1. setProperty()
				2. getProperty()
				3. populate(Object obj , Map map):将map集合的键值对信息，封装到对应的JavaBean对象中





------





# 15.Coocie和Session

	1. 会话技术
		1. Cookie
		2. Session
	2. JSP：入门学习



## 会话技术

	1. 会话：一次会话中包含多次请求和响应。
		* 一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止
	2. 功能：在一次会话的范围内的多次请求间，共享数据
	3. 方式：
		1. 客户端会话技术：Cookie
		2. 服务器端会话技术：Session

## Cookie：

	1. 概念：客户端会话技术，将数据保存到客户端
	
	2. 快速入门：
		* 使用步骤：
			1. 创建Cookie对象，绑定数据
				* new Cookie(String name, String value) 
			2. 发送Cookie对象
				* response.addCookie(Cookie cookie) 
			3. 获取Cookie，拿到数据
				* Cookie[]  request.getCookies()  


	3. 实现原理
		* 基于响应头set-cookie和请求头cookie实现
	
	4. cookie的细节
		1. 一次可不可以发送多个cookie?
			* 可以
			* 可以创建多个Cookie对象，使用response调用多次addCookie方法发送cookie即可。
		2. cookie在浏览器中保存多长时间？
			1. 默认情况下，当浏览器关闭后，Cookie数据被销毁
			2. 持久化存储：
				* setMaxAge(int seconds)
					1. 正数：将Cookie数据写到硬盘的文件中。持久化存储。并指定cookie存活时间，时间到后，cookie文件自动失效
					2. 负数：默认值
					3. 零：删除cookie信息
		3. cookie能不能存中文？
			* 在tomcat 8 之前 cookie中不能直接存储中文数据。
				* 需要将中文数据转码---一般采用URL编码(%E3)
			* 在tomcat 8 之后，cookie支持中文数据。特殊字符还是不支持，建议使用URL编码存储，URL解码解析
		4. cookie共享问题？
			1. 假设在一个tomcat服务器中，部署了多个web项目，那么在这些web项目中cookie能不能共享？
				* 默认情况下cookie不能共享
	
				* setPath(String path):设置cookie的获取范围。默认情况下，设置当前的虚拟目录
					* 如果要共享，则可以将path设置为"/"


​			

			2. 不同的tomcat服务器间cookie共享问题？
				* setDomain(String path):如果设置一级域名相同，那么多个服务器之间cookie可以共享
					* setDomain(".baidu.com"),那么tieba.baidu.com和news.baidu.com中cookie可以共享


	5. Cookie的特点和作用
		1. cookie存储数据在客户端浏览器
		2. 浏览器对于单个cookie 的大小有限制(4kb) 以及 对同一个域名下的总cookie数量也有限制(20个)
	
		* 作用：
			1. cookie一般用于存储少量的不太敏感的数据
			2. 在不登录的情况下，完成服务器对客户端的身份识别
	
	6. 案例：记住上一次访问时间
		1. 需求：
			1. 访问一个Servlet，如果是第一次访问，则提示：您好，欢迎您首次访问。
			2. 如果不是第一次访问，则提示：欢迎回来，您上次访问时间为:显示时间字符串
	
		2. 分析：
			1. 可以采用Cookie来完成
			2. 在服务器中的Servlet判断是否有一个名为lastTime的cookie
				1. 有：不是第一次访问
					1. 响应数据：欢迎回来，您上次访问时间为:2018年6月10日11:50:20
					2. 写回Cookie：lastTime=2018年6月10日11:50:01
				2. 没有：是第一次访问
					1. 响应数据：您好，欢迎您首次访问
					2. 写回Cookie：lastTime=2018年6月10日11:50:01
	
		3. 代码实现：
			package cn.itcast.cookie;
	
			import javax.servlet.ServletException;
			import javax.servlet.annotation.WebServlet;
			import javax.servlet.http.Cookie;
			import javax.servlet.http.HttpServlet;
			import javax.servlet.http.HttpServletRequest;
			import javax.servlet.http.HttpServletResponse;
			import java.io.IOException;
			import java.net.URLDecoder;
			import java.net.URLEncoder;
			import java.text.SimpleDateFormat;
			import java.util.Date;


		@WebServlet("/cookieTest")
		public class CookieTest extends HttpServlet {
		    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		        //设置响应的消息体的数据格式以及编码
		        response.setContentType("text/html;charset=utf-8");
		
		        //1.获取所有Cookie
		        Cookie[] cookies = request.getCookies();
		        boolean flag = false;//没有cookie为lastTime
		        //2.遍历cookie数组
		        if(cookies != null && cookies.length > 0){
		            for (Cookie cookie : cookies) {
		                //3.获取cookie的名称
		                String name = cookie.getName();
		                //4.判断名称是否是：lastTime
		                if("lastTime".equals(name)){
		                    //有该Cookie，不是第一次访问
		
		                    flag = true;//有lastTime的cookie
		
		                    //设置Cookie的value
		                    //获取当前时间的字符串，重新设置Cookie的值，重新发送cookie
		                    Date date  = new Date();
		                    SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
		                    String str_date = sdf.format(date);
		                    System.out.println("编码前："+str_date);
		                    //URL编码
		                    str_date = URLEncoder.encode(str_date,"utf-8");
		                    System.out.println("编码后："+str_date);
		                    cookie.setValue(str_date);
		                    //设置cookie的存活时间
		                    cookie.setMaxAge(60 * 60 * 24 * 30);//一个月
		                    response.addCookie(cookie);


​		

		                    //响应数据
		                    //获取Cookie的value，时间
		                    String value = cookie.getValue();
		                    System.out.println("解码前："+value);
		                    //URL解码：
		                    value = URLDecoder.decode(value,"utf-8");
		                    System.out.println("解码后："+value);
		                    response.getWriter().write("<h1>欢迎回来，您上次访问时间为:"+value+"</h1>");
		
		                    break;
		
		                }
		            }
		        }


​		

		        if(cookies == null || cookies.length == 0 || flag == false){
		            //没有，第一次访问
		
		            //设置Cookie的value
		            //获取当前时间的字符串，重新设置Cookie的值，重新发送cookie
		            Date date  = new Date();
		            SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss");
		            String str_date = sdf.format(date);
		            System.out.println("编码前："+str_date);
		            //URL编码
		            str_date = URLEncoder.encode(str_date,"utf-8");
		            System.out.println("编码后："+str_date);
		
		            Cookie cookie = new Cookie("lastTime",str_date);
		            //设置cookie的存活时间
		            cookie.setMaxAge(60 * 60 * 24 * 30);//一个月
		            response.addCookie(cookie);
		
		            response.getWriter().write("<h1>您好，欢迎您首次访问</h1>");
		        }


​		

		    }
		
		    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		        this.doPost(request, response);
		    }
		}


## JSP：入门学习

	1. 概念：
		* Java Server Pages： java服务器端页面
			* 可以理解为：一个特殊的页面，其中既可以指定定义html标签，又可以定义java代码
			* 用于简化书写！！！


	2. 原理
		* JSP本质上就是一个Servlet
	
	3. JSP的脚本：JSP定义Java代码的方式
		1. <%  代码 %>：定义的java代码，在service方法中。service方法中可以定义什么，该脚本中就可以定义什么。
		2. <%! 代码 %>：定义的java代码，在jsp转换后的java类的成员位置。
		3. <%= 代码 %>：定义的java代码，会输出到页面上。输出语句中可以定义什么，该脚本中就可以定义什么。


	4. JSP的内置对象：
		* 在jsp页面中不需要获取和创建，可以直接使用的对象
		* jsp一共有9个内置对象。
		* 今天学习3个：
			* request
			* response
			* out：字符输出流对象。可以将数据输出到页面上。和response.getWriter()类似
				* response.getWriter()和out.write()的区别：
					* 在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区数据，再找out缓冲区数据。
					* response.getWriter()数据输出永远在out.write()之前
				
	5. 案例:改造Cookie案例




## Session：主菜

	1. 概念：服务器端会话技术，在一次会话的多次请求间共享数据，将数据保存在服务器端的对象中。HttpSession
	2. 快速入门：
		1. 获取HttpSession对象：
			HttpSession session = request.getSession();
		2. 使用HttpSession对象：
			Object getAttribute(String name)  
			void setAttribute(String name, Object value)
			void removeAttribute(String name)  
	
	3. 原理
		* Session的实现是依赖于Cookie的。


​	

	4. 细节：
		1. 当客户端关闭后，服务器不关闭，两次获取session是否为同一个？
			* 默认情况下。不是。
			* 如果需要相同，则可以创建Cookie,键为JSESSIONID，设置最大存活时间，让cookie持久化保存。
				 Cookie c = new Cookie("JSESSIONID",session.getId());
		         c.setMaxAge(60*60);
		         response.addCookie(c);
	
		2. 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？
			* 不是同一个，但是要确保数据不丢失。tomcat自动完成以下工作
				* session的钝化：
					* 在服务器正常关闭之前，将session对象系列化到硬盘上
				* session的活化：
					* 在服务器启动后，将session文件转化为内存中的session对象即可。
				
		3. session什么时候被销毁？
			1. 服务器关闭
			2. session对象调用invalidate() 。
			3. session默认失效时间 30分钟
				选择性配置修改	
				<session-config>
			        <session-timeout>30</session-timeout>
			    </session-config>
	
	 5. session的特点
		 1. session用于存储一次会话的多次请求的数据，存在服务器端
		 2. session可以存储任意类型，任意大小的数据
	
		* session与Cookie的区别：
			1. session存储数据在服务器端，Cookie在客户端
			2. session没有数据大小限制，Cookie有
			3. session数据安全，Cookie相对于不安全


## 案例：验证码

	1. 案例需求：
		1. 访问带有验证码的登录页面login.jsp
		2. 用户输入用户名，密码以及验证码。
			* 如果用户名和密码输入有误，跳转登录页面，提示:用户名或密码错误
			* 如果验证码输入有误，跳转登录页面，提示：验证码错误
			* 如果全部输入正确，则跳转到主页success.jsp，显示：用户名,欢迎您


	2. 分析：


​	



------





# 16.综合案例

	1. 综合练习
		1. 简单功能
			1. 列表查询
			2. 登录
			3. 添加
			4. 删除
			5. 修改
			
		2. 复杂功能
			1. 删除选中
			2. 分页查询
				* 好处：
					1. 减轻服务器内存的开销
					2. 提升用户体验
			3. 复杂条件查询




## 2. 登录

	1. 调整页面，加入验证码功能
	2. 代码实现





------





# 17.Filter&Listener

	1. Filter：过滤器
	2. Listener：监听器




# Filter：过滤器

	1. 概念：
		* 生活中的过滤器：净水器,空气净化器，土匪、
		* web中的过滤器：当访问服务器的资源时，过滤器可以将请求拦截下来，完成一些特殊的功能。
		* 过滤器的作用：
			* 一般用于完成通用的操作。如：登录验证、统一编码处理、敏感字符过滤...
	
	2. 快速入门：
		1. 步骤
			1. 定义一个类，实现接口Filter
			2. 复写方法
			3. 配置拦截路径
				1. web.xml
				2. 注解
		2. 代码：
			@WebFilter("/*")//访问所有资源之前，都会执行该过滤器
			public class FilterDemo1 implements Filter {
			    @Override
			    public void init(FilterConfig filterConfig) throws ServletException {
			
			    }
			
			    @Override
			    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
			        System.out.println("filterDemo1被执行了....");


​			

			        //放行
			        filterChain.doFilter(servletRequest,servletResponse);
			
			    }
			
			    @Override
			    public void destroy() {
			
			    }
			}


	3. 过滤器细节：
		1. web.xml配置	
			<filter>
		        <filter-name>demo1</filter-name>
		        <filter-class>cn.itcast.web.filter.FilterDemo1</filter-class>
		    </filter>
		    <filter-mapping>
		        <filter-name>demo1</filter-name>
				<!-- 拦截路径 -->
		        <url-pattern>/*</url-pattern>
		    </filter-mapping>
		2. 过滤器执行流程
			1. 执行过滤器
			2. 执行放行后的资源
			3. 回来执行过滤器放行代码下边的代码
		3. 过滤器生命周期方法
			1. init:在服务器启动后，会创建Filter对象，然后调用init方法。只执行一次。用于加载资源
			2. doFilter:每一次请求被拦截资源时，会执行。执行多次
			3. destroy:在服务器关闭后，Filter对象被销毁。如果服务器是正常关闭，则会执行destroy方法。只执行一次。用于释放资源
		4. 过滤器配置详解
			* 拦截路径配置：
				1. 具体资源路径： /index.jsp   只有访问index.jsp资源时，过滤器才会被执行
				2. 拦截目录： /user/*	访问/user下的所有资源时，过滤器都会被执行
				3. 后缀名拦截： *.jsp		访问所有后缀名为jsp资源时，过滤器都会被执行
				4. 拦截所有资源：/*		访问所有资源时，过滤器都会被执行
			* 拦截方式配置：资源被访问的方式
				* 注解配置：
					* 设置dispatcherTypes属性
						1. REQUEST：默认值。浏览器直接请求资源
						2. FORWARD：转发访问资源
						3. INCLUDE：包含访问资源
						4. ERROR：错误跳转资源
						5. ASYNC：异步访问资源
				* web.xml配置
					* 设置<dispatcher></dispatcher>标签即可
				
		5. 过滤器链(配置多个过滤器)
			* 执行顺序：如果有两个过滤器：过滤器1和过滤器2
				1. 过滤器1
				2. 过滤器2
				3. 资源执行
				4. 过滤器2
				5. 过滤器1 
	
			* 过滤器先后顺序问题：
				1. 注解配置：按照类名的字符串比较规则比较，值小的先执行
					* 如： AFilter 和 BFilter，AFilter就先执行了。
				2. web.xml配置： <filter-mapping>谁定义在上边，谁先执行
	4. 案例：
		1. 案例1_登录验证
			* 需求：
				1. 访问day17_case案例的资源。验证其是否登录
				2. 如果登录了，则直接放行。
				3. 如果没有登录，则跳转到登录页面，提示"您尚未登录，请先登录"。


​	

		2. 案例2_敏感词汇过滤
			* 需求：
				1. 对day17_case案例录入的数据进行敏感词汇过滤
				2. 敏感词汇参考《敏感词汇.txt》
				3. 如果是敏感词汇，替换为 *** 
	
			* 分析：
				1. 对request对象进行增强。增强获取参数相关方法
				2. 放行。传递代理对象


			* 增强对象的功能：
				* 设计模式：一些通用的解决固定问题的方式
				1. 装饰模式
				2. 代理模式
					* 概念：
						1. 真实对象：被代理的对象
						2. 代理对象：
						3. 代理模式：代理对象代理真实对象，达到增强真实对象功能的目的
				 	* 实现方式：
					 	1. 静态代理：有一个类文件描述代理模式
					 	2. 动态代理：在内存中形成代理类
							* 实现步骤：
								1. 代理对象和真实对象实现相同的接口
								2. 代理对象 = Proxy.newProxyInstance();
								3. 使用代理对象调用方法。
								4. 增强方法
	
							* 增强方式：
								1. 增强参数列表
								2. 增强返回值类型
								3. 增强方法体执行逻辑	


## Listener：监听器

	* 概念：web的三大组件之一。
		* 事件监听机制
			* 事件	：一件事情
			* 事件源 ：事件发生的地方
			* 监听器 ：一个对象
			* 注册监听：将事件、事件源、监听器绑定在一起。 当事件源上发生某个事件后，执行监听器代码


	* ServletContextListener:监听ServletContext对象的创建和销毁
		* 方法：
			* void contextDestroyed(ServletContextEvent sce) ：ServletContext对象被销毁之前会调用该方法
			* void contextInitialized(ServletContextEvent sce) ：ServletContext对象创建后会调用该方法
		* 步骤：
			1. 定义一个类，实现ServletContextListener接口
			2. 复写方法
			3. 配置
				1. web.xml
						<listener>
	 					 <listener-class>cn.itcast.web.listener.ContextLoaderListener</listener-class>

   						</listener>

						* 指定初始化参数<context-param>
				2. 注解：
					* @WebListener





------





# 18.JQuery高级：

	1. JQuery 高级
		1. 动画
		2. 遍历
		3. 事件绑定
		4. 案例
		5. 插件




## JQuery 高级

	1. 动画
		1. 三种方式显示和隐藏元素
			1. 默认显示和隐藏方式
				1. show([speed,[easing],[fn]])
					1. 参数：
						1. speed：动画的速度。三个预定义的值("slow","normal", "fast")或表示动画时长的毫秒数值(如：1000)
						2. easing：用来指定切换效果，默认是"swing"，可用参数"linear"
							* swing：动画执行时效果是 先慢，中间快，最后又慢
							* linear：动画执行时速度是匀速的
						3. fn：在动画完成时执行的函数，每个元素执行一次。
	
				2. hide([speed,[easing],[fn]])
				3. toggle([speed],[easing],[fn])
			
			2. 滑动显示和隐藏方式
				1. slideDown([speed],[easing],[fn])
				2. slideUp([speed,[easing],[fn]])
				3. slideToggle([speed],[easing],[fn])
	
			3. 淡入淡出显示和隐藏方式
				1. fadeIn([speed],[easing],[fn])
				2. fadeOut([speed],[easing],[fn])
				3. fadeToggle([speed,[easing],[fn]])


	2. 遍历
		1. js的遍历方式
			* for(初始化值;循环结束条件;步长)
		2. jq的遍历方式
			1. jq对象.each(callback)
				1. 语法：
					jquery对象.each(function(index,element){});
						* index:就是元素在集合中的索引
						* element：就是集合中的每一个元素对象
	
						* this：集合中的每一个元素对象
				2. 回调函数返回值：
					* true:如果当前function返回为false，则结束循环(break)。
					* false:如果当前function返回为true，则结束本次循环，继续下次循环(continue)
			2. $.each(object, [callback])
			3. for..of: jquery 3.0 版本之后提供的方式
				for(元素对象 of 容器对象)
		
	3. 事件绑定
		1. jquery标准的绑定方式
			* jq对象.事件方法(回调函数)；
			* 注：如果调用事件方法，不传递回调函数，则会触发浏览器默认行为。
				* 表单对象.submit();//让表单提交
		2. on绑定事件/off解除绑定
			* jq对象.on("事件名称",回调函数)
			* jq对象.off("事件名称")
				* 如果off方法不传递任何参数，则将组件上的所有事件全部解绑
		3. 事件切换：toggle
			* jq对象.toggle(fn1,fn2...)
				* 当单击jq对象对应的组件后，会执行fn1.第二次点击会执行fn2.....
				
			* 注意：1.9版本 .toggle() 方法删除,jQuery Migrate（迁移）插件可以恢复此功能。
				 <script src="../js/jquery-migrate-1.0.0.js" type="text/javascript" charset="utf-8"></script>


	4. 案例
		1. 广告显示和隐藏
			<!DOCTYPE html>
			<html>
			<head>
			    <meta charset="UTF-8">
			    <title>广告的自动显示与隐藏</title>
			    <style>
			        #content{width:100%;height:500px;background:#999}
			    </style>
			
			    <!--引入jquery-->
			    <script type="text/javascript" src="../js/jquery-3.3.1.min.js"></script>
			    <script>
			        /*
			            需求：
			                1. 当页面加载完，3秒后。自动显示广告
			                2. 广告显示5秒后，自动消失。
			
			            分析：
			                1. 使用定时器来完成。setTimeout (执行一次定时器)
			                2. 分析发现JQuery的显示和隐藏动画效果其实就是控制display
			                3. 使用  show/hide方法来完成广告的显示
			         */
			
			        //入口函数，在页面加载完成之后，定义定时器，调用这两个方法
			        $(function () {
			           //定义定时器，调用adShow方法 3秒后执行一次
			           setTimeout(adShow,3000);
			           //定义定时器，调用adHide方法，8秒后执行一次
			            setTimeout(adHide,8000);
			        });
			        //显示广告
			        function adShow() {
			            //获取广告div，调用显示方法
			            $("#ad").show("slow");
			        }
			        //隐藏广告
			        function adHide() {
			            //获取广告div，调用隐藏方法
			            $("#ad").hide("slow");
			        }


​			
​			

			    </script>
			</head>
			<body>
			<!-- 整体的DIV -->
			<div>
			    <!-- 广告DIV -->
			    <div id="ad" style="display: none;">
			        <img style="width:100%" src="../img/adv.jpg" />
			    </div>
			
			    <!-- 下方正文部分 -->
			    <div id="content">
			        正文部分
			    </div>
			</div>
			</body>
			</html>



		2. 抽奖
			<!DOCTYPE html>
			<html>
			<head>
			    <meta charset="UTF-8">
			    <title>jquery案例之抽奖</title>
			    <script type="text/javascript" src="../js/jquery-3.3.1.min.js"></script>
			
			    <script language='javascript' type='text/javascript'>
			
			        /*
			            分析：
			                1. 给开始按钮绑定单击事件
			                    1.1 定义循环定时器
			                    1.2 切换小相框的src属性
			                        * 定义数组，存放图片资源路径
			                        * 生成随机数。数组索引


​			

			                2. 给结束按钮绑定单击事件
			                    1.1 停止定时器
			                    1.2 给大相框设置src属性
			
			         */
			        var imgs = ["../img/man00.jpg",
			                    "../img/man01.jpg",
			                    "../img/man02.jpg",
			                    "../img/man03.jpg",
			                    "../img/man04.jpg",
			                    "../img/man05.jpg",
			                    "../img/man06.jpg",
			                    ];
			        var startId;//开始定时器的id
			        var index;//随机角标
			        $(function () {
			            //处理按钮是否可以使用的效果
			            $("#startID").prop("disabled",false);
			            $("#stopID").prop("disabled",true);


​			

			           //1. 给开始按钮绑定单击事件
			            $("#startID").click(function () {
			                // 1.1 定义循环定时器 20毫秒执行一次
			                startId = setInterval(function () {
			                    //处理按钮是否可以使用的效果
			                    $("#startID").prop("disabled",true);
			                    $("#stopID").prop("disabled",false);


​			

			                    //1.2生成随机角标 0-6
			                    index = Math.floor(Math.random() * 7);//0.000--0.999 --> * 7 --> 0.0-----6.9999
			                    //1.3设置小相框的src属性
			                    $("#img1ID").prop("src",imgs[index]);
			
			                },20);
			            });


​			

			            //2. 给结束按钮绑定单击事件
			            $("#stopID").click(function () {
			                //处理按钮是否可以使用的效果
			                $("#startID").prop("disabled",false);
			                $("#stopID").prop("disabled",true);


​			

			               // 1.1 停止定时器
			                clearInterval(startId);
			               // 1.2 给大相框设置src属性
			                $("#img2ID").prop("src",imgs[index]).hide();
			                //显示1秒之后
			                $("#img2ID").show(1000);
			            });
			        });


​			
​			
​			

			    </script>
			
			</head>
			<body>
			
			<!-- 小像框 -->
			<div style="border-style:dotted;width:160px;height:100px">
			    <img id="img1ID" src="../img/man00.jpg" style="width:160px;height:100px"/>
			</div>
			
			<!-- 大像框 -->
			<div
			        style="border-style:double;width:800px;height:500px;position:absolute;left:500px;top:10px">
			    <img id="img2ID" src="../img/man00.jpg" width="800px" height="500px"/>
			</div>
			
			<!-- 开始按钮 -->
			<input
			        id="startID"
			        type="button"
			        value="点击开始"
			        style="width:150px;height:150px;font-size:22px">
			
			<!-- 停止按钮 -->
			<input
			        id="stopID"
			        type="button"
			        value="点击停止"
			        style="width:150px;height:150px;font-size:22px">


​			

			</body>
			</html>
	
	5. 插件：增强JQuery的功能
		1. 实现方式：
			1. $.fn.extend(object) 
				* 增强通过Jquery获取的对象的功能  $("#id")
			2. $.extend(object)
				* 增强JQeury对象自身的功能  $/jQuery







------





## 今日内容

	1. AJAX：
	2. JSON





# 19.AJAX：

	1. 概念： ASynchronous JavaScript And XML	异步的JavaScript 和 XML
		1. 异步和同步：客户端和服务器端相互通信的基础上
			* 客户端必须等待服务器端的响应。在等待的期间客户端不能做其他操作。
			* 客户端不需要等待服务器端的响应。在服务器处理请求的过程中，客户端可以进行其他的操作。
	
			Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。 [1] 
			通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
			传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。
	
			提升用户的体验
	
	2. 实现方式：
		1. 原生的JS实现方式（了解）
					 //1.创建核心对象
		            var xmlhttp;
		            if (window.XMLHttpRequest)
		            {// code for IE7+, Firefox, Chrome, Opera, Safari
		                xmlhttp=new XMLHttpRequest();
		            }
		            else
		            {// code for IE6, IE5
		                xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
		            }
		
		            //2. 建立连接
		            /*
		                参数：
		                    1. 请求方式：GET、POST
		                        * get方式，请求参数在URL后边拼接。send方法为空参
		                        * post方式，请求参数在send方法中定义
		                    2. 请求的URL：
		                    3. 同步或异步请求：true（异步）或 false（同步）
		
		             */
		            xmlhttp.open("GET","ajaxServlet?username=tom",true);
		
		            //3.发送请求
		            xmlhttp.send();
		
		            //4.接受并处理来自服务器的响应结果
		            //获取方式 ：xmlhttp.responseText
		            //什么时候获取？当服务器响应成功后再获取
		
		            //当xmlhttp对象的就绪状态改变时，触发事件onreadystatechange。
		            xmlhttp.onreadystatechange=function()
		            {
		                //判断readyState就绪状态是否为4，判断status响应状态码是否为200
		                if (xmlhttp.readyState==4 && xmlhttp.status==200)
		                {
		                   //获取服务器的响应结果
		                    var responseText = xmlhttp.responseText;
		                    alert(responseText);
		                }
		            }
		2. JQeury实现方式
			1. $.ajax()
				* 语法：$.ajax({键值对});
				 //使用$.ajax()发送异步请求
		            $.ajax({
		                url:"ajaxServlet1111" , // 请求路径
		                type:"POST" , //请求方式
		                //data: "username=jack&age=23",//请求参数
		                data:{"username":"jack","age":23},
		                success:function (data) {
		                    alert(data);
		                },//响应成功后的回调函数
		                error:function () {
		                    alert("出错啦...")
		                },//表示如果请求响应出现错误，会执行的回调函数
		
		                dataType:"text"//设置接受到的响应数据的格式
		            });
			2. $.get()：发送get请求
				* 语法：$.get(url, [data], [callback], [type])
					* 参数：
						* url：请求路径
						* data：请求参数
						* callback：回调函数
						* type：响应结果的类型
	
			3. $.post()：发送post请求
				* 语法：$.post(url, [data], [callback], [type])
					* 参数：
						* url：请求路径
						* data：请求参数
						* callback：回调函数
						* type：响应结果的类型



# 20.JSON：

	1. 概念： JavaScript Object Notation		JavaScript对象表示法
		Person p = new Person();
		p.setName("张三");
		p.setAge(23);
		p.setGender("男");
	
		var p = {"name":"张三","age":23,"gender":"男"};
	
		* json现在多用于存储和交换文本信息的语法
		* 进行数据的传输
		* JSON 比 XML 更小、更快，更易解析。
	
	2. 语法：
		1. 基本规则
			* 数据在名称/值对中：json数据是由键值对构成的
				* 键用引号(单双都行)引起来，也可以不使用引号
				* 值得取值类型：
					1. 数字（整数或浮点数）
					2. 字符串（在双引号中）
					3. 逻辑值（true 或 false）
					4. 数组（在方括号中）	{"persons":[{},{}]}
					5. 对象（在花括号中） {"address":{"province"："陕西"....}}
					6. null
			* 数据由逗号分隔：多个键值对由逗号分隔
			* 花括号保存对象：使用{}定义json 格式
			* 方括号保存数组：[]
		2. 获取数据:
			1. json对象.键名
			2. json对象["键名"]
			3. 数组对象[索引]
			4. 遍历
					 //1.定义基本格式
			        var person = {"name": "张三", age: 23, 'gender': true};
			
			        var ps = [{"name": "张三", "age": 23, "gender": true},
			            {"name": "李四", "age": 24, "gender": true},
			            {"name": "王五", "age": 25, "gender": false}];


​			
​			
​			

			        //获取person对象中所有的键和值
			        //for in 循环
			       /* for(var key in person){
			            //这样的方式获取不行。因为相当于  person."name"
			            //alert(key + ":" + person.key);
			            alert(key+":"+person[key]);
			        }*/
			
			       //获取ps中的所有值
			        for (var i = 0; i < ps.length; i++) {
			            var p = ps[i];
			            for(var key in p){
			                alert(key+":"+p[key]);
			            }
			        }


	3. JSON数据和Java对象的相互转换
	
		* JSON解析器：
			* 常见的解析器：Jsonlib，Gson，fastjson，jackson
		
		1. JSON转为Java对象
			1. 导入jackson的相关jar包
			2. 创建Jackson核心对象 ObjectMapper
			3. 调用ObjectMapper的相关方法进行转换
				1. readValue(json字符串数据,Class)
		2. Java对象转换JSON
			1. 使用步骤：
				1. 导入jackson的相关jar包
				2. 创建Jackson核心对象 ObjectMapper
				3. 调用ObjectMapper的相关方法进行转换
					1. 转换方法：
						* writeValue(参数1，obj):
		                    参数1：
		                        File：将obj对象转换为JSON字符串，并保存到指定的文件中
		                        Writer：将obj对象转换为JSON字符串，并将json数据填充到字符输出流中
		                        OutputStream：将obj对象转换为JSON字符串，并将json数据填充到字节输出流中
		                * writeValueAsString(obj):将对象转为json字符串
	
					2. 注解：
						1. @JsonIgnore：排除属性。
						2. @JsonFormat：属性值得格式化
							* @JsonFormat(pattern = "yyyy-MM-dd")
	
					3. 复杂java对象转换
						1. List：数组
						2. Map：对象格式一致


# 案例：

	* 校验用户名是否存在
		1. 服务器响应的数据，在客户端使用时，要想当做json数据格式使用。有两种解决方案：
			1. $.get(type):将最后一个参数type指定为"json"
			2. 在服务器端设置MIME类型
				response.setContentType("application/json;charset=utf-8");





------



## 今日内容

	1. redis
		1. 概念
		2. 下载安装
		3. 命令操作
			1. 数据结构
		4. 持久化操作
		5. 使用Java客户端操作redis



# 21.Redis

	1. 概念： redis是一款高性能的NOSQL系列的非关系型数据库


		1.1.什么是NOSQL
			NoSQL(NoSQL = Not Only SQL)，意即“不仅仅是SQL”，是一项全新的数据库理念，泛指非关系型的数据库。
			随着互联网web2.0网站的兴起，传统的关系数据库在应付web2.0网站，特别是超大规模和高并发的SNS类型的web2.0纯动态网站已经显得力不从心，暴露了很多难以克服的问题，而非关系型的数据库则由于其本身的特点得到了非常迅速的发展。NoSQL数据库的产生就是为了解决大规模数据集合多重数据种类带来的挑战，尤其是大数据应用难题。
	
			1.1.1.	NOSQL和关系型数据库比较
				优点：
					1）成本：nosql数据库简单易部署，基本都是开源软件，不需要像使用oracle那样花费大量成本购买使用，相比关系型数据库价格便宜。
					2）查询速度：nosql数据库将数据存储于缓存之中，关系型数据库将数据存储在硬盘中，自然查询速度远不及nosql数据库。
					3）存储数据的格式：nosql的存储格式是key,value形式、文档形式、图片形式等等，所以可以存储基础类型以及对象或者是集合等各种格式，而数据库则只支持基础类型。
					4）扩展性：关系型数据库有类似join这样的多表查询机制的限制导致扩展很艰难。
	
				缺点：
					1）维护的工具和资料有限，因为nosql是属于新的技术，不能和关系型数据库10几年的技术同日而语。
					2）不提供对sql的支持，如果不支持sql这样的工业标准，将产生一定用户的学习和使用成本。
					3）不提供关系型数据库对事务的处理。
	
			1.1.2.	非关系型数据库的优势：
				1）性能NOSQL是基于键值对的，可以想象成表中的主键和值的对应关系，而且不需要经过SQL层的解析，所以性能非常高。
				2）可扩展性同样也是因为基于键值对，数据之间没有耦合性，所以非常容易水平扩展。
	
			1.1.3.	关系型数据库的优势：
				1）复杂查询可以用SQL语句方便的在一个表以及多个表之间做非常复杂的数据查询。
				2）事务支持使得对于安全性能很高的数据访问要求得以实现。对于这两类数据库，对方的优势就是自己的弱势，反之亦然。
	
			1.1.4.	总结
				关系型数据库与NoSQL数据库并非对立而是互补的关系，即通常情况下使用关系型数据库，在适合使用NoSQL的时候使用NoSQL数据库，
				让NoSQL数据库对关系型数据库的不足进行弥补。
				一般会将数据存储在关系型数据库中，在nosql数据库中备份存储关系型数据库的数据
	
		1.2.主流的NOSQL产品
			•	键值(Key-Value)存储数据库
					相关产品： Tokyo Cabinet/Tyrant、Redis、Voldemort、Berkeley DB
					典型应用： 内容缓存，主要用于处理大量数据的高访问负载。 
					数据模型： 一系列键值对
					优势： 快速查询
					劣势： 存储的数据缺少结构化
			•	列存储数据库
					相关产品：Cassandra, HBase, Riak
					典型应用：分布式的文件系统
					数据模型：以列簇式存储，将同一列数据存在一起
					优势：查找速度快，可扩展性强，更容易进行分布式扩展
					劣势：功能相对局限
			•	文档型数据库
					相关产品：CouchDB、MongoDB
					典型应用：Web应用（与Key-Value类似，Value是结构化的）
					数据模型： 一系列键值对
					优势：数据结构要求不严格
					劣势： 查询性能不高，而且缺乏统一的查询语法
			•	图形(Graph)数据库
					相关数据库：Neo4J、InfoGrid、Infinite Graph
					典型应用：社交网络
					数据模型：图结构
					优势：利用图结构相关算法。
					劣势：需要对整个图做计算才能得出结果，不容易做分布式的集群方案。
		1.3 什么是Redis
			Redis是用C语言开发的一个开源的高性能键值对（key-value）数据库，官方提供测试数据，50个并发执行100000个请求,读的速度是110000次/s,写的速度是81000次/s ，且Redis通过提供多种键值数据类型来适应不同场景下的存储需求，目前为止Redis支持的键值数据类型如下：
				1) 字符串类型 string
				2) 哈希类型 hash
				3) 列表类型 list
				4) 集合类型 set
				5) 有序集合类型 sortedset
			1.3.1 redis的应用场景
				•	缓存（数据查询、短连接、新闻内容、商品内容等等）
				•	聊天室的在线好友列表
				•	任务队列。（秒杀、抢购、12306等等）
				•	应用排行榜
				•	网站访问统计
				•	数据过期处理（可以精确到毫秒
				•	分布式集群架构中的session分离


	2. 下载安装
		1. 官网：https://redis.io
		2. 中文网：http://www.redis.net.cn/
		3. 解压直接可以使用：
			* redis.windows.conf：配置文件
			* redis-cli.exe：redis的客户端
			* redis-server.exe：redis服务器端
		
	3. 命令操作
		1. redis的数据结构：
			* redis存储的是：key,value格式的数据，其中key都是字符串，value有5种不同的数据结构
				* value的数据结构：
					1) 字符串类型 string
					2) 哈希类型 hash ： map格式  
					3) 列表类型 list ： linkedlist格式。支持重复元素
					4) 集合类型 set  ： 不允许重复元素
					5) 有序集合类型 sortedset：不允许重复元素，且元素有顺序
		
		2. 字符串类型 string
			1. 存储： set key value
				127.0.0.1:6379> set username zhangsan
				OK
			2. 获取： get key
				127.0.0.1:6379> get username
				"zhangsan"
			3. 删除： del key
				127.0.0.1:6379> del age
				(integer) 1
		3. 哈希类型 hash
			1. 存储： hset key field value
				127.0.0.1:6379> hset myhash username lisi
				(integer) 1
				127.0.0.1:6379> hset myhash password 123
				(integer) 1
			2. 获取： 
				* hget key field: 获取指定的field对应的值
					127.0.0.1:6379> hget myhash username
					"lisi"
				* hgetall key：获取所有的field和value
					127.0.0.1:6379> hgetall myhash
					1) "username"
					2) "lisi"
					3) "password"
					4) "123"
					
			3. 删除： hdel key field
				127.0.0.1:6379> hdel myhash username
				(integer) 1
		
		4. 列表类型 list:可以添加一个元素到列表的头部（左边）或者尾部（右边）
			1. 添加：
				1. lpush key value: 将元素加入列表左表
					
				2. rpush key value：将元素加入列表右边
					
					127.0.0.1:6379> lpush myList a
					(integer) 1
					127.0.0.1:6379> lpush myList b
					(integer) 2
					127.0.0.1:6379> rpush myList c
					(integer) 3
			2. 获取：
				* lrange key start end ：范围获取
					127.0.0.1:6379> lrange myList 0 -1
					1) "b"
					2) "a"
					3) "c"
			3. 删除：
				* lpop key： 删除列表最左边的元素，并将元素返回
				* rpop key： 删除列表最右边的元素，并将元素返回


		5. 集合类型 set ： 不允许重复元素
			1. 存储：sadd key value
				127.0.0.1:6379> sadd myset a
				(integer) 1
				127.0.0.1:6379> sadd myset a
				(integer) 0
			2. 获取：smembers key:获取set集合中所有元素
				127.0.0.1:6379> smembers myset
				1) "a"
			3. 删除：srem key value:删除set集合中的某个元素	
				127.0.0.1:6379> srem myset a
				(integer) 1
		6. 有序集合类型 sortedset：不允许重复元素，且元素有顺序.每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
	
			1. 存储：zadd key score value
				127.0.0.1:6379> zadd mysort 60 zhangsan
				(integer) 1
				127.0.0.1:6379> zadd mysort 50 lisi
				(integer) 1
				127.0.0.1:6379> zadd mysort 80 wangwu
				(integer) 1
			2. 获取：zrange key start end [withscores]
				127.0.0.1:6379> zrange mysort 0 -1
				1) "lisi"
				2) "zhangsan"
				3) "wangwu"
	
				127.0.0.1:6379> zrange mysort 0 -1 withscores
				1) "zhangsan"
				2) "60"
				3) "wangwu"
				4) "80"
				5) "lisi"
				6) "500"
			3. 删除：zrem key value
				127.0.0.1:6379> zrem mysort lisi
				(integer) 1
	
		7. 通用命令
			1. keys * : 查询所有的键
			2. type key ： 获取键对应的value的类型
			3. del key：删除指定的key value


	4. 持久化
		1. redis是一个内存数据库，当redis服务器重启，获取电脑重启，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中。
		2. redis持久化机制：
			1. RDB：默认方式，不需要进行配置，默认就使用这种机制
				* 在一定的间隔时间中，检测key的变化情况，然后持久化数据
				1. 编辑redis.windwos.conf文件
					#   after 900 sec (15 min) if at least 1 key changed
					save 900 1
					#   after 300 sec (5 min) if at least 10 keys changed
					save 300 10
					#   after 60 sec if at least 10000 keys changed
					save 60 10000
					
				2. 重新启动redis服务器，并指定配置文件名称
					D:\JavaWeb2018\day23_redis\资料\redis\windows-64\redis-2.8.9>redis-server.exe redis.windows.conf	
				
			2. AOF：日志记录的方式，可以记录每一条命令的操作。可以每一次命令操作后，持久化数据
				1. 编辑redis.windwos.conf文件
					appendonly no（关闭aof） --> appendonly yes （开启aof）
					
					# appendfsync always ： 每一次操作都进行持久化
					appendfsync everysec ： 每隔一秒进行一次持久化
					# appendfsync no	 ： 不进行持久化
	
	5. Java客户端 Jedis
		* Jedis: 一款java操作redis数据库的工具.
		* 使用步骤：
			1. 下载jedis的jar包
			2. 使用
				//1. 获取连接
	    		Jedis jedis = new Jedis("localhost",6379);
	   			//2. 操作
	   			jedis.set("username","zhangsan");
	    		//3. 关闭连接
	    		jedis.close();


​		

		* Jedis操作各种redis中的数据结构
			1) 字符串类型 string
				set
				get
				
				 //1. 获取连接
		        Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
		        //2. 操作
		        //存储
		        jedis.set("username","zhangsan");
		        //获取
		        String username = jedis.get("username");
		        System.out.println(username);
		
		        //可以使用setex()方法存储可以指定过期时间的 key value
		        jedis.setex("activecode",20,"hehe");//将activecode：hehe键值对存入redis，并且20秒后自动删除该键值对
		
		        //3. 关闭连接
		        jedis.close();
	
			2) 哈希类型 hash ： map格式  
				hset
				hget
				hgetAll
				//1. 获取连接
		        Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
		        //2. 操作
		        // 存储hash
		        jedis.hset("user","name","lisi");
		        jedis.hset("user","age","23");
		        jedis.hset("user","gender","female");
		
		        // 获取hash
		        String name = jedis.hget("user", "name");
		        System.out.println(name);


​		

		        // 获取hash的所有map中的数据
		        Map<String, String> user = jedis.hgetAll("user");
		
		        // keyset
		        Set<String> keySet = user.keySet();
		        for (String key : keySet) {
		            //获取value
		            String value = user.get(key);
		            System.out.println(key + ":" + value);
		        }
		
		        //3. 关闭连接
		        jedis.close();


			3) 列表类型 list ： linkedlist格式。支持重复元素
				lpush / rpush
				lpop / rpop
				lrange start end : 范围获取
				
				 //1. 获取连接
		        Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
		        //2. 操作
		        // list 存储
		        jedis.lpush("mylist","a","b","c");//从左边存
		        jedis.rpush("mylist","a","b","c");//从右边存
		
		        // list 范围获取
		        List<String> mylist = jedis.lrange("mylist", 0, -1);
		        System.out.println(mylist);
		        
		        // list 弹出
		        String element1 = jedis.lpop("mylist");//c
		        System.out.println(element1);
		
		        String element2 = jedis.rpop("mylist");//c
		        System.out.println(element2);
		
		        // list 范围获取
		        List<String> mylist2 = jedis.lrange("mylist", 0, -1);
		        System.out.println(mylist2);
		
		        //3. 关闭连接
		        jedis.close();


			4) 集合类型 set  ： 不允许重复元素
				sadd
				smembers:获取所有元素
	
				//1. 获取连接
		        Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
		        //2. 操作


​		

		        // set 存储
		        jedis.sadd("myset","java","php","c++");
		
		        // set 获取
		        Set<String> myset = jedis.smembers("myset");
		        System.out.println(myset);
		
		        //3. 关闭连接
		        jedis.close();
			5) 有序集合类型 sortedset：不允许重复元素，且元素有顺序
				zadd
				zrange
	
				//1. 获取连接
		        Jedis jedis = new Jedis();//如果使用空参构造，默认值 "localhost",6379端口
		        //2. 操作
		        // sortedset 存储
		        jedis.zadd("mysortedset",3,"亚瑟");
		        jedis.zadd("mysortedset",30,"后裔");
		        jedis.zadd("mysortedset",55,"孙悟空");
		
		        // sortedset 获取
		        Set<String> mysortedset = jedis.zrange("mysortedset", 0, -1);
		
		        System.out.println(mysortedset);


​		

		        //3. 关闭连接
		        jedis.close();


​		

		* jedis连接池： JedisPool
			* 使用：
				1. 创建JedisPool连接池对象
				2. 调用方法 getResource()方法获取Jedis连接
					//0.创建一个配置对象
			        JedisPoolConfig config = new JedisPoolConfig();
			        config.setMaxTotal(50);
			        config.setMaxIdle(10);
			
			        //1.创建Jedis连接池对象
			        JedisPool jedisPool = new JedisPool(config,"localhost",6379);
			
			        //2.获取连接
			        Jedis jedis = jedisPool.getResource();
			        //3. 使用
			        jedis.set("hehe","heihei");


​			

			        //4. 关闭 归还到连接池中
			        jedis.close();
			
			* 连接池工具类
				public class JedisPoolUtils {
	
				    private static JedisPool jedisPool;
				
				    static{
				        //读取配置文件
				        InputStream is = JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
				        //创建Properties对象
				        Properties pro = new Properties();
				        //关联文件
				        try {
				            pro.load(is);
				        } catch (IOException e) {
				            e.printStackTrace();
				        }
				        //获取数据，设置到JedisPoolConfig中
				        JedisPoolConfig config = new JedisPoolConfig();
				        config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
				        config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));
				
				        //初始化JedisPool
				        jedisPool = new JedisPool(config,pro.getProperty("host"),Integer.parseInt(pro.getProperty("port")));


​				
​				

				    }


​				

				    /**
				     * 获取连接方法
				     */
				    public static Jedis getJedis(){
				        return jedisPool.getResource();
				    }
				}




## 案例：

	案例需求：
		1. 提供index.html页面，页面中有一个省份 下拉列表
		2. 当 页面加载完成后 发送ajax请求，加载所有省份


	* 注意：使用redis缓存一些不经常发生变化的数据。
		* 数据库的数据一旦发生改变，则需要更新缓存。
			* 数据库的表执行 增删改的相关操作，需要将redis缓存数据情况，再次存入
			* 在service对应的增删改方法中，将redis数据删除。















​	



