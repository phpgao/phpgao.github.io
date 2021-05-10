---
title: "SVN预提交(Pre-Commit)钩子的利用"
categories: [ "代码人生","服务器技术" ]
tags: [ "hook","svn","pre_commit" ]
draft: false
slug: "svn_pre_commit"
date: "2014-12-02 03:33:00"
url: "svn_pre_commit.html"
---

转自：http://java.dzone.com/articles/useful-subversion-pre-commit

1. Checks whether the commit message is not empty
1. Checks whether the commit message consists of at least 5 characters
1. Checks if the committed files are UTF-8 compliant
1. Checks whether the svn:eol-style property is set to LF on newly added files
1. Checks if the committed files have no TAB characters

The UTF-8 and TAB checks are performed on the following file suffixes
- *.java
- *.js
- *.xhtml
- *.css
- *.xml
- *.properties (only check for TABs here, no check for UTF-8 compliance)

翻译一下：

1. 检查提交日志是否为空
1. 检查提交日志最少需要N个字符
1. 检查提交文件是否是UTF-8格式
1. 检查新文件的换行模式是否为LF
1. 检查提交的文件是否含有TABs换行符

检查UTF-8编码和TABs换行符只针对以下后缀文件：
- *.java
- *.js
- *.xhtml
- *.css
- *.xml
- *.properties (只检查TABs，不检查UTF-8)


## 以下是代码

注意:针对Linux

```bash
#!/bin/bash

REPOS="$1"
TXN="$2"


# Make sure that the log message contains some text.
SVNLOOK=/usr/bin/svnlook
ICONV=/usr/bin/iconv

SVNLOOKOK=1
LOGMSG=`$SVNLOOK log -t "$TXN" "$REPOS" | wc -c` 
if [ "$LOGMSG" -lt 2 ];
then
   echo -e "\t That logmessage contains at least 2 alphanumeric characters. Commit aborted!" 1>&2
  exit 1
fi


# Make sure that all files to be committed are encoded in UTF-8.
while read changeline; 
do

  # Get just the file (not the add / update / etc. status).
  file=${changeline:4}

  # Only check source files.
  if [[ $file == *.java || $file == *.xhtml || $file == *.css || $file == *.xml || $file == *.js ]] ; then
    $SVNLOOK cat -t "$TXN" "$REPOS" "$file" | $ICONV -f UTF-8 -t UTF-8 -o /dev/null
    if [ "${PIPESTATUS[1]}" != 0 ] ; then
      echo "Only UTF-8 files can be committed ("$file")" 1>&2
      exit 1
    fi
  fi
done < <($SVNLOOK changed -t "$TXN" "$REPOS")

# Check files for svn:eol-style property
# Exit on all errors.
set -e
EOL_STYLE="LF"
echo "`$SVNLOOK changed -t "$TXN" "$REPOS"`" | while read REPOS_PATH
do
 if [[ $REPOS_PATH =~ A[[:blank:]]{3}(.*)\.(java|css|properties|xhtml|xml|js) ]]
 then
  if [ ${#BASH_REMATCH[*]} -ge 2 ]
    then
  FILENAME=${BASH_REMATCH[1]}.${BASH_REMATCH[2]};

  # Make sure every file has the right svn:eol-style property set
   if [ $EOL_STYLE != "`$SVNLOOK propget -t \"$TXN\" \"$REPOS\" svn:eol-style \"$FILENAME\" 2> /dev/null`" ]
    then
    ERROR=1;
      echo "svn ps svn:eol-style $EOL_STYLE \"$FILENAME\"" >&2
   fi
  fi
 fi
 test -z $ERROR || (echo "Please execute above commands to correct svn property settings. EOL Style LF must be used!" >& 2; exit 1)
done



# Block commits with tabs
# This is coded in python
# Exit on all errors
set -e

$SVNLOOK diff -t "$TXN" "$REPOS" | python /dev/fd/3 3<<'EOF'
import sys
ignore = True
SUFFIXES = [ ".java", ".css", ".xhtml", ".js", ".xml", ".properties" ]
filename = None

for ln in sys.stdin:

    if ignore and ln.startswith("+++ "):
        filename = ln[4:ln.find("\t")].strip()
        ignore = not reduce(lambda x, y: x or y, map(lambda x: filename.endswith(x), SUFFIXES))

    elif not ignore:
        if ln.startswith("+"):
        
           if ln.count("\t") > 0:
              sys.stderr.write("\n*** Transaction blocked, %s contains tab character:\n\n%s" % (filename, ln))
              sys.exit(1)

        if not (ln.startswith("@") or \
           ln.startswith("-") or \
           ln.startswith("+") or \
           ln.startswith(" ")):

           ignore = True

sys.exit(0)
EOF

# All checks passed, so allow the commit.
exit 0
```

## 如何使用

1. 重命名hooks文件夹下`pre-commit.tmpl` 为 `pre-commit`。
1. 修改文件内容
1. 将文件变为可执行 chmod +x pre-commit

## 提交完代码自动更新

```bash
vim hooks/post-commit

#!/bin/sh
export LANG=zh_CN.UTF-8  # 设置UTF-8编码
SVN=/usr/bin/svn         # 这里配置的是svn安装bin目录下的svn文件
WEB=/var/www/html      # 要更新的目录
$SVN update $WEB --username xxxx --password xxxx
```