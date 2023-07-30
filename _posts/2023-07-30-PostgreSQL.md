---
layout: mypost
title: PostgreSQL基础
categories: [PostgreSQL]
---

## PostgreSQL

PostgreSQL 是一个免费的对象-关系数据库服务器(ORDBMS)，在灵活的BSD许可证下发行。
PostgreSQL 开发者把它念作 post-gress-Q-L。
PostgreSQL 的 Slogan 是 "世界上最先进的开源关系型数据库"。

### PostgreSQL特征

+ 函数：通过函数，可以在数据库服务器端执行指令程序。
+ 索引：用户可以自定义索引方法，或使用内置的 B 树，哈希表与 GiST 索引。
+ 触发器：触发器是由SQL语句查询所触发的事件。如：一个INSERT语句可能触发一个检查数据完整性的触发器。触发器通常由INSERT或UPDATE语句触发。 多版本并发控制：PostgreSQL使用多版本并发控制（MVCC，Multiversion concurrency control）系统进行并发控制，该系统向每个用户提供了一个数据库的"快照"，用户在事务内所作的每个修改，对于其他的用户都不可见，直到该事务成功提交。
+ 规则：规则（RULE）允许一个查询能被重写，通常用来实现对视图（VIEW）的操作，如插入（INSERT）、更新（UPDATE）、删除（DELETE）。
+ 数据类型：包括文本、任意精度的数值数组、JSON 数据、枚举类型、XML 数据等。
+ 全文检索：通过 Tsearch2 或 OpenFTS，8.3版本中内嵌 Tsearch2。
+ NoSQL：JSON，JSONB，XML，HStore 原生支持，至 NoSQL 数据库的外部数据包装器。
+ 数据仓库：能平滑迁移至同属 PostgreSQL 生态的 GreenPlum，DeepGreen，HAWK 等，使用 FDW 进行 ETL。

### Ubuntu 安装 PostgreSQL

Ubuntu 可以使用 apt-get 安装 PostgreSQL：

```bash
sudo apt-get update
sudo apt-get install postgresql postgresql-client
```

安装完毕后，系统会创建一个数据库超级用户 postgres，密码为空。

```bash
#  sudo -i -u postgres
```

这时使用以下命令进入 postgres，输出以下信息，说明安装成功：

```bash
~$ psql 
psql (14.8 (Ubuntu 14.8-0ubuntu0.22.04.1))
Type "help" for help.

postgres=# 
```

输入以下命令退出 PostgreSQL 提示符：

```bash
\q
```

PostgreSQL 安装完成后默认是已经启动的，但是也可以通过下面的方式来手动启动服务。

```bash
sudo /etc/init.d/postgresql start   # 开启
sudo /etc/init.d/postgresql stop    # 关闭
sudo /etc/init.d/postgresql restart # 重启
```

在安装时，默认密码设置为“postgres”，这也是许多人采用的密码，这给数据库安全带来了隐患。因此，在安装PostgreSQL之后，最好更改初始密码，以提高数据库的安全性。

#### 更改初始密码

示例代码：[参看](https://www.python100.com/html/115598.html)

```bash
# 进入PostgreSQL Shell（psql）
> sudo -u postgres psql

# 更改密码
postgres=# ALTER USER postgres WITH PASSWORD 'new_password';
```

#### 连接数据库

```bash
: psql -h 127.0.0.1 -p 5432 -U postgres -d django_cmweb                                                                                                                                                [~]
Password for user postgres: 
psql (14.8 (Ubuntu 14.8-0ubuntu0.22.04.1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
Type "help" for help.

django_cmweb=# 

```

#### django连接postgreSQL

##### 安装

```bash
sudo apt-get install pkg-config
sudo apt-get install default-libmysqlclient-dev build-essential

pip install psycopg2 psycopg2-binary -i https://pypi.tuna.tsinghua.edu.cn/simple
```

##### 配置

```bash
# 使用postgresql时，改为此配置
DATABASE_ENGINE = "django.db.backends.postgresql_psycopg2"
DATABASE_NAME = 'django_cmweb' # mysql/postgresql 时使用

# 数据库地址 改为自己数据库地址
DATABASE_HOST = "127.0.0.1"
# # 数据库端口
DATABASE_PORT = 5432
# # 数据库用户名
DATABASE_USER = "postgres"
# # 数据库密码
DATABASE_PASSWORD = ""
```
