# InternalError

## django.db.utils.InternalError: (1366, "Incorrect string value: '\\xE7\\x94\\xA8\\xE6\\x88\\xB7...' for column 'name' at row 1")

### 错误1.1

报错信息：

```python
Traceback (most recent call last):
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/utils.py", line 84, in _execute
    return self.cursor.execute(sql, params)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/mysql/base.py", line 71, in execute
    return self.cursor.execute(query, args)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/cursors.py", line 170, in execute
    result = self._query(query)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/cursors.py", line 328, in _query
    conn.query(q)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 517, in query
    self._affected_rows = self._read_query_result(unbuffered=unbuffered)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 732, in _read_query_result
    result.read()
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 1075, in read
    first_packet = self.connection._read_packet()
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 684, in _read_packet
    packet.check_error()
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/protocol.py", line 220, in check_error
    err.raise_mysql_exception(self._data)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/err.py", line 109, in raise_mysql_exception
    raise errorclass(errno, errval)
pymysql.err.InternalError: (1366, "Incorrect string value: '\\xE7\\x94\\xA8\\xE6\\x88\\xB7...' for column 'name' at row 1")

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "manage.py", line 21, in <module>
    main()
  File "manage.py", line 17, in main
    execute_from_command_line(sys.argv)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/core/management/__init__.py", line 381, in execute_from_command_line
    utility.execute()
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/core/management/__init__.py", line 375, in execute
    self.fetch_command(subcommand).run_from_argv(self.argv)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/core/management/base.py", line 323, in run_from_argv
    self.execute(*args, **cmd_options)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/core/management/base.py", line 364, in execute
    output = self.handle(*args, **options)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/core/management/base.py", line 83, in wrapped
    res = handle_func(*args, **kwargs)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/core/management/commands/migrate.py", line 257, in handle
    self.verbosity, self.interactive, connection.alias, apps=post_migrate_apps, plan=plan,
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/core/management/sql.py", line 51, in emit_post_migrate_signal
    **kwargs
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/dispatch/dispatcher.py", line 175, in send
    for receiver in self._live_receivers(sender)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/dispatch/dispatcher.py", line 175, in <listcomp>
    for receiver in self._live_receivers(sender)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/contrib/auth/management/__init__.py", line 83, in create_permissions
    Permission.objects.using(using).bulk_create(perms)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/models/query.py", line 474, in bulk_create
    ids = self._batched_insert(objs_without_pk, fields, batch_size, ignore_conflicts=ignore_conflicts)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/models/query.py", line 1211, in _batched_insert
    self._insert(item, fields=fields, using=self.db, ignore_conflicts=ignore_conflicts)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/models/query.py", line 1186, in _insert
    return query.get_compiler(using=using).execute_sql(return_id)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/models/sql/compiler.py", line 1332, in execute_sql
    cursor.execute(sql, params)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/utils.py", line 99, in execute
    return super().execute(sql, params)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/utils.py", line 67, in execute
    return self._execute_with_wrappers(sql, params, many=False, executor=self._execute)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/utils.py", line 76, in _execute_with_wrappers
    return executor(sql, params, many, context)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/utils.py", line 84, in _execute
    return self.cursor.execute(sql, params)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/utils.py", line 89, in __exit__
    raise dj_exc_value.with_traceback(traceback) from exc_value
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/utils.py", line 84, in _execute
    return self.cursor.execute(sql, params)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/django/db/backends/mysql/base.py", line 71, in execute
    return self.cursor.execute(query, args)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/cursors.py", line 170, in execute
    result = self._query(query)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/cursors.py", line 328, in _query
    conn.query(q)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 517, in query
    self._affected_rows = self._read_query_result(unbuffered=unbuffered)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 732, in _read_query_result
    result.read()
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 1075, in read
    first_packet = self.connection._read_packet()
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/connections.py", line 684, in _read_packet
    packet.check_error()
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/protocol.py", line 220, in check_error
    err.raise_mysql_exception(self._data)
  File "/home/moluo/.virtualenvs/renran/lib/python3.6/site-packages/pymysql/err.py", line 109, in raise_mysql_exception
    raise errorclass(errno, errval)
django.db.utils.InternalError: (1366, "Incorrect string value: '\\xE7\\x94\\xA8\\xE6\\x88\\xB7...' for column 'name' at row 1")
```

错误代码：

```python
非代码原因报错
```

错误原因：

数据库未指定编码，进行数据库迁移时，因出现中文而报错。

解决方法：

指定数据库编码。