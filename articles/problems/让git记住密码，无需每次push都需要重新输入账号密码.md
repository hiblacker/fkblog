如果我们 `git clone` 的下载代码的时候是连接的 `https://` 而不是 `git@git (ssh)` 的形式，当我们操作 `git pull/push` 到远程的时候，总是提示我们输入账号和密码才能操作成功，频繁的输入账号和密码会很麻烦。

解决办法：
```
git config --global credential.helper store
```
提交的时候会让你再次输入密码，这次输入之后是客户端记录密码，以后再提交就免密了。