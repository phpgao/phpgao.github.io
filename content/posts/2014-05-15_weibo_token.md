---
title: "PHP获取新浪微博token"
categories: [ "代码人生" ]
tags: [ "weibo","token" ]
draft: false
slug: "weibo_token"
date: "2014-05-15 03:04:00"
url: "weibo_token.html"
---

最近在做采集微博的功能，由于要自动采集，所以必须获得最终的用户的token，获取了这个token以后就可以为所欲为啦！ 

贴代码 

    $s = new SaeTOAuthV2 ( WB_AKEY, WB_SKEY);
    //准备post数据
    $post ['action'] = 'login';
    //$post ['display'] = 'default';
    //$post ['withOfficalFlag'] = 0;
    //$post ['quick_auth'] = null;
    //$post ['withOfficalAccount'] = '';
    //$post ['scope'] = '';
    //$post ['ticket'] = '';
    //$post ['isLoginSina'] = '';
    //$post ['isLoginSina'] = 'code';
    //regCallback|appkey62需要预先请求一次拿到
    //$post ['regCallback'] = $match_regCallback[1];
    //这里不用urlencode,http_build_query会自动处理
    $post ['redirect_uri'] = WB_CALLBACK_URL;
    $post ['client_id'] = WB_AKEY;
    //$post ['appkey62'] = $match_appkey[1];
    //$post ['state'] = '';
    //$post ['verifyToken'] = null;
    //$post ['from'] = '';
    $post ['userId'] = '*******';//测试用户名
    $post ['passwd'] = '*******';//测试密码,不要有！@￥%……&*（）这些符号，最好全是字符或数字，否则验证会失败
    $post = http_build_query($post);
    //UA貌似没有也行，保险起见还是加上吧
    $UA = 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.101 Safari/537.36';
    //获取认证URL作为refer
    $url = $s->getAuthorizeURL (WB_CALLBACK_URL);
    //echo $url."\n";
    $ch = curl_init();
    curl_setopt($ch, CURLOPT_URL, 'https://api.weibo.com/oauth2/authorize');
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, 0);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    //自动跳转
    curl_setopt($ch, CURLOPT_FOLLOWLOCATION, 1);
    curl_setopt($ch, CURLOPT_REFERER, $url);
    curl_setopt($ch, CURLOPT_USERAGENT, $UA);
    curl_setopt($ch, CURLOPT_POST, true);
    curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
    $response = curl_exec($ch);
    //获取最后一个有效的url地址
    $token_url = curl_getinfo($ch, CURLINFO_EFFECTIVE_URL);
    echo $token_url."\n";
    curl_close($ch);
    $code = preg_match('#code\=(.*)#',$token_url,$match_url);
    //找到code
    if($code>=1)
    {
    	$code = $match_url[1];
    	$keys = array();
    	$keys['code'] = trim($code);
    	$keys['redirect_uri'] = WB_CALLBACK_URL;
    	try 
    	{
    		$token = $s->getAccessToken( 'code', $keys );
    	} catch (OAuthException $e) 
    	{
    		die($e->getMessage());
    	}
    	return $token;
    }else
    {
    	die("code获取失败");
    }



思路很简单，直接给 https://api.weibo.com/oauth2/authorize 发送登录信息，通过认证后，他会返回给你一个跳转URL，并带有一个code变量，这个code还是与开发者绑定的。

然后使用这个code，与服务器的  http://open.weibo.com/wiki/Oauth2/authorize 接口通信，返回的才是最终你需要的token，这个token同时与用户绑定，将他保存在session里随时调用！

 

思路参考：

http://hi.baidu.com/bing008ok/item/0a2bce068d1f8e0aeafe385d