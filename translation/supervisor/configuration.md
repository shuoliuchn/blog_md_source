# Supervisor 的配置文件

Supervisor 的配置文件通常命名为 `supervisord.conf`。它同时被 **supervisord** 和 **supervisorctl** 使用。如果那两个应用启动时没有指定 `-c` 选项（该选项用来把配置文件名明确告诉给应用），该应用就会按列举的顺序从下面的位置中寻找一个名为 `supervisord.conf` 的文件。它将使用最先找到的文件。

1. `../etc/supervisord.conf`（相对于可执行文件的位置）
2. `../supervisord.conf`（相对于可执行文件的位置）
3. `$CWD/supervisord.conf`
4. `$CWD/etc/supervisord.conf`
5. `/etc/supervisord.conf`
6. `/etc/supervisor/supervisord.conf`（Supervisor 3.3.0 以后）

> 许多 Debian 和 Ubuntu 管理的 Supervisor 版本包含一个增加 `/etc/supervisor/supervisord.conf` 至搜寻路径的补丁。最早包含此路径的 Supervisor 的 PyPI 包是 Supervisor 3.3.0。

## 文件格式

`supervisord.conf` 是一个 Windows 的 INI 风格（Python 的 ConfigParser）文件。它由节（由一个个的 `[header]` 表示）和节中的键/值对组成。这些节和它们允许指定的至如下所示。

### 环境变量

**supervisord** 启动时的环境存在的环境变量和可以在配置文件中以 Python 字符串表达式语法 `%(ENV_X)s` 使用：

```ini
[program:example]
command=/usr/bin/example --loglevel=%(ENV_LOGLEVEL)s
```

在上面的例子中，`%(ENV_LOGLEVEL)s` 表达式将被 `LOGLEVEL` 环境变量替换。

> 在 Supervisor 3.2 和更新的版本中，所有选项都支持 `%(ENV_X)s` 表达式。在从前的版本中，一些选项支持它们，不过大多数都不支持。参见下面的每个选项的文档。
>

## `[unix_http_server]` 节的设置

`supervisord.conf` 文件有一个名为 `[unix_http_server]` 的节，用来引入一个需要的监听 UNIX 域套接字 HTTP 服务器参数的配置。如果配置文件中没有 `[unix_http_server]` 节，就不会启动 UNIX 域套接字 HTTP 服务器。如下是允许使用的配置值。

### `[unix_http_server]` 节的值

#### file

一个 supervisor 将要监听 HTTP/XML-RPC 请求的 UNIX 域套接字。**supervisorctl** 在这个端口使用和 **supervisord** 交流。这个选项可以包括 `%(here)s` 格式的值，这将被替换成 **supervisord** 配置文件被发现的位置。

- 默认值：None
- 是否必填：否
- 最早引入版本：3.0

> **echo_supervisord_conf** 输出的配置示例使用 `/tmp/supervisor.sock` 作为套接字文件。那个路径仅仅是一个例子，很可能需要替换成一个更适合你系统的位置。有些系统会定期删除 `/tmp` 中较老的文件。如果套接字文件被删除了，**supervisorctl** 将无法连接到 **supervisord**。

#### chmod

启动时修改 UNIX 域套接字的 UNIX 权限模式位为这个值。

- 默认值：`0700`
- 是否必填：否
- 最早引入版本：3.0

#### chown

修改套接字文件的用户和组为这个值。可以是一个 UNIX 用户名（比如 `chrism`），也可以是用冒号分隔的 UNIX 用户名和组（比如 `chrism:wheel`）。

- 默认值：使用启动 supervisord 的用户名和组
- 是否必填：否
- 最早引入版本：3.0

#### username

这个 HTTP 服务器需要用来获取权限的用户名。

- 默认值：不需要用户名
- 是否必填：否
- 最早引入版本：3.0

#### password

这个 HTTP 服务器需要用来获取权限的密码。这可以是一个明文密码，也可以通过使用 `{SHA}` 前缀字符串的方式指定成 SHA-1 哈希值。比如，`{SHA}82ab876d1387bfafe46cc1c8a2ef074eae50cb1d` 是密码“thepassword”的 SHA 存储版本。

注意哈希过的密码必须是十六进制格式。

- 默认值：不需要密码
- 是否必填：否
- 最早引入版本：3.0

### `[unix_http_server]` 节的示例

```ini
[unix_http_server]
file = /tmp/supervisor.sock
chmod = 0777
chown= nobody:nogroup
username = user
password = 123
```

## `[inet_http_server]` 节的设置

`supervisord.conf` 文件有一个名为 `[inet_http_server]` 的节，用来引入一个需要的监听 TCP（网络） 套接字 HTTP 服务器参数的配置。如果配置文件中没有 `[inet_http_server]` 节，就不会启动网络套接字 HTTP 服务器。如下是允许使用的配置值。

> 默认情况下，网络 HTTP 服务器是未启用的。如果你选择启用它，请阅读以下安全警告。网络 HTTP 服务器仅应该在可靠的环境中启用。他应该仅绑定在本地 localhost 或仅可被隔离且可靠的网络访问。网络 HTTP 服务器不支持任何形式的加密。网络 HTTP 服务器默认情况下不使用任何权限认证（参见 `username=` 和 `password=` 选项）。网络 HTTP 服务器可以被 **supervisorctl** 远程操控。它也提供一个运行子进程启动和终止以及查看子进程日志的网络交互。**永远不要把网络 HTTP 服务器暴露到公共互联网上。**
>

