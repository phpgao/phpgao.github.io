---
title: "几条命令找出服务器上的垃圾文件"
categories: [ "服务器技术" ]
tags: [ "Linux","command" ]
draft: false
slug: "clean-up-your-linux"
date: "2015-05-24 04:25:00"
url: "clean-up-your-linux.html"
---

Linux用久了也可能出现很多垃圾文件，下面跟着老高用几行命令揪出来占用系统空间的家伙们！

当然，如果你的服务器容量是TB、PB级别的请无视此文。


<!--more-->


## 几个基础命令

### du

`du`命令是disk usage的缩写，很明显，今天的主角就是他了！

```bash
# du的帮助文件
du --help

用法：du [选项]... [文件]...
　或：du [选项]... --files0-from=F
计算每个文件的磁盘用量，目录则取总用量。

长选项必须使用的参数对于短选项时也是必需使用的。
  -a, --all		输出所有文件的磁盘用量，不仅仅是目录
      --apparent-size	显示表面用量，而并非是磁盘用量；虽然表面用量通常会
			小一些，但有时它会因为稀疏文件间的"洞"、内部碎
			片、非直接引用的块等原因而变大。
  -B, --block-size=大小	使用指定字节数的块
  -b, --bytes		等于--apparent-size --block-size=1
  -c, --total		显示总计信息
  -D, --dereference-args	解除命令行中列出的符号连接
      --files0-from=F	计算文件F 中以NUL 结尾的文件名对应占用的磁盘空间
			如果F 的值是"-"，则从标准输入读入文件名
  -H			等于--dereference-args (-D)
  -h, --human-readable	以可读性较好的方式显示尺寸(例如：1K 234M 2G)
      --si		类似-h，但在计算时使用1000 为基底而非1024
  -k			等于--block-size=1K
  -l, --count-links	如果是硬连接，就多次计算其尺寸
  -m			等于--block-size=1M
  -L, --dereference	找出任何符号链接指示的真正目的地
  -P, --no-dereference	不跟随任何符号链接(默认)
  -0, --null		将每个空行视作0 字节而非换行符
  -S, --separate-dirs	不包括子目录的占用量
  -s, --summarize	只分别计算命令列中每个参数所占的总用量
  -x, --one-file-system		跳过处于不同文件系统之上的目录
  -X, --exclude-from=文件	排除与指定文件中描述的模式相符的文件
      --exclude=PATTERN		排除与PATTERN 中描述的模式相符的文件
      --max-depth=N	显示目录总计(与--all 一起使用计算文件)
			当N 为指定数值时计算深度为N；
			--max-depth=0 等于--summarize
      --time		显示目录或该目录子目录下所有文件的最后修改时间
      --time=WORD	显示WORD 时间，而非修改时间：
			atime，access，use，ctime 或status
      --time-style=样式	按照指定样式显示时间(样式解释规则同"date"命令)：
			full-iso，long-iso，iso，+FORMAT
      --help		显示此帮助信息并退出
      --version		显示版本信息并退出

所显示的数值是来自 --block-size、DU_BLOCK_SIZE、BLOCK_SIZE
及 BLOCKSIZE 环境变量中第一个可用的 SIZE 单位。
否则，默认单位是 1024 字节(或是 512，若设定 POSIXLY_CORRECT 的话)。

SIZE 可以是一个可选的整数，后面跟着以下单位中的一个：
KB 1000，K 1024，MB 1000*1000，M 1024*1024，还有 G、T、P、E、Z、Y。
```

## sort

很明显`sort`就是排序的意思，我们需要对`du`出的结果排序。

```bash
用法：sort [选项]... [文件]...
　或：sort [选项]... --files0-from=F
串联排序所有指定文件并将结果写到标准输出。

长选项必须使用的参数对于短选项时也是必需使用的。
排序选项：

  -b, --ignore-leading-blanks	忽略前导的空白区域
  -d, --dictionary-order	只考虑空白区域和字母字符
  -f, --ignore-case		忽略字母大小写
  -g, --general-numeric-sort	按照常规数值排序
  -i, --ignore-nonprinting	只排序可打印字符
  -M, --month-sort		比较 (未知) < "一月" < ... < "十二月"
				在LC_ALL=C 时为(unknown) < `JAN' < ... < `DEC'
  -h, --human-numeric-sort    使用易读性数字(例如： 2K 1G)
  -n, --numeric-sort		根据字符串数值比较
  -R, --random-sort		根据随机hash 排序
      --random-source=文件	从指定文件中获得随机字节
  -r, --reverse			逆序输出排序结果
      --sort=WORD		按照WORD 指定的格式排序：
					一般数字-g，高可读性-h，月份-M，数字-n，
					随机-R，版本-V
  -V, --version-sort		在文本内进行自然版本排序

