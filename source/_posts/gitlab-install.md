---
title: linux 搭建gitlab
---
>其实最好的开发文档就是官方的开发文档，每次要搭建一个架构或者是学习一种新的的语言或者其它第一件事情就是找官方文档.  
>所以gitlab https://about.gitlab.com/installation 官方文档。
>可以按着官方文档一步一步往下，如果出了错再去查找错误的解决方法。

我装的系统是centOS 5.6
- 安装配置依赖项 将在系统防火墙里面开放HTTP和SSH端口。如果你根本就没设置防火墙，或者没有限制那些端口可以跳过。  
````  
1.sudo yum install curl policycoreutils openssh-server openssh-clients
2.sudo systemctl enable sshd
3.sudo systemctl start sshd
4.sudo yum install postfix
5.sudo systemctl enable postfix
6.sudo systemctl start postfix
7.sudo firewall-cmd --permanent --add-service=http
8.sudo systemctl reload firewalld
```
- 添加GitLab仓库,并安装到服务器上(这里是管道安装，还有其它的安装方式。比如rpm安装，下载源码编译安装等)  
``` bash
curl -sS http://packages.gitlab.cc/install/gitlab-ce/script.rpm.sh | sudo bash
```
``` bash
sudo yum install gitlab-ce
```
- 上面没有报错的执行完了，你会发现 gitlab 就安装在/opt/gitlab 下面，配置文件在 /etc/gitlab 下面。还有3个全局脚本,主要用到的就是 gitlab-ctl。具体的
参数可以 gitlab-ctl --help 查看。配置生效和启动 gitlab。  

```
sudo gitlab-ctl reconfigure
```
### 注意的是每次修改配置文件都要 gitlab-ctl reconfigure 配置才能生效。如果你自己有装nginx或者apace服务且端口用的是80，这样就会发生冲突。因为gitlab有内置nginx端口为80，只能修改端口了。  
- gitlab 用已有的nginx  
禁用内置nginx
vim /etc/gitlab/gitlab.rb
配置 nginx['enable'] = false
然后配置生效  sudo gitlab-ctl reconfigure  
- 给现有nginx增加一个虚拟主机

upstream gitlab {
  \# gitlab socket 文件地址
  \# 7.x 版本在此位置
  \# server unix:/var/opt/gitlab/gitlab-rails/tmp/sockets/gitlab.socket;
  \# 8.0 位置
  server unix://var/opt/gitlab/gitlab-rails/sockets/gitlab.socket;
}

server {
  listen \*:80;
  server_name gitlab.liaohuqiu.com;   # 请修改为你的域名
  server_tokens off;     # don't show the version number, a security best practice
  root /opt/gitlab/embedded/service/gitlab-rails/public;
  client_max_body_size 250m;

  access_log  /var/log/gitlab/nginx/gitlab_access.log;
  error_log   /var/log/gitlab/nginx/gitlab_error.log;

  location / {
    try_files $uri $uri/index.html $uri.html @gitlab;
  }

  location @gitlab {
    proxy_read_timeout 300; # Some requests take more than 30 seconds.
    proxy_connect_timeout 300; # Some requests take more than 30 seconds.
    proxy_redirect     off;
    proxy_set_header   X-Forwarded-Proto $scheme;
    proxy_set_header   Host              $http_host;
    proxy_set_header   X-Real-IP         $remote_addr;
    proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header   X-Frame-Options   SAMEORIGIN;
    proxy_pass http://gitlab;
  }

然后重启 nginx。
## 还有一个很重要的直接访问会报502，那是因为现有nginx没有访问 gitlab 用户的 socket 文件权限原因,于是简单粗暴的加上  
```
sudo chmod -R o+x /var/opt/gitlab/gitlab-rails  
```
### 每一次 reconfigure,gitlab 文件都会都会重新生成，所以每次 reconfigure 完，都要重新授权。
