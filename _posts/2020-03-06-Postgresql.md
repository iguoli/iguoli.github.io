---
title: PostgreSQL
date: 2020-03-06
modify_date: 2021-12-16
tags: Database
key: Postgresql-2020-03-06
---

## [psql] - PostgreSQL 交互式终端

PostgreSQL 交互式终端 `psql` 的使用，包括连接字符串，免密设置，元命令以及常见的 SQL 语句。

<!--more-->

### macOS 上安装 [psql]

```sh
brew install libpq

# libpq is keg-only, you can add its opt-path to the PATH
# and write to ~/.zshrc
export PATH="/usr/local/opt/libpq/bin:$PATH"
```

### [psql] 命令摘要

```sh
psql [option...] [dbname [username]]

General options:
  -c, --command=COMMAND    run only single command (SQL or internal) and exit
  -d, --dbname=DBNAME      database name to connect to (default: "john")
  -f, --file=FILENAME      execute commands from file, then exit
  -l, --list               list available databases, then exit
```

### 连接数据库

要连接到数据库，需要*目标数据库名称*，*服务器主机名*，*端口号*及*用户名*。可以通过命令行选项（分别为`-d`，`-h`，`-p`和`-U`）告知psql这些参数。

```sh
psql -h HOSTNAME -U USERNAME -d DBNAME

# 或
psql -h HOSTNAME DBNAME USERNAME
```

通过设置环境变量 *PGDATABASE*，*PGHOST*，*PGPORT*，*PGUSER*可以避免每次输入这些信息。

```sh
export PGHOST='192.168.10.10'
export PGDATABASE='mydb'
export PGUSER='myname'

psql
```

通过设置 `pgpass` 文件可以避免每次输入密码，文件路径为：

- Unix: `~/.pgpass`

- Windows: `%APPDATA%\postgresql\pgpass.conf`

也可以通过环境变量 `PGPASSFILE` 来指定文件路径。

`pgpass` 中每行的格式为：

***hostname:port:database:username:password***

前四个字段可以是具体的字符串或`*`，第一个匹配当前连接的密码会被使用。因此，应该把具体的条目放前面，把通配符多的条目放后面。在 Unix 系统上，还要注意文件的权限是`0600`

```sh
$ vim ~/.pgpass
# hostname:port:database:username:password
192.168.10.10:5432:*:postgres:mypassword

$ chmod 0600 ~/.pgpass

# 测试免密连接
$ psql -c '\conninfo'
You are connected to database "postgres" as user "postgres" on host "192.168.10.10" at port "5432".

$ psql mydb -c '\conninfo'
You are connected to database "mydb" as user "postgres" on host "192.168.10.10" at port "5432".
```

另一种连接数据库的方式是使用[连接字符串(connection strings)][Connection Strings]，有 *Keyword/Value连接字符串* 和 *连接URI(Connection URI)* 两种格式。

```sh
# keyword=value
psql "host=localhost user=postgres dbname=mydb connect_timeout=10 sslmode=require"

# URI
psql postgresql://localhost:5433/mydb?sslmode=require
```

#### Keyword/Value 连接字符串

这种格式中，每个参数都以 `keyword = value` 的形式来设置，`=` 两边的空格是可选的。如果参数值为空，或参数值中含有空格，则用单引号把参数值括起来，例如：`keyword='a value'`。这里了解可识别的[参数关键字][Parameter Key Values]。

```text
host=localhost port=5432 dbname=mydb connect_timeout=10
```

#### Connection URIs

一般形式

```http
postgresql://[user[:password]@][netloc][:port][,...][/dbname][?param1=value1&...]
```

URI scheme指示符可以是 `postgresql://` 或 `postgres://`，每个URI部分都是可选的。以下示例说明了有效的URI语法用法：

```http
postgresql://
postgresql://localhost
postgresql://localhost:5433
postgresql://localhost/mydb
postgresql://user@localhost
postgresql://user:secret@localhost
postgresql://other@localhost/otherdb?connect_timeout=10&application_name=myapp
postgresql://host1:123,host2:456/somedb?target_session_attrs=any&application_name=myapp
```

为了改善与JDBC connection URI的兼容性，将参数 `ssl=true` 转换为 `sslmode=require`。

### [psql] 常用命令

