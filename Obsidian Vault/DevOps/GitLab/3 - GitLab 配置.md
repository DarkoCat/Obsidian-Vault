# GitLab 配置



## 调整防火墙规则

查看活动防火墙的当前状态

```shell
sudo ufw status
```

由于GitLab是一个Web应用程序，我们应该允许HTTP访问。
`/etc/services`文件中提供了HTTP和HTTPS的端口映射协议，因此我们可以按名称允许该流量。如果您尚未启用OpenSSH流量，则现在也应该允许该流量

```shell
sudo ufw allow http
sudo ufw allow https
sudo ufw allow OpenSSH
```



## 修改gitlab.rb文件
```shell
vi /docker/gitlab/etc/gitlab/gitlab.rb
```

修改以下信息

```shell
external_url 'http://192.168.10.123:80'`
######
gitlab_rails['time_zone'] = 'Asia/Shanghai'
gitlab_rails['gitlab_email_from'] = 'xxxxxx@163.com'
......
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.163.com"
gitlab_rails['smtp_port'] = 25
gitlab_rails['smtp_user_name'] = "xxxxxx@163.com"
gitlab_rails['smtp_password'] = "111111"
gitlab_rails['smtp_domain'] = "163.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
######
user["git_user_email"] = "xxxxxx@163.com"
```

只要修改配置文件就要reconfigure

```shell
sudo gitlab-ctl reconfigure
```



## 字段说明

 如果使用公有云且配置了域名了，可以直接设置为域名，如下

`external_url 'http://gitlab.redrose2100.com'`

如果没有域名，则直接使用宿主机的ip，如下

`external_url 'http://172.22.27.162'`

同样如果有域名，这里也可以直接使用域名

`gitlab_rails['gitlab_ssh_host'] =  'gitlab.redrosee2100.com'`

同样如果没有域名，则直接使用宿主机的ip地址

`gitlab_rails['gitlab_ssh_host'] = '172.22.27.162'`

端口为启动docker时映射的ssh端口

`gitlab_rails['gitlab_shell_ssh_port'] =10010`

设置时区为东八区，即北京时间

`gitlab_rails['time_zone'] = 'Asia/Shanghai'`

邮箱地址

`letsencrypt['contact_emails'] = 'add@sample.com'`



## 修改页面

修改gitlab的配置文件

```shell
sudo vim /etc/gitlab/gitlab.rb
```

修改如下配置

```shell
gitlab_pages['enable'] = true;     # 开启pages服务
pages_external_url  '域名地址';     # 替换成自己的域名
gitlab_pages['inplace_chroot'] = true;    # 用docker必须开启
pages_nginx['enable'] = true;      # 开启pages服务的vhost，该项开启后将会在  /var/opt/gitlab/nginx/conf  目录下生成独立的名为 gitlab-pages.conf  Nginx配置文件
gitlab_pages['access_control'] = true；   # 开启pages访问控制
```

重新加载配置

```shell
sudo gitlab-ctl reconfigure
```



## 修改端口

GitLab默认端口是80，如果我们想更改成9091端口，则需要修改GitLab配置文件。

```shell
sudo vim /etc/gitlab/gitlab.rb
```

修改如下配置

```shell
nginx['listen_port'] = 9091 // GitLab端口，默认80端口
unicorn['port'] = 9092 // 可不修改，默认监听8080端口
```

重新加载配置

```shell
sudo gitlab-ctl reconfigure
```



## 邮箱配置

安装SMTP发送邮件软件（可选）

```shell
sudo apt-get install -y postfix
```

邮箱配置
```shell
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.163.com"   # 邮箱服务器
gitlab_rails['smtp_port'] = 465    # 邮箱服务对应的端口号
gitlab_rails['smtp_user_name'] = "hitredrose@163.com"   # 发件箱的邮箱地址
gitlab_rails['smtp_password'] = "xxxxxxxxxxx"    # 发件箱对应的授权码，注意不是登录密码，是授权码
gitlab_rails['smtp_domain'] = "163.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
gitlab_rails['gitlab_email_enabled'] = true
gitlab_rails['gitlab_email_from'] = 'hitredrose@163.com'     # 发件箱地址
gitlab_rails['gitlab_email_display_name'] = 'gitlab.redrose2100.com' # 显示名称
gitlab_rails['gitlab_email_reply_to'] = 'noreply@example.com'     # 提示不要回复
```

发送测试邮件

```shell
sudo gitlab-rails console

#进入控制台，然后发送邮件
Notify.test_email('测试邮箱地址','邮件标题','邮件正文').deliver_now
```



## 重启gitlab

```shell
gitlab-ctl restart
```



## 查看状态

```shell
gitlab-ctl status
```

```shell
run: alertmanager: (pid 12758) 66s; run: log: (pid 12379) 219s 
run: gitaly: (pid 12684) 70s; run: log: (pid 11883) 357s 
run: gitlab-monitor: (pid 12721) 69s; run: log: (pid 12309) 240s 
run: gitlab-workhorse: (pid 12708) 70s; run: log: (pid 12226) 264s 
run: grafana: (pid 12781) 65s; run: log: (pid 12538) 113s 
run: logrotate: (pid 12259) 253s; run: log: (pid 12268) 252s 
run: nginx: (pid 12242) 259s; run: log: (pid 12250) 258s 
run: node-exporter: (pid 12715) 69s; run: log: (pid 12296) 244s 
run: postgres-exporter: (pid 12770) 65s; run: log: (pid 12399) 213s 
run: postgresql: (pid 12004) 349s; run: log: (pid 12016) 346s 
run: prometheus: (pid 12732) 69s; run: log: (pid 12351) 225s 
run: redis: (pid 11840) 367s; run: log: (pid 11847) 364s 
run: redis-exporter: (pid 12725) 69s; run: log: (pid 12330) 232s 
run: sidekiq: (pid 12200) 271s; run: log: (pid 12211) 270s 
run: unicorn: (pid 12171) 277s; run: log: (pid 12189) 276s
```



## 开机启动

设置GitLab开机自启动命令为

```shell
sudo systemctl enable gitlab-runsvdir.service
```

禁止GitLab开机自启动命令为

```shell
sudo systemctl disable gitlab-runsvdir.service
```



## 修改clone http地址*

修改clone http地址

```shell
vim /opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml
```

设置host & port 后 重启gitlab

```shell
gitlab-ctl restart
```
