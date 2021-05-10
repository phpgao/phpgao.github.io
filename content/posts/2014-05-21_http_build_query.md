---
title: "http_build_query妙用"
categories: [ "代码人生" ]
tags: [ "post","curl","http_build_query","get" ]
draft: false
slug: "http_build_query"
date: "2014-05-21 09:30:00"
url: "http_build_query.html"
---

http_build_query这个函数可以很方便的构造一个请求所需要的参数。(不分GET,POST)

这个函数可以把一个数组转化为一串字符

栗子1：
    
    <?php
     
    $data = array('foo'=>'bar',
                  'baz'=>'boom',
                  'cow'=>'milk',
                  'php'=>'hypertext processor');
     
    //第二个参数是分割使用的符号
    echo http_build_query($data) . "\n";
    echo http_build_query($data, '', '&amp;');
    # foo=bar&baz=boom&cow=milk&php=hypertext+processor
    # foo=bar&amp;baz=boom&amp;cow=milk&amp;php=hypertext+processor
    ?>
栗子二：

    <?php
    $data = array('user'=>array('name'=>'Bob Smith',
                                'age'=>47,
                                'sex'=>'M',
                                'dob'=>'5/12/1956'),
                  'pastimes'=>array('golf', 'opera', 'poker', 'rap'),
                  'children'=>array('bobby'=>array('age'=>12,
                                                   'sex'=>'M'),
                                    'sally'=>array('age'=>8,
                                                   'sex'=>'F')),
                  'CEO');
    //第二个参数，如果数组没有键值，则自动添加键值
    echo http_build_query($data, 'flags_');
    ?>
输出：

> user%5Bname%5D=Bob+Smith&user%5Bage%5D=47&user%5Bsex%5D=M&user%5Bdob%5D=5%2F12%2F1956&pastimes%5B0%5D=golf&pastimes%5B1%5D=opera&pastimes%5B2%5D=poker&pastimes%5B3%5D=rap&children%5Bbobby%5D%5Bage%5D=12&children%5Bbobby%5D%5Bsex%5D=M&children%5Bsally%5D%5Bage%5D=8&children%5Bsally%5D%5Bsex%5D=F&flags_0=CEO

把上面的请求传给一个PHP文件处理，打印传递来的参数，会得到以下结果：
    
    Array
    (
        [_GET] => Array
            (
                [user] => Array
                    (
                        [name] => Bob Smith
                        [age] => 47
                        [sex] => M
                        [dob] => 5/12/1956
                    )
     
                [pastimes] => Array
                    (
                        [0] => golf
                        [1] => opera
                        [2] => poker
                        [3] => rap
                    )
     
                [children] => Array
                    (
                        [bobby] => Array
                            (
                                [age] => 12
                                [sex] => M
                            )
     
                        [sally] => Array
                            (
                                [age] => 8
                                [sex] => F
                            )
     
                    )
     
                [flags_0] => CEO
            )
     
        [_POST] => Array
            (
            )
     
        [_COOKIE] => Array
            (
                [PHPSESSID] => 5u7l53inhrl5j1ojmn65ok4k44
            )
     
        [_FILES] => Array
            (
            )
     
        [GLOBALS] => Array
     *RECURSION*
    )
很神奇吧！

栗子三：(构造HTTP请求)

    
    <?php
     
    $postdata = http_build_query(
        array(
            'var1' => 'some content',
            'var2' => 'doh'
        )
    );
     
    $opts = array('http' =>
        array(
            'method'  => 'POST',
            'header'  => 'Content-type: application/x-www-form-urlencoded',
            'content' => $postdata
        )
    );
     
    $context = stream_context_create($opts);
     
    $result = file_get_contents('http://example.com/submit.php', false, $context);
     
    ?>

这些函数配合起来就可以用file_get_contents构造出一个POST或GET请求了，比CURL方便很多！