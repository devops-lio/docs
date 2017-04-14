# 轻松上手

首先, 让我们先 [安装 Deployer](installation.md). 在你的终端运行以下命令即可:

```sh
curl -LO https://deployer.org/deployer.phar
mv deployer.phar /usr/local/bin/dep
chmod +x /usr/local/bin/dep
```

接着你就可以在终端使用 `dep` 命令了。
在终端你的项目根目录里运行以下命令:

```sh
dep init
```

这个命令将会在当前目录创建一个 `deploy.php` 文件. 它叫做 *清单（recipe）* 包含了部署项目所需要的配置内容。
所有的清单默认都继承自 [common](https://github.com/deployphp/deployer/blob/master/recipe/common.php) 清单.


定义你自己的任务是相当简单的:

```php
task('test', function () {
    writeln('Hello world');
});
```

只需要这条命令即可运行上面定义的任务了:

```sh
dep test
```

你将会看到如下的输出结果:

```text
➤ Executing task test
Hello world
✔ Ok
```

现在让我们创建一些将在远程服务器上运行的部署任务， 首先我们得配置我们的服务器。
像下面这样在刚才生成的 `deploy.php` 文件里使用 `server` 函数来配置服务器信息:

```php
server('production', 'domain.com')
    ->user('username')
    ->identityFile()
    ->set('deploy_path', '/var/www/domain.com');
```

你可以从 [这里](servers.md) 查看关于服务器配置的更多信息。 现在让我们定义一个在服务器上运行 `pwd` 命令的任务:

```php
task('pwd', function () {
    $result = run('pwd');
    writeln("Current dir: $result");
});
```

运行 `dep pwd` 命令, 将会看到这样的结果:

```text
➤ Executing task pwd
Current dir: /var/www/domain.com
✔ Ok
```

现在开始我们的第一个部署配置。 需要配置一些参数，比如 `repository`, `shared_files` 还有一些其它项目相关的参数:

```php
set('repository', 'git@domain.com:username/repository.git');
set('shared_files', [...]);
```

你可以在任务定义中使用 `get` 函数来读取所有使用 `set` 设置的参数。
当然你也可以在定义任务的时候为每个任务覆盖上面定义的参数:

```php
server('production', 'domain.com')
    ...
    ->set('shared_files', [...]);
```

更多关于配置的使用请参考 [这里](configuration.md).


现在我们来部署我们的项目:

```sh
dep deploy
```

如果你想要看看过程中究竟发生了什么，您可以使用 `--verbose` 选项来指定输出执行信息的详细级别：

* `-v`  一般信息,
* `-vv`  更详细的输出,
* `-vvv`  输出调试信息.

首先 Deployer 将会在服务器上创建以下目录:

* `releases`  用于放置各部署版本,
* `shared` 用于存放共享目录与文件,
* `current` 当前线上版本的软链接.

需要将你的项目入口配置到 `current`。

> 请注意 Deployer 默认使用 [ACL](https://en.wikipedia.org/wiki/Access_control_list) 来设置权限。
> 你可以通过配置 `writable_mode` 参数来配置其它方式。

默认 Deployer 保留 5 个部署版本, 但是你可以很方便的修改这个值:

```php
set('keep_releases', 10);
```

如果在部署过程中出现问题，或者新版本出现问题，
只需运行以下命令回滚到上一个正常工作的版本:

```sh
dep rollback
```

您可能希望在另一个任务之前/之后运行一些任务。 配置起来真的很简单！

让我们来定义一个在 `deploy` 任务执行结束后重启 php-fpm 服务的任务:

```php
task('reload:php-fpm', function () {
    run('sudo /usr/sbin/service php5-fpm reload');
});

after('deploy', 'reload:php-fpm');
```

更多关于配置的使用请参考 [配置](configuration.md) 。
