iboxsPHP 5.1 Workerman 扩展
===============

## 安装
composer require iboxs/iboxs-worker

## 使用方法

### SocketServer

首先创建控制器类并继承 iboxs\worker\Server，然后设置属性和添加回调方法

~~~
namespace app\index\controller;

use iboxs\worker\Server;

class Worker extends Server
{
	protected $socket = 'http://0.0.0.0:2346';

	public function onMessage($connection,$data)
	{
		$connection->send(json_encode($data));
	}
}
~~~
支持workerman所有的回调方法定义（回调方法必须是public类型）


在应用根目录增加入口文件 server.php

~~~
#!/usr/bin/env php
<?php
define('APP_PATH', __DIR__ . '/application/');

define('BIND_MODULE','index/Worker');

// 加载框架引导文件
require __DIR__ . '/iboxsphp/start.php';
~~~

在命令行启动服务端
~~~
php server.php start
~~~


linux下面可以支持下面指令
~~~
php server.php start|stop|status|restart|reload
~~~

在浏览器中进行客户端测试
http://127.0.0.1:2346/?id=1

### HttpServer

在应用根目录创建server.php

~~~
<?php
require __DIR__ . '/iboxsphp/base.php';

use iboxs\worker\Application;
use iboxs\worker\Server;

class Http extends Server
{
    protected $app;

    public function onWorkerStart($worker)
    {
        $this->app = new Application;
        $this->app->initialize();
    }

    public function onMessage($connection, $data)
    {
        $this->app->worker($connection, $data);
    }

}

new Http();
~~~

在命令行启动服务端
~~~
php server.php
~~~