---
title: linux 安装nginx
---

##安装步骤

>由于工作需要安装了好几次nginx，感觉好麻烦。于是就自己写了一个安装脚本，准备了安装材料。

- 版本nginx1.8.1
- 安装系统cent os
下载地址 百度云 链接：http://pan.baidu.com/s/1hsnAcgW 密码：nvz3
安装说明：
- 1.将下载的zip包放在 /usr底下，解压
  ``` bash
  $ cd /usr
  ```
  ``` bash
  $ uzip nginx1.8.1.zip
  ```
- 2.给脚本执行权限，执行脚本
  ``` bash
  $ chmod u+x nginx-install.sh
  ```
  ``` bash
  $ ./nginx-install.sh
  ```
>等待安装完毕，nginx 就安装在了 /usr/local/nginx 里面。  
出了问题，解决然后重新跑。我自己安装是没有出现问题，执行的时候出现了问题就解决，然后重新跑。
