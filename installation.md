# 安装

你可以通过两种方式安装 Deployer：使用 phar 包或者使用 Composer。

### Phar 包

使用 phar 包安装 Deployer：

```sh
curl -LO https://deployer.org/deployer.phar
mv deployer.phar /usr/local/bin/dep
chmod +x /usr/local/bin/dep
```

如果你需要其它版本的 Deployer，你可以从 [下载](https://deployer.org/download) 页面找到。
然后，使用以下命令来更新 Deployer 版本:

```sh
dep self-update
```

要升级到下一个主要版本（如果可用），请使用 `--upgrade(-u)` 选项：

```sh
dep self-update --upgrade
```

### Composer

使用 Composer 包安装 Deployer：

```sh
composer require deployer/deployer --dev
```

当然你也可以把 Deployer 作为全局命令安装：

``` sh
composer global require deployer/deployer
```

关于 Composer 全局命令的使用: https://getcomposer.org/doc/03-cli.md#global

然后在项目根目录使用 Deployer：

```sh
php vendor/bin/dep
```

> 如果您已经使用上面 **两种** 方法安装了 Deployer，则运行 `dep` 命令时将使用 composer 安装的版本。

### 源码

如果你想使用源码方式编译安装, 你可以从 GitHub clone 后使用：

```sh
git clone https://github.com/deployphp/deployer.git
```

然后使用以下命令来编译打包：

```sh
php ./build
```

将会在当前目录生成一个 `deployer.phar` 文件。

接下来，请阅读 [轻松上手](getting-started.md)。
