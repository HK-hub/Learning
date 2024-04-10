# MySQL导出数据库所有表结构和数据

### [mysql 导出表结构和表数据 mysqldump用法](http://joewalker.iteye.com/blog/408879)

**命令行下具体用法如下：**  

**mysqldump -u用戶名 -p密码 -d 数据库名 表名 > 脚本名;**

 

 

导出整个数据库结构和数据:

```sql
mysqldump -h localhost -uroot -p123456 database > dump.sql
```

 

导出单个数据表结构和数据
mysqldump -h localhost -uroot -p123456  database table > dump.sql

 

导出整个数据库结构（不包含数据）

mysqldump -h localhost -uroot -p123456  -d database > dump.sql

 

 

导出单个数据表结构（不包含数据）
mysqldump -h localhost -uroot -p123456  -d database table > dump.sql

 

 

mysqldump 备份导出数据排除某张表

就用 --ignore-table=dbname.tablename参数就行了。

mysqldump -uusername -ppassword -h192.168.0.1 -P3306 dbname --ignore-table=dbname.dbtanles > dump.sql

 