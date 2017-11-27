# PostgreSQL数据库操作


## 创建数据库的姿势
#### 1. 命令行创建
- 命令行创建数据库，首先你要把Linux用户切换到`postgres`用户下，再使用`createdb`命令创建数据库

```sql
[root@host ~]# su - postgres
-bash-4.1$ createdb ziawang_db -O ziawang
-bash-4.1$ psql
psql (9.5.10)
Type "help" for help.

postgres=# \l
                                  List of databases
    Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
------------+----------+----------+-------------+-------------+-----------------------
 postgres   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 template1  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 test       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 ziawang_db | ziawang  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
(5 rows)

postgres=# 	

```

- 参数
	- `-O`为数据库指定所有者，如果不指定，则默认该数据库所有者为`postgres`用户
	- `-E`指定数据库将使用的编码
	- `-T`指定用来创建该数据库的模板
	- `-h`指定要连接的服务端所在的主机IP地址
	- `-p`指定服务端主机监听客户端TCP请求的端口
	- `-U`指定用户
	- 

#### 2. 在psql中创建
- 在数据库客户端psql命令行中创建数据库
- 首先，我们还是要切换到`postgres`Linux用户下，使用`psql`命令直接进入到`postgres`数据库下，创建数据库

```
postgres=# create database ziawang_db owner ziawang;
CREATE DATABASE
postgres=# \l
                                  List of databases
    Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
------------+----------+----------+-------------+-------------+-----------------------
 postgres   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 template1  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 test       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 ziawang_db | ziawang  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
(5 rows)


```

- `owner`后指定数据库所有者


## 选择数据库
#### 命令行中使用`psql`选择数据库
- 使用`psql --help`命令可以获取命令行下psql命令的各种花里胡哨的参数

```linux
[ziawang@host ~]$ psql --help
psql is the PostgreSQL interactive terminal.

Usage:
  psql [OPTION]... [DBNAME [USERNAME]]

General options:
  -c, --command=COMMAND    run only single command (SQL or internal) and exit
  -d, --dbname=DBNAME      database name to connect to (default: "ziawang")
  -f, --file=FILENAME      execute commands from file, then exit
  -l, --list               list available databases, then exit
  -v, --set=, --variable=NAME=VALUE
                           set psql variable NAME to VALUE
                           (e.g., -v ON_ERROR_STOP=1)
  -V, --version            output version information, then exit
  -X, --no-psqlrc          do not read startup file (~/.psqlrc)
  -1 ("one"), --single-transaction
                           execute as a single transaction (if non-interactive)
  -?, --help[=options]     show this help, then exit
      --help=commands      list backslash commands, then exit
      --help=variables     list special variables, then exit

Input and output options:
  -a, --echo-all           echo all input from script
  -b, --echo-errors        echo failed commands
  -e, --echo-queries       echo commands sent to server
  -E, --echo-hidden        display queries that internal commands generate
  -L, --log-file=FILENAME  send session log to file
  -n, --no-readline        disable enhanced command line editing (readline)
  -o, --output=FILENAME    send query results to file (or |pipe)
  -q, --quiet              run quietly (no messages, only query output)
  -s, --single-step        single-step mode (confirm each query)
  -S, --single-line        single-line mode (end of line terminates SQL command)

Output format options:
  -A, --no-align           unaligned table output mode
  -F, --field-separator=STRING
                           field separator for unaligned output (default: "|")
  -H, --html               HTML table output mode
  -P, --pset=VAR[=ARG]     set printing option VAR to ARG (see \pset command)
  -R, --record-separator=STRING
                           record separator for unaligned output (default: newline)
  -t, --tuples-only        print rows only
  -T, --table-attr=TEXT    set HTML table tag attributes (e.g., width, border)
  -x, --expanded           turn on expanded table output
  -z, --field-separator-zero
                           set field separator for unaligned output to zero byte
  -0, --record-separator-zero
                           set record separator for unaligned output to zero byte

Connection options:
  -h, --host=HOSTNAME      database server host or socket directory (default: "local socket")
  -p, --port=PORT          database server port (default: "5432")
  -U, --username=USERNAME  database user name (default: "ziawang")
  -w, --no-password        never prompt for password
  -W, --password           force password prompt (should happen automatically)

```

- 最常用的
	- `-h`
	- `-p`
	- `-W`

#### 交互环境中使用"\c"命令选择数据库
- 在每一个数据库用户的某一个数据库操作环境中，都是可以使用`\c db_name`来切换当前数据库到指定数据库下

```
postgres=# \l
                                  List of databases
    Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
------------+----------+----------+-------------+-------------+-----------------------
 postgres   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 template1  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
            |          |          |             |             | postgres=CTc/postgres
 test       | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 ziawang    | ziawang  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =Tc/ziawang          +
            |          |          |             |             | ziawang=CTc/ziawang
 ziawang_db | ziawang  | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =Tc/ziawang          +
            |          |          |             |             | ziawang=CTc/ziawang
(6 rows)

postgres=# \c ziawang
You are now connected to database "ziawang" as user "postgres".
ziawang=# \q
-bash-4.1$ su - ziawang
[ziawang@host ~]$ psql 
psql (9.5.10)
Type "help" for help.

ziawang=> \c ziawang_db 
You are now connected to database "ziawang_db" as user "ziawang".
ziawang_db=> \c test
You are now connected to database "test" as user "ziawang".
test=> \c postgres
You are now connected to database "postgres" as user "ziawang".
postgres=> 

```


## 删除数据库
#### "dropdb"在命令行删除数据库
- 使用`dropdb --help`查看删除数据库时使用的参数

```
[ziawang@host ~]$ dropdb --help
dropdb removes a PostgreSQL database.

Usage:
  dropdb [OPTION]... DBNAME

Options:
  -e, --echo                show the commands being sent to the server
  -i, --interactive         prompt before deleting anything
  -V, --version             output version information, then exit
  --if-exists               don't report error if database doesn't exist
  -?, --help                show this help, then exit

Connection options:
  -h, --host=HOSTNAME       database server host or socket directory
  -p, --port=PORT           database server port
  -U, --username=USERNAME   user name to connect as
  -w, --no-password         never prompt for password
  -W, --password            force password prompt
  --maintenance-db=DBNAME   alternate maintenance database

```

- 常用的
	- `-h`
	- `-U`
	- `-p`
	- `-W`


#### "drop"在数据库删除指定数据库
- 语法
	- `drop database [if exists] db_name`


## 查看数据库
- 在数据库交互命令中，使用`\l`命令可以列出当前所有数据库

