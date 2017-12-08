---
layout: post
title: "使用 deployer 自动化部署 laravel"
category: "php"
tags: ['laravel', 'deployer']
---

## 安装

### phar 包方式

```shell
$ curl -LO https://deployer.org/deployer.phar
$ mv deployer.phar /usr/local/bin/dep
$ chmod +x /usr/local/bin/dep
```

### composer 方式

```shell
$ composer require deployer/deployer --dev
```

当以 composer 方式安装时，需要这样运行：

```shell
$ php vendor/bin/dep
```

<!-- more -->

## 简单用法

### 1. 初始化配置

```shell
$ dep init
```

### 2. 本地执行任务

```php
task('build', function () {
})->local();
```

### 3. 任务中调用其它任务

```php
task('build', function () {
  set('deploy_path', __DIR__ . '/.build');
  invoke('deploy:prepare');
  invoke('deploy:release');
  invoke('deploy:update_code');
  invoke('deploy:vendors');
  invoke('deploy:symlink');
})->local();
```


## 部署 Laravel 项目

### 纯服务器方案

执行 `dep init` 后，根据提示，选择 laravel 项目，会生成下面的配置文件：

```php
namespace Deployer;

// 被引用文件保存在 deployer/deployer 项目中
require 'recipe/laravel.php';

// 项目名
set('application', 'om');

// 项目仓库
set('repository', 'git@code.git');

// 可选：Allocate tty for git clone. Default value is false.
set('git_tty', true); 

// 多次部署之间共享的文件或目录
add('shared_files', []);
add('shared_dirs', []);

// 服务器上需要写权限的目录
add('writable_dirs', []);
set('allow_anonymous_stats', false);

// 部署的域名
host('mystie.com')->set('deploy_path', '~/{{application}}');
    
// 定义任务
task('build', function () {
    run('cd {{release_path}} && build');
});

// 可选：部署失败，自动解锁文件
after('deploy:failed', 'deploy:unlock');

// 在创建软链接前，执行迁移数据库脚本
before('deploy:symlink', 'artisan:migrate');
```

执行 `dep deploy`，使用该配置部署。

部署任务对应的步骤定义在 `deployer/deployer` 项目的 `recipe/laravel.php` 中：

```php
/**
 * Main task
 */
desc('Deploy your project');
task('deploy', [
    'deploy:info',
    'deploy:prepare',
    'deploy:lock',
    'deploy:release',
    'deploy:update_code',
    'deploy:shared',
    'deploy:vendors',
    'deploy:writable',
    'artisan:storage:link',
    'artisan:view:clear',
    'artisan:cache:clear',
    'artisan:config:cache',
    'artisan:optimize',
    'deploy:symlink',
    'deploy:unlock',
    'cleanup',
]);

after('deploy', 'success');
```

这些任务都是在登录到远端服务器后执行。

### 本地+服务器方案

**流程**

```

    +-------------+       +-------------+        +--------------+
    |  .dep       |       | compute ver |        |              |
    |  releases   +-----> + release     +------> +  git clone   |
    |  shared     |       |             |        |              |
    +-------------+       +-------------+        +--------------+
                                                         |
                                                         |
                                                         v
    +-------------+       +-------------+        +--------------+
    |             |       | npm install |        |   composer   |
    |   upload    |<------+ npm build   | <------+    vendor    |
    |             |       |             |        |              |
    +-------------+       +-------------+        +--------------+

```

**部署脚本**

```php
namespace Deployer;

require 'recipe/laravel.php';
require 'recipe/npm.php';


///////////////////////////////////////////////////////////////////////////////
// 定义部署参数

// Project name
set('application', 'om');

// Project repository
set('repository', 'git@code.cn');

// [Optional] Allocate tty for git clone. Default value is false.
set('git_tty', true);

set('allow_anonymous_stats', false);

set('current_script_path', __DIR__);

// Shared files/dirs between deploys 
add('shared_files', []);
// 上传资源目录和下载资源目录，更好的方案是统一使用 public/storage
// 详见：https://laravel.com/docs/5.5/filesystem
add('shared_dirs', [
  'public/download',
  'public/upload'
]);

// Writable dirs by web server 
add('writable_dirs', [
  'public/download',
  'public/upload'
]);

// Hosts
host('mysite.com')->set('deploy_path', '~/{{application}}');


///////////////////////////////////////////////////////////////////////////////
// 绑定事件

// [Optional] if deploy fails automatically unlock.
after('deploy:failed', 'deploy:unlock');

// Migrate database before symlink new release.
before('deploy:symlink', 'artisan:migrate');


///////////////////////////////////////////////////////////////////////////////
// 任务

task('local:phpunit', function () {
  run("cd {{local_release_path}} && phpunit");
})->local();

// build npm
task('npm:build', function () {
  run("cd {{release_path}} && {{bin/npm}} run production");
})->local();


task('npm:copy_install', function () {
  set('bin/npm', function () {
    return run('which npm');
  });

  if (test('[ -d {{current_script_path}}/node_modules ]')) {
    run('cp -R {{current_script_path}}/node_modules {{release_path}}');
  }
  run("cd {{release_path}} && {{bin/npm}} install");
});


task('copy_deploy:vendors', function () {
  if (test('[ -d {{current_script_path}}/vendor ]')) {
    run('cp -R {{current_script_path}}/vendor {{release_path}}');
  }
  run('cd {{release_path}} && {{bin/composer}} {{composer_options}}');
});

// 本地编译
task('build:local', function () {
  set('deploy_path', __DIR__ . '/.build');
  invoke('deploy:prepare');     // create releases and shared dirs locally
  invoke('deploy:release');     // release number locally
  invoke('deploy:update_code'); // git clone locally
  invoke('copy_deploy:vendors');// composer install locally
  invoke('npm:copy_install');   // npm install locally
  invoke('npm:build');          // npm install locally
  invoke('local:phpunit');      // phpunit tests locally
  invoke('deploy:symlink');     // symlink /current locally
})->local();


// local -> server
task('upload', function () {
  upload(__DIR__ . "/.build/current/", '{{release_path}}');
});


// 部署流程
task('deploy', [
  'deploy:info',
  'build:local',
  'deploy:prepare',
  'deploy:lock',
  'deploy:release',
  'upload',
  'deploy:shared',          // shared and .env linking on server
  'deploy:writable',        // ensure shared paths are writable on server
  'artisan:storage:link',   // execute artisan storage:link
  'artisan:view:clear',
  'artisan:cache:clear',
  'artisan:config:cache',
  'artisan:optimize',
  'deploy:symlink',
  'deploy:unlock',
  'cleanup',
  'success'
]);
```

## 更多“配方”

上面光定义了 laravel 一种部署方案，更多部署方案请参考：

* [deployer 官方](https://deployer.org/recipes)
* [deployer github](https://github.com/deployphp/recipes)