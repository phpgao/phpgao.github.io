---
title: "Laravel的容器的工作原理"
categories: [ "代码人生" ]
tags: [ "laravel","container" ]
draft: false
slug: "laravel_container"
date: "2017-03-14 16:08:00"
url: "laravel_container.html"
---

使用Laravel有一段时间了，终于抽点了时间看了一下其laravel实现的IOC控制反转的概念，确实很有意思。

Laravel的容器虽然和docker的容器概念不同，但是都一样承载了十分重要的信息！docker的容器为程序提供了可读写的环境，Lravel的容器为类的初始化提供了便利，解决了以依赖注入和资源获取的问题。


<!--more-->


以下代码来自[《Laravel框架关键技术解析》][1]一书，本人有改动，用来说明laravel容器的工作原理。

```php
<?php

//容器类装实例或提供实例的回调函数
class Container {

    //用于装提供实例的回调函数，真正的容器还会装实例等其他内容
    //从而实现单例等高级功能
    public $bindings = [];

    //绑定接口和生成相应实例的回调函数
    public function bind($abstract, $concrete=null, $shared=false) {
        echo "binding {$abstract}\n";
        //如果提供的参数不是回调函数，则产生默认的回调函数
        if(!$concrete instanceof Closure) {
            echo "not closure\n";
            $concrete = $this->getClosure($abstract, $concrete);
        }else{
            echo "Closure\n";
        }
        //第一个参数$abstrac作为key
        $this->bindings[$abstract] = compact('concrete', 'shared');
    }

    //默认生成实例的回调函数
    protected function getClosure($abstract, $concrete) {
        //返回的闭包在真正使用时才会被调用
        echo "making closure\n";
        return function($c) use ($abstract, $concrete) {
            $method = ($abstract == $concrete) ? 'build' : 'make';
            // $this->make/build('class')
            return $c->$method($concrete);
        };

    }
    //生成实例对象，首先解决接口和要实例化类之间的依赖关系
    public function make($abstract) {
        echo "making {$abstract}\n";
        //获取bind后的key对应的构造函数
        $concrete = $this->getConcrete($abstract);
        //是否能够build

        if($this->isBuildable($concrete, $abstract)) {
            echo "{$abstract} is buildable!\n";
            $object = $this->build($concrete);
        } else {
            echo "{$abstract} is not buildable!\n";
            $object = $this->make($concrete);
        }

        return $object;
    }
    //能够build的条件是$concrete 全等 $abstract 或 $concret为闭包
    protected function isBuildable($concrete, $abstract) {
        if ($concrete === $abstract) echo "eqs\n";
        if ($concrete instanceof Closure) echo "Buildable Closure\n";
        return $concrete === $abstract || $concrete instanceof Closure;
    }

    //获取绑定的回调函数
    protected function getConcrete($abstract) {
        // 这个条件使得 $app->make("A") == new A();
        if(!isset($this->bindings[$abstract])) {
            return $abstract;
        }
        // 返回对应的 concrete，也就是实体类
        return $this->bindings[$abstract]['concrete'];
    }

    //实例化对象
    public function build($concrete) {
        echo "Building \n";
        // 如果是闭包
        if($concrete instanceof Closure) {
            echo "return closeure\n";
            return $concrete($this);
        }
        echo "need reflection!\n";
        //使用反射
        $reflector = new ReflectionClass($concrete);
        if(!$reflector->isInstantiable()) {
            echo $message = "Target [$concrete] is not instantiable";
        }

        $constructor = $reflector->getConstructor();
        //该类如果没有构造函数，直接初始化
        if(is_null($constructor)) {
            return new $concrete;
        }
        //获取初始化参数
        $dependencies = $constructor->getParameters();
        //解析参数对应的类名
        $instances = $this->getDependencies($dependencies);
        //使用解析后的类初始化目标类
        return $reflector->newInstanceArgs($instances);
    }

    //解决通过反射机制实例化对象时的依赖
    protected function getDependencies($parameters) {
        $dependencies = [];
        //按顺序解析参数，并存入dependencies，用于初始化目标类
        foreach($parameters as $parameter) {
            $dependency = $parameter->getClass();
            if(is_null($dependency)) {
                $dependencies[] = NULL;
            } else {
                $dependencies[] = $this->resolveClass($parameter);
            }
        }

        return (array)$dependencies;
    }
    //根据参数解析并返回实例化的类
    protected function resolveClass(ReflectionParameter $parameter) {
        //此处获取真正的类名
        echo "want {$parameter->getClass()->name}\n";
        return $this->make($parameter->getClass()->name);
    }

}
```
考虑下面代码的运行结果！

