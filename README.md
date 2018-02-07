# GitLab安装指南

本指南仅指导用户在CentOS7下进行GitLab的安装及汉化，其他操作系统下的程序安装请前往[GitLab官网](https://about.gitlab.com/installation/)寻求指导。

## 目录

[TOC]



## 安装英文原版

#### 安装并配置必要的依赖

在CentOS 7（RedHat / Oracle / Scientific Linux 7）上，用以下命令在系统防火墙中打开HTTP和SSH访问	。

```shell
sudo yum install -y curl policycoreutils-python openssh-server
sudo systemctl enable sshd
sudo systemctl start sshd
sudo firewall-cmd --permanent --add-service=http
sudo systemctl reload firewalld
```

接下来，安装Postfix邮件发送通知。 如果您想使用其他方式发送电子邮件，请跳过此步骤并在安装GitLab后配置外部SMTP服务器。

```shell
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix
```

在Postfix安装期间，可能会出现一个配置屏幕。 选择“Internet Site”并按回车。 使用您服务器的外部DNS “mail name”，然后按回车。 如果出现其他屏幕，请继续按回车键以接受默认值。

#### 添加GitLab软件包储存库并安装软件包

添加GitLab软件包库。

```bash
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
```

接下来，安装GitLab软件包。 将`http://gitlab.example.com`更改为您要访问您的GitLab实例的URL。 安装将自动配置并启动该URL的GitLab。 HTTPS安装后需要额外的配置。

```shell
sudo EXTERNAL_URL="http://gitlab.example.com" yum install -y gitlab-ee
```
#### 浏览主机并登陆

在第一次访问时，您将被重定向到密码重置界面。初始默认管理员帐户`root`的密码，您将被重定向回登录界面。使用`root`登录。

更详细的内容，请参阅[安装配置详细说明](https://docs.gitlab.com/omnibus/README.html#installation-and-configuration-using-omnibus-package)。

## 手动汉化

本汉化演示gitlab-ee-10.4.2版本的汉化，其他版本汉化可以举一反三。

#### 停止gitlab服务

```shell
gitlab-ctl stop
```
#### 查看版本

```shell
cat /opt/gitlab/embedded/service/gitlab-rails/VERSION
10.4.2-ee
```

#### 克隆获取汉化版本库

> 如果你安装的是gitlab-10.4.2-ee版本，那么恭喜你，你可以下载本安装指南根目录下的`gitlab-10.4.2-zh.diff`文件放到`/tmp`目录下，跳过三步，直接进行【安装patch并将差异变更到gitlab-rails】步骤。

```shell
cd /root
git clone https://gitlab.com/xhang/gitlab.git v10.4.2-zh
```

#### 查看该汉化补丁的版本

```shell
cat /root/v10.4.2-zh/VERSION
10.4.2
```

#### 将版本差异导出临时文件

```shell
git diff v10.4.2 v10.4.2-zh >/tmp/10.4.2-zh.diff
```

#### 安装patch并将差异变更到gitlab-rails

```shell
yum install patch -y
patch -d /opt/gitlab/embedded/service/gitlab-rails -p1 < /tmp/10.4.2-zh.diff
```

#### 启动gitlab

```shell
gitlab-ctl start
```

#### 重新配置gitlab

```shell
gitlab-ctl reconfigure
```