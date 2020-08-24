1.导出整个数据库
mysqldump -u 用户名 -p 数据库名 > 导出的文件名
mysqldump -u dbuser -p dbname > dbname.sql

2.导出一个表
mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
mysqldump -u dbuser -p dbname users> dbname_users.sql

导出一个数据库结构
mysqldump -u dbuser -p -d --add-drop-table dbname >d:/dbname_db.sql
-d 没有数据 --add-drop-table 在每个create语句之前增加一个drop table

4.导入数据库
常用source 命令
进入mysql数据库控制台，如
mysql -u root -p
mysql>use 数据库
然后使用source命令，后面参数为脚本文件(如这里用到的.sql)
mysql>source d:/dbname.sql

mysql -u用户名 -p密码 数据库名 < 数据库名.sql
#mysql -uabc_f -p abc < abc.sql

source导入速度慢的解决办法：

 

假设要讲A数据库的数据导入到B数据库

1、首先确定目标库(B)的参数值，登录数据库B，执行以下命令

mysql>show variables like 'max_allowed_packet';
mysql>show variables like 'net_buffer_length';

2、根据参数值，在A数据库中使用mysqldump命令，如：

E:\eis>mysqldump -uroot -p eis_db goodclassification -e --max_allowed_packet=1048576 --net_buffer_length=16384 >good3.sql

-e 使用包括几个VALUES列表的多行INSERT语法;
--max_allowed_packet=XXX 客户端/服务器之间通信的缓存区的最大大小;
--net_buffer_length=XXX  TCP/IP和套接字通信缓冲区大小,创建长度达net_buffer_length的行。

注意：max_allowed_packet和net_buffer_length不能比目标数据库的设定数值 大，否则可能出错。

3、登录数据库B，执行source命令导入

导出全部数据库
mysqldump -uroot -p --all-databases > sqlfile.sql

https://www.cnblogs.com/qlqwjy/p/8325823.html

##

```bash
[root@gs1-game-malay ~]# cat db_dump.sh 
#!/bin/sh
backup_dir=/root/log
db_name=/root/databases.txt
[ -d ${backup_dir} ] || mkdir ${backup_dir} -p
mysqlpasswd='sv5cYPq9gpyfqnh'

mysql -uroot -p${mysqlpasswd} -e "show databases" 2>/dev/null |grep "gh*" >$db_name

for i in `cat $db_name`
do
        mysqldump  -uroot -p${mysqlpasswd} --skip-lock-tables -d $i > ${backup_dir}/${i}.sql
done

```