```php
//数据库接口
interface Sql
{
    public function query();
}

class Mysql implements Sql {

      public function __construct(){}

      public function query()
      {
          echo "Mysql is working!\n";
      }
}

class Postgresql  implements Sql  {

      public function __construct(){}

      public function query()
      {
          echo "Postgresql is working!\n";
      }
}

class MSsql{

    public function query()
      {
          echo "MSsql is working!\n";
      }
}

class doQuery{
    protected $dosql;

    public function __construct(Sql $sql, A $a)
    {
        $this->dosql = $sql;
        $this->a = $a;
    }

    public function query()
    {

        $this->a->do();
        $this->dosql->query();
    }
}

class A{
    public function do()
    {
        echo "A works!\n";
    }
}

$app = new Container();
// Sql的实现，也就是concrete为Postgresql
$app->bind("Sql", "Postgresql");
// myQuery是abstract，可以当作别名，而doQuery是其实现
$app->bind("myQuery", "doQuery");
$app->bind("closure", function($c){
    echo "closure works!\n";
});
echo "\n\n";
$app->make("closure");
echo "\n\n";
$app->make("A")->do();
echo "\n\n";
// make的过程：
// 1. 由于创建的是myQuery，所以找到doQuery
// 2. 容器在初始化doQuery时发现他是闭包，于是执行$app->make("doQuery")
// 3. 回到make的getConcrete()，发现返回doQuery，因为没有doQuery对应的bind
// 4. isBuildable()发现$concrete === $abstract，于是可build
// 5. 进入build流程，跳过闭包检测，开始执行反射
// 6. 如果没有构造函数，直接实例化，如果有，解析出依赖
// 7. 拿出一个依赖，如果不为空，进入依赖解析环节，此时发现doQuery依赖$db
// 8. $db实现了Sql的接口，于是调用$app->make(Sql)，而SQL在初始化的时候被bind到了Postgresql上
// 9. 回到3
$myQuery = $app->make("myQuery");
echo "\n\n";
$myQuery->query();
echo "\n\n";
$app->bind("Sql", "Mysql");
$myQuery = $app->make("myQuery"); 
$myQuery->query();
echo "\n\n";
// MSsql没有依赖，getConcrete返回MSsql，然后实例化
$myQuery = $app->make("MSsql");
$myQuery->query();
```

输出：

```
binding Sql
not closure
making closure
binding myQuery
not closure
making closure
binding closure
Closure


making closure
Buildable Closure
closure is buildable!
Building
return closeure
closure works!


making A
eqs
A is buildable!
Building
need reflection!
A works!


making myQuery
Buildable Closure
myQuery is buildable!
Building
return closeure
making doQuery
eqs
doQuery is buildable!
Building
need reflection!
want Sql
making Sql
Buildable Closure
Sql is buildable!
Building
return closeure
making Postgresql
eqs
Postgresql is buildable!
Building
need reflection!
want A
making A
eqs
A is buildable!
Building
need reflection!


A works!
Postgresql is working!


binding Sql
not closure
making closure
making myQuery
Buildable Closure
myQuery is buildable!
Building
return closeure
making doQuery
eqs
doQuery is buildable!
Building
need reflection!
want Sql
making Sql
Buildable Closure
Sql is buildable!
Building
return closeure
making Mysql
eqs
Mysql is buildable!
Building
need reflection!
want A
making A
eqs
A is buildable!
Building
need reflection!
A works!
Mysql is working!


making MSsql
eqs
MSsql is buildable!
Building
need reflection!
MSsql is working!
```

  [1]: http://dwz.cn/5x5cnh