# Supervisor 的子进程

**supervisord** 的首要目的是创建和管理配置文件中指定的进程。它通过创建子进程实现这个功能。每一个由 supervisor 创建的进程在其整个生命周期中都由 supervisord（**supervisord** 是其创建的所有进程的父进程）管理。如果某个子进程意外终止，supervisor 会通过 `SIGCHLD` 信号得知其死讯，然后它会执行适当的操作。

## 非守护子进程

想要使用 supervisor 运行的程序不应该是守护进程。相反，它们应该在前台执行。它们不应该从启动它们的终端中分离。

判断一个程序是否会前台运行的最方便的办法是从 shell 命令提示符中运行调用该程序的命令。如果它把终端的控制权交还给你，但该程序仍保持运行，那么它就是守护进程，以这种方式使用 supervisor 运行它很可能就是不对的了。你希望的是一个必须通过按下 `Ctrl-C` 才能拿回终端控制权的命令。如果在执行它之后还没等你键入 `Ctrl-C` 就把 shell 命令提示符交还给你，这个命令就不能用 supervisor。所有程序都有在前台运行的选项，但是没有实现它的“定式”，你需要阅读每一个程序的文档了解如何前台运行它。

下面是一些已知的使用 Supervisor 以“前台”模式启动的常见程序配置文件示例。

### 程序配置示例

这里是一些“真实世界”的程序配置示例：

#### Apache 2.2.6

```ini
[program:apache2]
command=/path/to/httpd -c "ErrorLog /dev/stdout" -DFOREGROUND
redirect_stderr=true
```

#### 两个 Zope 2.X 实例和一个 ZEO 服务

```ini
[program:zeo]
command=/path/to/runzeo
priority=1

[program:zope1]
command=/path/to/instance/home/bin/runzope
priority=2
redirect_stderr=true

[program:zope2]
command=/path/to/another/instance/home/bin/runzope
priority=2
redirect_stderr=true
```

#### Postgres 8.X

```ini
[program:postgres]
command=/path/to/postmaster
; 我们使用“快速的”关闭信号 SIGINT
stopsignal=INT
redirect_stderr=true
```

#### OpenLDAP slapd

```ini
[program:slapd]
command=/path/to/slapd -f /path/to/slapd.conf -h ldap://0.0.0.0:8888
redirect_stderr=true
```

### 其他示例

可以从 http://thedjbway.b0llix.net/services.html 找到一些其他的可以使用 **supervisord** 启动服务的 shell 脚本。这些示例其实是为 **daemontools** 准备的，不过它和 supervisor 的要求是一致的。

还可以从 http://smarden.org/runit/runscripts.html 获得另一个前台启动不同程序的菜单集。

## pidproxy 程序

一些程序（比如 **mysqld**）会无视 **supervisord** 发送给实际进程的信号。相反，这样的程序会创建一个“特别的”线程/进程来处理信号。这就麻烦了，因为 **supervisord** 只能杀死自己创建的进程。如果某个由 **supervisord** 创建的进程创建了自己的子进程，**supervisord** 是没办法杀掉这些子进程的。

幸运的是，这样的程序通常会把“特别的”进程的 PID 写进一个“pid 文件”中，这样做就是为了想要杀死这个进程的时候可以被读取并使用。于是有一个解决办法，一个特别的 **pidproxy** 程序可以处理这种进程的启动。**pidproxy** 程序是用来启动进程的小桥梁，一旦它接收到一个信号，它会把信号发送给 pid 文件中提供的 pid。如下是一个可用 pidproxy 启用的程序的简单配置。

```ini
[program:mysql]
command=/path/to/pidproxy /path/to/pidfile /path/to/mysqld_safe
```

The **pidproxy** program is put into your configuration’s `$BINDIR` when supervisor is installed (it is a “console script”).

**pidproxy** 程序会在 supervisor 安装的时候自动放到你配置的 `$BINDIR` 中（它是一个“控制台脚本”）。

## 子进程环境

子进程会继承启动 **supervisord** 程序的 shell 环境。**supervisord** 本身也会为它的子进程环境设置几个环境变量，包括 `SUPERVISOR_ENABLED`（一个表明此进程由 supervisor 控制的标识）、`SUPERVISOR_PROCESS_NAME`（该进程由配置文件指定的进程名）和 `SUPERVISOR_GROUP_NAME`（由配置文件制定的该子进程所属的进程组）。

这些环境变量可以通过 `[supervisord]` 节的名为 `environment` 的配置选项（对所有子进程适用）或每个 `[program:x]` 节的 `environment` 配置选项（仅对 `[program:x]` 节中制定的子进程适用）覆盖。这些“环境”设置都是叠加覆盖的。换句话说，每一个子进程的环境由如下步骤组成：

