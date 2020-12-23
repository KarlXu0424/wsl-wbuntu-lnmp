## WSL2 & Ubuntu 安装 LNMP 开发环境

- 更新并升级

```
sudo apt update

sudo apt upgrade
```

- 安装 nginx

`sudo apt install nginx`

验证 nginx 配置 

`sudo nginx -t` 

`sudo service nginx start` 启动服务

本地浏览器访问 127.0.0.1 出现 nginx 欢迎页面表示安装成功

- 安装 mysql

`sudo apt install mysql-server`

安装过程会提示设置 root 用户的密码

`sudo service mysql start` 启动服务

本地工具远程连接 wsl 的 mysql 权限设置

`mysql -u root -p` 输入刚才设置的密码进入 mysql 

如果安装过程没有要求设置 mysql 的 root 用户密码

需要 `su root` 切换到 root 输入 `mysql -u root -p` 直接进入 mysql

```
use mysql;

// 查看已有用户和权限
select User,authentication_string,Host from user;

// 新增一个用户设置密码并赋予权限
GRANT ALL PRIVILEGES ON *.* TO 'YourUsername'@'%' IDENTIFIED BY 'YourPassword';

或者

// 直接修改 root 用户的访问权限
update from user set Host = '%' where User = 'root';

// 刷新权限
flush privileges;
```

- 安装 php

php7.3以上版本需要换源，7.3以下版本系统源中自带，以下为7.3版本安装步骤

```
// 更换源
sudo apt-get install software-properties-common

sudo add-apt-repository ppa:ondrej/php

sudo apt-get update

// 安装php和常用模块
sudo apt-get install -y php7.3

sudo apt-get install php-pear php7.3-fpm php7.3-curl php7.3-gd php7.3-mbstring php7.3-zip php7.3-mysql php7.3-xml
```

安装完成后，执行 `php -v` 查看是否安装成功

修改监听端口并启动服务

```
vim /etc/php/7.3/fpm/pool.d/www.conf

; listen = /run/php/php7.2-fpm.sock
listen = 127.0.0.1:9001

sudo service php7.3-fpm start
```

- 安装 redis 

`sudo apt-get install redis-server`

增加校验密码

`vim /etc/redis/redis.conf` 找到 requirepass foobared 将 foobared 换成 YourPassword

`sudo service redis-server start` 启动 redis 


Ubuntu 设置命令别名

`sudo vim /home/YouUserName/.bashrc` 

```
alias run-nginx='sudo service nginx'

alias run-php73='sudo service php7.3-fpm'

alias run-php70='sudo service php7.0-fpm'

alias run-mysql='sudo service mysql'

alias run-redis='sudo service redis-server'

alias lnmp-start='run-nginx start && run-php73 start && run-php70 start && run-mysql start && run-redis start'

alias lnmp-stop='run-nginx stop && run-php73 stop && run-php70 stop && run-mysql stop && run-redis stop'

alias lnmp-restart='run-nginx restart && run-php73 restart && run-php70 restart && run-mysql restart && run-redis restart'

alias lnmp-status='run-nginx status && run-php73 status && run-php70 status && run-mysql status && run-redis status'
```

