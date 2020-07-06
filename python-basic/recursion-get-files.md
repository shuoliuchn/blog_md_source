## 递归获取目录中所有文件

获取目录中所有文件，可以进行判断筛选，全凭自己定制。

```python
FILE_IGNORE = []    # 要忽略的文件或文件夹名
def get_files(project_path, path_list: list):
    if os.path.isdir(project_path):
        file_list = os.listdir(project_path)
        for file in file_list:
            if file in FILE_IGNORE:
                continue
            file_path = os.path.join(project_path, file)
            if os.path.isfile(file_path):
                path_list.append(file_path)
            else:
                get_files(file_path, path_list)
    else:
        path_list.append(project_path)

# 使用方法：
path_list = []
get_files('C:\Users\Sure\test', path_list)
print(path_list)
```

