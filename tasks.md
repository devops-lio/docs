# 部署任务

使用 `task` 函数来定义你自己的部署任务， 此外，您可以使用 `desc` 函数设置任务描述：

```php
desc('My task');
task('my_task', function () {
    run(...);
});
```

运行指定任务:

```sh
dep my_task
```

列出所有可用的命令:

```sh
dep list
```

在指定的平台服务器上运行部署任务:

```sh
dep deploy main
```

If you task contains only `run` calls or just one bash command, you can simplify task definition:

如果任务仅包含 `run` 调用或仅一个 bash 命令，则可以简化任务定义写法：

```php
task('build', 'npm build');
```

或者这样使用换行来定义多条 bash 命令:

```php
task('build', '
    gulp build;
    webpack -p;
    echo "Build done";
');
```

### Task 分组

你可以这样来定义一个任务组:

```php
task('deploy', [
    'deploy:prepare',
    'deploy:update_code',
    'deploy:vendors',
    'deploy:symlink',
    'cleanup'
]);
```

### 任务执行前/执行后

您可以在某些任务之前或之后定义要运行的任务。

``` php
task('deploy:done', function () {
    write('Deploy done!');
});

after('deploy', 'deploy:done');
```

在 `deploy` 任务执行后, `deploy:done` 将会执行。

### 任务执行条件

你可以使用 `onlyOn` 方法来指定在哪些服务器上执行任务：

``` php
desc('Run tests for application.');
task('test', function () {
    ...
})->onlyOn('test_server');
```

你可以使用: `onlyOn('server1', 'server2', ...)` 或者一个数组 `onlyOn(['server1', 'server2', ...])` 方式来指定在多台服务器上执行任务。

使用 `onlyForStage` 方法指定的平台服务器上运行部署任务：

```php
task('notify', function () {
    ...
})->onlyForStage('prod');
```

同样你也可以使用: `onlyForStage('stage1', 'stage2', ...)` 或者一个数组 `onlyForStage(['stage1', 'stage2', ...])` 方式来指定在多个平台执行任务。

### 单次任务

Mark task `once` to run it locally and only one time, independent of servers count.

```php
task('notify', function () {
    ...
})->once();
```

> Note what calling `run` inside that task will have same effect as calling `runLocally`.

### 重新配置

You can reconfigure tasks, e.g. provided by 3rd part recipes by retrieving them by name:

```php
task('notify')->onlyOn([
  'firstserver',
  'thirdserver',
]);
```

### 获取输入选项

你可以在定义任务之前定义额外的输入或选项:

``` php
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Input\InputArgument;

argument('stage', InputArgument::OPTIONAL, 'Run tasks only on this server or group of servers.');
option('tag', null, InputOption::VALUE_OPTIONAL, 'Tag to deploy.');
```

你可以像下面这样在任务内获取输入或者选项：

``` php
task('foo:bar', function() {
    // For arguments
    $stage = null;
    if (input()->hasArgument('stage')) {
        $stage = input()->getArgument('stage');
    }

    // For option
    $tag = null;
    if (input()->hasOption('tag')) {
        $tag = input()->getOption('tag');
    }
}
```

### 并行执行

如果我们定义了多台服务器，Deploery 可以同时并行执行部署任务：

<svg width="600" height="305" viewBox="990 42 600 305" xmlns="http://www.w3.org/2000/svg">
  <g fill="none" fill-rule="evenodd">
    <path d="M996.726 67.258h141.256v275.34H996.726V67.26zM990 63h600v283.857H990V63zm154.71 4.258h141.254v275.34H1144.71V67.26zm147.98 0h141.256v275.34H1292.69V67.26zm148.655 0H1582.6v275.34h-141.255V67.26z" fill="#D8D8D8"/>
    <g transform="translate(1000.762 73.09)">
      <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="22.363">Task 1</tspan>
      </text>
    </g>
    <g transform="translate(1148.744 104.704)">
      <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="22.363">Task 1</tspan>
      </text>
    </g>
    <g transform="translate(1296.726 138.336)">
      <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="22.363">Task 1</tspan>
      </text>
    </g>
    <g transform="translate(1444.71 169.95)">
      <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="22.363">Task 1</tspan>
      </text>
    </g>
    <g>
      <g transform="translate(1000.09 209.637)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
      <g transform="translate(1148.072 241.25)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
      <g transform="translate(1296.054 274.883)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
      <g transform="translate(1444.036 306.498)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
    </g>
    <path d="M994.73 205.292h588.942" stroke="#979797"/>
    <g font-size="18" font-family="HelveticaNeue-Light, Helvetica Neue" fill="#000" font-weight="300">
      <text transform="translate(1031 42)">
        <tspan x="0" y="17">Server 1</tspan>
      </text>
      <text transform="translate(1031 42)">
        <tspan x="146" y="17">Server 2</tspan>
      </text>
      <text transform="translate(1031 42)">
        <tspan x="304" y="17">Server 3</tspan>
      </text>
      <text transform="translate(1031 42)">
        <tspan x="450" y="17">Server 4</tspan>
      </text>
    </g>
  </g>
</svg>

你可以使用 `--parallel` 或者 `-p` 选项来在多台服务器上并行执行部署任务。如果其中一台服务器执行时间比其它服务器长，Deploery 将会一直等到所有服务器上的任务执行完成。

<svg width="600" height="305" viewBox="990 418 600 305" xmlns="http://www.w3.org/2000/svg">
  <g fill="none" fill-rule="evenodd">
    <path d="M996.726 443.258h141.256v275.34H996.726V443.26zM990 439h600v283.857H990V439zm154.71 4.258h141.254v275.34H1144.71V443.26zm147.98 0h141.256v275.34H1292.69V443.26zm148.655 0H1582.6v275.34h-141.255V443.26z" fill="#D8D8D8"/>
    <g transform="translate(1000.762 449.09)">
      <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="22.363">Task 1</tspan>
      </text>
    </g>
    <g transform="translate(1148.762 449.09)">
      <rect fill="#B8E986" width="134.529" height="40.923" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="24.093">Task 1</tspan>
      </text>
    </g>
    <g transform="translate(1296.762 449.09)">
      <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="22.363">Task 1</tspan>
      </text>
    </g>
    <g transform="translate(1444.762 449.09)">
      <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
      <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
        <tspan x="41.031" y="22.363">Task 1</tspan>
      </text>
    </g>
    <g>
      <g transform="translate(999.09 498.637)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
      <g transform="translate(1149.09 498.637)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
      <g transform="translate(1296.09 498.637)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
      <g transform="translate(1446.09 498.637)">
        <rect fill="#B8E986" width="134.529" height="30.942" rx="8"/>
        <text font-family="HelveticaNeue-Light, Helvetica Neue" font-size="18" font-weight="300" fill="#000">
          <tspan x="41.031" y="22.363">Task 2</tspan>
        </text>
      </g>
    </g>
    <path d="M994.73 494.292h588.942" stroke="#979797"/>
    <g font-size="18" font-family="HelveticaNeue-Light, Helvetica Neue" fill="#000" font-weight="300">
      <text transform="translate(1031 418)">
        <tspan x="0" y="17">Server 1</tspan>
      </text>
      <text transform="translate(1031 418)">
        <tspan x="146" y="17">Server 2</tspan>
      </text>
      <text transform="translate(1031 418)">
        <tspan x="304" y="17">Server 3</tspan>
      </text>
      <text transform="translate(1031 418)">
        <tspan x="450" y="17">Server 4</tspan>
      </text>
    </g>
  </g>
</svg>

下一篇: [服务器配置](servers.md).
