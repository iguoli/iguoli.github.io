---
title: PostgreSQL 客户端应用
date: 2020-03-06
modify_date: 2020-08-12
tags: Database
key: Postgresql-psql-2020-03-06
---

## [psql] - PostgreSQL 交互式终端

PostgreSQL 交互式终端 `psql` 的使用，包括连接字符串，免密设置，元命令以及常见的 SQL 语句。

<!--more-->

### MacOS 上安装 [psql]

```bash
brew install libpq

# libpq is keg-only, you can add its opt-path to the PATH
# and write to ~/.zshrc
export PATH="/usr/local/opt/libpq/bin:$PATH"
```

### [psql] 命令摘要

```bash
psql [option...] [dbname [username]]

General options:
  -c, --command=COMMAND    run only single command (SQL or internal) and exit
  -d, --dbname=DBNAME      database name to connect to (default: "john")
  -f, --file=FILENAME      execute commands from file, then exit
  -l, --list               list available databases, then exit
```

### 连接数据库

要连接到数据库，需要*目标数据库名称*，*服务器主机名*，*端口号*及*用户名*。可以通过命令行选项（分别为`-d`，`-h`，`-p`和`-U`）告知psql这些参数。

```bash
psql -h HOSTNAME -U USERNAME -d DBNAME

# 或
psql -h HOSTNAME DBNAME USERNAME
```

通过设置环境变量 *PGDATABASE*，*PGHOST*，*PGPORT*，*PGUSER*可以避免每次输入这些信息。

```bash
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

```bash
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

```bash
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

```bash
# 连接数据库，执行命令，然后退出
psql -c '\?'
psql -c '\x' -c 'select * from table1'
psql <<EOF
\x
select * from table1;
select * from table2;
EOF
```

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

## [SQL Commands]

### DDL

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

-- DATABASE
-- 创建数据库 mydb
CREATE DATABASE mydb OWNER dbuser;

-- 修改数据库名
ALTER DATABASE mydb RENAME TO testdb;

-- 将数据库拥有者改为 testuser
ALTER DATABASE mydb OWNER TO testuser;

-- DROP DATABASE
DROP DATABASE mydb;

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

-- DROP TABLE
DROP TABLE films, distributors;

-- GRANT
-- 授权 dbuser 可以对 myschema 中的所有表执行 SELECT INSERT 和 UPDATE 操作
GRANT SELECT INSERT UPDATE ON myschema TO dbuser;

-- 授权所有用户对 catalog 表有 SELECT 权限
GRANT SELECT ON catalog TO PUBLIC;

GRANT ALL PRIVILEGES ON DATABASE mydb TO dbuser;
```

### DML

CRUD

```sql
-- SELECT by order and display top 5 record
SELECT * FROM films ORDER BY date_prod DESC LIMIT 5;

-- 获取近10年出品的电影
SELECT * FROM films where date_prod > (now() - interval '10 years');

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

-- UPDATE
UPDATE films SET kind = 'Dramatic' WHERE kind = 'Drama';

-- DELETE
DELETE FROM films where title='Yojimbo';
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

### 使用 [`Copy`][Copy] 语句在数据库与文件系统之间复制数据

```sql
-- copies a table to the client using the vertical bar (|) as the field delimite
COPY country TO STDOUT (DELIMITER '|');

-- copy data from a file into the country table
COPY country FROM '/usr1/proj/bray/sql/country_data';

-- copy into a file just the countries whose names start with 'A'
COPY (SELECT * FROM country WHERE country_name LIKE 'A%') TO '/usr1/proj/bray/sql/a_list_countries.copy';

-- copy into a compressed file, you can pipe the output through an external compression program
COPY country TO PROGRAM 'gzip > /usr1/proj/bray/sql/country_data.gz';

-- copy into a csv file with header
COPY country TO '/tmp/table.csv' (FORMAT csv, HEADER)
```

注意，输入文件路径可以是绝对路径或相对路径，输出文件路径必须是绝对路径
{:.warning}

## [pg_dump] 和 [pg_restore]

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

### 示例

将数据库导出为 SQL-script 文件

```bash
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

## [pg_basebackup]

数据库集群备份。`pg_dump` 只能备份单个数据库，而 `pg_basebackup` 可以备份整个 PostgreSQL 实例。

编辑主数据库的 [pg_hba.conf] 文件设置数据库复制权限

```conf
# TYPE  DATABASE        USER            ADDRESS                 METHOD
local   replication     postgres                                peer  # 允许本机与数据库同名的 postgres 用户对数据库进行复制
host    replication     postgres        10.0.0.2/32             md5   # 允许从 IP 为 10.0.0.2 的主机使用 postgres 用户及密码登录数据库进行复制
host    replication     all             0.0.0.0/0               trust # 允许从任意主机使用任意数据库用户登录数据库进行复制
```

在本地备份为 tar.gz 包

```bash
pg_basebackup -D backup -Ft -Xs -z -P
```

在远程机器进行复制并启用

```bash
service posgresql-9.4 stop

mv /var/lib/pgsql/9.4/data /var/lib/pgsql/9.4/data.bak

pg_basebackup -h 10.0.0.1 -U postgres -D /var/lib/pgsql/9.4/data -Xs -P

service postgresql-9.4 start
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