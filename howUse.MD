 laravel-smart
---------

- 项目的路由,视图文件均在扩展包内,默认的后台请不要作改动啦,项目主要的优势在于API接口的开发,自动生成标准的API文件格式
- 普通web应用也能做,如果需要添加额外的路由,请直接在根目录下的 `routes/web.php`  `routes/api.php` 里进行添加.
- 本项目的初衷是制作一个纯粹的扩展包,能安装即用,卸载即清.
- 演示站点正在搭建中, 手册什么的也在计划编写,请关注本项目,以便了解项目的最新动态.
大家可以积极提交 ISSUE 
- 为方便大家交流,以及项目代码bug反馈,大家可以添加QQ群 : _195857398_

-  [演示地址](http://112.124.44.7/backend/index/index)
     > 帐户 admin@admin.com 密码 123123
     
**简介**
--------
- 基于 laravel 5.5 
- 系统管理平台
- 商户管理平台
- RBAC 用户权限管理
- RESTful 接口
- 微信接口


安装步骤
--------
- 首先安装好laravel,并且设置好数据库连接
1. 在当目录，创建一个项目模板
```
laravel new
```
2. 在当目录创建一个blog项目
```
 laravel new blog
```
或者这么安装指定的版本
```
composer create-project --prefer-dist laravel/laravel blog "5.5.*"
```
>执行有点慢，耐心等待
>
3. 设置数据链接
 3.1 如果项目根目录没有.env 文件 则 cmd，进入cmd窗口，进入项目目录执行
```
 copy .env.example .env
``` 
 3.2  打开.env文件 修改以下代码
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=blog
DB_USERNAME=root
DB_PASSWORD=

```
> DB_PASSWORD可以为空


- 设置composer.json,找到config元素配置地方更改为如下

      "config": {
              "preferred-install": "dist",
              "sort-packages": true,
              "optimize-autoloader": true
       },"minimum-stability": "dev",
          "repositories": {
              "packagist": {
                  "type": "composer",
                  "url": "https://packagist.phpcomposer.com"
           }
       }

> 修改composer的repositories路径，安装更快一些

- composer 安装laravel-smart

    `composer require dominator88/laravel-smart:dev-master`

> 执行这一行命令可能报错，有可能laravel版本不对，这个工具依赖的版是5.5.*
> 用laravel new blog 创建的是laravel最新版本的项目,所以用 composer create-project --prefer-dist laravel/laravel blog "5.5.*" 创建需要的版本

    
- 运行下面命令来发布资源,以及安装数据库
    
    `php artisan vendor:publish --provider="Smart\SmartServiceProvider"`
        
    `php artisan make:auth`
    
    配置 config/backend.php,这里可以设置初始化超级管理员帐户,也可使用默认默认(不作 修改),在后面会提到默认配置的帐户名密码
    
    `php artisan smart:install`
> 执行这句，报错Grenral error:1709 Index column size too large. The maximum column size is 767 bytes.(SQL: alter table `users` add unique 'users_email_unique'('emial'))
> 解决这个问题:
> 1.改变数据库的配置，这种方法试验了没有成功。
> 2.尝试修改这个模块创建数据库的配置。
>修改/database/migrations/2014_10_12_000000_create_users_table.php报错的表,unique字段，不超过190 180试验成功
```

 $table->string('username',180);
```
> initData success laravel-smart install success!出现这个提示，说明安装成功了


为权限认证添加 模块,并设置为当前认证模型,  更改 confit/auth.php 文件

    <?php

    return [
    'defaults' => [
        'guard' => 'admin',
        'passwords' => 'users',
    ],
    'guards' => [
            'web' => [
                'driver' => 'session',
                'provider' => 'users',
            ],
            'admin' => [
                'driver' => 'session',
                'provider' => 'admin',
            ],
    
            'api' => [
                'driver' => 'token',
                'provider' => 'users',
            ],
        ],
        'providers' => [
                'users' => [
                    'driver' => 'eloquent',
                    'model' => App\Models\User::class,
                ],
                'admin' => [
                    'driver' => 'eloquent',
                    'model' => Smart\Models\SysUser::class,
                ],
        
                // 'users' => [
                //     'driver' => 'database',
                //     'table' => 'users',
                // ],
            ],
            'passwords' => [
                    'users' => [
                        'provider' => 'users',
                        'table' => 'password_resets',
                        'expire' => 60,
                    ],
                ],
            
            ];
            
### 图片上传设置            
 图片上传设置,需要运行如下命令,为图片的路径建立软链接,如果安装环境为docker,需要自行进入项目目录bash,将软链接设置正确
 
            php artisan storage:link

### 如此便可以进行访问：

>后台地址: http:// sitename /backend/index/index

>帐户名: sys_admin

>密码: 123123

>邮箱：admin@admin.com



从项目根目录进入 public 目录，执行命令：

  `npm update`
  
在此请注意 需要将 config/backend.php 里 baseUri 更改为当前域名 , 否则页面会出现样式错乱 
  
不知道 npm 如何安装的可以在搜索引擎上查找。

创建模块

   `php artisan smart:install 模块名`

>有哪些模块？如何用？为什么这么用?

执行上面命令后,会在app目录下生成对应模块

>进入后台，后台的菜单点不了，前端的地址不对,怎么改？需要进入模块进行改


### 接口说明(如何用？）
--------

> 接口uri 
> <https:// sitename /api>

> 接口密钥
> smart2_api_secret

#### header

| 名称                | 说明             |  描述                         |
| ------------------ |:----------------:| ----------------------------:|
| device              | 客户端系统类型    | Apple iphone 7               |
| device-os-version  | 客户端系统版本号   | 如: 10.0.2                    |
| timestamp          | 时间戳           | 如: 1476340001                |
| signature          | 签名             | 如: 103ed1d081... 详见签名规则  |


#### body

所有键和值全部为字符串

    {
      key1 : val1 ,
      key2 : val2,
      key2 : "[{k1 : v1} , { k2 : v2 }]" //用json字符串传输 数组
      ...
    }

#### 返回结果

- 单行数据

        {
          code : 0       // 0 表示成功 , 403 表示需要登录 , 其余为其他错误
          msg : "文字信息" ,
          data : {
            key1 : val1,
            key2 : val2,
            ...
          }
        }
    
- 多行数据

        {
          code : 0 
          msg : "文字信息" ,
          data : {
            rows : [
              {
                key01 : val01,
                key02 : val02,
                ...
              },
              {
                key11 : val11,
                key12 : val12,
                ...
              },
              ...
            ]
          },
          key : value 
        }


#### 签名规则

- 假设 要发送的数据 meta 为: 

      var meta = {
        token : "103ed1d0811212312" ,
        merId : "1",
      }
      签名密钥为 : smart_api_secret 

- 将 timestamp 加入meta数据中 , 如

      var signatureMeta = {
        token : "103ed1d0811212312" ,
        merId : "1",
        timestamp : 1476340001
      }

- signatureMeta 按key的字符正序排列 ,并转为 key1=val1&key2=val2... 类型的字符串,如:

      var signatureString = merId=1&timestamp=1476340001&token=103ed1d0811212312

- 在字符串后加上签名密钥 ,如: "&secret=签名密钥"

      var signatureString = merId=1&timestamp=1476340001&token=103ed1d0811212312&secret=smart2_api_secret

- md5 signatureString 得到签名





