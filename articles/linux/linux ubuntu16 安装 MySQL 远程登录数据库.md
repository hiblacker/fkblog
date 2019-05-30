## 下载
```
sudo apt-get install mysql-server
```
下载好后输入数据库密码

## 登录 
```
mysql -u root -p
```
输入刚才的密码

## 建立数据库独立用户

root用户拥有数据库的所有操作权限，因此不能轻易给别人用。在一个MySQL实例中，我们可以创建多个数据库，而这些数据库可能会分属不同的项目，那么每个数据库的操作角色也就不一样。对此，我们可以针对不同的数据库，去指定用户进行访问。

### 创建数据库
命令后要接`;`
```
mysql> create database db_blog;
```
查看是否创建成功：
```
mysql> show database;
```

### 授权
将这个数据库授予一个叫 myblog 的用户使用 密码为 myblog 
```
mysql> grant all privileges on db_blog.* to myblog@localhost identified by 'myblog';
```

### 开放远程登录权限

#### 1. 首先修改MySQL的配置文件，允许监听远程登录。
```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```
找到bind-address所在行,将 bind-address值修改为本机IP即可。

#### 2. 授予用户远程登录权限。

```
mysql> GRANT ALL PRIVILEGES ON db_blog.* TO blog@"%" IDENTIFIED BY "blog";
```

### 开启服务
#### 启动
```
sudo service mysql start
```

#### 停止

```
sudo service mysql stop
```

#### 重启

```
service mysql restart
```