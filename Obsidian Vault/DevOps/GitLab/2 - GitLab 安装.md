# GitLab 安装

## 系统环境
Ubuntu 20.04 LTS



## 安装版本
GitLab CE



## 安装依赖项

键入以下内容来安装依赖项：

```shell
sudo apt update
sudo apt install ca-certificates  curl  openssh-server postfix
```

添加GitLab包仓库

```shell
$ curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```



## 方式1  镜像安装
*注意: gitlab-ce 镜像仅支持 x86-64 架构*

 **1.信任 GitLab 的 GPG 公钥**

```shell
curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null
```

**2.选择Ubuntu 版本，Ubuntu 20.04 LTS （清华大学开源软件镜像地址）**

```shell
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu focal main
```

文本框中内容写进 `/etc/apt/sources.list.d/gitlab-ce.list`

**3.安装 gitlab-ce**

```shell
sudo apt-get update
sudo apt-get install gitlab-ce
```



## 方式2  Docker安装指南

[[Docker 安装]]

*不负责 docker for windows*

**1.查询可用docker镜像**

```shell
docker search gitlab
```

**2.下载镜像**

```shell
docker pull gitlab/gitlab-ce
```

**3.启动服务**

```shell
docker run -d -p 10008:80 -p 10009:443 -p 10010:22 --restart always --name gitlab -v /docker/gitlab/etc/gitlab:/etc/gitlab -v /docker/gitlab/var/log/gitlab:/var/log/gitlab -v /docker/gitlab/var/opt/gitlab:/var/opt/gitlab --privileged=true gitlab/gitlab-ce
```



## 安装成功显示
![[GitLab安装成功.png]]