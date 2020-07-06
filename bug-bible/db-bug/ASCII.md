# ASCII

## '\0' appeared in the statement

### 错误1.1

报错信息：

```mysql
ERROR: ASCII '\0' appeared in the statement, but this is not allowed unless option --binary-mode is enabled and mysql is run in non-interactive mode. Set --binary-mode to 1 if ASCII '\0' is expected. Query: 'ÿþ-'.
```

错误代码：

```shell
# Windows的Powershell终端：
mysqldump crm > crm.sql
# Linux终端的MySQL
source /tmp/py/CRM/crm.sql
```

错误原因：

使用 Powershell 到处数据库会出现编码问题。

解决方法：

使用 cmd 导出数据库。