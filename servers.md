# 服务器配置

你可以使用 `server` 函数来定义你的服务器。这里有一个例子供参考：

~~~ php
server('prod_1', 'domain.com')
    ->user('user')
    ->password('pass')
    ->set('deploy_path', '/home/www')
    ->stage('production');

server('prod_2', 'domain.com')
    ->user('user')
    ->password('pass')
    ->set('deploy_path', '/home/www')
    ->set('branch', 'master')
    ->set('extra_stuff', '...')
    ->stage('production');
~~~

这个函数接受两个参数，像这样: `server(server_name, host)` (端口可以放到第三个参数)。 它将返回一个 `Deployer\Server\Builder` 对象。

要指定如何使用 SSH 连接到服务器，有几种方法：

### 使用指定的用户名密码

~~~ php
server(...)
  ->user('name')
  ->password('pass')
~~~

### 使用用户名与输入密码

~~~ php
server(...)
  ->user('name')
  ->password(null)
~~~

将密码设置为 *null* 将会在部署时让你手动输入。

### 使用密钥

~~~ php
server(...)
    ->user('name')
    ->identityFile();
~~~

如果您的密钥是使用密码创建的，或者它们位于 `.ssh` 目录之外，则可以通过提供完整路径指定位置：

~~~ php
server(...)
    ...
    ->identityFile('~/.ssh/id_rsa.pub', '~/.ssh/id_rsa', 'pass phrase');
~~~

### 使用 密钥 + 密码 或 Google 身份验证器 的两步认证方式：

如果您的服务器使用两步认证方式进行安全保护，则可以通过私钥 + 两步认证器（密码 或 Google 身份验证器）进行连接。 注意当前版本只支持 *PhpSecLib*：

~~~ php
server(...)
    ->user('name')
    ->identityFileAndPassword();
~~~

与普通的 identityFile() 调用一样，如果您的密钥是使用密码创建的，或者它们位于 `.ssh` 目录之外，则可以通过提供完整路径指定位置：

~~~ php
server(...)
    ...
    ->identityFileAndPassword('~/.ssh/id_rsa.pub', '~/.ssh/id_rsa', 'pass phrase', 'password');
~~~

这个 `~` 符号将会替换为你的家目录（本机非目标服务器）。

### 通过配置文件

Deployer 也可以使用你的 SSH 配置文件来指定连接方式。

~~~ php
server(...)
    ->user('name')
    ->configFile('/path/to/file');
~~~

### 使用证书文件

~~~ php
server('ec2', 'host.aws.amazon.com')
    ->user('ec2-user')
    ->pemFile('~/.ssh/keys.pem');
~~~

> 使用 pem 文件的身份验证方式目前仅支持 PhpSecLib。

### 使用 PHP SSH2 拓展

要使用这种认证方式，需要用到拓展 `herzult/php-ssh`， 它没有默认包含在 `deployer.phar`，所以你需要使用 Composer 安装它：

```
composer require deployer/deployer herzult/php-ssh
```

示例清单 `deploy.php`:

```php
require 'vendor/autoload.php';
require 'vendor/deployer/deployer/recipe/symfony.php';

set('ssh_type', 'ext-ssh2');
//...
```

## 上传与下载

你可以使用 `upload(local, remote)` 函数来完成上传任务。

同样你也可以使用 `download(local, remote)` 函数来从服务器下载内容。

## 服务器列表

你可以使用一个 YAML 文件来定义你的服务器列表：

~~~ yml
prod:
  host: domain.com
  user: www
  identity_file: ~
  stage: production
  deploy_path: /home/www/

prod.a:
  host: a.domain.com
  user: www
  identity_file:
    public_key:  /path/to/public.key
    private_key: /path/to/private.key
    password:    optional-password-for-private-key-or-null
  stage: production
  deploy_path: /home/www/

beta:
  host: beta.domain.com
  user: www
  password: pass
  stage: beta
  deploy_path: /home/www/

test:
  host: test.domain.com
  user: www
  password: pass
  stage: beta
  deploy_path: /home/www/
~~~

然后在 `deploy.php` 中使用下面的方式来注册它们：

~~~ php
serverList('servers.yml');
~~~

## 服务器列表的 YAML 文件格式与服务器配置

您可以为每个服务器定义配置变量。 解析服务器配置时，除下列之外的所有都被视为服务器配置变量，可以使用 `get('key')` 或 `{{key}}` 来读取它们：

* `local`
* `host`
* `port`
* `identity_file`
* `forward_agent`
* `user`
* `password`
* `stage`
* `pem_file`


## 本地服务器

此外，您可以定义 *本地服务器*，它在本地运行所有命令而不使用 ssh。

~~~ php
localServer(...)
    ->stage('local');
~~~

在外部的 .yml 中定义，你可以使用关键字 "local: "。

~~~ yml
development:
    local: true
    # ....
~~~

例如，您可以使用它来更新本地项目。

## 平台（Stages）

您可以为每个服务器定义一个平台，或一个平台列表:

~~~ php
server(...)
    ->stage('prod');

server(...)
    ->stage(['prod', 'stage']);

server(...)
    ->env('stages', ['stage']);
~~~

然后你可以使用命令 `dep [task] [server or stage]` 在不同的平台运行部署任务, 它将会只在指定的平台运行。

如果您不在命令行指定平台将会在默认平台上执行。 您可以通过设置 `default_stage` 参数来指定默认平台。

~~~ php
set('default_stage', 'staging');
~~~

如果没有指定一个平台或默认平台去执行一个命令，它会在所有没有指定平台的服务器上执行。