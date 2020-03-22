[//]: # (mysqldump 常用命令以及参数详情)

### 常用命令

#### 备份（导出）
1.导出一个表  
mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名  
mysqldump -u dbuser -p dbname tablename> dbname_tablename.sql  

2.导出一个表的结构  
mysqldump -u dbuser -p -d dbname tablename > dbname_tablename.sql

3.导出整个数据库  
mysqldump -u 用户名 -p 数据库名 > 导出的文件名  
mysqldump -u dbuser -p dbname > dbname.sql  

4.导出一个数据库结构  
mysqldump -u dbuser -p -d --add-drop-table dbname > dbname.sql  
-d 没有数据 --add-drop-table 在每个create语句之前增加一个drop table  

5.导出全部数据库  
mysqldump -uroot -p --all-databases > sqlfile.sql  
--add-drop-database 每个数据库创建之前添加drop数据库语句  

6.压缩备份文件  
mysqldump -uroot -p'123456'  -B mytest | gzip > /mnt/mytest_bak_.sql.gz


#### 恢复（导入）
1.mysqldum   

mysql -u用户名 -p密码 数据库名 < 数据库名.sql
mysql -u dbuser -p dbname < abc.sql

2.source 命令  
进入mysql数据库控制台，如
mysql -u root -p
mysql>use 数据库
然后使用source命令，后面参数为脚本文件(如这里用到的.sql)
mysql>source /data/dbname.sql



### 参数详情

> mysqldum 参数解析
> 1 -A --all-databases：导出全部数据库
> 2 -Y --all-tablespaces：导出全部表空间
> 3 -y --no-tablespaces：不导出任何表空间信息
> 4 --add-drop-database每个数据库创建之前添加drop数据库语句。
> 5 --add-drop-table每个数据表创建之前添加drop数据表语句。(默认为打开状态，使用--skip-add-drop-table取消选项)
> 6 --add-locks在每个表导出之前增加LOCK TABLES并且之后UNLOCK TABLE。(默认为打开状态，使用--skip-add-locks取消选项)
> 7 --comments附加注释信息。默认为打开，可以用--skip-comments取消
> 8 --compact导出更少的输出信息(用于调试)。去掉注释和头尾等结构。可以使用选项：--skip-add-drop-table --skip-add-locks --skip-comments --skip-disable-keys
> 9 -c --complete-insert：使用完整的insert语句(包含列名称)。这么做能提高插入效率，但是可能会受到max_allowed_packet参数的影响而导致插入失败。
> 10 -C --compress：在客户端和服务器之间启用压缩传递所有信息
> 11 -B--databases：导出几个数据库。参数后面所有名字参量都被看作数据库名。
> 12 --debug输出debug信息，用于调试。默认值为：d:t:o,/tmp/
> 13 --debug-info输出调试信息并退出
> 14 --default-character-set设置默认字符集，默认值为utf8
> 15 --delayed-insert采用延时插入方式（INSERT DELAYED）导出数据
> 16 -E--events：导出事件。
> 17 --master-data：在备份文件中写入备份时的binlog文件，在恢复进，增量数据从这个文件之后的日志开始恢复。值为1时，binlog文件名和位置没有注释，为2时，则在备份文件中将binlog的文件名和位置进行注释
> 18 --flush-logs开始导出之前刷新日志。请注意：假如一次导出多个数据库(使用选项--databases或者--all-databases)，将会逐个数据库刷新日志。除使用--lock-all-tables或者--master-data外。在这种情况下，日志将会被刷新一次，相应的所以表同时被锁定。因此，如果打算同时导出和刷新日志应该使用--lock-all-tables 或者--master-data 和--flush-logs。
> 19 --flush-privileges在导出mysql数据库之后，发出一条FLUSH PRIVILEGES 语句。为了正确恢复，该选项应该用于导出mysql数据库和依赖mysql数据库数据的任何时候。
> 20 --force在导出过程中忽略出现的SQL错误。
> 21 -h --host：需要导出的主机信息
> 22 --ignore-table不导出指定表。指定忽略多个表时，需要重复多次，每次一个表。每个表必须同时指定数据库和表名。例如：--ignore-table=database.table1 --ignore-table=database.table2 ……
> 23 -x --lock-all-tables：提交请求锁定所有数据库中的所有表，以保证数据的一致性。这是一个全局读锁，并且自动关闭--single-transaction 和--lock-tables 选项。
> 24 -l --lock-tables：开始导出前，锁定所有表。用READ LOCAL锁定表以允许MyISAM表并行插入。对于支持事务的表例如InnoDB和BDB，--single-transaction是一个更好的选择，因为它根本不需要锁定表。请注意当导出多个数据库时，--lock-tables分别为每个数据库锁定表。因此，该选项不能保证导出文件中的表在数据库之间的逻辑一致性。不同数据库表的导出状态可以完全不同。
> 25 --single-transaction：适合innodb事务数据库的备份。保证备份的一致性，原理是设定本次会话的隔离级别为Repeatable read，来保证本次会话（也就是dump）时，不会看到其它会话已经提交了的数据。
> 26 -F：刷新binlog，如果binlog打开了，-F参数会在备份时自动刷新binlog进行切换。
> 27 -n --no-create-db：只导出数据，而不添加CREATE DATABASE 语句。
> 28 -t --no-create-info：只导出数据，而不添加CREATE TABLE 语句。
> 29 -d --no-data：不导出任何数据，只导出数据库表结构。
> 30 -p --password：连接数据库密码
> 31 -P --port：连接数据库端口号
> 32 -u --user：指定连接的用户名。
> 

### 参考

- [MySQL入门篇（六）之mysqldump备份和恢复](https://www.cnblogs.com/linuxk/p/9371475.html)