```sh
# 连接数据库，执行命令，然后退出
psql -c '\?'
psql -c '\x' -c 'select * from table1'
psql <<EOF
\x
select * from table1;
select * from table2;
EOF
```

### 在命令执行 SQL 文件

有时有太多 sql 语句，不方便在命令行执行，可以放到文件中来一起执行。

```sql
psql -h 192.168.1.3 -U user -d mydb -a -f run.sql
```

`-a` 或 `--echo-all` 会将执行的每一行 sql 语句同时打在命令行上。在 psql 的 shell 中，可以使用元命令 `\i` 读取文件执行 sql。

### [psql] 元命令(Meta-Commands)

`psql` 中以反斜杠开头的命令是 `psql` 元命令，该命令由 `psql` 本身处理。以下是常用命令:

```text
# 连接到 mydb 数据库
\c mydb

# 列出各类数据库对象及信息
# S = show system objects, + = additional detail
\d[S+]                 list tables, views, and sequences
\d[S+]  NAME           describe table, view, sequence, or index
\da[S]  [PATTERN]      list aggregates
\db[+]  [PATTERN]      list tablespaces
\ddp    [PATTERN]      list default privileges
\dg[S+] [PATTERN]      list roles
\di[S+] [PATTERN]      list indexes
\dm[S+] [PATTERN]      list materialized views
\dn[S+] [PATTERN]      list schemas
\dO[S+] [PATTERN]      list collations
\dp     [PATTERN]      list table, view, and sequence access privileges
\ds[S+] [PATTERN]      list sequences
\dt[S+] [PATTERN]      list tables
\dT[S+] [PATTERN]      list data types
\du[S+] [PATTERN]      list roles
\dv[S+] [PATTERN]      list views
\l[+]   [PATTERN]      list databases
\sf[+]  FUNCNAME       show a function's definition
\sv[+]  VIEWNAME       show a view's definition
\z      [PATTERN]      same as \dp
```

常用元命令

```text
# 列出所有 schema
\dn *

# 列出 pg_catalog schema 下的所有表
\dt pg_catalog.*
\dt pg_*

# 查看 pg_catalog.pg_user 表定义
\d pg_catalog.pg_user
```

## [Schemas]

Schema 是一个命名空间，可以包含表，视图，索引，数据类型，函数，操作符等数据库命名对象。可以将其类比文件系统中的目录，只是不能多层嵌套。

使用模式的原因有：

- 允许多个用户使用一个数据库而不会互相干扰。
- 将数据库对象组织到逻辑组中，使得它们更易于管理。
- 第三方应用可以放到单独的 schema 中，不会与现有的对象产生命名冲突。

PostgreSQL 会为每个新建数据库创建一个名为 `public` 的 schema，你创建的任何对象，如果没有指定 schema，PostgreSQL会自动将它放入 `public` schema，所以下面两条语句作用相同。

```sql
CREATE TABLE mytable(...);
CREATE TABLE public.mytable(...);
```

### [The System Catalog Schema]

除了 `public` 和用户创建的 schema，每个数据库还包含一个 `pg_catalog` schema，该 schema 包含系统表以及所有内置数据类型，函数和运算符，每个系统表都以 `pg_` 前辍开头。

```sql
SELECT * FROM pg_catalog.pg_user;
```

创建 Schema

```sql
-- 为当前用户创建 myschema
CREATE SCHEMA myschema;

-- 创建一个与用户 dbuser 同名的 schema
CREATE SCHEMA AUTHORIZATION dbuser;

-- 为用户 dbuser 创建名为 test 的 schema, 如果该 schema 已经存在，则不做操作
CREATE SCHEMA IF NOT EXISTS test AUTHORIZATION dbuser;
```

删除 Schema

```sql
-- 删除一个空 Schema（不包含任何对象）
DROP SCHEMA myschema;

-- 删除 myschema 和其包含的所有对象
DROP SCHEMA myschema CASCADE;
```

## [SQL Commands] - DDL

### User

```sql
-- USER
-- 创建用户 dbuser，该用户有创建数据库和用户的权限
CREATE USER dbuser WITH CREATEDB CREATEROLE PASSWORD 'test';

-- 删除用户 dbuser
DROP USER dbuser;

-- 修改用户名
ALTER USER dbuser RENAME TO testuser;

-- 修改用户 dbuser 的密码
ALTER USER dbuser WITH PASSWORD '123';

-- 给用户添加 SUPERUSER 权限
ALTER USER dbuser WITH SUPERUSER;

-- 撤销用户SUPERUSER CREATEUSER的权限
ALTER USER dbuser WITH NOSUPERUSER NOCREATEDB;
```

