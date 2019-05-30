firekylin 搭建的博客，PM2 启动的应用，总是无法重启nginx，导致配置的http，ssl 一直无法生效。
一直报错 80 端口被占用
```
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
```
网上搜索得到的答案基本都是，`netstat -ntpl` 查看 80 端口的 pid 然后 `kill` 掉。

但我 `kill` 掉后，又会重新启动。所以并没有解决我的问题。

经过一番研究我发现，原来是 `pm2` 会‘守护’进程，关闭后会自动重启，所以需要先停止：

``` bash
# 假设任务id为0
pm2 stop 0
```

查看端口占用情况

```
netstat -ntpl
```

看到没有80端口被占用了，再重启nginx。

接着重启 nginx ：
```base
nginx -c /etc/nginx/nginx.conf
nginx -t
nginx -s reload
```


