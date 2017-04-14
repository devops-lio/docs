# 配置清单

你可以使用 `set` 函数来定义配置变量, 然后使用 `get` 函数来读取它们。

```php
set('param', 'value');

task('deploy', function () {
    $param = get('param');
});
```

每个变量都可以在指定服务器覆盖:

```php
server(...)
    ->set('param', 'new value');
```

你也可以使用匿名函数来指定变量值，它将会在第一次读取操作 `get` 时被调用：

```php
set('param', function () {
    return run(...)->toString();
});
```

你可以在任务定义中使用 `{{}}` 来代替 `get` 读取变量值：

```php
run('cd ' . get('release_path') . ' && command');
```

可以简写为:

```php
run('cd {{release_path}} && command');
```

通用配置清单已经预定义了一些变量，你可以使用 `dep config:dump` 查看。

### deploy_path

远程服务器上的部署目录。

### ssh_type

* `phpseclib` (*default*) 使用 [phpseclib](https://github.com/phpseclib/phpseclib) 作为 SSH 客户端,
* `ext-ssh2` 使用 php [ssh2](http://php.net/manual/en/book.ssh2.php) 拓展,
* `native` 使用原生 ssh 客户端

### ssh_multiplexing

使用 [ssh multiplexing](https://en.wikibooks.org/wiki/OpenSSH/Cookbook/Multiplexing) 来加速 SSH 执行。

```php
set('ssh_multiplexing', true);
```

### default_stage

指定 `dep deploy` 使用的默认阶段。

```php
set('default_stage', 'prod');

server(...)
    ->stage('prod');
```

### keep_releases

指定保留的版本数，指定 `-1` 时保留全部版本。

### repository

代码仓库地址。

要使用私有代码库，需要在服务器上生成一个 SSH 密钥并添加到存储库中作为部署密钥（a.k.a. Access Key）。 此密钥将允许您的服务器拉代码。

Note that at the first time server can ask to add host in `known_hosts` file. The easiest way to do it is
running `git clone <repo>` on your server and saying `yes`.

请注意，服务器第一次会要求在 `known_hosts` 文件中添加信任主机。 最简单的方法是在您的服务器上运行一次 `git clone <repo>` 并输入 "yes"。

### branch

指定部署的代码分支

如果你想使用 tag 或者指定版本号部署，在命令 `dep deploy` 中使用 `--tag` 或 `--revision` 选项即可。

```bash
dep deploy --tag="v0.1"
dep deploy --revision="5daefb59edbaa75"
```

需要注意的是，`tag` 比 `branch` 优先级高，比 `revision` 优先级低。

### shared_dirs

共享目录列表

```php
set('shared_dirs', [
    'logs',
    'var',
    ...
]);
```

### shared_files

共享文件列表

### copy_dirs

指定需要在两个版本间复杂的目录

### writable_dirs

指定 web server 可写的目录

### writable_mode

可写模式。

* `acl` (*default*) 使用 `setfacl` 修改目录权限.
* `chmod` 使用 `chmod` 命令,
* `chown` 使用 `chown` 命令,
* `chgrp` 使用 `chgrp` 命令,

### writable_use_sudo

是否使用 `sudo` 来修改可读权限. 默认 `false`。

### writable_chmod_mode

使用 `writable_mode` 为 `chmod` 时值权限值。 默认: `0755`。

### writable_chmod_recursive

是否递归修改目录权限. 默认: `true`.

### http_user

web server 的用户名，如果不指定，Deploery 会自动检测并设置该值。

### clear_paths

升级完成后需要删除的目录。

### clear_use_sudo

是否使用 `sudo` 模式删除目录，默认： `false`。

### use_relative_symlink

是否使用相对路径创建软链接，默认 deployer 会自动检测系统支持的方式设置该值。

### composer_action

Composer 安装命令，默认： `install`。

### composer_options

Composer 命令选项。

### env_vars

环境变量。

更多请阅读 [部署任务](tasks.md).