### Database

```sql
-- DATABASE
-- 创建数据库 mydb
CREATE DATABASE mydb OWNER dbuser;

-- 修改数据库名
ALTER DATABASE mydb RENAME TO testdb;

-- 将数据库拥有者改为 testuser
ALTER DATABASE mydb OWNER TO testuser;

-- DROP DATABASE
DROP DATABASE mydb;
```

### Privileges

```sql
-- GRANT
-- 授权 dbuser 可以对 myschema 中的所有表执行 SELECT INSERT 和 UPDATE 操作
GRANT SELECT INSERT UPDATE ON myschema TO dbuser;

-- 授权所有用户对 catalog 表有 SELECT 权限
GRANT SELECT ON catalog TO PUBLIC;

GRANT ALL PRIVILEGES ON DATABASE mydb TO dbuser;
```

## [SQL Commands] - DML

### Create/Insert

```sql
-- CREATE TABLE
CREATE TABLE films (
    code        char(5) CONSTRAINT firstkey PRIMARY KEY,
    title       varchar(40) NOT NULL,
    did         integer NOT NULL,
    date_prod   date,
    kind        varchar(10),
    len         interval hour to minute
);

CREATE TABLE distributors (
     did    integer PRIMARY KEY GENERATED BY DEFAULT AS IDENTITY,
     name   varchar(40) NOT NULL CHECK (name <> '')
);

-- Copy a table from an existing table including both table structure and data
CREATE TABLE copied AS TABLE existing_table;

-- Copy a table structure without data
CREATE TABLE copied AS TABLE existing_table WITH NO DATA;

-- Copy a table with partial data from an existing table
CREATE TABLE copied AS SELECT * FROM existing_table WHERE conditions;

-- Rename an existing column
ALTER TABLE distributors RENAME COLUMN address TO city;

-- change the types of two existing columns in one operation
ALTER TABLE distributors
  ALTER COLUMN email TYPE varchar(50),
  ALTER COLUMN city TYPE varchar(30);

-- DROP TABLE
DROP TABLE films, distributors;

-- INSERT
INSERT INTO films VALUES
    ('UA502', 'Bananas', 105, '1971-07-13', 'Comedy', '82 minutes');

-- 没有 len 字段，该字段会填入默认值
INSERT INTO films (code, title, did, date_prod, kind)
    VALUES ('T_601', 'Yojimbo', 106, '1961-06-16', 'Drama');

-- 插入多条记录，第二条记录的 date_prod 字段使用 DEFAULT 子句生成默认值
INSERT INTO films (code, title, did, date_prod, kind) VALUES
    ('B6717', 'Tampopo', 110, '1985-02-10', 'Comedy'),
    ('HG120', 'The Dinner Game', 140, DEFAULT, 'Comedy');

-- Insert data from another table
INSERT INTO mytable SELECT * FROM anthoer_tabel WHERE conditions;
```

### Read

```sql
-- SELECT by order and display top 5 record
SELECT * FROM films ORDER BY date_prod DESC LIMIT 5;

-- 获取近10年出品的电影
SELECT * FROM films WHERE date_prod > (now() - interval '10 years');
```

#### 查询 jsonb

```sql
SELECT * FROM json_table WHERE json_data::TEXT LIKE '%String%' ORDER BY created LIMIT 10;
```

### Update

```sql
UPDATE films SET kind = 'Dramatic' WHERE kind = 'Drama';
```

### Delete

```sql
DELETE FROM films WHERE title='Yojimbo';
```

[日期函数]

```sql
-- 获取当前完整时间，下面两条命令等效
select now();
select current_timestamp;

-- 获取当前日期
select current_date;

-- 获取当前时间
select current_time;

-- 时间计算，years, months, weeks, days, hours, minutes, seconds
-- 两天后的时间
select now() + interval '2 days';

-- 截取部分时间
select extract(year from now());
select date_part(day, now());
```

### [Copy][Copy]

