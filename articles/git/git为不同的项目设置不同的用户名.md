在公司使用 gitlab，同时维护自己的 git 项目时，有时 commit 的名字并不是自己所希望的。可以单独配置如下：
```
git  config  user.name  "xxxxx"
```
来修改当前项目提交代码时用到的用户名。

也可以修改 `.git` 文件下的 `config`:
```
[user]
    name = XXX(自己的名称英文)
    email = XXXX(邮箱)
```

全局修改的命令是：

```
git  config --global  user.name  "xxxxx"
```

同时配置了当前项目和全局的 username 时，优先显示当前项目的配置。
