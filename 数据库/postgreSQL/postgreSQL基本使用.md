```shell
简单操作

创建数据库ttdb01:
CREATE DATABASE ttdb01;

使用 \l 用于查看已经存在的数据库:
postgres=# \l

接下来我们可以使用 \c + 数据库名 来进入数据库：
postgres=# \c ttdb01 
You are now connected to database "ttdb01" as user "postgres".

创建表：
ttdb01=# create table  test1(id int  not null  primary key,name  varchar(20),age int );
CREATE TABLE

插入数据:
ttdb01=# insert into test1 values(1,'aaa',28);

简单查询:
ttdb01=# select * from test1;

查看表结构:
ttdb01=#\d t1;

查看用户:
ttdb01=#\du;

================================================================
postgreSQL 创建用户并授权

使用超级用户登录数据库

然后执行

CREATE USER testUser WITH PASSWORD '*****';
这时就创建了用户 testUser

GRANT ALL PRIVILEGES ON DATABASE testDB TO testUser;
将数据库 testDB 权限授权于 testUser
但此时用户还是没有读写权限，需要继续授权表

GRANT ALL PRIVILEGES ON all tables in schema public TO testUser;
注意，该sql语句必须在所要操作的数据库里执行
这一句是将当前数据库下 public schema 的表都授权于 testUser
如果要单独一个权限以及单独一个表，则：

GRANT SELECT ON TABLE mytable TO testUser;
这一句就是将 mytable 这张表的查询权限授予 testUser

postgresql 修改用户密码
如何安全地修改密码：

方式1 
使用psql，连接到Postgres Server：

test1=> \password
Enter new password:
Enter it again:
test1=>
我将原密码hello，修改为hellojava.123456
这种修改方式相当于向postgres server 发送了如下命令：
 
ALTER USER postgres PASSWORD ' md53175af154as54df5as4d5f45sd6af';
后面的字符串是  hellojava经过md5加密后的字符串
12345
注意：尽量不要使用postgres作为用户密码，防止被攻击。

方式2：可以直接发送sql修改：

`这种方式不仅仅限于psql了，其余客户端也能修改，如pgadmin等
 
ALTER USER test1 PASSWORD 'secret'
弊端：通过sql修改，有可能会将修改语句记录在相关工具的log里。
例如：通过psql 运行该条sql，则在.psql_history文件中会有相应语句的记录
      有密码泄露的风险

================================================================

数据库实例初始化完成后，在数据目录的根目录下会有postgresql.conf、postgresql.auto.conf、pg_hba.conf和pg_ident.conf这几个配置文件。
除身份认证以外的数据库系统行为都由postgresql.conf配置。

修改数据目录下的postgresql.conf 及 pg_hba.conf文件
postgresql.conf --配置PostgreSQL数据库服务器的相应的参数。
pg_hba.conf --配置对数据库的访问权限，连接认证

1、vim postgresql.conf
修改 listen_addresses 为 * ，代表所有主机皆可访问

listen_addresses = ‘*’ # what IP address(es) to listen on;

2、
vim pg_hba.conf
#IPv4 local connections:
host all all 127.0.0.1/32 trust
host all all 0.0.0.0/0 trust

```