```sql
-- copies a table to the client using the vertical bar (|) as the field delimite
COPY country TO STDOUT (DELIMITER '|');

-- copy data from a file into the country table
COPY country FROM '/usr1/proj/bray/sql/country_data';

-- copy into a file just the countries whose names start with 'A'
COPY (SELECT * FROM country WHERE country_name LIKE 'A%') TO '/usr1/proj/bray/sql/a_list_countries.copy';

-- copy into a compressed file, you can pipe the output through an external compression program
COPY country TO PROGRAM 'gzip > /usr1/proj/bray/sql/country_data.gz';

-- export to a csv file with header
COPY country TO '/tmp/table.csv' (FORMAT csv, HEADER);

-- import the csv file into a table with header
COPY country (country, text, currency) FROM '/var/lib/pgsql/data.csv' (DELIMITER ',', FORMAT csv, header);
```

注意，输入文件路径可以是绝对路径或相对路径，输出文件路径必须是绝对路径
{:.warning}

## 备份与恢复

### [pg_dump] 和 [pg_restore]

数据库导出/导入工具。

```text
pg_dump dumps a database as a text file or to other formats.

Usage:
  pg_dump [OPTION]... [DBNAME]

General options:
  -f, --file=FILENAME          output file or directory name
  -F, --format=c|d|t|p         output file format (custom, directory, tar, plain text (default))
  -v, --verbose                verbose mode
  -V, --version                output version information, then exit

Options controlling the output content:
  -t, --table=PATTERN          dump the specified table(s) only
```

```text
pg_restore restores a PostgreSQL database from an archive created by pg_dump.

Usage:
  pg_restore [OPTION]... [FILE]

General options:
  -d, --dbname=NAME        connect to database name

Options controlling the restore:
  -a, --data-only              restore only the data, no schema
  -c, --clean                  clean (drop) database objects before recreating
  -C, --create                 create the target database
  -n, --schema=NAME            restore only objects in this schema
  -O, --no-owner               skip restoration of object ownership
  -s, --schema-only            restore only the schema, no data
  -t, --table=NAME             restore named relation (table, view, etc.)
```

#### 示例

将数据库导出为 SQL-script 文件

```sh
# 导出 mydb 数据库到 SQL-script 文件
pg_dump -h localhost -p 5432 -U postgres mydb > db.sql

# 将所有数据恢复到新建的 newdb 数据库
psql -U postgres -c 'CREATE DATABASE newdb;'
psql -U postgres -d newdb -f db.sql
```

将数据库导出为 `custom-format` 格式的归档文件

```text
# 使用 custom-format 格式将数据库 mydb 导出到归档文件
pg_dump -U postgres -Fc mydb > db.dump

# 将所有数据恢复到新创建的 newdb 数据库
psql -U postgres -c 'CREATE DATABASE newdb;'
pg_restore -U postgres -d newdb db.dump

# 自动创建存档文件所使用的同名数据库，并将所有数据恢复到该数据库
# 使用 -C 选项时，-d 指定的数据库仅用于连接并执行DROP DATABASE和CREATE DATABASE命令。
pg_restore -U postgres -d postgres -C db.dump
```

导出单个数据库表到 SQL-script 文件

```text
pg_dump -U postgres -t mytab mydb > db.sql

psql -U postgres -d mydb -f db.sql
```

### [pg_basebackup]

数据库集群备份。`pg_dump` 只能备份单个数据库，而 `pg_basebackup` 可以备份整个 PostgreSQL 实例。

编辑主数据库的 [pg_hba.conf] 文件设置数据库复制权限

```conf
# TYPE  DATABASE        USER            ADDRESS                 METHOD
local   replication     postgres                                peer  # 允许本机与数据库同名的 postgres 用户对数据库进行复制
host    replication     postgres        192.168.33.10/32        md5   # 允许从 IP 为 10.0.0.2 的主机使用 postgres 用户及密码登录数据库进行复制
host    replication     all             0.0.0.0/0               trust # 允许从任意主机使用任意数据库用户登录数据库进行复制
```

在本地备份为 tar.gz 包

```sh
pg_basebackup -D backup -Ft -Xs -z -P
```

在远程机器进行复制并启用

```sh
service posgresql-9.4 stop

mv /var/lib/pgsql/9.4/data /var/lib/pgsql/9.4/data.bak

pg_basebackup -h 10.0.0.1 -U postgres -D /var/lib/pgsql/9.4/data -Xs -P

service postgresql-9.4 start
```

