---
title: "thinkphp验证码不显示"
categories: [ "代码人生" ]
tags: [ "thinkphp","verify" ]
draft: false
slug: "thinkphp_verify"
date: "2014-07-10 09:02:00"
url: "thinkphp_verify.html"
---

今天解决了一个验证码不显示的问题，值得大家注意


<!--more-->


TP的验证码已经做得很傻瓜了，两行代码搞定

    $Verify = new \Think\Verify();
    $Verify->entry();

firefox浏览器显示：

> 因其本身有错无法显示

分析了半天，用下面的代码发现图片输出前已经输出了有3个字节字符，导致图片无法被正确识别

    var_dump( ob_get_clean() );
    

3个字节很诡异，立马能想到UTF-8 with BOM，立马检查所有文件，果不其然，某一个文件就是UTF-8 with BOM的！果断改之，解决！

最后，如果实在找不到原因，在输出图片之前清一下缓冲区即可

    ob_clean();
    

这个问题还好是项目刚刚开始发现的，否则以后动辄几百上千的文件，要发现这个问题就麻烦了，网上有一键去BOM的工具，可以用一用。当然，这些都是后话，团队开发一定要预先确定编码规范，这样能够节约N多时间去找一个让人MAD的BUG，一定要防患于未然！

* * *

另外要**强调**，还有一个情况也会出现这个情况！

就是PHP文件结束标记后出现空格或tab，也会被输出

[详情请参考这里][1]

 [1]: http://9iphp.com/opensystem/thinkphp/358.html