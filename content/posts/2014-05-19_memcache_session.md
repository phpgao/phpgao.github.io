---
title: "PHP之负载均衡下的session共用(Memcache实现)"
categories: [ "性能优化" ]
tags: [ "PHP","memcache","session" ]
draft: false
slug: "memcache_session"
date: "2014-05-19 08:08:00"
url: "memcache_session.html"
---

转自http://www.cnblogs.com/see7di/p/3560329.html

此代码片段的功能是在做均衡负载的时候将session的维护工作交给Memcached，这样不仅性能上来了，session不同步的问题也解决了！

但是要求做Memcache的计算机内存要足够大！
    
    $_ENV=array(
        'SYS'=>array(
            'Memip'=>'127.0.0.1',            //MEMCACHE的ip
            'Mempt'=>11211,              //MEMCACHE的port
            'Memtim'=>10,                    //MEMCACHE的超时时间
        )
    );
     
    //托管SESSION到MEMCACHE,如果开启失败,则说明MEMCACHE没有配置正确
    final class S{
        public static function open(){
        //禁止session自动开启
            session_write_close();
        ini_set('session.auto_start',0);
     
        //使用五十分之一的概率启动GC清理会话
        ini_set('session.gc_probability',1);
        ini_set('session.gc_divisor',50);
        ini_set('session.use_cookies',1);
     
        //session的寿命
        //ini_set('session.gc_maxlifetime',$_ENV['SYS']['Memtim']);
        //ini_set('session.cookie_lifetime',$_ENV['SYS']['Memtim']);
        //ini_set('session.save_handler','files');
        //ini_set('session.save_path',Run.'_tmp');
        //ini_set('session.save_handler','memcache');
        //ini_set('session.save_path','tcp://127.0.0.1:11211');
            //$_ENV['S_tim'] = ini_get('session.gc_maxlifetime');
     
        //建立memcache对象
        $_ENV['S_mem']=new Memcache;
        $_ENV['S_mem']->connect($_ENV['SYS']['Memip'],$_ENV['SYS']['Mempt']) or die('Memcache连接失败!');
            return TRUE;
        }
     
        /**
            读取
            返回:读到的内容
        /**/
        public static function read($id){
            return $_ENV['S_mem']->get('s_'.$id);
        }
     
        /**
            写入
            返回:bool
        /**/
        public static function write($id,$data){
            return $_ENV['S_mem']->set('s_'.$id,$data,MEMCACHE_COMPRESSED,$_ENV['SYS']['Memtim']);
        }
     
        /**
            关闭
            返回:bool
        /**/
        public static function close(){
            $_ENV['S_mem']->close();
            unset($_ENV['S_mem'],$_ENV['SYS']['Memtim']);
            return TRUE;
        }
     
        /**
            删除
            返回:bool
        /**/
        public static function destroy($id){
            return $_ENV['S_mem']->delete('s_'.$id);
        }
     
        /**
            清理
            返回:bool
        /**/
        public static function gc(){
            return TRUE;
        }
    }
    session_set_save_handler('S::open','S::close','S::read','S::write','S::destroy','S::gc');
    $_ENV['sessionid']=(isset($_REQUEST['sessionid'])) ? trim($_REQUEST["sessionid"]) : session_id();
    if($_ENV['sessionid']!=''){session_id($_ENV['sessionid']);}
    session_start();