## 安装 PostgreSQL 数据库

- 安装系统 AppStream Repo 中的 *postgresql module*

```sh
yum module list 'postgresql*'

yum module install postgresql:9.6
```

- 安装官网 Repo 中 *postgresql-server*，参考[官网安装文档][install-postgresql]

[install-postgresql]: https://www.postgresql.org/download/linux/redhat/

注意，安装官网 Repo 的 *postgresql-server* 安装包，需要先禁用系统 AppStream Repo 中的 *postgresql module stream*，否则将无法看到该包。
{:.warning}

```sh
# Install the repository RPM:
sudo dnf install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# Disable the built-in PostgreSQL module:
sudo dnf -qy module disable postgresql

# Install PostgreSQL:
sudo dnf install -y postgresql96-server

# Optionally initialize the database and enable automatic start:
sudo /usr/pgsql-9.6/bin/postgresql96-setup initdb
sudo systemctl enable postgresql-9.6
sudo systemctl start postgresql-9.6
```

- 安装不再支持的 PostgreSQL 数据库版本，参考[官方文档][archived-postgresql]

[archived-postgresql]: https://yum.postgresql.org/repopackages/

```sh
# Create special repo config file
cat << EOF > /etc/yum.repos.d/pgdg-94.repo
[pgdg94]
name=PostgreSQL 9.4 RPMs for RHEL/CentOS 8
baseurl=https://yum-archive.postgresql.org/9.4/redhat/rhel-8-x86_64
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-PGDG
EOF

# Disable the built-in PostgreSQL module:
sudo dnf -qy module disable postgresql

# Install PostgreSQL:
sudo dnf install -y postgresql94-server

# Optionally initialize the database and enable automatic start:
sudo /usr/pgsql-9.4/bin/postgresql94-setup initdb
sudo systemctl enable postgresql-9.4
sudo systemctl start postgresql-9.4
```

## 升级 PostgreSQL 数据库

### 9.4 → 9.6

如果已经安装过9.6版本，需要再次将9.4数据库升级到9.6，可以将9.6的data目录备份，然后使用 `/usr/pgsql-9.6/initdb` 新建data目录。

```sh
mv /var/lib/pgsql/9.6/data{,.bak}

/usr/pgsql-9.6/bin/initdb -D /var/lib/pgsql/9.6/data
```

### 兼容性检查

```sh
/usr/pgsql-9.6/bin/pg_upgrade --old-bindir=/usr/pgsql-9.4/bin/ --new-bindir=/usr/pgsql-9.6/bin/ --old-datadir=/var/lib/pgsql/9.4/data/ --new-datadir=/var/lib/pgsql/9.6/data/ --check
```

```text
Performing Consistency Checks
-----------------------------
Checking cluster versions                                   ok
Checking database user is the install user                  ok
Checking database connection settings                       ok
Checking for prepared transactions                          ok
Checking for system-defined composite types in user tables  ok
Checking for reg* system OID user data types                ok
Checking for contrib/isn with bigint-passing mismatch       ok
Checking for roles starting with 'pg_'                      ok
Checking for presence of required libraries                 ok
Checking database user is the install user                  ok
Checking for prepared transactions                          ok
Checking for new cluster tablespace directories             ok

*Clusters are compatible*
```

停掉 9.4 的数据库

```sh
service postgresql-9.4 stop
```

开始升级

```sh
/usr/pgsql-9.6/bin/pg_upgrade --old-bindir=/usr/pgsql-9.4/bin/ --new-bindir=/usr/pgsql-9.6/bin/ --old-datadir=/var/lib/pgsql/9.4/data/ --new-datadir=/var/lib/pgsql/9.6/data/
```

启动 9.6 数据库

```sh
service postgresql-9.6 start
```

使用 psql 登录数据库检查数据库版本

```sql
select version();
```

## 流复制

### 创建流复制用户

```sql
CREATE USER replica WITH REPLICATION LOGIN PASSWORD 'P@ssw0rd!';
```

### 数据库配置查询命令

```sql
show config_file;
show hba_file;
```

### postgresql.conf

```ini
listen_addresses = '*'
port             = 5432
max_connections  = 1024

# replication
max_wal_senders   = 2
wal_level         = hot_standby
wal_keep_segments = 32
hot_standby       = on
```

