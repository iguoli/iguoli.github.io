---
title: PostgreSQL 客户端应用
date: 2020-03-06
modify_date: 2020-03-12
tags: Database
key: Postgresql-psql-2020-03-06
---

PostgreSQL 交互式终端 `psql` 的使用，包括连接字符串，免密设置，元命令以及常见的 SQL 语句。

<!--more-->

## [psql] - PostgreSQL 交互式终端

### MacOS 上安装 [psql]

```zsh
brew install libpq

# libpq is keg-only, you can add its opt-path to the PATH
# and write to ~/.zshrc
export PATH="/usr/local/opt/libpq/bin:$PATH"
```

### [psql] 命令摘要

```zsh
psql [option...] [dbname [username]]

General options:
  -c, --command=COMMAND    run only single command (SQL or internal) and exit
  -d, --dbname=DBNAME      database name to connect to (default: "john")
  -f, --file=FILENAME      execute commands from file, then exit
  -l, --list               list available databases, then exit
```

### 连接数据库

要连接到数据库，需要*目标数据库名称*，*服务器主机名*，*端口号*及*用户名*。可以通过命令行选项（分别为`-d`，`-h`，`-p`和`-U`）告知psql这些参数。

```zsh
psql -h HOSTNAME -U USERNAME -d DBNAME

# 或
psql -h HOSTNAME DBNAME USERNAME
```

通过设置环境变量 *PGDATABASE*，*PGHOST*，*PGPORT*，*PGUSER*可以避免每次输入这些信息。

```zsh
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

```zsh
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

```zsh
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

```zsh
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

```pgsql
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

## [Schemas]

Schema 是一个命名空间，可以包含表，视图，索引，数据类型，函数，操作符等数据库命名对象。

使用模式的原因有：

- 允许多个用户使用同一个数据库而不会互相干扰。

- 将数据库对象组织到逻辑组中，使得它们更易于管理。

PostgreSQL 会为每个新建数据库创建一个名为 `public` 的 schema，你创建的任何对象，如果没有指定 schema，PostgreSQL会自动将它放入 `public` schema，所以下面两条语句作用相同。

```pgsql
CREATE TABLE mytable(...);
CREATE TABLE public.mytable(...);
```

## [SQL Commands]

```pgsql
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

-- SCHEMA
-- 为当前用户创建 myschema
CREATE SCHEMA myschema;

-- 为用户 dbuser 创建一个 schema，名称与用户名相同
CREATE SCHEMA AUTHORIZATION dbuser;

-- 为用户 dbuser 创建名为 test 的 schema, 如果该 schema 已经存在，则不做操作
CREATE SCHEMA IF NOT EXISTS test AUTHORIZATION dbuser;

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

-- GRANT
-- 授权 dbuser 可以对 myschema 中的所有表执行 SELECT INSERT 和 UPDATE 操作
GRANT SELECT INSERT UPDATE ON myschema TO dbuser;

-- 授权所有用户对 catalog 表有 SELECT 权限
GRANT SELECT ON catalog TO PUBLIC;

GRANT ALL PRIVILEGES ON DATABASE mydb TO dbuser;
```

### 使用 [`Copy`][Copy] 语句在数据库与文件系统之间复制数据

```pgsql
# copies a table to the client using the vertical bar (|) as the field delimite
COPY country TO STDOUT (DELIMITER '|');

# copy data from a file into the country table
COPY country FROM '/usr1/proj/bray/sql/country_data';

# copy into a file just the countries whose names start with 'A'
COPY (SELECT * FROM country WHERE country_name LIKE 'A%') TO '/usr1/proj/bray/sql/a_list_countries.copy';

# copy into a compressed file, you can pipe the output through an external compression program
COPY country TO PROGRAM 'gzip > /usr1/proj/bray/sql/country_data.gz';

# copy into a csv file with header
COPY country TO '/tmp/table.csv' (FORMAT csv DELIMITER ',' HEADER)
```

## 参考文档

- [PostgreSQL 官方文档](https://www.postgresql.org/docs/current/index.html)
- [PostgreSQL Client Applications](https://www.postgresql.org/docs/current/reference-client.html)
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)

[psql]: https://www.postgresql.org/docs/current/app-psql.html
[Connection Strings]: https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING
[Parameter Key Values]: https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-PARAMKEYWORDS
[Schemas]: https://www.postgresql.org/docs/current/ddl-schemas.html
[SQL Commands]: https://www.postgresql.org/docs/current/sql-commands.html
[Copy]: https://www.postgresql.org/docs/current/sql-copy.html