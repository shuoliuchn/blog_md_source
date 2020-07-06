## 解决 Django 自定义用户模型之前就已经数据迁移的 BUG

Django 建议我们对于 AUTH_USER_MODEL 参数的设置一定要在第一次数据库迁移之前就设置好，否则后续使用可能出现未知错误。

所以自定义用户模型以后，执行 `python manage.py migrate` 命令时，系统报错类似如下：

```bash
django.db.migrations.exceptions.InconsistentMigrationHistory: Migration reversion.0001_squashed_0004_auto_20160611_1202 is applied before its dependency users.0001_initial on database 'default'.
```

这是表示有一个叫 reversion 的子应用使用了原来的废弃的 users 模型，但是目前数据库已经设置了默认的子应用为 `users` 的模型了，所以产生了冲突。对于这种冲突，我们需要清除原来的迁移文件和数据库中的所有信息来解决。

解决步骤：
1. 备份数据库，删除关于用户原来的数据表信息和表结构（如果刚开始开发，则直接清除库中所有数据表即可）

   ```mysql
   drop database renran;
   create database renran charset=utf8mb4;    -- 一定要指定编码！！！
   ```

2. 删除子应用 users 中 migrations 目录下除了`__init__.py` 以外的所有迁移文件

3. 删除在 `django.contrib.admin` 和 `django.contrib.auth` 模块里面的 migrations 迁移文件，除了 `__init__.py`

4. 删除在 xadmin 和 reversion 模块中的 migrations 的迁移文件，除了 `__init__.py`

5. 执行数据迁移，把备份数据，除了用户以外的全部恢复执行即可

6. 正常使用 `manage.py createsuperuser` 创建管理员即可

当然，也可以清空数据库，删除虚拟环境，再重新配置虚拟环境，推倒重来。