### [pg_hba.conf]

| TYPE  | DATABASE    | USER    | ADDRESS          | AUTH-METHOD |
| ----- | ----------- | ------- | ---------------- | ----------- |
| local | all         | all     |                  | peer        |
| host  | all         | all     | 127.0.0.1/32     | trust       |
| host  | all         | all     | 192.168.33.0/24  | md5         |
| host  | replication | replica | 192.168.33.10/32 | md5         |

配置修改后，可以使用 `/usr/pgsql-9.4/bin/pg_ctl reload` 命令使配置生效而不重启数据库。

### [recovery.conf]

```ini
recovery_target_timeline = 'latest'
standby_mode = 'on'
primary_conninfo = 'host=192.168.33.10 port=5432 user=replica password=P@ssw0rd!'
```

### 备库设置

在第二台机器安装好 postgresql 服务器，不要启动，为 postgres 用户建立 `.pgpass` 文件

```sh
cat << EOF > ~/.pgpass
192.168.33.10:5432:*:replica:P@ssw0rd!
EOF

chmod 0600 ~/.pgpass
```

使用 `pg_basebackup` 生成备库

```sh
pg_basebackup -h 192.168.33.10 -U replica -D /var/lib/pgsql/9.4/data -Xs -P
```

### 查看数据库状态

1. 主/备库状态

```sh
# version 9.4
/usr/pgsql-9.4/bin/pg_controldata ${PGDATA}
/usr/pgsql-9.4/bin/pg_controldata ${PGDATA} | grep state

# version 12
/usr/pgsql-12/bin/pg_controldata -D ${PGDATA}
```

显示结果

```text
# 主库
Database cluster state:               in production

# 备库
Database cluster state:               in archive recovery
```

2. 检查异步流复制状态，查询主库 *pg_stat_replication* 表

```sql
select pid,state,client_addr,sync_priority,sync_state from pg_stat_replication;
```

3. 检查当前数据库是否处于备库状态，是的话返回 **(t)rue**，否的话返回 **(f)alse**

```sql
select pg_is_in_recovery();
```

### 主从切换

1. 停止主库服务

```sh
sudo service postgresql-9.4 stop
```

2. 提升备库为主库

```sh
sudo -i -u postgres /usr/pgsql-9.4/bin/pg_ctl promote -D /var/lib/pgsql/9.4/data
```

3. 设置原主库 recovery.conf

4. 启动原主库作为备库

```sh
sudo service postgresql-9.4 start
```

## 临时命令

```sql
show archive_modes

-- Create restore point
select pg_create_restore_point('first_point');
```

## 常见问题

系统开启 SELinux 的话可能会阻止 web 应用访问数据库 (比如通过 php 访问数据库)，如果在不关闭 SELinux 的情况下允许对数据库访问，可以设置 **httpd_can_network_connect** 参数为 `on`。

> **httpd_can_network_connect** (HTTPD Service):: Allow HTTPD scripts and modules to connect to the network.

```sh
sudo setsebool -P httpd_can_network_connect_db on
```

## 参考文档

- [PostgreSQL 官方文档](https://www.postgresql.org/docs/current/index.html)
- [PostgreSQL Client Applications](https://www.postgresql.org/docs/current/reference-client.html)
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)

[psql]: https://www.postgresql.org/docs/current/app-psql.html
[Connection Strings]: https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING
[Parameter Key Values]: https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-PARAMKEYWORDS
[Schemas]: https://www.postgresql.org/docs/current/ddl-schemas.html
[The System Catalog Schema]: https://www.postgresql.org/docs/current/ddl-schemas.html#DDL-SCHEMAS-CATALOG

[SQL Commands]: https://www.postgresql.org/docs/current/sql-commands.html
[日期函数]: https://www.postgresql.org/docs/current/functions-datetime.html
[Copy]: https://www.postgresql.org/docs/current/sql-copy.html

[pg_dump]: https://www.postgresql.org/docs/current/app-pgdump.html
[pg_restore]: https://www.postgresql.org/docs/current/app-pgrestore.html

[pg_basebackup]: https://www.postgresql.org/docs/current/app-pgbasebackup.html
[pg_hba.conf]: https://www.postgresql.org/docs/current/auth-pg-hba-conf.html
[recovery.conf]: https://www.postgresql.org/docs/9.6/recovery-config.html