---
title: "golang使用multiconfig后导致glog无法接受命令行参数"
categories: [ "代码人生" ]
tags: [ "golang","multiconfig" ]
draft: false
slug: "multiconfig_vs_glog"
date: "2016-03-18 06:04:00"
url: "multiconfig_vs_glog.html"
---

![flag][1]

老高在最近的一个项目(GOLANG)中加入了[koding/multiconfig][2]。

这个包可以用默认值(struct tag)<文件<环境变量<命令行参数的顺序设置参数，很好很强大，极大地方便了测试和开发。

但是同时他也带来了一个问题，和glog一起工作的时候会报错：


> flag provided but not defined: -alsologtostderr


<!--more-->


因为项目同时使用了glog包，而命令行参数 `-alsologtostderr` 的意思是同时将日志打印到标准输出。但是自从使用了multiconfig后，这个命令直接会导致程序停止。


在对multiconfig源码分析之后，老高找到了问题的所在，这还要从[FLAG][3]包讲起。


## FLAG

> Package flag implements command-line flag parsing.

flag包实现了命令行的参数解析，说白了，就是把命令后面跟的参数选项映射为程序里的变量，好让程序判断处理逻辑。当给一个程序传入了未定义的参数或选项时，就会得到类似`flag provided but not defined`的错误。

[文档][4]中列出了flag包的各种方法，其中比较重要的就是Parse()方法，在绑定好了各种参数后，此方法必须被调用，否则无法捕获参数。

再看看两个核心的结构体

```golang
// A FlagSet represents a set of defined flags.  The zero value of a FlagSet
// has no name and has ContinueOnError error handling.
type FlagSet struct {
    // Usage is the function called when an error occurs while parsing flags.
    // The field is a function (not a method) that may be changed to point to
    // a custom error handler.
    Usage func()

    name          string
    parsed        bool
    actual        map[string]*Flag
    formal        map[string]*Flag
    args          []string // arguments after flags
    errorHandling ErrorHandling
    output        io.Writer // nil means stderr; use out() accessor
}

// A Flag represents the state of a flag.
type Flag struct {
    Name     string // name as it appears on command line
    Usage    string // help message
    Value    Value  // value as set
    DefValue string // default value (as text); for usage message
}
```

Flag是一个单独的解析实例，而FlagSet是一个flag的集合。

系统默认会创建一个FlagSet，当执行Int(),Bool等方法时，会被保存在这个默认的CommandLine中。

```golang
var CommandLine = NewFlagSet(os.Args[0], ExitOnError)
```

## 分析

了解到这个基本知识后，定位到multiconfig的flag.go的源代码中，发现FlagLoader在接口方法Load中，新建了一个NewFlagSet，并将结合配置填充进去数据，随后方法调用了`flagSet.Parse(args)`，就会有找不到配置的报错。

glog在init方法中新建了6个flag

```
flag.BoolVar(&logging.toStderr, "logtostderr", false, "log to standard error instead of files")
flag.BoolVar(&logging.alsoToStderr, "alsologtostderr", false, "log to standard error as well as files")
flag.Var(&logging.verbosity, "v", "log level for V logs")
flag.Var(&logging.stderrThreshold, "stderrthreshold", "logs at or above this threshold go to stderr")
flag.Var(&logging.vmodule, "vmodule", "comma-separated list of pattern=N settings for file-filtered logging")
flag.Var(&logging.traceLocation, "log_backtrace_at", "when logging hits line file:N, emit a stack trace")
```

他们被默认添加进了CommandLine中，但是在multiconfig中只是在自己定义的flagSet中进行了校验，所以导致了这个BUG，修复的方法就是在Parse前将glog设置的flag导入到当前的set中，所有老高在return之前，写下如下代码：

```bash
// 其实flag.VisitAll在源码里直接调用了CommandLine的VisitAll方法
flag.VisitAll(func(ff *flag.Flag) {
    f.flagSet.Var(ff.Value, ff.Name, ff.Usage)
})
```

之后还要保证glog在multiconfig之前被载入即可！




## 还有一个方法

其实你应该想到一个方法，可以在不修改源码的情况下消除此bug，就是在自己的配置中加入glog的六个配置结构即可！不过这种做法移植性不强，不过也算是一个hack啦~


  [1]: https://blog.phpgao.com/usr/uploads/2016/03/506524902.png
  [2]: https://github.com/koding/multiconfig
  [3]: https://godoc.org/flag
  [4]: https://godoc.org/flag#pkg-index