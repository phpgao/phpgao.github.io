---
title: "PHP导出项目的数据字典"
categories: [ "代码人生" ]
tags: [ "PHP","data","dictionary" ]
draft: false
slug: "data_dictionary_exported_by_php"
date: "2014-09-23 03:55:00"
url: "data_dictionary_exported_by_php.html"
---

突然想到这个方法，果然已经有人实现了。

有这个脚本也能看出来，数据表的很多信息都保存在INFORMATION_SCHEMA.TABLES表里，还有数据表注释的重要性！

    <?php
    /**
     * 生成mysql数据字典
     * @author niming<287384795@qq.com>
     */
    
    //配置数据库
    $dbserver   = "localhost";
    $dbusername = "root";
    $dbpassword = "root";
    $database   = 'mydb';
    //其他配置
    $title = '系统数据字典';
    
    $mysql_conn = @mysql_connect("$dbserver", "$dbusername", "$dbpassword") or die("Mysql connect is error.");
    mysql_select_db($database, $mysql_conn);
    mysql_query('SET NAMES utf8', $mysql_conn);
    $table_result = mysql_query('show tables', $mysql_conn);
    //取得所有的表名
    while ($row = mysql_fetch_array($table_result)) {
        $tables[]['TABLE_NAME'] = $row[0];
    }
    
    //循环取得所有表的备注
    foreach ($tables AS $k=>$v) {
        $sql  = 'SELECT * FROM ';
        $sql .= 'INFORMATION_SCHEMA.TABLES ';
        $sql .= 'WHERE ';
        $sql .= "table_name = '{$v['TABLE_NAME']}'  AND table_schema = '{$database}'";
        $table_result = mysql_query($sql, $mysql_conn);
        while ($t = mysql_fetch_array($table_result) ) {
            $tables[$k]['TABLE_COMMENT'] = $t['TABLE_COMMENT'];
        }
    
        $sql  = 'SELECT * FROM ';
        $sql .= 'INFORMATION_SCHEMA.COLUMNS ';
        $sql .= 'WHERE ';
        $sql .= "table_name = '{$v['TABLE_NAME']}' AND table_schema = '{$database}'";
    
        $fields = array();
        $field_result = mysql_query($sql, $mysql_conn);
        while ($t = mysql_fetch_array($field_result) ) {
            $fields[] = $t;
        }
        $tables[$k]['COLUMN'] = $fields;
    }
    mysql_close($mysql_conn);
    
    
    $html = '';
    //循环所有表
    foreach ($tables AS $k=>$v) {
        $html .= '<p><h2>'. $v['TABLE_COMMENT'] . '&nbsp;</h2>';
        $html .= '<table  border="1" cellspacing="0" cellpadding="0" align="center">';
        $html .= '<caption>' . $v['TABLE_NAME'] . '</caption>';
        $html .= '<tbody><tr><th>字段名</th><th>数据类型</th><th>默认值</th>
        <th>允许非空</th>
        <th>自动递增</th><th>备注</th></tr>';
        $html .= '';
    
        foreach ($v['COLUMN'] AS $f) {
            $html .= '<tr><td class="c1">' . $f['COLUMN_NAME'] . '</td>';
            $html .= '<td class="c2">' . $f['COLUMN_TYPE'] . '</td>';
            $html .= '<td class="c3">&nbsp;' . $f['COLUMN_DEFAULT'] . '</td>';
            $html .= '<td class="c4">&nbsp;' . $f['IS_NULLABLE'] . '</td>';
            $html .= '<td class="c5">' . ($f['EXTRA']=='auto_increment'?'是':'&nbsp;') . '</td>';
            $html .= '<td class="c6">&nbsp;' . $f['COLUMN_COMMENT'] . '</td>';
            $html .= '</tr>';
        }
        $html .= '</tbody></table></p>';
    }
    
    
    //输出
    echo '<html>
    <head>
    <title>' . $title . '</title>
    <style>
    body,td,th {font-family:"宋体"; font-size:12px;}
    table{border-collapse:collapse;border:1px solid #CCC;background:#efefef;}
    table caption{text-align:left; background-color:#fff; line-height:2em; font-size:14px; font-weight:bold; }
    table th{text-align:left; font-weight:bold;height:26px; line-height:26px; font-size:12px; border:1px solid #CCC;}
    table td{height:20px; font-size:12px; border:1px solid #CCC;background-color:#fff;}
    .c1{ width: 120px;}
    .c2{ width: 120px;}
    .c3{ width: 70px;}
    .c4{ width: 80px;}
    .c5{ width: 80px;}
    .c6{ width: 270px;}
    </style>
    </head>
    <body>';
    echo '<h1>' . $title . '</h1>';
    echo $html;
    echo '</body></html>';

修改一下用户名密码即可，之后打印成PDF即可分发给团队成员使用了。

转自[http://www.cnblogs.com/zemliu/archive/2012/06/04/2533555.html][1]


  [1]: http://www.cnblogs.com/zemliu/archive/2012/06/04/2533555.html