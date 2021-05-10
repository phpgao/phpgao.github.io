---
title: "dede栏目路径自动生成"
categories: [ "性能优化" ]
tags: [ "dede","path" ]
draft: false
slug: "dede_catalog"
date: "2014-03-29 15:41:00"
url: "dede_catalog.html"
---

抽空写了个这。。。虽然很头大，但是还是写完了。 下面由我来翻译下面这一大段话，首先，函数使用了PINYIN函数，将栏目名称转化成英文缩写，剩下的工作就是拼接和查询了，其实原理很简单。 


<!--more-->


    #一级栏目
    UPDATE dede_arctype AS a SET a.typedir = CONCAT( '{cmspath}/', PINYIN (a.typename));
     
    SELECT a.id, a.reid, a.topid, a.typename, a.typedir FROM dede_arctype AS a WHERE a.reid = 0;
     
    #二级栏目
    UPDATE dede_arctype AS a SET a.typedir = CONCAT(( SELECT temp.typedir FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.id = a.reid ), '/', RIGHT (PINYIN(a.typename), 2)) WHERE a.reid IN ( SELECT temp.id FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.reid = 0 );
     
    SELECT a.id, a.reid, a.topid, a.typename, a.typedir FROM dede_arctype AS a WHERE a.reid IN ( SELECT b.id FROM dede_arctype AS b WHERE b.reid = 0 );
     
    #三级栏目
    UPDATE dede_arctype AS a SET a.typedir = CONCAT(( SELECT temp.typedir FROM ( SELECT f.id, f.typedir FROM dede_arctype AS f WHERE f.reid IN ( SELECT b.id FROM dede_arctype AS b WHERE b.reid = 0 )) AS temp WHERE temp.id = a.reid ), '/', RIGHT (PINYIN(a.typename), 2)) WHERE a.reid IN ( SELECT temp.id FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.reid IN ( SELECT temp.id FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.reid = 0 ));
     
    SELECT a.id, a.reid, a.topid, a.typename, a.typedir FROM dede_arctype AS a WHERE a.reid IN ( SELECT c.id FROM dede_arctype AS c WHERE c.reid IN ( SELECT b.id FROM dede_arctype AS b WHERE b.reid = 0 ));

使用方法：
首先请为您的dede数据库加入PINYIN函数，该函数引自`http://www.javaqa.net/2012/02/mysql-hqhzpyszm/`
我在其中有些更改，其中第三步最重要，编码问题直接影响到pinyin函数的运行

第一步：

    DROP TABLE IF EXISTS `pinyin`;
    CREATE TABLE `pinyin` (
      `letter` char(1) NOT NULL,
      `chinese` char(1) NOT NULL,
      PRIMARY KEY  (`letter`)
    ) ENGINE=MyISAM DEFAULT CHARSET=gbk;

第二步： 

    INSERT INTO `pinyin` VALUES
    ('A','驁'),
    ('B','簿'),
    ('C','錯'),
    ('D','鵽'),
    ('E','樲'),
    ('F','鰒'),
    ('G','腂'),
    ('H','夻'),
    ('J','攈'),
    ('K','穒'),
    ('L','鱳'),
    ('M','旀'),
    ('N','桛'),
    ('O','漚'),
    ('P','曝'),
    ('Q','囕'),
    ('R','鶸'),
    ('S','蜶'),
    ('T','籜'),
    ('W','鶩'),
    ('X','鑂'),
    ('Y','韻'),
    ('Z','咗');


第三步：注意输入和输入的字符集必须使用GBK

    -- Function structure for `PINYIN`
    -- ----------------------------
    DROP FUNCTION IF EXISTS `PINYIN`;
    DELIMITER ;;
    CREATE DEFINER=`root`@`localhost` FUNCTION `PINYIN`(str CHAR(255) CHARSET gbk) RETURNS char(255) CHARSET gbk
    BEGIN
    DECLARE hexCode char(4);
    DECLARE pinyin varchar(255);
    DECLARE firstChar char(1);
    DECLARE aChar char(1);
    DECLARE pos int;
    DECLARE strLength int;
    
    SET pinyin    = '';
    SET strLength = CHAR_LENGTH(LTRIM(RTRIM(str)));
    SET pos       = 1;
    SET @str      = (CONVERT(str USING gbk));
    WHILE pos <= strLength DO
    	SET @aChar = SUBSTRING(@str,pos,1);
    	SET hexCode = HEX(@aChar); 
    
    	IF hexCode >= "8140" AND hexCode <= "FEA0" THEN
    		SELECT letter into firstChar
    		FROM   pinyin
    		WHERE  chinese >= @aChar
    		LIMIT  1;
    	ELSE 
    	  SET firstChar = @aChar;
    	END IF;
    
    	SET pinyin = CONCAT(pinyin,firstChar);
    	SET pos = pos + 1;
    END WHILE;  
    
    RETURN UPPER(pinyin);
    END
    ;;
    DELIMITER ; 

