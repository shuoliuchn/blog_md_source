# 运行 Supervisor

这部分需要引用一个 `BINDIR` 来解释 **supervisord** 和 **supervisorctl** 命令是如何运行的。这是你的 Python 安装时配置的那个“bindir”路径。比如，通过 `./configure --prefix=/usr/local/py; make; make install` 命令安装的 Python，`BINDIR` 就是 `/usr/local/py/bin`。不同平台的 Python 解释器使用不同的 `BINDIR`。如果你不知道你的在哪的话，可以看一看 `setup.py install` 的输出。

## 添加一个程序（Program）

你需要在配置中添加至少一个 `program` 节，入此才能让 **supervisord** 真正地给你做些事。`program` 节定义一个当你执行 **supervisord** 时运行和管理的程序。要增加一个 program，你需要编辑 `supervisord.conf` 文件。

可能运行的程序中最简单的一个应该是 UNIX 的 **cat** 程序了。下面展示的是一个当 **supervisord** 进程启动和会运行 `cat` 的 `program` 节。

```ini
[program:foo]
command=/bin/cat
```

这一节可以复制粘贴到 `supervisord.conf` 文件中。这是可能运行的程序中最简单的配置，因为它只是指定了一个命令。Program 节还有很多其他的配置选项，并没有全部在这里展示出来。参见 [*节设置*](http://supervisord.org/configuration.html#programx-section) 获取更多信息

## 运行 supervisord

执行 `$BINDIR/supervisord` 命令启动 **supervisord**。创建的进程会以守护进程的形式脱离终端执行。它默认会在 `$CWD/supervisor.log` 维护一个日志。

你可以通过在命令行中传入 `-n` 参数让 **supervisord** 在前台执行。这在调试启动问题时非常有用。

> 当 **supervisord** 启动了之后，它会在*包括当前目录* 的默认位置寻找配置文件。如果你很有安全意识，最好在命令后通过“-c”参数声明配置文件的绝对路径。这样可以避免意外地在包含有恶意 `supervisord.conf` 文件的路径中启动 supervisor。当使用 root 启动 supervisor 时，如果不指定 `-c` 参数，将会抛出一个警告。

要修改由 **supervisord** 控制的程序，编辑 `supervisord.conf` 文件然后 `kill -HUP`，或者重启 **supervisord** 进程。这个文件中有几个程序定义的例子。

**supervisord** 命令支持很多命令行选项。每一个命令行选项都将覆盖掉配置文件中的等效值。

### supervisord 命令行选项

| 选项                               | 说明                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| -c FILE, --configuration=FILE      | **supervisord** 配置文件的路径。                             |
| -n, --nodaemon                     | 在前台运行 **supervisord**。                                 |
| -s, --silent                       | 不直接向标准输出中输出。                                     |
| -h, --help                         | 显示 **supervisord** 命令帮助。                              |
| -u USER, --user=USER               | UNIX 用户名或数字的用户 id。如果是以 root 用户启动的**supervisord**，在启动过程中越早指定这个用户的 uid 越好。 |
| -m OCTAL, --umask=OCTAL            | 八进制数（例如 022）代表 **supervisord** 启动后使用的 [*umask*](http://supervisord.org/glossary.html#term-umask)。 |
| -d PATH, --directory=PATH          | 当以守护进程的形式执行 supervisord 的时候，在启动守护进程前先进入到这个路径下。 |
| -l FILE, --logfile=FILE            | supervisord 活动日志所使用的文件路径名                       |
| -y BYTES, --logfile_maxbytes=BYTES | 轮回覆盖前 supervisord 活动日志的最大容量。这个值是可以加后缀的，比如“1” 是 1 字节，“1MB”是 1 兆字节，“1GB”是 1 吉字节。 |
| -z NUM, --logfile_backups=NUM      | supervisord 活动日志保存的最多备份数。每个日志文件的容量都是`logfile_maxbytes`。 |
| -e LEVEL, --loglevel=LEVEL         | supervisor 应该写入到活动日志中的日志级别。有效的值包括 `trace`、`debug`、`info`、`warn`、`error` 和 `critical`。 |
| -j FILE, --pidfile=FILE            | supervisord 应该把其 pid 写入到该文件的文件名                |
| -i STRING, --identifier=STRING     | 暴露给很多 UI 客户端来找到 supervisor 实例的任意字符串标识符。 |
| -q PATH, --childlogdir=PATH        | supervisor 在其 `AUTO` -模式下子进程日志的目录路径（必须是已经存在的）。 |
| -k, --nocleanup                    | 禁止 **supervisord** 在启动时执行清理操作（移除旧的 `AUTO` 进程日志文件）。 |
| -a NUM, --minfds=NUM               | 在 supervisord 进程成功启动前需要获得的最少文件描述符数。    |
| -t, --strip_ansi                   | 从所有子日志进程中删除ANSI转义序列。                         |
| -v, --version                      | 打印 supervisord 版本号到标准输出流并退出。                  |
| --profile_options=LIST             | 用于分析的逗号分隔的选项列表。这会令 **supervisord** 在分析器下运行，并给予选项输出结果。逗号分隔的列表有这些组成：`cumulative`、`calls`、`callers`。比如 `cumulative,callers`。 |
| --minprocs=NUM                     | supervisord 进程成功启动前所需的最少操作系统进程余量。       |

## 运行 supervisorctl

运行 `$BINDIR/supervisorctl` 命令来启动 **supervisorctl**。这样会出现一个 shell 页面，你可以以此来操控那些当前正在被 **supervisord** 管理着的进程。在提示符中输入“help”来获取所支持的命令的信息。

**supervisorctl** 可执行文件可以通过在命令行中指定参数，调用“一次性”的命令。例如：`supervisorctl stop all`。如果命令行中有参数，将禁止调用交互模式的 shell。相反，该命令会被执行并且 `supervisorctl` 将在成功的时候返回 0，在出错的时候返回非零信息。例如：如果任何一个进程没有在运行的话，`supervisorctl status all` 将会返回非零。

如果交互模式下调用的 **supervisorctl** 和需要权限的 **supervisord** 冲突，你将会被要求进行权限认证。

### supervisorctl 命令行选项

| 选项                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| -c, --configuration | 配置文件路径（默认 /etc/supervisord.conf）                   |
| -h, --help          | 打印用法信息并退出                                           |
| -i, --interactive   | 执行命令后启动交互 shell                                     |
| -s, --serverurl URL | supervisord 服务正在监听的 URL（默认为“[http://localhost:9001](http://localhost:9001/)”） |
| -u, --username      | 服务器权限认证所需要的用户名                                 |
| -p, --password      | 服务器权限认证所需要的密码                                   |
| -r, --history-file  | 保留阅读线历史（如果阅读线 readline 可用）                   |

活动/action [参数/arguments]

活动/Actions 就是像“tail”或者“stop”这样的命令。如果在命令行中指定了 -i 或者没有指定任何活动，就会启动一个交互式输入活动的“shell”解释器。使用“”活动来了解可用的活动。

### supervisorctl 的活动 Actions

TODO: 使用表格重构。。

help

> 打印出可用的活动的序列

help <action>

> 打印 <action> 的帮助信息

add <name> [...]

> 激活 process/group 配置中所有的更新

remove <name> [...]

> 取消 process/group 的配置激活

update

> 重新载入配置，并且按需增加/删除，而且会重启涉及到的程序

update all

> 重新载入配置，并且按需增加/删除，而且会重启涉及到的程序

update <gname> [...]

> 更新指定的组，而且会重启涉及到的程序

clear <name>

> 清除一个进程的日志文件

clear <name> <name>

> 清除多个进程的日志文件

clear all

> 清除所有进程的日志文件

fg <process>

> 前台模式连接一个进程，键入 Ctrl+C 退出前台模式

pid

> 获取 supervisord 的 PID

pid <name>

> 获取指定名字的单个子进程的 PID

pid all

> 获取所有子进程的 PID，每行一个

reload

> 重启远程 supervisord

reread

> 重新载入守护进程的配置文件，不会新增/删除（不会重启）

restart <name>

> 重启进程。注意：重启不会重新读取配置文件。如果你希望那样，参见 reread 和 update

restart <gname>:*

> 重启一个组中的所有进程。注意：重启不会重新读取配置文件。如果你希望那样，参见 reread 和 update

restart <name> <name>

> 重启多个进程或组。注意：重启不会重新读取配置文件。如果你希望那样，参见 reread 和 update

restart all

> 重启所有进程。注意：重启不会重新读取配置文件。如果你希望那样，参见 reread 和 update

signal

> No help on signal

start <name>

> 启动一个进程

start <gname>:*

> 启动一个组中的所有进程

start <name> <name>

> 启动多个进程或组

start all

> Start all processes

status

> Get all process status info.

status <name>

> Get status on a single process by name.

status <name> <name>

> Get status on multiple named processes.

stop <name>

> Stop a process

stop <gname>:*

> Stop all processes in a group

stop <name> <name>

> Stop multiple processes or groups

stop all

> Stop all processes

tail [-f] <name> [stdout|stderr] (default stdout)

> Output the last part of process logs Ex: tail -f <name> Continuous tail of named process stdout Ctrl-C to exit. tail -100 <name> last 100 *bytes* of process stdout tail <name> stderr last 1600 *bytes* of process stderr

## Signals

The **supervisord** program may be sent signals which cause it to perform certain actions while it’s running.

You can send any of these signals to the single **supervisord** process id. This process id can be found in the file represented by the `pidfile` parameter in the `[supervisord]` section of the configuration file (by default it’s `$CWD/supervisord.pid`).

### Signal Handlers

```
SIGTERM
```

> **supervisord** and all its subprocesses will shut down. This may take several seconds.

```
SIGINT
```

> **supervisord** and all its subprocesses will shut down. This may take several seconds.

```
SIGQUIT
```

> **supervisord** and all its subprocesses will shut down. This may take several seconds.

```
SIGHUP
```

> **supervisord** will stop all processes, reload the configuration from the first config file it finds, and start all processes.

```
SIGUSR2
```

> **supervisord** will close and reopen the main activity log and all child log files.

## Runtime Security

The developers have done their best to assure that use of a **supervisord** process running as root cannot lead to unintended privilege escalation. But **caveat emptor**. Supervisor is not as paranoid as something like DJ Bernstein’s [*daemontools*](http://supervisord.org/glossary.html#term-daemontools), inasmuch as **supervisord** allows for arbitrary path specifications in its configuration file to which data may be written. Allowing arbitrary path selections can create vulnerabilities from symlink attacks. Be careful when specifying paths in your configuration. Ensure that the **supervisord** configuration file cannot be read from or written to by unprivileged users and that all files installed by the supervisor package have “sane” file permission protection settings. Additionally, ensure that your `PYTHONPATH` is sane and that all Python standard library files have adequate file permission protections.

## Running supervisord automatically on startup

If you are using a distribution-packaged version of Supervisor, it should already be integrated into the service management infrastructure of your distribution.

There are user-contributed scripts for various operating systems at: https://github.com/Supervisor/initscripts

There are some answers at Serverfault in case you get stuck: [How to automatically start supervisord on Linux (Ubuntu)](http://serverfault.com/questions/96499/how-to-automatically-start-supervisord-on-linux-ubuntu)