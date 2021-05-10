---
title: "新浪微博商业API使用参考"
categories: [ "代码人生" ]
tags: [ "api","weibo" ]
draft: false
slug: "sina_weibo_business_api"
date: "2014-05-12 06:07:00"
url: "sina_weibo_business_api.html"
---

工作中需要微博的商业API，记录一下 不了解的TX先去看说说明 http://open.weibo.com/wiki/商业数据API 
> 商业数据API：包括数据分析、舆情监测、获取全量粉丝三大特性，同时还有实时数据推送、历史数据检索、监测粉丝变化趋势、活跃&活动粉丝分析等多项功能，协助和指引企业开展社会化营销，让真实的用户数据变为企业的决策。   
## 申请试用 申请试用的邮箱为

<span id="tipFromAddr_readmail" class="tcolor" style="color: #ff0000;">businessapi@staff.sina.com.cn </span>给这个地址发邮件说明意图即可 这一步挺麻烦的，需要提供很多企业信息，需要注意的是：商业API只提供商业用途，所以如果你的APP的服务对象不是企业，请绕道。 然后填写两张申请单，分别为  订阅服务申请单、REST接口申请单-测试版。 一个是订阅微博，粉丝登上数据用的，另一个是标准API，可以使用搜索等高级接口！
 
## 价格

<table style="height: 185px;" width="574"> 
   <tbody> 
    <tr> 
     <td width="93"> <b>套餐</b> </td> 
     <td width="241"> <b>搜索、订阅返回条数</b> </td> 
     <td width="140"> <b>使用期限</b> </td> 
     <td width="210"> <b>套餐使用费</b> </td> 
     <td width="257"> <b>套餐外使用费</b> </td> 
    </tr> 
    <tr> 
     <td width="93"> <b>A</b> </td> 
     <td width="241"> 200万条 </td> 
     <td width="140"> 1月 </td> 
     <td width="210"> 6,000元 </td> 
     <td width="257"> 3元/千条 </td> 
    </tr> 
    <tr> 
     <td width="93"> <b>B</b> </td> 
     <td width="241"> 2000万条 </td> 
     <td width="140"> 1年 </td> 
     <td width="210"> 60,000元 </td> 
     <td width="257"> 2.5元/千条 </td> 
    </tr> 
    <tr> 
     <td width="93"> <b>C</b> </td> 
     <td width="241"> 1亿条 </td> 
     <td width="140"> 1年 </td> 
     <td width="210"> 250,000元 </td> 
     <td width="257"> 1.6元/千条 </td> 
    </tr> 
    <tr> 
     <td width="93"> <b>D</b> </td> 
     <td width="241"> 5亿条 </td> 
     <td width="140"> 1年 </td> 
     <td width="210"> 800,000元 </td> 
     <td width="257"> 0.6元/千条 </td> 
    </tr> 
   </tbody> 
</table> 

## 确定试用时间 

试用时间一般两周，请充分利用好这宝贵的时间吧！ 

## 测试 

REST接口很简单，和普通接口一样直接调用。就是订阅接口比较麻烦，当你生成订阅列表后，他会把满足你需要的数据放进一个类似队列的东西，然后你需要不断从这个队列中取出你需要的信息，而且这个获取过程需要HTTP长链接。。。。。。说起来很复杂，其实做起来更复杂 :mad: ！ 下面就来讲讲订阅接口，先看看这两个文档： 

*   订阅接口的服务手册  <http://open.weibo.com/wiki/Subscription_guide>
*   订阅管理接口  <http://open.weibo.com/wiki/C/2/subscribe/update_subscribe>

看完这两个接口就基本知道了订阅到底是干啥用的。 

### 订阅开启 

首先，开启了订阅接口服务以后，你会有推送时间，还有推送IP等很多需要设置的东西，当然完成了这些基本设置后，就可以开始配置订阅规则了，订阅规则基于以下两个条件： 

1.  订阅用户
2.  订阅关键字 从subscribe/update_subscribe这个接口的示例来看，订阅有以下特点：

    * 订阅规则即刻生效
    * 规则可以添加，修改
    * 规则可以随时查询
    * 每次修改量很有限（用户：50个；关键词：20个）

### 使用订阅 

添加与删除一样 需要说明的是： 

    $params['subid'] = '***';  //你的订阅ID 新浪提供
    $params['source'] = '***';  //你的APPid
    
    
    session_start();
    
    include_once( 'config.php' );
    include_once( 'saetv2.ex.class.php' );
    
    $c = new SaeTClientV2( WB_AKEY , WB_SKEY , $_SESSION['token']['access_token'] );
    $uid_get = $c->get_uid();
    $uid = $uid_get['uid'];
    
    //获取所有关注，默认一次50个，可以自己写循环
    $friends = $c->friends_by_id($uid);
    $subscribe = '';
    foreach( $friends['users'] as $value )
    {
    	$subscribe .= $value['id'].',';
    }
    $subscribe =  trim($subscribe,',');
    
    //准备数据
    $params = array();
    $params['subid'] = '***';
    $params['source'] = '***';
    $params['add_uids'] = $subscribe;
    //$params['del_uids'] = $subscribe;
    
    $rs = $c->oauth->get('http://c.api.weibo.com/subscribe/update_subscribe.json', $params);
    print_r($rs);
    
    
    if($rs['result']){
    	echo "订阅用户成功";
    }

###  获取订阅信息 

可以访问网址：

[http://open.weibo.com/apps/yourappid/payservice/subscribe][1] 

也可以使用API读取

    <?php
    session_start();
    
    include_once( 'config.php' );
    include_once( 'saetv2.ex.class.php' );
    
    $c = new SaeTClientV2( WB_AKEY , WB_SKEY , $_SESSION['token']['access_token'] );
    $params = array();
    $params['subid'] = '*****';
    $params['source'] = '*****';
    $rs = $c->oauth->get('http://c.api.weibo.com/subscribe/update_subscribe.json', $params);
    print_r($rs);

 [1]: http://open.weibo.com/apps/3280790228/payservice/subscribe