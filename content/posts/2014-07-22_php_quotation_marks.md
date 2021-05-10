---
title: "单引号和双引号的区别及用法"
categories: [ "代码人生" ]
tags: [ "PHP","quote" ]
draft: false
slug: "php_quotation_marks"
date: "2014-07-22 08:07:00"
url: "php_quotation_marks.html"
---

双引号，单引号问题自古以来一直处在混沌状态，不懂的人觉得没啥区别，懂的人貌似也说不出来啥区别，其实区别还是有很多，转给大家看看，自己也留一份参考

转自[http://blog.sina.com.cn/s/blog_706476980100xapp.html][1]


1.单引号和双引号都可以作为字符串的开始符和关闭符，并且只能‍同一种单或者双引号来定义开始和结束；单引号之间的字符都被认为是字符，即使是转义符\和变量符$,例外的是\’标识单引号。
2.双引号之间的字符是需要php解析的，包括$,\，{都保留了php赋予的特殊含义。
3." "双引号里面的字段会经过编译器解释，然后再当作HTML代码输出；而' '单引号里面的不进行解释，直接输出。
例如： 

    $abc='my name is tome';
    echo $abc //结果是:my name is tom
    echo '$abc' //结果是:$abc
    echo "$abc" //结果是:my name is tom

特别在使用MYSQL语句的时候，双引号和单引号的用法让新手不知所措，在这里，举个例子，来进行说明。
假设查询条件中使用的是常量，例如：

    select    *    from    abc_table where user_name='abc';
    
SQL语句可以写成:

    SQLstr = "select    *    from abc_table where user _name= 'abc'" ;

假设查询条件中使用的是变量，例如：

    $user_name = $_REQUEST['user_name']; //字符串变量

或

    $user=array ("name"=> $_REQUEST['user_name‘,"age"=>$_REQUEST['age'];//数组变量

SQL语句就可以写成：

    SQLstr = "select    *    from abc_table where user_name = ' " . $user_name . " ' ";
    SQLstr = "select * from abc_table where user_name = ' " . $user["name"] . " ' ";

对比一下:

    SQLstr="select * from abc_table where user_name = ' abc ' " ;
    SQLstr="select * from abc_table where user_name =' " . $user _name . " ' ";
    SQLstr="select * from abc_table where user_name =' " . $user["name"] . " ' ";

SQLstr可以分解为以下3个部分:

    1："select * from table where user_name = ' " //固定SQL语句 
    2：$user //变量 
    3：" ' "
    1,2,3部分字符串之间用"." 来连接


<!--more-->


---


## 一、引号定义字符串

在PHP中，通常一个字符串被定义在一对引号中，如：
    'I am a string in single quotes'
    "I am a string in double quotes"

PHP语法分析器是用成对的引号来判断一个字符串的。因此，所有字符串必须使用同一种单或者双
引号来定义开始和结束。例如，下面的字串定义是不合法的：

    "I am not a valid string since I have unmatching quote marks'
    'Me neither!"

定义字符串时，只有一种引号被视为定义符，即单引号或双引号。于是，如果一个字符串由双引
号开始，那么只有双引号被分析器解析。这样，你就可以在双引号串中包含任何其他字符，甚至单引
号。下面的引号串都是合法的：

    $s = "I am a 'single quote string' inside a double quote string";
    $s = 'I am a "double quote string" inside a single quote string';

当PHP遇到与串的开头相对应的引号时，便认为已经到了字符串尾部，于是：

    "Why doesn't "this" work?"

实际上被PHP语法分析器分成三个部分:

    "Why doesn't "——包含一个单引号的双引号串
    this——多余的字符，分析器无法处理
    " work?" ——普通字符串

上面的这个例子企图在双引号串中包含双引号，而分析器在遇到第二个双引号时便认为字符串结
束了。要达到包含引号的目的，必须分析器在遇到串内普通引号的时候忽略它的原意，我们在引号的
前面加上一个反斜杠来告诉PHP：这个引号是字符串的一部分，正确的表示方法是这样:

    "Why doesn't \"that\" work?"

在英文字符串中一个常见的问题是撇号'的使用，因为它就是一个单引号，而在英文串中十分常见
（英文所有格）。你必须小心处理这些字符:

    'You\'d better escape your apostrophes'

可以看到反斜杠在字符串中有他的特殊含义，当我们需要在字符串中包含反斜杠本身时，需要在
该符号前面多加一个反斜杠。例如：

    $file = "c:\windows\system.ini";
    echo $file; // 打印结果为： c:windowssystem.ini
    $file = "c:\\windows\\system.ini";
    echo $file; // 打印结果为： c:\windows\system.ini

另一种字符串定义方式，能够消除特殊字符的烦恼，而且便于引用较长的文本。该字符串定义方
法以<<<符号紧跟一个自定义字符串开头，最后一行以该自定义字符串结束，并且必须顶格。

## 二、字串的连接

字串可以使用字串连接符(.)来连接，如：

    $first_name = 'Charlie';
    $last_name = 'Brown';
    $full_name = $first_name . ' ' . $last_name;

常见的用途是建立大块的HTML字串代码，赋值号 (=) 连接符 (.) 可以被简写合并为 (.=) 符
号，如：

    $html = '<table>';
    $html .= '<tr><td>number</td><td>square</td></tr>';
    for ( $i=0 ; $i<10 ; $i++) {
    $square = $i * $i;
    $html .= '<tr><td>' . $i . '</td><td>' . $square . '</td></tr>';
    }
    $html .= '</table>';

## 三、在字串中使用变量

这个功能让你无须使用连接符号来粘和大量的简单字符串。PHP允许我们在双引号串中直接包含字
串变量，我们可以发现下面的两个字串的处理结果是相同的。

    $full_name = $first_name . ' ' . $last_name;
    $full_name = "$first_name $last_name";

单引号串和双引号串在PHP中的处理是不相同的。双引号串中的内容可以被解释而且替换，而单引
号串中的内容总被认为是普通字符。例如：

    $foo = 2;
    echo "foo is $foo"; // 打印结果: foo is 2
    echo 'foo is $foo'; // 打印结果: foo is $foo
    echo "foo is $foo\n"; // 打印结果: foo is 2 (同时换行)
    echo 'foo is $foo\n'; // 打印结果: foo is $foo\n

正如你所看到的，在单引号串中甚至反斜杠也失去了他的扩展含义（除了插入反斜杠\\和插入单
引号\'）。所以，当你想在字串中进行变量代换和包含\n（换行符）等转义序列时，你应该使用双引
号。单引号串可以用在其他任何地方，脚本中使用单引号串处理速度会更快些，因为PHP语法分析器对
单引号串的处理方式比较单纯，而双引号的处理由于串内部也需要解析，因此更复杂些，所以处理速
度略慢。
在字符串中引用复杂的变量组合时，可能会产生一些问题，下面的代码会正常工作：

    echo "value = $foo";
    echo "value = $a[$i]";

而下面的代码却不能得到我们希望的结果:

    echo "value = $a[$i][$j]"; //我们希望打印二维数组$a的某个元素。

为避免这些字串使用中的潜在问题，我们通常把复杂的变量从字串中分离开来，就像这样：

    echo 'value = ' . $a[$i][$j];

还有一种办法是将复杂变量用花括号括起来，语法分析器就能正确辨认了:

	echo "value = {$a[$i][$j]}" //打印二维数组$a的某个元素

这样，又出现新问题了。当我们想在字串中引用花括号字符本身时，就要记得使用转义符了：

    $var = 3;
    echo "value = {$var}"; // 打印结果 "value = 3"
    echo "value = \{$var}"; // 打印结果 "value = {3}"

## 四、斜杠和SQL语句

生成HTML代码或SQL查询语句是编写PHP程序时经常遇到而且是件有趣的事情。为什么这么说呢，
因为这涉及到生成另外一种类型的代码，你必须仔细地考虑和遵循这种代码所要求的编写语法和规
则。
我们来看这样一个例子，假如你想查询数据库中名字是“O'Keefe”的用户，通常SQL语句的形式
是这样的：

    select * from users where last_name = 'O\'Keefe'

请注意SQL语句这个英文所有格（撇号）需使用反斜杠转义。PHP专门提供了一些函数来处理这样
的情况，函数AddSlashes($str)的用途就是自动在字串中对引号字符插入反斜杠转义符：

    $last_name = "O'Keefe";
    $sql = "select * from users where last_name = '" . addslashes($last_name) . "'";

在这个例子中，你还要在last_name字串外面括上单引号（SQL语法要求），由于这里使用的是双
引号串，所以对这对单引号就无须使用转义了。下面的这个语句是使用单引号串的等价形式：

    $sql = 'select * from users where last_name = \'' . addslashes($last_name) . '\'';

任何时候你要在数据库中写入字串，你都必须确保里面的引号正确使用了转义符号，这是很多PHP
初学者常犯的错误。

## 五、双引号和HTML
与SQL语句不同，在标准HTML语言中双引号常被用来表示字串（现在很多浏览器具备较强的容错功
能，允许在HTML中用单引号甚至不用引号表示字符串），例如：

    $html = '<a href="'.$url.'">'.$link.'</a>';
    $html = "<a href=\"$url\">$link</a>";

HTML语言不支持反斜杠转义，这一点在我们使用表单的hidden inputs来传输数据的时候就会有所
体会了。设置hidden inputs的值的最好办法，是使用htmlspecialchars()函数来编码。下面的语句可
以正常传输一个可能包含双引号的数据：

<input type=hidden name=var value="<?php echo htmlspecialchars($var) ?>">

经常有人问我，听说在PHP中处理字符串用单引号会快，那么如果存在变量替换的时候，是使用单引号连接快呢，还是双引号快呢？ 简单的回答，显然是苍白无力的。 今天我们来做个实验，看看到底单引号和双引号有什么区别，谁快，谁慢。
测试代码如下:

    <?php
    $single_quotes = 'This is a String';
    $double_quotes = "This is a String";
    echo $single_quotes;
    echo $double_quotes;
    $var = 'String';
    $single_quotes_var = 'This is a '.$var;
    $double_quotes_var = "This is a $var";
    echo $single_quotes_var;
    echo $double_quotes_var;
    $var = 'This';
    $single_quotes_var_pre = $var . ' is a String';
    $double_quotes_var_pre = "$var is a String";
    echo $single_quotes_var_pre;
    echo $double_quotes_var_pre;
    ?>

接下来，让我们以前文章提到过的Opcodes生成器来看看，最终我们的这段代码是怎么被执行的：

    Branch analysis from position: 0
    Return found
    filename: /home/xinchen/string.php
    function name: (null)
    number of ops: 24
    compiled vars: !0 = $single_quotes, !1 = $double_quotes, !2 = $var, !3 = $single_quotes_var, !4 = $double_quotes_var, !5 = $single_quotes_var_pre, !6 = $double_quotes_var_pre
    line # op fetch ext return operands
    -------------------------------------------------------------------------------
       2 0 ASSIGN !0, 'This+is+a+String'
       3 1 ASSIGN !1, 'This+is+a+String'
       4 2 ECHO !0
       5 3 ECHO !1
       7 4 ASSIGN !2, 'String'
       8 5 CONCAT ~3 'This+is+a+', !2
       6 ASSIGN !3, ~3
       9 7 INIT_STRING ~5
       8 ADD_STRING ~5 ~5, 'This+is+a+'
       9 ADD_VAR ~5 ~5, !2
       10 ASSIGN !4, ~5
    11 11 ECHO !3
    12 12 ECHO !4
    14 13 ASSIGN !2, 'This'
    16 14 CONCAT ~8 !2, '+is+a+String'
       15 ASSIGN !5, ~8
    17 16 INIT_STRING ~10
       17 ADD_VAR ~10 ~10, !2
       18 ADD_STRING ~10 ~10, '+is+a+String'
       19 ASSIGN !6, ~10
    19 20 ECHO !5
    20 21 ECHO !6
    22 22 RETURN 1
       23* ZEND_HANDLE_EXCEPTION
   
注意第0到第3条op line, 可以看出在没有使用变量替换的情况下，双引号的和单引号所产生的Opcodes是一样的。
再来看：第4到第12条， 可以发现，在使用变量替换的情况下，使用双引号和单引号所生成的Opcodes是不一样的，我们来分析双引号情况下的Opcodes：

	7 INIT_STRING 初始化一个字符串变量，存放在~5临时变量中。
	8 ADD_STRING 将第一部分字符串写入。
	9 ADD_VAR 将变量替换的字符串写入。

第16-28行同理。

从这里我们可以发现，在使用双引号的情况下和使用单引号的情况下，同样的逻辑，所经历的执行确实不一样的(因为,Opcodes对于PHP来说就是最终的执行代码)。单单从生成的Opcods数量来说，就已经足以证明确实是使用单引号会快了。
至于编译阶段，双引号和单引号的区别也是很大的， 我就举个数字来说明： 在scanning阶段， 对于双引号的词法规则有14条，而对于单引号，仅仅只有6条。

呵呵，是不是经过这样的分析，你会更清楚的了解到以后该怎么使用单引号和双引号了呢？
顺便说一句，对于不需要变量替换的纯字符串，大家都知道，因为在C/C++中，双引号才表示字符串，所以这种情况下，还是使用双引号的好。

另外对于W3C标准来说， HTML中的属性值应该是使用双引号来包含的 ，所以不要习惯了单引号，到处滥用哦
以上是单引号与双引号的区别，摘记下来，便于随时翻看。

  [1]: http://blog.sina.com.cn/s/blog_706476980100xapp.html