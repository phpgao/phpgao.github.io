---
title: "php-bbcode过滤"
categories: [ "代码人生" ]
tags: [  ]
draft: false
slug: "bbcode-filter"
date: "2014-07-16 06:42:00"
url: "bbcode-filter.html"
---

[BBCODE][1]，即BB代码，属于轻量级标记语言（Lightweight Markup Language）的一种，如字面上所显示的，它主要是使用在BBS、论坛、Blog等网络应用上。BBCode最初由Ultimate Bulletin Board讨论区系统发展出来，因此常见UBB代码的称呼。BBCode的语法通常为[标记]这种形式，即语法左右用两个中括号包围，以作为与正常文字间的区别。系统解译时遇上中括号便知道该处是BBcode，会在解译结果输出到客户端时转换成最为通用的HTML语法。

要过滤他有两个思路，一个是先转化为html，再用strip_tags即可，下面是具体函数


    <?php
    function bb_parse($string)
    {
    	$tags = 'b|i|size|color|center|quote|url|img';
    	while (preg_match_all('`\[(' . $tags . ')=?(.*?)\](.+?)\[/\1\]`', $string, $matches))
    	{
    		foreach ($matches[0] as $key => $match) {
    			list($tag, $param, $innertext) = array(
    				$matches[1][$key],
    				$matches[2][$key],
    				$matches[3][$key]
    			);
    			switch ($tag) {
    				case 'b':
    					$replacement = "<strong>$innertext</strong>";
    					break;
    				case 'i':
    					$replacement = "<em>$innertext</em>";
    					break;
    				case 'size':
    					$replacement = "<span style=\"font-size: $param;\">$innertext</span>";
    					break;
    				case 'color':
    					$replacement = "<span style=\"color: $param;\">$innertext</span>";
    					break;
    				case 'center':
    					$replacement = "<div class=\"centered\">$innertext</div>";
    					break;
    				case 'quote':
    					$replacement = "<blockquote>$innertext</blockquote>" . $param ? "<cite>$param</cite>" : '';
    					break;
    				case 'url':
    					$replacement = '<a href="' . ($param ? $param : $innertext) . "\">$innertext</a>";
    					break;
    				case 'img':
    					@list($width, $height) = preg_split('`[Xx]`', $param);
    					$replacement = "<img src=\"$innertext\" " . (is_numeric($width) ? "width=\"$width\" " : '') . (is_numeric($height) ? "height=\"$height\" " : '') . '/>';
    					break;
    			}
    			$string = str_replace($match, $replacement, $string);
    		}
    
    	}
    	return $string;
    }
    
    
    $text = <<<EOF
    [b]Bold Text[/b]
    [i]Italic Text[/i]
    [url]http://www.php.net/[/url]
    [url=http://pecl.php.net/][b]Content Text[/b][/url]
    [img]http://static.php.net/www.php.net/images/php.gif[/img]
    [url=http://www.php.net/][img]http://static.php.net/www.php.net/images/php.gif[/img][/url]
    EOF;
    
    echo bb_parse($text);
    
    echo strip_tags( bb_parse($text) );
    ?> 

第二种办法更简单，直接用正则把[]内的内容删除即可

    $cont_str = trim( preg_replace("/\[.*\]/", '', $cont_str) );

推荐第二种方法

  [1]: http://zh.wikipedia.org/wiki/BBCode