---
title: "通过微博的mid获取微博的URL"
categories: [ "代码人生" ]
tags: [  ]
draft: false
slug: "mid_weibo_url"
date: "2014-05-16 08:31:00"
url: "mid_weibo_url.html"
---

代码网上参考的，一共有两种，自己优化了一下：

1.离线方法

参考：http://www.iganlei.cn/demo/186.html

    <?php
        function int10to62($int10)
        {
            static $str62keys;
            $str62keys = array("0","1","2","3","4","5","6","7","8","9","a","b","c","d","e","f","g","h","i","j","k","l","m","n","o","p","q","r","s","t","u","v","w","x","y","z","A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z");
            $s62       = '';
            $r         = 0;
            while ($int10 != 0) {
                $r     = $int10 % 62;
                $s62   = $str62keys[$r] . $s62;
                $int10 = floor($int10 / 62);
            }
            return $s62;
        }
        
    function getCodeByMid($mid)
    {
        $url = '';
        for ($i = strlen($mid) - 7; $i > -7; $i -= 7) //从最后往前以7字节为一组读取mid
            {
            $offset1 = $i < 0 ? 0 : $i;
            $offset2 = $i + 7;
            $num     = substr($mid, $offset1, $offset2 - $offset1);
            $num     = int10to62($num);
            $url     = $num . $url;
        }
        return $url;
    }
    
    function getNewUrl($uid,$mid)
    {
        $newUrl = 'http://weibo.com/' . $uid . '/' . getCodeByMid($mid);
        return $newUrl;
    }
    
    
    echo getNewUrl('phpgao', '3524952365496186');
    
2.api方法

参考：http://blog.csdn.net/k1988/article/details/6684114

mid说明：http://open.weibo.com/wiki/Querymid



    $re = json_decode(file_get_contents("http://api.t.sina.com.cn/queryid.json?mid=xhMRc8nNu&isBase62=1&type=1"));
    $id = $re->id;
    echo $id;
     
    $re = json_decode(file_get_contents("http://api.t.sina.com.cn/querymid.json?id=$id"));
    echo $re->mid;
    exit;