其他选项：

      --batch-size=NMERGE	一次最多合并NMERGE 个输入；如果输入更多
					则使用临时文件
  -c, --check, --check=diagnose-first	检查输入是否已排序，若已有序则不进行操作
  -C, --check=quiet, --check=silent	类似-c，但不报告第一个无序行
      --compress-program=程序	使用指定程序压缩临时文件；使用该程序
					的-d 参数解压缩文件
      --files0-from=文件	从指定文件读取以NUL 终止的名称，如果该文件被
					指定为"-"则从标准输入读文件名
  -k, --key=位置1[,位置2]	在位置1 开始一个key，在位置2 终止(默认为行尾)
  -m, --merge			合并已排序的文件，不再进行排序
  -o, --output=文件		将结果写入到文件而非标准输出
  -s, --stable			禁用last-resort 比较以稳定比较算法
  -S, --buffer-size=大小	指定主内存缓存大小
  -t, --field-separator=分隔符	使用指定的分隔符代替非空格到空格的转换
  -T, --temporary-directory=目录	使用指定目录而非$TMPDIR 或/tmp 作为
					临时目录，可用多个选项指定多个目录
  -u, --unique		配合-c，严格校验排序；不配合-c，则只输出一次排序结果
  -z, --zero-terminated	以0 字节而非新行作为行尾标志
      --help		显示此帮助信息并退出
      --version		显示版本信息并退出

POS 是F[.C][OPTS]，F 代表域编号，C 是域中字母的位置，F 和C 均从1开始计数
如果没有有效的-t 或-b 选项存在，则从前导空格后开始计数字符。OPTS 是一个或多个
由单个字母表示的顺序选项，以此覆盖此key 的全局顺序设置。如果没有指定key 则
将其整个行。

指定的大小可以使用以下单位之一：
内存使用率% 1%，b 1、K 1024 (默认)，M、G、T、P、E、Z、Y 等依此类推。
```

### head

`head`命令的功能是提取目标的前N行！

```bash
用法：head [选项]... [文件]...
将每个指定文件的头10 行显示到标准输出。
如果指定了多于一个文件，在每一段输出前会给出文件名作为文件头。
如果不指定文件，或者文件为"-"，则从标准输入读取数据。

长选项必须使用的参数对于短选项时也是必需使用的。
  -c,  --bytes=[-]K	显示每个文件的前K 字节内容；
			如果附加"-"参数，则除了每个文件的最后K字节数据外
			显示剩余全部内容
  -n, --lines=[-]K	显示每个文件的前K 行内容；
			如果附加"-"参数，则除了每个文件的最后K 行外显示
			剩余全部内容
  -q, --quiet, --silent	不显示包含给定文件名的文件头
  -v, --verbose		总是显示包含给定文件名的文件头
      --help		显示此帮助信息并退出
      --version		显示版本信息并退出

K 后面可以跟乘号:
b 512, kB 1000, K 1024, MB 1000*1000, M 1024*1024,
GB 1000*1000*1000, G 1024*1024*1024, 对于T, P, E, Z, Y 同样适用。
```

下面的命令如果看不懂请回来查看帮助

## 从根目录开始

先查看根目录下的总大小

```bash

# 进入 / 后在运行
cd /
du -sh

# 或者直接指明根目录，注意命令差异
du / -sh

3.8G	.


# 查看根目录下每个目录的占用
du --max-depth=1 -ah

34M	./root
8.0K	./.pki
1.9G	./usr
4.0K	./srv
7.1M	./bin
3.8G	.
```

## 进一步查找

刚才我们发现 `/usr` 目录很大，其实很正常。。。但是我们还是进去看一下，到底是什么鬼！

```bash
cd /usr
du --max-depth=1 -ah


4.0K	./etc
300M	./lib64
35M	./include
200M	./src
183M	./lib
367M	./local
21M	./sbin
4.0K	./uwsgi_temp
4.0K	./kerberos
4.0K	./scgi_temp
4.0K	./games
697M	./share
12K	./html
95M	./bin
44M	./libexec
1.9G	.
```

通过此方法，我们就可以进一步探索服务器上的垃圾文件了！

> 你知道share、local等比较大的文件夹都用什么重要的功能呢？

## 对结果进行排序

为了更好的比较，我们通过管道符对之前的结果用`sort`进行排序。

```bash
# 正序输出
du --max-depth=1 -a|sort -n

# 逆序输出
du --max-depth=1 -a|sort -rn
```

## 排序后截取前10

我们继续对刚才的结果进行处理，取前十条

```bash
du --max-depth=1 -a|sort -rn|head -10

du --max-depth=1 -a|sort -n|tail -10
```

> tail与head相反，取后面的N条数据。