### `[inet_http_server]` 节的值

#### port

一个 supervisor 用来监听 HTTP/XML-RPC 请求的 TCP 主机:端口 值，或者比如说 `127.0.0.1:9001`。**supervisorctl** 将在这个端口上使用 XML-RPC 和 **supervisord** 进行交流。若要监听此机器的所有接口，使用 `:9001` 或者 `*:9001`。请阅读上面的安全警告。

- 默认值：无
- 是否必填：是
- 最早引入版本：3.0

#### username

这个 HTTP 服务器需要用来获取权限的用户名。

- 默认值：不需要用户名
- 是否必填：否
- 最早引入版本：3.0

#### password

这个 HTTP 服务器需要用来获取权限的密码。这可以是一个明文密码，也可以通过使用 `{SHA}` 前缀字符串的方式指定成 SHA-1 哈希值。比如，`{SHA}82ab876d1387bfafe46cc1c8a2ef074eae50cb1d` 是密码“thepassword”的 SHA 存储版本。

注意哈希过的密码必须是十六进制格式。

- 默认值：不需要密码
- 是否必填：否
- 最早引入版本：3.0

### `[inet_http_server]` 节的示例

```ini
[inet_http_server]
port = 127.0.0.1:9001
username = user
password = 123
```

## `[supervisord]` 节的设置

`supervisord.conf` 文件有一个名为 `[inet_http_server]` 的节，用来引入关于 **supervisord** 进程的全局配置。如下所示。

### `[supervisord]` 节的值

#### logfile

supervisord 进程活动日志的路径。这个选项可以包括 `%(here)s` 值，它会被替换成配置文件被找到的目录。

> 如果 `logfile` 设置成像 `/dev/stdout` 的特殊文件是没办法找到的，要想禁用滚动刷日志只能通过设置 `logfile_maxbytes = 0`。

- 默认值：`$CWD/supervisord.log`
- 是否必填：否
- 最早引入版本：3.0

#### logfile_maxbytes

在滚动刷日志之前活动日志文件所能占用的最大字节数（这个值可以使用像“KB”、“MB”和“GB”的单位）。把这个值设置成 0 意味着不限制日志容量。

- 默认值：50MB
- 是否必填：否
- 最早引入版本：3.0

#### logfile_backups

在活动日志滚动刷新产生文件保存的备份数目。如果设置为 0，则不会保存备份。

- 默认值：10
- 是否必填：否
- 最早引入版本：3.0

#### loglevel

