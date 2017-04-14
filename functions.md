# 函数

Deployer 提供了许多工具函数，请在使用之前详细了解它们。

```php
run(string $command)
```

在远程服务器上运行命令：

```php
cd(string $path)
```

为 `run` 命令设置工作目录。
在每个任务开始前都会重置工作目录。

```php
runLocally(string $command, int $timeout = 60)
```

在你本地运行命令。
默认超时: 60 秒

```php
upload(string $file, string $uploadFile)
```

上传一个文件到远程服务器上。
你可以在 $file 和 $uploadFile 使用环境变量，这意味着你可以像下面这样写：

upload('.deploy/parameters.{{env}}.yml', '{{release_path}}/app/config/parameters.yml');

将被转换为：

upload('.deploy/parameters.dev.yml', '/var/www/release/current/app/config/parameters.yml');

```php
download(string $localFile, string $deploymentFile)
```

从远程服务器下载一个文件到本地。


```php
write(string $message)
```

在命令行输出消息，你可以使用这些标签来格式化消息样式： `<info>...</info>`, `<comment></comment>` 或 `<error></error>` (see [Symfony Console](http://symfony.com/doc/current/components/console/introduction.html#coloring-the-output)).

```php
writeln(string $message)
```

与 `write` 函数相同，结尾有换行。

```php
ask(string $message, mixed $default)
```

要求用户输入内容，你也可以指定一个默认值来方便快速操作。

```php
askChoice(string $message, array $availableChoices, mixed $default, bool $multiselect)
```

让用户选择对应的内容，选项使用 key/value 指定，多选可用时，使用 `,` 号分隔默认值，默认值将在安静模式下使用，否则第一个可用选项将被选中。

```php
askConfirmation(string $message[, bool $default = false])
```

让用户做一个选择，yes or no。

```php
askHiddenResponse(string $message)
```

要求用户输入敏感内容，输入时不可见。

```php
output()
```

获取当前命令行输出对象。