- 环境变量设置成启动 supervisord 的 shell 的样子...

  ... 再加上/覆盖为 ...

  - ... 全局“environment”配置选项中设置的环境变量 ...

    ... 再加上/覆盖为 ...

    - ... supervisor 指定的环境变量（`SUPERVISOR_ENABLED`、`SUPERVISOR_PROCESS_NAME`、`SUPERVISOR_GROUP_NAME`）...

      ... 再加上/覆盖为 ...

      - 子进程的“environment”配置选项中设置的环境变量

当 **supervisord** 运行子进程时不会执行任何脚本，所以除非重新指定，否则诸如 `USER`、`PATH`、`HOME`、`SHELL`、`LOGNAME` 等环境变量一直都是其默认值。特别是当你通过 **supervisord** 以 root 用户运行程序并在节中指定了 `user=` 时一定要知晓此事。与 **cron** 不同的是，当给 `user=` 程序配置选项定义的用户执行设置 uid 的操作时，**supervisord** 不会试图预测和重写诸如 `USER`、`PATH`、`HOME` 和 `LOGNAME` 之类“基本的”环境变量。如果你需要给某个指定的程序设置执行终端时为某个指定的用户设定的环境变量，你必须在 `environment=` 程序配置选项中明确指定。如下是一个设置这些环境变量的例子。

```ini
[program:apache2]
command=/home/chrism/bin/httpd -c "ErrorLog /dev/stdout" -DFOREGROUND
user=chrism
environment=HOME="/home/chrism",USER="chrism"
```

## 进程状态

一个被 supervisord 控制的进程在任意给定的时间将会是以下状态的一种。你可能会在客户端的各种用户界面元素中看到这些状态名称。

| 状态名称         | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| `STOPPED` (0)    | 该进程因收到终止请求而已经终止或从未启动                     |
| `STARTING` (10)  | 该进程因收到启动请求而正在启动                               |
| `RUNNING` (20)   | 该进程正在运行                                               |
| `BACKOFF` (30)   | 该进程曾进入`STARTING` 状态，但是后来还未进入`RUNNING` 状态很快就推出了 |
| `STOPPING` (40)  | 该进程因收到中止请求而正在终止                               |
| `EXITED` (100)   | 该进程由`RUNNING` 状态退出（可预料地或不可预料地）           |
| `FATAL` (200)    | 该进程无法成功启动                                           |
| `UNKNOWN` (1000) | 该进程正处于一个未知状态（**supervisord** 程序错误）         |

所有由 supervisor 运行的进程都会按照下图的方向演变。

![子进程状态转换图](subprocess.assets/subprocess-transitions.png)

子进程状态转换图

如果某个进程处在 `STOPPED` 状态，那么它要么曾经被正常终止要么从未启动。

如果某个自动重启的进程处在 `BACKOFF` 状态，那么它将会自动被 **supervisord** 重启。它将会切换成 `STARTING` 或 `BACKOFF` 状态直到其 `startretries` 达到最大次数而无法重启，这时它会转变为 `FATAL` 状态。每次重启尝试都会花费越来越多的时间。

当某个进程处在 `EXITED` 状态，它是否会自动重启：

- 如果它的 `autorestart` 参数设置为 `false` 则永远不会重启
- 如果它的 `autorestart` 参数设置为 `true` 则会无条件重启
- 如果它的 `autorestart` 参数设置为 `unexpected` 则会有条件地重启。如果它以一个不匹配该进程 `exitcodes` 配置参数中定义的退出码退出，它将会被重启。

如果某个进程配置成有条件或无条件重启，从结果上看它会自动从 `EXITED` 状态转变成 `RUNNING` 状态。`RUNNING` 状态和 `EXITED` 状态之间转换的次数是无论如何都没有限制的：可以创建配置无限重启一个退出的进程。这是一个特性，而非 bug。

如果某个自动重启的进程以 `FATAL` 状态（此状态的进程必须手动重启）结束，那么它就不会再自动重启了。

一个进程可以经由合规的终止请求转换成 `STOPPING` 状态，最终会成为 `STOPPED` 状态。

不能成功终止的进程将永久停留在 `STOPPING` 状态。这种情况在常规操作下应该不可能出现，因为它意味着该进程没有响应由 supervisor 发送的 `SIGKILL` 信号，而这在 UNIX 上是“不可能”的。

这些状态转换永远需要用户操作来实现：

- `FATAL` -> `STARTING`
- `RUNNING` -> `STOPPING`

这些状态转换通常（不是必须）需要用户操作来实现，例外的情况已经标注出来了：

- `STOPPED` -> `STARTING`（除非在启动 supervisd 时，配置成为自动启动的进程）
- `EXITED` -> `STARTING`（除非该进程配置为自动重启）

其他所有状态转变都由 supervisord 自动管理。