日志级别，决定了将要写入 supervisord 活动日志的内容。可以是如下选项之一： `critical`、`error`、`warn`、`info`、`debug`、`trace` 或 `blather`。注意在 `debug` 日志级别下，supervisord 日志文件将会记录它子进程的 stderr/stdout 输出和关于进程状态改变的额外信息，这对调试一个没有正常启动的进程十分有用。参见：[*活动日志级别*](http://supervisord.org/logging.html#activity-log-levels)。

- 默认值：info
- 是否必填：否
- 最早引入版本：3.0

#### pidfile

supervisord 存放 pid 文件的位置。这个选项可以包括 `%(here)s` 格式的值，这将被替换成 supervisord 配置文件被发现的位置。

- 默认值：`$CWD/supervisord.pid`
- 是否必填：否
- 最早引入版本：3.0

#### umask

supervisord 进程的 [*umask*](http://supervisord.org/glossary.html#term-umask)。

- 默认值：`022`
- 是否必填：否
- 最早引入版本：3.0

#### nodaemon

如果设置成 true，supervisord 将会在前台运行，而非以守护进程的形式。

- 默认值：false
- 是否必填：否
- 最早引入版本：3.0

#### silent

如果设置成 true 且以非守护进程形式执行，日志将不会指向标准输出（stdout）。

- 默认值：false
- 是否必填：否
- 最早引入版本：4.2.0

#### minfds

supervisord 能够成功启动所需要的最小文件描述符数目。为了试图让 supervisord 进程的软硬限制提高到适合 `minfds` 的水平，将会调用 setrlimit 方法。硬限制或许只有当以 root 运行时才会提高。supervisord 会频繁使用文件描述符，如果不能从操作系统中获取时会进入失败模式，所以能指定一个确保程序运行过程中不会用光它们的最小值是很有用的。这些限制会被它管理的子进程继承。这个选项在 Solaris 上极其有用，因为它默认每个进程的 fd 限制都很小。

- 默认值：1024
- 是否必填：否
- 最早引入版本：3.0

#### minprocs

supervisord 能够成功启动所需要的最小进程描述符数目。为了试图让 supervisord 进程的软硬限制提高到适合 `minprocs` 的水平，将会调用 setrlimit 方法。硬限制或许只有当以 root 运行时才会提高。如果操作系统用光了进程描述符，supervisord 将会进入失败模式，所以在 **supervisord** 启动时确保有足够的进程描述符时很有用的。

- 默认值：200
- 是否必填：否
- 最早引入版本：3.0

#### nocleanup

阻止 supervisord 在启动的时候清理任何存在的 `AUTO` 子日志文件。这个在调试的时候很有用。

- 默认值：false
- 是否必填：否
- 最早引入版本：3.0

#### childlogdir

`AUTO` 子日志文件使用的路径。这个选项可以包括 `%(here)s` 格式的值，这将被替换成 **supervisord** 配置文件被发现的位置。

- 默认值：Python 的 `tempfile.get_tempdir()` 值
- 是否必填：否
- 最早引入版本：3.0

#### user

指示 **supervisord** 在执行任何有意义的进程之前，将用户切换为这个 UNIX user 账号。只有当 **supervisord** 以 root 用户启动的时候才能切换用户。

- 默认值：不切换用户
- 是否必填：否
- 最早引入版本：3.0
- 变更历史：3.3.4 版本。如果 **supervisord** 不能切换到指定的用户，将会在 `stderr` 写一条错误信息然后立即退出。在早期版本中，仍然会继续执行，不过会记录一条 `critical` 级别的日志信息。

#### directory

当 **supervisord** 以守护进程运行的时候，切换到这个路径。这个选项可以包括 `%(here)s` 格式的值，这将被替换成 **supervisord** 配置文件被发现的位置。

- 默认值：不切换路径
- 是否必填：否
- 最早引入版本：3.0

#### strip_ansi

从子日志文件中删除所有 ANSI 转义序列。

- 默认值：false
- 是否必填：否
- 最早引入版本：3.0

#### environment

一串 `KEY="val",KEY2="val2"` 形式的键/值对将会配置到 **supervisord** 进程的环境中（也会导致它所有的子进程环境中有这些变量）。这个选项可以包括 `%(here)s` 格式的值，这将被替换成 supervisord 配置文件被发现的位置。包含非数字之母符号的值需要用引号引起来（比如 `KEY="val:123",KEY2="val,456"`）。在其他情况下不必非要把值引起来，但还是建议用引号引一下。要转义百分号，只需使用两个即可（比如 `URI="/first%%20name"`）。**注意**子进程将继承启动 **supervisord** 时使用的终端的环境变量，除非它们被此处或 program 节的 `environment` 选项覆盖掉。参见[子进程环境](subprocess.md#子进程环境)。

- 默认值：无值
- 是否必填：否
- 最早引入版本：3.0

#### identifier

此 supervisor 进程的描述符字符串，RPC 接口会用到。

- 默认值：supervisor
- 是否必填：否
- 最早引入版本：3.0

### `[supervisord]` 节的示例

```ini
[supervisord]
logfile = /tmp/supervisord.log
logfile_maxbytes = 50MB
logfile_backups=10
loglevel = info
pidfile = /tmp/supervisord.pid
nodaemon = false
minfds = 1024
minprocs = 200
umask = 022
user = chrism
identifier = supervisor
directory = /tmp
nocleanup = true
childlogdir = /tmp
strip_ansi = false
environment = KEY1="value1",KEY2="value2"
```

## `[supervisorctl]` 节的设置

配置文件中可以包括 **supervisorctl** 交互终端程序的设置。这些选项如下所示。

### `[supervisorctl]` 节的值

#### serverurl

访问 supervisord 服务器所需使用的 URL，比如 `http://localhost:9001`。对于 UNIX 域套接字，使用 `unix:///absolute/path/to/file.sock`。

- 默认值： `http://localhost:9001`
- 是否必填：否
- 最早引入版本：3.0

#### username

通过 supervisord 服务器权限认证所需的用户名。这应该和你试图访问的 supervisord 服务器的端口或 UNIX 域套接字配置的 `username` 相同。

- 默认值：无用户名
- 是否必填：否
- 最早引入版本：3.0

#### password

通过 supervisord 服务器权限认证所需的密码。这应该和你试图访问的 supervisord 服务器的端口或 UNIX 域套接字配置的明文 `password` 相同。这个值不能以 SHA 哈希值传递。与本文件中声明的其他密码不同，这里必须提供明文密码。

- 默认值：无密码
- 是否必填：否
- 最早引入版本：3.0

#### prompt

supervisorctl 使用的提示字符串。

- 默认值：`supervisor`
- 是否必填：否
- 最早引入版本：3.0

#### history_file

`readline` 持久化历史文件的路径。如果你通过指定一个路径启用了此特性，你的 supervisorctl 命令将被保存在该文件中，而且你可以使用 readline（比如上箭头）调用你上一个 supervisorctl 回话中执行过的命令。

- 默认值：无文件
- 是否必填：否
- 最早引入版本：3.0a5

### `[supervisorctl]` 节的示例

```ini
[supervisorctl]
serverurl = unix:///tmp/supervisor.sock
username = chris
password = 123
prompt = mysupervisor
```

## `[program:x]` 节的设置

配置文件中必须有一个或多个 `program` 节，只有这样 supervisord 才能知道它应该开启和控制哪些程序。它头部的值是一个复合值。它以单词“program”开头，然后是一个英文的冒号，最后是程序的名字。形如的 `[program:foo]` 头部值表示的是一个名为“foo”的程序。这个名字在管理由此配置文件生成的进程的客户端应用中使用。创建一个没有名字的 `program` 节是错误的。名字中一定不能含有冒号和括号这样的字符。其他值可以通过使用 `%(program_name)s` 字符串表达式来指代程序名。

> 一个 `[program:x]` 节实际上指代的是一个 supervisor 的“同质进程组”（在 3.0 版本中）。组中的成员由配置中的 `numprocs` 和 `process_name` 参数联合定义。默认情况下，如果 numprocs 和 process_name 没有改变其默认值，由 `[program:x]` 表示的组将被命名为 `x`，而且在其中会有一个名为 `x` 的单个的进程。这给老一点的 supervisor 发行版提供一点向上兼容性，因为从前没有把 program 节定义为同质进程组。
>
> 不过比如说，如果你有一个 `[program:foo]` 节，把 `numprocs` 值配置为 3，把 `process_name` 值配置为 `%(program_name)s_%(process_num)02d` 表达式，那么“foo”组将包含三个进程，名字分别为 `foo_00`、`foo_01` 和 `foo_02`。这使得仅使用一个 `[program:x]` 节创建很多相似的进程成为可能。所有的日志文件名，所有的环境字符串，还有程序的命令也都可以包括类似的 Python 字符串表达式，从而给每个进程传递略微不同的参数。

### `[program:x]` 节的值

#### command

此程序启动时将会执行的命令。命令可以是绝对的（比如 `/path/to/programname`）或相对的（比如 `programname`）。如果使用相对命令，将会在 supervisord 的 `$PATH` 环境变量中寻找可执行文件。程序可以接受参数，比如 `/path/to/program foo bar`。命令行可以把带有空格的一组参数使用双引号传递给程序，比如 `/path/to/program/name -p "foo bar"`。注意 `command` 的值可以包含 Python 字符串表达式，比如 `/path/to/programname --port=80%(process_num)02d` 在运行时会展开为 `/path/to/programname --port=8000`。字符串表达式可以是包含键 `group_name`、`host_node_name`、`program_name`、`process_num`、`numprocs`、`here`（supervisord 配置文件的路径）的字典和所有以 `ENV_` 开头的 supervisord 的环境变量。被控制的程序本身不能是守护进程，因为 supervisord 认为它是其子进程的守护进程（参见[非守护子进程](subprocess.md#非守护子进程)）。

> 如果命令看起来像是配置文件注释，它将会被截断，比如 `command=bash -c 'foo ; bar'` 会被截断成 `command=bash -c 'foo`。引号也无法阻止这个行为，因为配置文件加载器不会像 shell 那样解析命令。

- 默认值：无默认值
- 是否必填：是
- 最早引入版本：3.0
- 变更历史：4.2.0 版本。增加对 `numprocs` 展开的支持。

#### process_name

用来构成这个进程的 supervisor 进程名的 Python 字符串表达式。如果你不修改 `numprocs` 那么一般是不用设置这一项的。字符串表达式可以是包含键 `group_name`、`host_node_name`、`program_name`、`process_num`、`numprocs`、`here`（supervisord 配置文件的路径）的字典。

- 默认值：`%(program_name)s`
- 是否必填：否
- 最早引入版本：3.0

#### numprocs

Supervisor 将会启动和 numprocs 指明的数目一样多的程序实例。注意如果numprocs > 1，那么 `process_name` 表达式必须包含 `%(process_num)s`（或者其他任何有效的包含 `process_num` 的 Python 字符串表达式）。

- 默认值：1
- 是否必填：否
- 最早引入版本：3.0

#### numprocs_start

用来计算 `numprocs` 从哪个数字开始的一个整数偏移量。

- 默认值：0
- 是否必填：否
- 最早引入版本：3.0

#### priority

程序在启动和终止顺序上的相对优先级。较低的优先级意味着在各种客户端中使用聚合命令（比如：“start all”/“stop all”）启动时会先启动后。

> The relative priority of the program in the start and shutdown ordering. Lower priorities indicate programs that start first and shut down last at startup and when aggregate commands are used in various clients (e.g. “start all”/”stop all”). Higher priorities indicate programs that start last and shut down first.
>
> *Default*: 999
>
> *Required*: No.
>
> *Introduced*: 3.0

```
autostart
```

> If true, this program will start automatically when supervisord is started.
>
> *Default*: true
>
> *Required*: No.
>
> *Introduced*: 3.0

```
startsecs
```

> The total number of seconds which the program needs to stay running after a startup to consider the start successful (moving the process from the `STARTING` state to the `RUNNING` state). Set to `0` to indicate that the program needn’t stay running for any particular amount of time.
>
> Note
>
> Even if a process exits with an “expected” exit code (see `exitcodes`), the start will still be considered a failure if the process exits quicker than `startsecs`.
>
> *Default*: 1
>
> *Required*: No.
>
> *Introduced*: 3.0

```
startretries
```

> The number of serial failure attempts that **supervisord** will allow when attempting to start the program before giving up and putting the process into an `FATAL` state. See [*Process States*](http://supervisord.org/subprocess.html#process-states) for explanation of the `FATAL` state.
>
> *Default*: 3
>
> *Required*: No.
>
> *Introduced*: 3.0

```
autorestart
```

> Specifies if **supervisord** should automatically restart a process if it exits when it is in the `RUNNING` state. May be one of `false`, `unexpected`, or `true`. If `false`, the process will not be autorestarted. If `unexpected`, the process will be restarted when the program exits with an exit code that is not one of the exit codes associated with this process’ configuration (see `exitcodes`). If `true`, the process will be unconditionally restarted when it exits, without regard to its exit code.
>
> Note
>
> `autorestart` controls whether **supervisord** will autorestart a program if it exits after it has successfully started up (the process is in the `RUNNING` state).
>
> **supervisord** has a different restart mechanism for when the process is starting up (the process is in the `STARTING` state). Retries during process startup are controlled by `startsecs` and `startretries`.
>
> *Default*: unexpected
>
> *Required*: No.
>
> *Introduced*: 3.0

```
exitcodes
```

> The list of “expected” exit codes for this program used with `autorestart`. If the `autorestart` parameter is set to `unexpected`, and the process exits in any other way than as a result of a supervisor stop request, **supervisord** will restart the process if it exits with an exit code that is not defined in this list.
>
> *Default*: 0
>
> *Required*: No.
>
> *Introduced*: 3.0
>
> Note
>
> In Supervisor versions prior to 4.0, the default was `0,2`. In Supervisor 4.0, the default was changed to `0`.

```
stopsignal
```

> The signal used to kill the program when a stop is requested. This can be any of TERM, HUP, INT, QUIT, KILL, USR1, or USR2.
>
> *Default*: TERM
>
> *Required*: No.
>
> *Introduced*: 3.0

```
stopwaitsecs
```

> The number of seconds to wait for the OS to return a SIGCHLD to **supervisord** after the program has been sent a stopsignal. If this number of seconds elapses before **supervisord** receives a SIGCHLD from the process, **supervisord** will attempt to kill it with a final SIGKILL.
>
> *Default*: 10
>
> *Required*: No.
>
> *Introduced*: 3.0

```
stopasgroup
```

> If true, the flag causes supervisor to send the stop signal to the whole process group and implies `killasgroup` is true. This is useful for programs, such as Flask in debug mode, that do not propagate stop signals to their children, leaving them orphaned.
>
> *Default*: false
>
> *Required*: No.
>
> *Introduced*: 3.0b1

```
killasgroup
```

> If true, when resorting to send SIGKILL to the program to terminate it send it to its whole process group instead, taking care of its children as well, useful e.g with Python programs using `multiprocessing`.
>
> *Default*: false
>
> *Required*: No.
>
> *Introduced*: 3.0a11

```
user
```

> Instruct **supervisord** to use this UNIX user account as the account which runs the program. The user can only be switched if **supervisord** is run as the root user. If **supervisord** can’t switch to the specified user, the program will not be started.
>
> Note
>
> The user will be changed using `setuid` only. This does not start a login shell and does not change environment variables like `USER` or `HOME`. See [*Subprocess Environment*](http://supervisord.org/subprocess.html#subprocess-environment) for details.
>
> *Default*: Do not switch users
>
> *Required*: No.
>
> *Introduced*: 3.0

```
redirect_stderr
```

> If true, cause the process’ stderr output to be sent back to **supervisord** on its stdout file descriptor (in UNIX shell terms, this is the equivalent of executing `/the/program 2>&1`).
>
> Note
>
> Do not set `redirect_stderr=true` in an `[eventlistener:x]` section. Eventlisteners use `stdout` and `stdin` to communicate with `supervisord`. If `stderr` is redirected, output from `stderr` will interfere with the eventlistener protocol.
>
> *Default*: false
>
> *Required*: No.
>
> *Introduced*: 3.0, replaces 2.0’s `log_stdout` and `log_stderr`

```
stdout_logfile
```

> Put process stdout output in this file (and if redirect_stderr is true, also place stderr output in this file). If `stdout_logfile` is unset or set to `AUTO`, supervisor will automatically choose a file location. If this is set to `NONE`, supervisord will create no log file. `AUTO` log files and their backups will be deleted when **supervisord** restarts. The `stdout_logfile` value can contain Python string expressions that will evaluated against a dictionary that contains the keys `group_name`, `host_node_name`, `process_num`, `program_name`, and `here` (the directory of the supervisord config file).
>
> Note
>
> It is not possible for two processes to share a single log file (`stdout_logfile`) when rotation (`stdout_logfile_maxbytes`) is enabled. This will result in the file being corrupted.
>
> Note
>
> If `stdout_logfile` is set to a special file like `/dev/stdout` that is not seekable, log rotation must be disabled by setting `stdout_logfile_maxbytes = 0`.
>
> *Default*: `AUTO`
>
> *Required*: No.
>
> *Introduced*: 3.0, replaces 2.0’s `logfile`

```
stdout_logfile_maxbytes
```

> The maximum number of bytes that may be consumed by `stdout_logfile` before it is rotated (suffix multipliers like “KB”, “MB”, and “GB” can be used in the value). Set this value to 0 to indicate an unlimited log size.
>
> *Default*: 50MB
>
> *Required*: No.
>
> *Introduced*: 3.0, replaces 2.0’s `logfile_maxbytes`

```
stdout_logfile_backups
```

> The number of `stdout_logfile` backups to keep around resulting from process stdout log file rotation. If set to 0, no backups will be kept.
>
> *Default*: 10
>
> *Required*: No.
>
> *Introduced*: 3.0, replaces 2.0’s `logfile_backups`

```
stdout_capture_maxbytes
```

> Max number of bytes written to capture FIFO when process is in “stdout capture mode” (see [*Capture Mode*](http://supervisord.org/logging.html#capture-mode)). Should be an integer (suffix multipliers like “KB”, “MB” and “GB” can used in the value). If this value is 0, process capture mode will be off.
>
> *Default*: 0
>
> *Required*: No.
>
> *Introduced*: 3.0

```
stdout_events_enabled
```

> If true, PROCESS_LOG_STDOUT events will be emitted when the process writes to its stdout file descriptor. The events will only be emitted if the file descriptor is not in capture mode at the time the data is received (see [*Capture Mode*](http://supervisord.org/logging.html#capture-mode)).
>
> *Default*: 0
>
> *Required*: No.
>
> *Introduced*: 3.0a7

```
stdout_syslog
```

> If true, stdout will be directed to syslog along with the process name.
>
> *Default*: False
>
> *Required*: No.
>
> *Introduced*: 4.0.0

```
stderr_logfile
```

> Put process stderr output in this file unless `redirect_stderr` is true. Accepts the same value types as `stdout_logfile` and may contain the same Python string expressions.
>
> Note
>
> It is not possible for two processes to share a single log file (`stderr_logfile`) when rotation (`stderr_logfile_maxbytes`) is enabled. This will result in the file being corrupted.
>
> Note
>
> If `stderr_logfile` is set to a special file like `/dev/stderr` that is not seekable, log rotation must be disabled by setting `stderr_logfile_maxbytes = 0`.
>
> *Default*: `AUTO`
>
> *Required*: No.
>
> *Introduced*: 3.0

```
stderr_logfile_maxbytes
```

> The maximum number of bytes before logfile rotation for `stderr_logfile`. Accepts the same value types as `stdout_logfile_maxbytes`.
>
> *Default*: 50MB
>
> *Required*: No.
>
> *Introduced*: 3.0

```
stderr_logfile_backups
```

> The number of backups to keep around resulting from process stderr log file rotation. If set to 0, no backups will be kept.
>
> *Default*: 10
>
> *Required*: No.
>
> *Introduced*: 3.0

```
stderr_capture_maxbytes
```

> Max number of bytes written to capture FIFO when process is in “stderr capture mode” (see [*Capture Mode*](http://supervisord.org/logging.html#capture-mode)). Should be an integer (suffix multipliers like “KB”, “MB” and “GB” can used in the value). If this value is 0, process capture mode will be off.
>
> *Default*: 0
>
> *Required*: No.
>
> *Introduced*: 3.0

```
stderr_events_enabled
```

> If true, PROCESS_LOG_STDERR events will be emitted when the process writes to its stderr file descriptor. The events will only be emitted if the file descriptor is not in capture mode at the time the data is received (see [*Capture Mode*](http://supervisord.org/logging.html#capture-mode)).
>
> *Default*: false
>
> *Required*: No.
>
> *Introduced*: 3.0a7

```
stderr_syslog
```

> If true, stderr will be directed to syslog along with the process name.
>
> *Default*: False
>
> *Required*: No.
>
> *Introduced*: 4.0.0

```
environment
```

> A list of key/value pairs in the form `KEY="val",KEY2="val2"` that will be placed in the child process’ environment. The environment string may contain Python string expressions that will be evaluated against a dictionary containing `group_name`, `host_node_name`, `process_num`, `program_name`, and `here` (the directory of the supervisord config file). Values containing non-alphanumeric characters should be quoted (e.g. `KEY="val:123",KEY2="val,456"`). Otherwise, quoting the values is optional but recommended. **Note** that the subprocess will inherit the environment variables of the shell used to start “supervisord” except for the ones overridden here. See [*Subprocess Environment*](http://supervisord.org/subprocess.html#subprocess-environment).
>
> *Default*: No extra environment
>
> *Required*: No.
>
> *Introduced*: 3.0

```
directory
```

> A file path representing a directory to which **supervisord** should temporarily chdir before exec’ing the child.
>
> *Default*: No chdir (inherit supervisor’s)
>
> *Required*: No.
>
> *Introduced*: 3.0

```
umask
```

> An octal number (e.g. 002, 022) representing the umask of the process.
>
> *Default*: No special umask (inherit supervisor’s)
>
> *Required*: No.
>
> *Introduced*: 3.0

```
serverurl
```

> The URL passed in the environment to the subprocess process as `SUPERVISOR_SERVER_URL` (see `supervisor.childutils`) to allow the subprocess to easily communicate with the internal HTTP server. If provided, it should have the same syntax and structure as the `[supervisorctl]` section option of the same name. If this is set to AUTO, or is unset, supervisor will automatically construct a server URL, giving preference to a server that listens on UNIX domain sockets over one that listens on an internet socket.
>
> *Default*: AUTO
>
> *Required*: No.
>
> *Introduced*: 3.0

### `[program:x]` Section Example

```
[program:cat]
command=/bin/cat
process_name=%(program_name)s
numprocs=1
directory=/tmp
umask=022
priority=999
autostart=true
autorestart=unexpected
startsecs=10
startretries=3
exitcodes=0
stopsignal=TERM
stopwaitsecs=10
stopasgroup=false
killasgroup=false
user=chrism
redirect_stderr=false
stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_capture_maxbytes=1MB
stdout_events_enabled=false
stderr_logfile=/a/path
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_capture_maxbytes=1MB
stderr_events_enabled=false
environment=A="1",B="2"
serverurl=AUTO
```

## `[include]` Section Settings

The `supervisord.conf` file may contain a section named `[include]`. If the configuration file contains an `[include]` section, it must contain a single key named “files”. The values in this key specify other configuration files to be included within the configuration.

Note

The `[include]` section is processed only by `supervisord`. It is ignored by `supervisorctl`.

### `[include]` Section Values

```
files
```

> A space-separated sequence of file globs. Each file glob may be absolute or relative. If the file glob is relative, it is considered relative to the location of the configuration file which includes it. A “glob” is a file pattern which matches a specified pattern according to the rules used by the Unix shell. No tilde expansion is done, but `*`, `?`, and character ranges expressed with `[]` will be correctly matched. The string expression is evaluated against a dictionary that includes `host_node_name` and `here` (the directory of the supervisord config file). Recursive includes from included files are not supported.
>
> *Default*: No default (required)
>
> *Required*: Yes.
>
> *Introduced*: 3.0
>
> *Changed*: 3.3.0. Added support for the `host_node_name` expansion.

### `[include]` Section Example

```
[include]
files = /an/absolute/filename.conf /an/absolute/*.conf foo.conf config??.conf
```

## `[group:x]` Section Settings

It is often useful to group “homogeneous” process groups (aka “programs”) together into a “heterogeneous” process group so they can be controlled as a unit from Supervisor’s various controller interfaces.

To place programs into a group so you can treat them as a unit, define a `[group:x]` section in your configuration file. The group header value is a composite. It is the word “group”, followed directly by a colon, then the group name. A header value of `[group:foo]` describes a group with the name of “foo”. The name is used within client applications that control the processes that are created as a result of this configuration. It is an error to create a `group` section that does not have a name. The name must not include a colon character or a bracket character.

For a `[group:x]`, there must be one or more `[program:x]` sections elsewhere in your configuration file, and the group must refer to them by name in the `programs` value.

If “homogeneous” process groups (represented by program sections) are placed into a “heterogeneous” group via `[group:x]` section’s `programs` line, the homogeneous groups that are implied by the program section will not exist at runtime in supervisor. Instead, all processes belonging to each of the homogeneous groups will be placed into the heterogeneous group. For example, given the following group configuration:

```
[group:foo]
programs=bar,baz
priority=999
```

Given the above, at supervisord startup, the `bar` and `baz` homogeneous groups will not exist, and the processes that would have been under them will now be moved into the `foo` group.

### `[group:x]` Section Values

```
programs
```

> A comma-separated list of program names. The programs which are listed become members of the group.
>
> *Default*: No default (required)
>
> *Required*: Yes.
>
> *Introduced*: 3.0

```
priority
```

> A priority number analogous to a `[program:x]` priority value assigned to the group.
>
> *Default*: 999
>
> *Required*: No.
>
> *Introduced*: 3.0

### `[group:x]` Section Example

```
[group:foo]
programs=bar,baz
priority=999
```

## `[fcgi-program:x]` Section Settings

Supervisor can manage groups of [FastCGI](http://www.fastcgi.com/) processes that all listen on the same socket. Until now, deployment flexibility for FastCGI was limited. To get full process management, you could use mod_fastcgi under Apache but then you were stuck with Apache’s inefficient concurrency model of one process or thread per connection. In addition to requiring more CPU and memory resources, the process/thread per connection model can be quickly saturated by a slow resource, preventing other resources from being served. In order to take advantage of newer event-driven web servers such as lighttpd or nginx which don’t include a built-in process manager, you had to use scripts like cgi-fcgi or spawn-fcgi. These can be used in conjunction with a process manager such as supervisord or daemontools but require each FastCGI child process to bind to its own socket. The disadvantages of this are: unnecessarily complicated web server configuration, ungraceful restarts, and reduced fault tolerance. With fewer sockets to configure, web server configurations are much smaller if groups of FastCGI processes can share sockets. Shared sockets allow for graceful restarts because the socket remains bound by the parent process while any of the child processes are being restarted. Finally, shared sockets are more fault tolerant because if a given process fails, other processes can continue to serve inbound connections.

With integrated FastCGI spawning support, Supervisor gives you the best of both worlds. You get full-featured process management with groups of FastCGI processes sharing sockets without being tied to a particular web server. It’s a clean separation of concerns, allowing the web server and the process manager to each do what they do best.

Note

The socket manager in Supervisor was originally developed to support FastCGI processes but it is not limited to FastCGI. Other protocols may be used as well with no special configuration. Any program that can access an open socket from a file descriptor (e.g. with [socket.fromfd](http://docs.python.org/library/socket.html#socket.fromfd) in Python) can use the socket manager. Supervisor will automatically create the socket, bind, and listen before forking the first child in a group. The socket will be passed to each child on file descriptor number `0` (zero). When the last child in the group exits, Supervisor will close the socket.

Note

Prior to Supervisor 3.4.0, FastCGI programs (`[fcgi-program:x]`) could not be referenced in groups (`[group:x]`).

All the options available to `[program:x]` sections are also respected by `fcgi-program` sections.

### `[fcgi-program:x]` Section Values

`[fcgi-program:x]` sections have a few keys which `[program:x]` sections do not have.

```
socket
```

> The FastCGI socket for this program, either TCP or UNIX domain socket. For TCP sockets, use this format: `tcp://localhost:9002`. For UNIX domain sockets, use `unix:///absolute/path/to/file.sock`. String expressions are evaluated against a dictionary containing the keys “program_name” and “here” (the directory of the supervisord config file).
>
> *Default*: No default.
>
> *Required*: Yes.
>
> *Introduced*: 3.0

```
socket_backlog
```

> Sets socket listen(2) backlog.
>
> *Default*: socket.SOMAXCONN
>
> *Required*: No.
>
> *Introduced*: 3.4.0

```
socket_owner
```

> For UNIX domain sockets, this parameter can be used to specify the user and group for the FastCGI socket. May be a UNIX username (e.g. chrism) or a UNIX username and group separated by a colon (e.g. chrism:wheel).
>
> *Default*: Uses the user and group set for the fcgi-program
>
> *Required*: No.
>
> *Introduced*: 3.0

```
socket_mode
```

> For UNIX domain sockets, this parameter can be used to specify the permission mode.
>
> *Default*: 0700
>
> *Required*: No.
>
> *Introduced*: 3.0

Consult [*[program:x\] Section Settings*](http://supervisord.org/configuration.html#programx-section) for other allowable keys, delta the above constraints and additions.

### `[fcgi-program:x]` Section Example

```
[fcgi-program:fcgiprogramname]
command=/usr/bin/example.fcgi
socket=unix:///var/run/supervisor/%(program_name)s.sock
socket_owner=chrism
socket_mode=0700
process_name=%(program_name)s_%(process_num)02d
numprocs=5
directory=/tmp
umask=022
priority=999
autostart=true
autorestart=unexpected
startsecs=1
startretries=3
exitcodes=0
stopsignal=QUIT
stopasgroup=false
killasgroup=false
stopwaitsecs=10
user=chrism
redirect_stderr=true
stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_events_enabled=false
stderr_logfile=/a/path
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_events_enabled=false
environment=A="1",B="2"
serverurl=AUTO
```

## `[eventlistener:x]` Section Settings

Supervisor allows specialized homogeneous process groups (“event listener pools”) to be defined within the configuration file. These pools contain processes that are meant to receive and respond to event notifications from supervisor’s event system. See [*Events*](http://supervisord.org/events.html#events) for an explanation of how events work and how to implement programs that can be declared as event listeners.

Note that all the options available to `[program:x]` sections are respected by eventlistener sections *except* for `stdout_capture_maxbytes`. Eventlisteners cannot emit process communication events on `stdout`, but can emit on `stderr` (see [*Capture Mode*](http://supervisord.org/logging.html#capture-mode)).

### `[eventlistener:x]` Section Values

`[eventlistener:x]` sections have a few keys which `[program:x]` sections do not have.

```
buffer_size
```

> The event listener pool’s event queue buffer size. When a listener pool’s event buffer is overflowed (as can happen when an event listener pool cannot keep up with all of the events sent to it), the oldest event in the buffer is discarded.

```
events
```

> A comma-separated list of event type names that this listener is “interested” in receiving notifications for (see [*Event Types*](http://supervisord.org/events.html#event-types) for a list of valid event type names).

```
result_handler
```

> A [pkg_resources entry point string](http://peak.telecommunity.com/DevCenter/PkgResources) that resolves to a Python callable. The default value is `supervisor.dispatchers:default_handler`. Specifying an alternate result handler is a very uncommon thing to need to do, and as a result, how to create one is not documented.

Consult [*[program:x\] Section Settings*](http://supervisord.org/configuration.html#programx-section) for other allowable keys, delta the above constraints and additions.

### `[eventlistener:x]` Section Example

```
[eventlistener:theeventlistenername]
command=/bin/eventlistener
process_name=%(program_name)s_%(process_num)02d
numprocs=5
events=PROCESS_STATE
buffer_size=10
directory=/tmp
umask=022
priority=-1
autostart=true
autorestart=unexpected
startsecs=1
startretries=3
exitcodes=0
stopsignal=QUIT
stopwaitsecs=10
stopasgroup=false
killasgroup=false
user=chrism
redirect_stderr=false
stdout_logfile=/a/path
stdout_logfile_maxbytes=1MB
stdout_logfile_backups=10
stdout_events_enabled=false
stderr_logfile=/a/path
stderr_logfile_maxbytes=1MB
stderr_logfile_backups=10
stderr_events_enabled=false
environment=A="1",B="2"
serverurl=AUTO
```

## `[rpcinterface:x]` Section Settings

Adding `rpcinterface:x` settings in the configuration file is only useful for people who wish to extend supervisor with additional custom behavior.

In the sample config file, there is a section which is named `[rpcinterface:supervisor]`. By default it looks like the following.

```
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface
```

The `[rpcinterface:supervisor]` section *must* remain in the configuration for the standard setup of supervisor to work properly. If you don’t want supervisor to do anything it doesn’t already do out of the box, this is all you need to know about this type of section.

However, if you wish to add rpc interface namespaces in order to customize supervisor, you may add additional `[rpcinterface:foo]` sections, where “foo” represents the namespace of the interface (from the web root), and the value named by `supervisor.rpcinterface_factory` is a factory callable which should have a function signature that accepts a single positional argument `supervisord` and as many keyword arguments as required to perform configuration. Any extra key/value pairs defined within the `[rpcinterface:x]` section will be passed as keyword arguments to the factory.

Here’s an example of a factory function, created in the `__init__.py` file of the Python package `my.package`.

```
from my.package.rpcinterface import AnotherRPCInterface

def make_another_rpcinterface(supervisord, **config):
    retries = int(config.get('retries', 0))
    another_rpc_interface = AnotherRPCInterface(supervisord, retries)
    return another_rpc_interface
```

And a section in the config file meant to configure it.

```
[rpcinterface:another]
supervisor.rpcinterface_factory = my.package:make_another_rpcinterface
retries = 1
```

### `[rpcinterface:x]` Section Values

```
supervisor.rpcinterface_factory
```

> `pkg_resources` “entry point” dotted name to your RPC interface’s factory function.
>
> *Default*: N/A
>
> *Required*: No.
>
> *Introduced*: 3.0

### `[rpcinterface:x]` Section Example

```
[rpcinterface:another]
supervisor.rpcinterface_factory = my.package:make_another_rpcinterface
retries = 1
```
