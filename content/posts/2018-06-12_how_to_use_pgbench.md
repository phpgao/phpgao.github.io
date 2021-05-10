---
title: "使用pgbench测试你的数据库性能"
categories: [ "服务器技术" ]
tags: [ "sql","postgresql","pgbench" ]
draft: false
slug: "how_to_use_pgbench"
date: "2018-06-12 13:01:00"
url: "how_to_use_pgbench.html"
---

老高最近遇到了一些性能问题，排查起来很麻烦，其中一个步骤就是需要确定当前DB的抗压能力，Google后收获很大，所以赶紧和老高一起学习研究如果使用pgbench测试你的数据库性能吧！


<!--more-->


## 简单翻译

```
pgbench is a benchmarking tool for PostgreSQL.

Usage:
  pgbench [OPTION]... [DBNAME]

Initialization options: # 初始化选项
  -i, --initialize         invokes initialization mode # 初始化数据库
  -F, --fillfactor=NUM     set fill factor  # 设定填充因子
  -n, --no-vacuum          do not run VACUUM after initialization # 完成后不收缩
  -q, --quiet              quiet logging (one message each 5 seconds) # 静默模式
  -s, --scale=NUM          scaling factor # 规模因子
  --foreign-keys           create foreign key constraints between tables # 在表间创建外键
  --index-tablespace=TABLESPACE
                           create indexes in the specified tablespace # 在指定表空间创建索引
  --tablespace=TABLESPACE  create tables in the specified tablespace # 在指定表空间创建表
  --unlogged-tables        create tables as unlogged tables # 创建unlogged类型的表

Options to select what to run:
  -b, --builtin=NAME[@W]   add builtin script NAME weighted at W (default: 1)
                           (use "-b list" to list available scripts)
  -f, --file=FILENAME[@W]  add script FILENAME weighted at W (default: 1)
  -N, --skip-some-updates  skip updates of pgbench_tellers and pgbench_branches
                           (same as "-b simple-update")
  -S, --select-only        perform SELECT-only transactions
                           (same as "-b select-only")

Benchmarking options:
  -c, --client=NUM         number of concurrent database clients (default: 1) # 模拟客户端数
  -C, --connect            establish new connection for each transaction # 为每个事务启用新链接
  -D, --define=VARNAME=VALUE
                           define variable for use by custom script # 用户脚本定义的自定义变量
  -j, --jobs=NUM           number of threads (default: 1) # 工作线程数
  -l, --log                write transaction times to log file # 记录每个事务的时间
  -L, --latency-limit=NUM  count transactions lasting more than NUM ms as late # 标记超时
  -M, --protocol=simple|extended|prepared
                           protocol for submitting queries (default: simple) # 使用的查询协议，默认simple，老高建议使用prepared比较接近实际需求
  -n, --no-vacuum          do not run VACUUM before tests # 测试前不收缩表
  -P, --progress=NUM       show thread progress report every NUM seconds #  # 每隔$$秒输出一次线程进度报告
  -r, --report-latencies   report average latency per command # 报告每个sql的平均执行延迟
  -R, --rate=NUM           target rate in transactions per second # 目标TPS
  -s, --scale=NUM          report this scale factor in output # 在输出中报告规模因子
  -t, --transactions=NUM   number of transactions each client runs (default: 10) # 每个客户端执行的事务数
  -T, --time=NUM           duration of benchmark test in seconds # 测试执行时间
  -v, --vacuum-all         vacuum all four standard tables before tests # 测试前收缩表
  --aggregate-interval=NUM aggregate data over NUM seconds # 每隔N秒聚合一次数据
  --log-prefix=PREFIX      prefix for transaction time log file
                           (default: "pgbench_log")
  --progress-timestamp     use Unix epoch timestamps for progress
  --sampling-rate=NUM      fraction of transactions to log (e.g., 0.01 for 1%)

Common options:
  -d, --debug              print debugging output
  -h, --host=HOSTNAME      database server host or socket directory
  -p, --port=PORT          database server port number
  -U, --username=USERNAME  connect as specified database user
  -V, --version            output version information, then exit
  -?, --help               show this help, then exit
```

