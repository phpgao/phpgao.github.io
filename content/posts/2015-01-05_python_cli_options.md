---
title: "python接受命令选项-h"
categories: [ "代码人生" ]
tags: [ "python","option","cli" ]
draft: false
slug: "python_cli_options"
date: "2015-01-05 05:44:54"
url: "python_cli_options.html"
---

python在用命令行的时候能够接受很多参数，到底是如何接受那些参数和选项呢？


```python

import sys, getopt

opts, args = getopt.getopt(sys.argv[1:], "hi:o:")
input_file=""
output_file=""
 
for op, value in opts:
    if op == "-i":
        input_file = value
    elif op == "-o":
        output_file = value
    elif op == "-h":
        usage()
        sys.exit()


```