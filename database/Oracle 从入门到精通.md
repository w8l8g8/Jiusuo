# Oracle 从入门到精通

## 1 数据定义语言（DDL）

1 创建表

```sql
CREATE TABLE schema.table_name(
  column_name data_type,
  column_name data_type
  [constraint]
);

CREATE TABLE employees_demo
	( employee_id NUMBER(6)
	, first_name VARCHAR2(20)
	, last_name VARCHAR2(25)
	CONSTRAINT emp_last_name_nn_demo NOT NULL
	, email VARCHAR2(25)
	CONSTRAINT emp_email_nn_demo NOT NULL
	, phone_number VARCHAR2(20)
	, hire_date DATE DEFAULT SYSDATE
	CONSTRAINT emp_hire_date_nn_demo NOT NULL
	, job_id VARCHAR2(10)
	CONSTRAINT emp_job_nn_demo NOT NULL
	, salary NUMBER(8,2)
	CONSTRAINT emp_salary_nn_demo NOT NULL
	, commission_pct NUMBER(2,2)
	, manager_id NUMBER(6)
	, department_id NUMBER(4)
	, dn VARCHAR2(300)
	, CONSTRAINT emp_salary_min_demo
	CHECK (salary > 0)
	, CONSTRAINT emp_email_uk_demo
	UNIQUE (email)
) ;
```

2 修改表结构

```sql
ALTER TABLE table_name
ADD column_name | MODIFY column_name | DROP COLUMN column_name;
```

3 删除表

```sql
DROP TABLE table_name
```

## 2 在Maven仓库中添加Oracle JDBC驱动

由于Oracle授权问题，Maven3不提供Oracle JDBC driver，为了在Maven项目中应用Oracle JDBC driver,必须手动添加到本地仓库。

首先要得到Oracle JDBC Driver

1.通过Oracle官方网站下载相应版本：[http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html](http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html)

2.通过Oracle的安装目录获得，位置在`{ORACLE_HOME}\jdbc\lib\ojdbc14.jar` 

手动安装请参考另一篇文章：**maven3 手动安装本地jar到仓库**

或者命令如下：

```shell
mvn install:install-file -Dfile=E:\app\Administrator\product\11.2.0\dbhome_1\jdbc\lib\ojdbc6.jar-DgroupId=com.oracle -DartifactId=ojdbc6 -Dversion=11.2.0 -Dpackaging=jar
```

安装完后，在pom.xml文件中添加引用

```xml
  <dependencies>
    <!-- 添加oracle jdbc driver -->  
    <dependency>    
        <groupId>com.oracle</groupId>    
        <artifactId>ojdbc14</artifactId>    
        <version>10.2.0.4.0</version>
    </dependency>
  </dependencies>
```

好了，可以正常引用了。