## 准备数据库

```
# -h 1.1.1.1 -U laogao -p 5432 指定了主机地址 用户名以及使用端口
# 实际使用时酌情忽略
createdb -h 1.1.1.1 -U laogao -p 5432 pgbench_test

# or
createdb pgbench_test
```


## 初始化数据库

> 需要注意两个参数 -F -s，-F的基数为1000，-s后的数字会在此基数上翻倍。如 -F 100 -s 10 = 1000*100再乘10

```
createdb -h 1.1.1.1 -U laogao -p 5432 -i -F 100 -s 10 pgbench_test

# or
pgbench -i -F 100 -s 10 pgbench_test

# 或者更简单
pgbench -i pgbench_test
```

## 使用脚本测试

再次阶段我们可以使用自己的脚本，也可以使用程序自带的脚本。程序自带的脚本老高贴在下面，可以看到包括有一条插入，三条更新和一条查询语句。

```
\set aid random(1, 100000 * :scale)
\set bid random(1, 1 * :scale)
\set tid random(1, 10 * :scale)
\set delta random(-5000, 5000)
BEGIN;
UPDATE pgbench_accounts SET abalance = abalance + :delta WHERE aid = :aid;
SELECT abalance FROM pgbench_accounts WHERE aid = :aid;
UPDATE pgbench_tellers SET tbalance = tbalance + :delta WHERE tid = :tid;
UPDATE pgbench_branches SET bbalance = bbalance + :delta WHERE bid = :bid;
INSERT INTO pgbench_history (tid, bid, aid, delta, mtime) VALUES (:tid, :bid, :aid, :delta, CURRENT_TIMESTAMP);
END;
```


### 使用默认脚本测试

> 停停停，我们现在回忆一下帮助文档里的内容。我们主要关心的是，-T 60，总时间 -c 客户端数 -j 工作线程数 -r 输出每个SQL的执行延迟

```
pgbench -T60 -c8 -j8 pgbench_test -U laogao -p 5432 pgbench_test

# or
pgbench -T60 -c8 -j8 pgbench_test
```

### 使用自定义脚本测试

#### 写

当然，我们可以使用自己的脚本进行独立测试，我们可以事先准备好我们的数据表，再次老高偷个懒，使用之前生成的数据表用来演示！所以我们直接准备一个sql文件，内容如下。我们将其保存为insert.sql

```
\set aid random(1, 100000 * :scale)
\set bid random(1, 1 * :scale)
\set tid random(1, 10 * :scale)
\set delta random(-5000, 5000)
INSERT INTO pgbench_history (tid, bid, aid, delta, mtime) VALUES (:tid, :bid, :aid, :delta, CURRENT_TIMESTAMP);
```

然后执行下面的命令

```
# 看出来没，老高在压自己的机器，结果当然不准确了
pgbench -M prepared -rf ./insert.sql -c 4 -j 4 -T 60 pgbench_test

# 换成其他机器就行
pgbench -M prepared -rf ./insert.sql -c 4 -j 4 -T 60 -U laogao -p 5432 pgbench_test
```

晒晒结果：

```
starting vacuum...end.
transaction type: ./insert.sql
scaling factor: 1
query mode: prepared
number of clients: 4
number of threads: 4
duration: 60 s
number of transactions actually processed: 676592
latency average = 0.355 ms
tps = 11276.383687 (including connections establishing)
tps = 11278.183073 (excluding connections establishing)
script statistics:
 - statement latencies in milliseconds:
         0.002  \set aid random(1, 100000 * :scale)
         0.001  \set bid random(1, 1 * :scale)
         0.001  \set tid random(1, 10 * :scale)
         0.001  \set delta random(-5000, 5000)
         0.352  INSERT INTO pgbench_history (tid, bid, aid, delta, mtime) VALUES (:tid, :bid, :aid, :delta, CURRENT_TIMESTAMP);
```

#### update

待续。。。