添加后可以使用如以下： 

    SELECT PINYIN('老高@PHPer'); 

来得到拼音缩写'LG@PHPER' 如果不喜欢大写，可以修改UPPER为LOWER函数使之返回小写字母。

2013年9月12日补充： 如果出现如下错误 

> ERROR 1418 (HY000): This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled (you \*might\* want to use the less safe log\_bin\_trust\_function\_creators variable) 

请在使用前运行如下SQL： 

    #原因：
    #这是我们开启了bin-log, 我们就必须指定我们的函数是否是
    #1 DETERMINISTIC 不确定的
    #2 NO SQL 没有SQl语句，当然也不会修改数据
    #3 READS SQL DATA 只是读取数据，当然也不会修改数据
    #4 MODIFIES SQL DATA 要修改数据
    #5 CONTAINS SQL 包含了SQL语句
    set global log_bin_trust_function_creators=1;


将以上所有SQL合并，可以一次搞定！ 

    DROP TABLE IF EXISTS `pinyin`;
    CREATE TABLE `pinyin` (
      `letter` char(1) NOT NULL,
      `chinese` char(1) NOT NULL,
      PRIMARY KEY  (`letter`)
    ) ENGINE=MyISAM DEFAULT CHARSET=gbk;
    
    INSERT INTO `pinyin` VALUES
    ('A','驁'),
    ('B','簿'),
    ('C','錯'),
    ('D','鵽'),
    ('E','樲'),
    ('F','鰒'),
    ('G','腂'),
    ('H','夻'),
    ('J','攈'),
    ('K','穒'),
    ('L','鱳'),
    ('M','旀'),
    ('N','桛'),
    ('O','漚'),
    ('P','曝'),
    ('Q','囕'),
    ('R','鶸'),
    ('S','蜶'),
    ('T','籜'),
    ('W','鶩'),
    ('X','鑂'),
    ('Y','韻'),
    ('Z','咗');
    
    -- ----------------------------
    -- Function structure for `PINYIN`
    -- ----------------------------
    DROP FUNCTION IF EXISTS `PINYIN`;
    DELIMITER ;;
    CREATE DEFINER=`root`@`localhost` FUNCTION `PINYIN`(str CHAR(255) CHARSET gbk) RETURNS char(255) CHARSET gbk
    BEGIN
    DECLARE hexCode char(4);
    DECLARE pinyin varchar(255);
    DECLARE firstChar char(1);
    DECLARE aChar char(1);
    DECLARE pos int;
    DECLARE strLength int;
    
    SET pinyin    = '';
    SET strLength = CHAR_LENGTH(LTRIM(RTRIM(str)));
    SET pos       = 1;
    SET @str      = (CONVERT(str USING gbk));
    WHILE pos <= strLength DO
    	SET @aChar = SUBSTRING(@str,pos,1);
    	SET hexCode = HEX(@aChar); 
    
    	IF hexCode >= "8140" AND hexCode <= "FEA0" THEN
    		SELECT letter into firstChar
    		FROM   pinyin
    		WHERE  chinese >= @aChar
    		LIMIT  1;
    	ELSE 
    	  SET firstChar = @aChar;
    	END IF;
    
    	SET pinyin = CONCAT(pinyin,firstChar);
    	SET pos = pos + 1;
    END WHILE;  
    
    RETURN LOWER(pinyin);
    END
    ;;
    DELIMITER ;
    
    UPDATE dede_arctype set temparticle='{style}/article.htm';
    UPDATE dede_arctype set templist='{style}/list.htm';
    UPDATE dede_arctype set tempindex='{style}/lindex.htm';
    
    UPDATE dede_arctype AS a SET a.typedir = CONCAT( '{cmspath}/a/', PINYIN (a.typename));
    
    UPDATE dede_arctype AS a SET a.typedir = CONCAT(( SELECT temp.typedir FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.id = a.reid ), '/', RIGHT (PINYIN(a.typename), 2)) WHERE a.reid IN ( SELECT temp.id FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.reid = 0 );
    
    UPDATE dede_arctype AS a SET a.typedir = CONCAT(( SELECT temp.typedir FROM ( SELECT f.id, f.typedir FROM dede_arctype AS f WHERE f.reid IN ( SELECT b.id FROM dede_arctype AS b WHERE b.reid = 0 )) AS temp WHERE temp.id = a.reid ), '/', RIGHT (PINYIN(a.typename), 2)) WHERE a.reid IN ( SELECT temp.id FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.reid IN ( SELECT temp.id FROM (SELECT * FROM dede_arctype) AS temp WHERE temp.reid = 0 )); 