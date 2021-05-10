---
title: "PHP一些不为人知的使用方法"
categories: [ "代码人生" ]
tags: [ "PHP" ]
draft: false
slug: "php_barely_used"
date: "2014-09-24 08:13:00"
url: "php_barely_used.html"
---

今天为大家介绍一些比较抽象，但十分有用的关于PHP的用法。老高在通读了THINKPHP源代码后才发现了这一篇天地。合理的使用这些方法或者常量能帮助你更快速的开发或者DEBUG。


<!--more-->


PHP的预定义常量

------

这些常量在 PHP 的内核中定义，大约230多个。它包含 PHP、Zend 引擎和 SAPI 模块，这些常量无法修改，是PHP内核定义的。

老高已经帮你用正则提取出来了，靠背下面的代码在你的环境中执行

以下代码提取自[PHP: 预定义常量][1]

    <?php
    echo "PHP_VERSION: ".PHP_VERSION; //!PHP版本
    echo "\n";
    echo "PHP_OS: ".PHP_OS; //!运行系统
    echo "\n";
    echo "PHP_SAPI: ".PHP_SAPI;  //!运行方式 server api
    echo "\n";
    echo "PHP_EOL: ".PHP_EOL; //!换行符 不同的系统换行符不同
    echo "\n";
    echo "PHP_INT_MAX: ".PHP_INT_MAX; //最大INT值
    echo "\n";
    echo "PHP_INT_SIZE: ".PHP_INT_SIZE; //INT的字长
    echo "\n";
    echo "DEFAULT_INCLUDE_PATH: ".DEFAULT_INCLUDE_PATH; //!默认查找路径
    echo "\n";
    echo "PEAR_INSTALL_DIR: ".PEAR_INSTALL_DIR; //PEAR的安装目录
    echo "\n";
    echo "PEAR_EXTENSION_DIR: ".PEAR_EXTENSION_DIR; //PEAR扩展目录
    echo "\n";
    echo "PHP_EXTENSION_DIR: ".PHP_EXTENSION_DIR;  //PHP扩展目录
    echo "\n";
    echo "PHP_PREFIX: ".PHP_PREFIX;  //PHP安装目录
    echo "\n";
    echo "PHP_BINDIR: ".PHP_BINDIR;  //PHP的bin目录
    echo "\n";
    echo "PHP_LIBDIR: ".PHP_LIBDIR;  //PHPlib目录
    echo "\n";
    echo "PHP_DATADIR: ".PHP_DATADIR;  //......太多不翻译了，有兴趣的TX自行GOOGLE吧
    echo "\n";
    echo "PHP_SYSCONFDIR: ".PHP_SYSCONFDIR;
    echo "\n";
    echo "PHP_LOCALSTATEDIR: ".PHP_LOCALSTATEDIR;
    echo "\n";
    echo "PHP_CONFIG_FILE_PATH: ".PHP_CONFIG_FILE_PATH;
    echo "\n";
    echo "PHP_CONFIG_FILE_SCAN_DIR: ".PHP_CONFIG_FILE_SCAN_DIR;
    echo "\n";
    echo "PHP_SHLIB_SUFFIX: ".PHP_SHLIB_SUFFIX;
    echo "\n";
    echo "PHP_OUTPUT_HANDLER_START: ".PHP_OUTPUT_HANDLER_START;
    echo "\n";
    echo "PHP_OUTPUT_HANDLER_CONT: ".PHP_OUTPUT_HANDLER_CONT;
    echo "\n";
    echo "PHP_OUTPUT_HANDLER_END: ".PHP_OUTPUT_HANDLER_END;
    echo "\n";
    echo "E_ERROR: ".E_ERROR;
    echo "\n";
    echo "E_WARNING: ".E_WARNING;
    echo "\n";
    echo "E_PARSE: ".E_PARSE;
    echo "\n";
    echo "E_NOTICE: ".E_NOTICE;
    echo "\n";
    echo "E_CORE_ERROR: ".E_CORE_ERROR;
    echo "\n";
    echo "E_CORE_WARNING: ".E_CORE_WARNING;
    echo "\n";
    echo "E_COMPILE_ERROR: ".E_COMPILE_ERROR;
    echo "\n";
    echo "E_COMPILE_WARNING: ".E_COMPILE_WARNING;
    echo "\n";
    echo "E_USER_ERROR: ".E_USER_ERROR;
    echo "\n";
    echo "E_USER_WARNING: ".E_USER_WARNING;
    echo "\n";
    echo "E_USER_NOTICE: ".E_USER_NOTICE;
    echo "\n";
    echo "E_ALL: ".E_ALL;
    echo "\n";
    echo "E_STRICT: ".E_STRICT;
    echo "\n";
    echo "EXTR_OVERWRITE: ".EXTR_OVERWRITE;
    echo "\n";
    echo "EXTR_SKIP: ".EXTR_SKIP;
    echo "\n";
    echo "EXTR_PREFIX_SAME: ".EXTR_PREFIX_SAME;
    echo "\n";
    echo "EXTR_PREFIX_ALL: ".EXTR_PREFIX_ALL;
    echo "\n";
    echo "EXTR_PREFIX_INVALID: ".EXTR_PREFIX_INVALID;
    echo "\n";
    echo "EXTR_PREFIX_IF_EXISTS: ".EXTR_PREFIX_IF_EXISTS;
    echo "\n";
    echo "EXTR_IF_EXISTS: ".EXTR_IF_EXISTS;
    echo "\n";
    echo "SORT_ASC: ".SORT_ASC;
    echo "\n";
    echo "SORT_DESC: ".SORT_DESC;
    echo "\n";
    echo "SORT_REGULAR: ".SORT_REGULAR;
    echo "\n";
    echo "SORT_NUMERIC: ".SORT_NUMERIC;
    echo "\n";
    echo "SORT_STRING: ".SORT_STRING;
    echo "\n";
    echo "CASE_LOWER: ".CASE_LOWER;
    echo "\n";
    echo "CASE_UPPER: ".CASE_UPPER;
    echo "\n";
    echo "COUNT_NORMAL: ".COUNT_NORMAL;
    echo "\n";
    echo "COUNT_RECURSIVE: ".COUNT_RECURSIVE;
    echo "\n";
    echo "ASSERT_ACTIVE: ".ASSERT_ACTIVE;
    echo "\n";
    echo "ASSERT_CALLBACK: ".ASSERT_CALLBACK;
    echo "\n";
    echo "ASSERT_BAIL: ".ASSERT_BAIL;
    echo "\n";
    echo "ASSERT_WARNING: ".ASSERT_WARNING;
    echo "\n";
    echo "ASSERT_QUIET_EVAL: ".ASSERT_QUIET_EVAL;
    echo "\n";
    echo "CONNECTION_ABORTED: ".CONNECTION_ABORTED;
    echo "\n";
    echo "CONNECTION_NORMAL: ".CONNECTION_NORMAL;
    echo "\n";
    echo "CONNECTION_TIMEOUT: ".CONNECTION_TIMEOUT;
    echo "\n";
    echo "INI_USER: ".INI_USER;
    echo "\n";
    echo "INI_PERDIR: ".INI_PERDIR;
    echo "\n";
    echo "INI_SYSTEM: ".INI_SYSTEM;
    echo "\n";
    echo "INI_ALL: ".INI_ALL;
    echo "\n";
    echo "M_E: ".M_E;
    echo "\n";
    echo "M_LOG2E: ".M_LOG2E;
    echo "\n";
    echo "M_LOG10E: ".M_LOG10E;
    echo "\n";
    echo "M_LN2: ".M_LN2;
    echo "\n";
    echo "M_LN10: ".M_LN10;
    echo "\n";
    echo "M_PI: ".M_PI;
    echo "\n";
    echo "M_PI_2: ".M_PI_2;
    echo "\n";
    echo "M_PI_4: ".M_PI_4;
    echo "\n";
    echo "M_1_PI: ".M_1_PI;
    echo "\n";
    echo "M_2_PI: ".M_2_PI;
    echo "\n";
    echo "M_2_SQRTPI: ".M_2_SQRTPI;
    echo "\n";
    echo "M_SQRT2: ".M_SQRT2;
    echo "\n";
    echo "M_SQRT1_2: ".M_SQRT1_2;
    echo "\n";
    echo "CRYPT_SALT_LENGTH: ".CRYPT_SALT_LENGTH;
    echo "\n";
    echo "CRYPT_STD_DES: ".CRYPT_STD_DES;
    echo "\n";
    echo "CRYPT_EXT_DES: ".CRYPT_EXT_DES;
    echo "\n";
    echo "CRYPT_MD5: ".CRYPT_MD5;
    echo "\n";
    echo "CRYPT_BLOWFISH: ".CRYPT_BLOWFISH;
    echo "\n";
    echo "DIRECTORY_SEPARATOR: ".DIRECTORY_SEPARATOR;
    echo "\n";
    echo "SEEK_SET: ".SEEK_SET;
    echo "\n";
    echo "SEEK_CUR: ".SEEK_CUR;
    echo "\n";
    echo "SEEK_END: ".SEEK_END;
    echo "\n";
    echo "LOCK_SH: ".LOCK_SH;
    echo "\n";
    echo "LOCK_EX: ".LOCK_EX;
    echo "\n";
    echo "LOCK_UN: ".LOCK_UN;
    echo "\n";
    echo "LOCK_NB: ".LOCK_NB;
    echo "\n";
    echo "HTML_SPECIALCHARS: ".HTML_SPECIALCHARS;
    echo "\n";
    echo "HTML_ENTITIES: ".HTML_ENTITIES;
    echo "\n";
    echo "ENT_COMPAT: ".ENT_COMPAT;
    echo "\n";
    echo "ENT_QUOTES: ".ENT_QUOTES;
    echo "\n";
    echo "ENT_NOQUOTES: ".ENT_NOQUOTES;
    echo "\n";
    echo "INFO_GENERAL: ".INFO_GENERAL;
    echo "\n";
    echo "INFO_CREDITS: ".INFO_CREDITS;
    echo "\n";
    echo "INFO_CONFIGURATION: ".INFO_CONFIGURATION;
    echo "\n";
    echo "INFO_MODULES: ".INFO_MODULES;
    echo "\n";
    echo "INFO_ENVIRONMENT: ".INFO_ENVIRONMENT;
    echo "\n";
    echo "INFO_VARIABLES: ".INFO_VARIABLES;
    echo "\n";
    echo "INFO_LICENSE: ".INFO_LICENSE;
    echo "\n";
    echo "INFO_ALL: ".INFO_ALL;
    echo "\n";
    echo "CREDITS_GROUP: ".CREDITS_GROUP;
    echo "\n";
    echo "CREDITS_GENERAL: ".CREDITS_GENERAL;
    echo "\n";
    echo "CREDITS_SAPI: ".CREDITS_SAPI;
    echo "\n";
    echo "CREDITS_MODULES: ".CREDITS_MODULES;
    echo "\n";
    echo "CREDITS_DOCS: ".CREDITS_DOCS;
    echo "\n";
    echo "CREDITS_FULLPAGE: ".CREDITS_FULLPAGE;
    echo "\n";
    echo "CREDITS_QA: ".CREDITS_QA;
    echo "\n";
    echo "CREDITS_ALL: ".CREDITS_ALL;
    echo "\n";
    echo "STR_PAD_LEFT: ".STR_PAD_LEFT;
    echo "\n";
    echo "STR_PAD_RIGHT: ".STR_PAD_RIGHT;
    echo "\n";
    echo "STR_PAD_BOTH: ".STR_PAD_BOTH;
    echo "\n";
    echo "PATHINFO_DIRNAME: ".PATHINFO_DIRNAME;
    echo "\n";
    echo "PATHINFO_BASENAME: ".PATHINFO_BASENAME;
    echo "\n";
    echo "PATHINFO_EXTENSION: ".PATHINFO_EXTENSION;
    echo "\n";
    echo "PATH_SEPARATOR: ".PATH_SEPARATOR;
    echo "\n";
    echo "CHAR_MAX: ".CHAR_MAX;
    echo "\n";
    echo "LC_CTYPE: ".LC_CTYPE;
    echo "\n";
    echo "LC_NUMERIC: ".LC_NUMERIC;
    echo "\n";
    echo "LC_TIME: ".LC_TIME;
    echo "\n";
    echo "LC_COLLATE: ".LC_COLLATE;
    echo "\n";
    echo "LC_MONETARY: ".LC_MONETARY;
    echo "\n";
    echo "LC_ALL: ".LC_ALL;
    echo "\n";
    echo "LC_MESSAGES: ".LC_MESSAGES;
    echo "\n";
    echo "ABDAY_1: ".ABDAY_1;
    echo "\n";
    echo "ABDAY_2: ".ABDAY_2;
    echo "\n";
    echo "ABDAY_3: ".ABDAY_3;
    echo "\n";
    echo "ABDAY_4: ".ABDAY_4;
    echo "\n";
    echo "ABDAY_5: ".ABDAY_5;
    echo "\n";
    echo "ABDAY_6: ".ABDAY_6;
    echo "\n";
    echo "ABDAY_7: ".ABDAY_7;
    echo "\n";
    echo "DAY_1: ".DAY_1;
    echo "\n";
    echo "DAY_2: ".DAY_2;
    echo "\n";
    echo "DAY_3: ".DAY_3;
    echo "\n";
    echo "DAY_4: ".DAY_4;
    echo "\n";
    echo "DAY_5: ".DAY_5;
    echo "\n";
    echo "DAY_6: ".DAY_6;
    echo "\n";
    echo "DAY_7: ".DAY_7;
    echo "\n";
    echo "ABMON_1: ".ABMON_1;
    echo "\n";
    echo "ABMON_2: ".ABMON_2;
    echo "\n";
    echo "ABMON_3: ".ABMON_3;
    echo "\n";
    echo "ABMON_4: ".ABMON_4;
    echo "\n";
    echo "ABMON_5: ".ABMON_5;
    echo "\n";
    echo "ABMON_6: ".ABMON_6;
    echo "\n";
    echo "ABMON_7: ".ABMON_7;
    echo "\n";
    echo "ABMON_8: ".ABMON_8;
    echo "\n";
    echo "ABMON_9: ".ABMON_9;
    echo "\n";
    echo "ABMON_10: ".ABMON_10;
    echo "\n";
    echo "ABMON_11: ".ABMON_11;
    echo "\n";
    echo "ABMON_12: ".ABMON_12;
    echo "\n";
    echo "MON_1: ".MON_1;
    echo "\n";
    echo "MON_2: ".MON_2;
    echo "\n";
    echo "MON_3: ".MON_3;
    echo "\n";
    echo "MON_4: ".MON_4;
    echo "\n";
    echo "MON_5: ".MON_5;
    echo "\n";
    echo "MON_6: ".MON_6;
    echo "\n";
    echo "MON_7: ".MON_7;
    echo "\n";
    echo "MON_8: ".MON_8;
    echo "\n";
    echo "MON_9: ".MON_9;
    echo "\n";
    echo "MON_10: ".MON_10;
    echo "\n";
    echo "MON_11: ".MON_11;
    echo "\n";
    echo "MON_12: ".MON_12;
    echo "\n";
    echo "AM_STR: ".AM_STR;
    echo "\n";
    echo "PM_STR: ".PM_STR;
    echo "\n";
    echo "D_T_FMT: ".D_T_FMT;
    echo "\n";
    echo "D_FMT: ".D_FMT;
    echo "\n";
    echo "T_FMT: ".T_FMT;
    echo "\n";
    echo "T_FMT_AMPM: ".T_FMT_AMPM;
    echo "\n";
    echo "ERA: ".ERA;
    echo "\n";
    echo "ERA_YEAR: ".ERA_YEAR;
    echo "\n";
    echo "ERA_D_T_FMT: ".ERA_D_T_FMT;
    echo "\n";
    echo "ERA_D_FMT: ".ERA_D_FMT;
    echo "\n";
    echo "ERA_T_FMT: ".ERA_T_FMT;
    echo "\n";
    echo "ALT_DIGITS: ".ALT_DIGITS;
    echo "\n";
    echo "INT_CURR_SYMBOL: ".INT_CURR_SYMBOL;
    echo "\n";
    echo "CURRENCY_SYMBOL: ".CURRENCY_SYMBOL;
    echo "\n";
    echo "CRNCYSTR: ".CRNCYSTR;
    echo "\n";
    echo "MON_DECIMAL_POINT: ".MON_DECIMAL_POINT;
    echo "\n";
    echo "MON_THOUSANDS_SEP: ".MON_THOUSANDS_SEP;
    echo "\n";
    echo "MON_GROUPING: ".MON_GROUPING;
    echo "\n";
    echo "POSITIVE_SIGN: ".POSITIVE_SIGN;
    echo "\n";
    echo "NEGATIVE_SIGN: ".NEGATIVE_SIGN;
    echo "\n";
    echo "INT_FRAC_DIGITS: ".INT_FRAC_DIGITS;
    echo "\n";
    echo "FRAC_DIGITS: ".FRAC_DIGITS;
    echo "\n";
    echo "P_CS_PRECEDES: ".P_CS_PRECEDES;
    echo "\n";
    echo "P_SEP_BY_SPACE: ".P_SEP_BY_SPACE;
    echo "\n";
    echo "N_CS_PRECEDES: ".N_CS_PRECEDES;
    echo "\n";
    echo "N_SEP_BY_SPACE: ".N_SEP_BY_SPACE;
    echo "\n";
    echo "P_SIGN_POSN: ".P_SIGN_POSN;
    echo "\n";
    echo "N_SIGN_POSN: ".N_SIGN_POSN;
    echo "\n";
    echo "DECIMAL_POINT: ".DECIMAL_POINT;
    echo "\n";
    echo "RADIXCHAR: ".RADIXCHAR;
    echo "\n";
    echo "THOUSANDS_SEP: ".THOUSANDS_SEP;
    echo "\n";
    echo "THOUSEP: ".THOUSEP;
    echo "\n";
    echo "GROUPING: ".GROUPING;
    echo "\n";
    echo "YESEXPR: ".YESEXPR;
    echo "\n";
    echo "NOEXPR: ".NOEXPR;
    echo "\n";
    echo "YESSTR: ".YESSTR;
    echo "\n";
    echo "NOSTR: ".NOSTR;
    echo "\n";
    echo "CODESET: ".CODESET;
    echo "\n";
    echo "LOG_EMERG: ".LOG_EMERG;
    echo "\n";
    echo "LOG_ALERT: ".LOG_ALERT;
    echo "\n";
    echo "LOG_CRIT: ".LOG_CRIT;
    echo "\n";
    echo "LOG_ERR: ".LOG_ERR;
    echo "\n";
    echo "LOG_WARNING: ".LOG_WARNING;
    echo "\n";
    echo "LOG_NOTICE: ".LOG_NOTICE;
    echo "\n";
    echo "LOG_INFO: ".LOG_INFO;
    echo "\n";
    echo "LOG_DEBUG: ".LOG_DEBUG;
    echo "\n";
    echo "LOG_KERN: ".LOG_KERN;
    echo "\n";
    echo "LOG_USER: ".LOG_USER;
    echo "\n";
    echo "LOG_MAIL: ".LOG_MAIL;
    echo "\n";
    echo "LOG_DAEMON: ".LOG_DAEMON;
    echo "\n";
    echo "LOG_AUTH: ".LOG_AUTH;
    echo "\n";
    echo "LOG_SYSLOG: ".LOG_SYSLOG;
    echo "\n";
    echo "LOG_LPR: ".LOG_LPR;
    echo "\n";
    echo "LOG_NEWS: ".LOG_NEWS;
    echo "\n";
    echo "LOG_UUCP: ".LOG_UUCP;
    echo "\n";
    echo "LOG_CRON: ".LOG_CRON;
    echo "\n";
    echo "LOG_AUTHPRIV: ".LOG_AUTHPRIV;
    echo "\n";
    echo "LOG_LOCAL0: ".LOG_LOCAL0;
    echo "\n";
    echo "LOG_LOCAL1: ".LOG_LOCAL1;
    echo "\n";
    echo "LOG_LOCAL2: ".LOG_LOCAL2;
    echo "\n";
    echo "LOG_LOCAL3: ".LOG_LOCAL3;
    echo "\n";
    echo "LOG_LOCAL4: ".LOG_LOCAL4;
    echo "\n";
    echo "LOG_LOCAL5: ".LOG_LOCAL5;
    echo "\n";
    echo "LOG_LOCAL6: ".LOG_LOCAL6;
    echo "\n";
    echo "LOG_LOCAL7: ".LOG_LOCAL7;
    echo "\n";
    echo "LOG_PID: ".LOG_PID;
    echo "\n";
    echo "LOG_CONS: ".LOG_CONS;
    echo "\n";
    echo "LOG_ODELAY: ".LOG_ODELAY;
    echo "\n";
    echo "LOG_NDELAY: ".LOG_NDELAY;
    echo "\n";
    echo "LOG_NOWAIT: ".LOG_NOWAIT;
    echo "\n";
    echo LOG_PERROR;

PHP 选项/信息 函数
-------

这里的函数更接近PHP的核心，有些函数可以直接修改PHP的设置，有些则可以统计PHP的内存使用情况，总之，这些函数在你写框架或者系统的时候十分有用。

参考链接[PHP 选项/信息 函数 ][2]


  [1]: http://php.net/manual/zh/reserved.constants.php
  [2]: http://php.net/manual/zh/ref.info.php