 ##### Linux第三章实验报告

## 实验步骤

- 在asciinema注册一个账号，并根据官方提供的指令在本地安装配置好asciinema
- 确保本地已经完成asciinema auth，并在asciinema成功关联了本地账号和在线账号
- 在命令行中远程连接虚拟机并进行相应操作

## 软件环境：Virtualbox、Ubuntu 20.04 Server 64bit

3.2 

https://asciinema.org/a/xMvw4uzHRtUpGJdxpd8pO5Hox

3.3

https://asciinema.org/a/BdaSP3Sfs7gNMPjDOt69q3OIt

3.4-4.1

https://asciinema.org/a/sRivCpJDMbBoGoNhLmF3ErJTC

4.2https://asciinema.org/a/aq1dI5nTO93fI7A6P0nBKbVxi

4.3-4.4

 https://asciinema.org/a/Tqesrjzf7W396fUUmWlQZbhxm

5. 1-6https://asciinema.org/a/ZTkg57grrB1spEeI7GFcT3Lme

7

https://asciinema.org/a/lISL3XloNnOV3QymKfpQUSzaL



## 自查清单

- 如何添加一个用户并使其具备sudo执行程序的权限？

  > add user *username* //添加一个用户 sudo usermod -aG sudo *username* //赋予用户sudo权限（加入到sudo组）

 

- **如何将一个用户添加到一个用户组？**

  > sudo *usermod* -aG *groupname* *username*

 

- **如何查看当前系统的分区表和文件系统详细信息？**

  > fdisk -l 或者 df -T

 

- **如何实现开机自动挂载Virtualbox的共享目录分区？**

  步骤：
  1.安装增强功能
  2.**在虚拟机关机时**设置共享文件夹:设置-共享文件夹-新增共享文件夹
  3.输入以下代码：

  > mkdir /mnt/share //建立目录 sudo mount -t vboxsf exp03 /mnt/share //实现挂载

  4.在*/etc/rc.local*中加入以下代码：

  > mount -t vboxsf linux /mnt/share //实现开机自启动

 

- **基于LVM（逻辑分卷管理）的分区如何实现动态扩容和缩减容量？**

  1.扩容

  > lvextend -l [+]Number[PERCENT]/-L [+]Size[m|UNIT] position_args

  2.缩减

  > lvreduce -L|--size [-]Size[m|UNIT] LV

 

- **如何通过systemd设置实现在网络连通时运行一个指定脚本，在网络断开时运行另一个脚本？**

  > systemctl cat networking.service //查看配置文件

  将[Service]中添加以下内容

  > ExecStartPre = /脚本1位置
  > ExecStopPost = /脚本2位置

 

- **如何通过systemd设置实现一个脚本在任何情况下被杀死之后会立即重新启动？实现杀不死？**

  修改该脚本[service]的*Unit*

  > Restart=always