# 工作流

如果您的部署清单基于 *common* 清单或其它与 Deployer 内置的框架部署方案，那么您正在使用我们的默认流程之一。
每个流程描述为 `deploy` 命名空间中的其他任务的组任务。 普通部署流程可能看起来像这样：

```php
task('deploy', [
    'deploy:prepare',
    'deploy:lock',
    'deploy:release',
    'deploy:update_code',
    'deploy:shared',
    'deploy:writable',
    'deploy:vendors',
    'deploy:clear_paths',
    'deploy:symlink',
    'deploy:unlock',
    'cleanup',
    'success'
]);
```

框架部署清单可能有差异，但基本结构是一样的。 您可以通过覆盖 "deploy" 任务来创建自己的流程，但最好是使用已有的。

例如，如果要在创建软链接到新版本目录之前运行一些任务:

```php
before('deploy:symlink', 'deploy:build');
```

或者在成功部署时显示一些通知消息:

```php
after('success', 'notify');
```

在下一节中，我将简要介绍每个任务。

### deploy:prepare

部署前的预处理工作， 检查 `deploy_path` 目录是否存在，否则创建它。 同时也会检查这些目录：

* `releases` – 用于放置各部署版本。
* `shared` – 各部署版本共享的内容。
* `.dep` – Deployer 专用的一些信息。

### deploy:lock

部署锁定。 所以只有一个 concurent 部署任务可以运行。 要锁定部署，首先检查现有的 `.dep/deploy.lock` 文件。 在部署过程中，Ctrl + C 来取消部署任务，运行 `dep deploy：unlock` 删除这个锁文件。 "deploy: unlock" 任务将在部署顺利完成时自动触发。

### deploy:release

创建基于 `release_name` 配置的部署文件夹. 同时也会检查 `.dep/releases` 得到之前已经部署的各版本列表。

同时删除 `deploy_path` 下上一个版本软链接。

### deploy:update_code

使用 git 更新代码。 如果 git 版本为 2.0 并且 `git_cache` 为打开状态时，任务将会使用上一个版本未改动的代码， 仅仅拉取更新部分的代码。

你可以在 `deploy.php` 覆盖这个任务来创建自己的代码更新策略：

```php
task('deploy:update_code', function () {
    upload('.', '{{release_path}}');
});
```

### deploy:shared

从 `shared` 到 `release_path` 创建共享文件目录。 你可以在 `shared_dirs` 和 `shared_files` 中来配置哪些目录或文件需要共享。 整个流程分为以下几步：

* 从 `release_path` 目录拷贝目录到 `shared`，如果不存在的话,
* 从 `release_path` 删除这些目录，
* 从 `shared` 创建软链接到 `release_path` 目录下。

共享文件的做法是相同的步骤， 如果您的系统支持相对软链接，则将使用相对路径方式创建，否则将绝对路径方式。

### deploy:writable

确保 `writable_dirs` 所配置的目录为可写权限. 默认使用 `acl` 模式 (使用 `setfacl` 命令)。这个任务将会尝试检测 http_user 名称, 或者使用你在清单中配置的:

```php
set('http_user', 'www-data');

// 指定服务器不同的 http_user
server(...)
    ->set('http_user', 'www-data');
```

可写权限同样支持以下的模式:

* chown
* chgrp
* chmod
* acl

在清单中指定需要使用的模式即可:

```php
set('writable_mode', 'chmod');
```

使用 `writable_use_sudo` 来指定是否使用 sudo 模式:

```php
set('writable_use_sudo', true);
```

### deploy:vendors

Composer 安装项目依赖的过程。 你可以通过 `composer_options` 来配置安装选项。

### deploy:clear_paths

清理 `clear_paths` 所指定的目录与文件，你可以使用 `clear_use_sudo` 选项指定是否使用 sudo 模式运行。

### deploy:symlink

在 `release_path` 下创建一个 `current` 软链接到最新部署的版本。 将会自动使用系统支持的软链接方式。

### deploy:unlock

删除 `.dep/deploy.lock` 锁文件。你也可以使用如下命令来直接移除锁文件：

```sh
dep deploy:unlock staging
```

### cleanup

清理旧版本，会保留最近版本中 `keep_releases` 指定的个数。如果设置为 `-1` 将会全部保留。

### success

打印成功消息。
