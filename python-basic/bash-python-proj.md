## 交互模式下启动 Python 项目

命令行运行 Python 文件是每一个 Python 初学者第一天都会了解到的基本知识。无外乎在命令行中，输入类似于下面的命令，即可执行一个写好的 Python 文件了：

```bash
python ./hello.py
```

这样就会运行当前目录下的 hello.py 这个文件。

我这里要讨论的当然不是这个问题。我这里要讨论的不是启动一个独立的 Python 文件，而是一个由多个相互关联调用的 Python 文件组成的 Python 项目的启动。

要启动一个 Python 项目，可以在根目录下，通过 -u -m 参数，在当前目录下启动其他目录下的 Python 文件，类似于：

```bash
python -u -m web.http_server
```

注意，这样启动 Python 项目时不要加 .py 后缀的，文件之间的路径以点分隔。

除了 -u 和 -m 之外，使用 python -help 还可以看到很多 Python 交互模式下启动项目可能用到的参数，我将他们复制过来，贴到下面：

```bash
usage: python3 [option] ... [-c cmd | -m mod | file | -] [arg] ...
Options and arguments (and corresponding environment variables):
-b     : issue warnings about str(bytes_instance), str(bytearray_instance)
         and comparing bytes/bytearray with str. (-bb: issue errors)
-B     : don't write .pyc files on import; also PYTHONDONTWRITEBYTECODE=x
-c cmd : program passed in as string (terminates option list)
-d     : debug output from parser; also PYTHONDEBUG=x
-E     : ignore PYTHON* environment variables (such as PYTHONPATH)
-h     : print this help message and exit (also --help)
-i     : inspect interactively after running script; forces a prompt even
         if stdin does not appear to be a terminal; also PYTHONINSPECT=x
-I     : isolate Python from the user's environment (implies -E and -s)
-m mod : run library module as a script (terminates option list)
-O     : remove assert and __debug__-dependent statements; add .opt-1 before
         .pyc extension; also PYTHONOPTIMIZE=x
-OO    : do -O changes and also discard docstrings; add .opt-2 before
         .pyc extension
-q     : don't print version and copyright messages on interactive startup
-s     : don't add user site directory to sys.path; also PYTHONNOUSERSITE
-S     : don't imply 'import site' on initialization
-u     : force the stdout and stderr streams to be unbuffered;
         this option has no effect on stdin; also PYTHONUNBUFFERED=x
-v     : verbose (trace import statements); also PYTHONVERBOSE=x
         can be supplied multiple times to increase verbosity
-V     : print the Python version number and exit (also --version)
         when given twice, print more information about the build
-W arg : warning control; arg is action:message:category:module:lineno
         also PYTHONWARNINGS=arg
-x     : skip first line of source, allowing use of non-Unix forms of #!cmd
-X opt : set implementation-specific option
--check-hash-based-pycs always|default|never:
    control how Python invalidates hash-based .pyc files
file   : program read from script file
-      : program read from stdin (default; interactive mode if a tty)
arg ...: arguments passed to program in sys.argv[1:]
```

