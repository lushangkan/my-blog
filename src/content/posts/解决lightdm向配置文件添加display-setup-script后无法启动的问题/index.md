---
title: 解决lightdm向配置文件添加display-setup-script后无法启动的问题
published: "2023-11-20 21:21:54"
draft: false
description: "配置服务器时，为了可以绕过登录直接运行X11应用，需要在每次启动X11服务器后都运行xhost +local:，以禁用本地运行X11程序时需要认证的问题。"
author: 路上看见
tags:
  - 默认分类
coverImage:
  src: ./2791068578.png
  alt: 解决lightdm向配置文件添加display-setup-script后无法启动的问题
toc: true
---


配置服务器时，为了可以绕过登录直接运行X11应用，需要在每次启动X11服务器后都运行`xhost +local:`，以禁用本地运行X11程序时需要认证的问题。

在Google了一番后，在AskUbuntu发现了[一个相关的讨论][1]，内容是使用lightdm的启动脚本来运行`xhost +local:`。

当我按照讨论的结果想向`/etc/lightdm/lightdm.conf`添加display-setup-script后，发现linux mint并没有默认的`lightdm.conf`，而是在`/etc/lightdm/lightdm.conf.d/`目录中添加不同的配置文件，于是我在里面创建了配置文件，命名为`99-start-script.conf`(前面的数字越大，优先级越高)，并向文件添加以下内容:

    [Seat:*]
    display-setup-script=/usr/share/lightdm-start-script.sh

[scode type="yellow"]可能需要root权限创建文件[/scode]

保存后，在`/usr/share/`文件夹创建文件`lightdm-start-script.sh`，并在文件输入:

    #!/bin/bash
    xhost +local:
    # 设置LVDS-1-1的分辨率，并设置为默认显示器
    xrandr --output "LVDS-1-1" --mode '1366x768' --rate 60 --primary
    # 设置VGA-0的分辨率和panning
    xrandr --output 'VGA-0' --mode '1920x1080' --panning '1920x1080+1366+0' --rate 60

保存后，使用`service lightdm restart`重启，却发现lightdm无法启动，于是我查看了`/var/log/lightdm/lightdm.log`,但是日志文件并没有报错信息。

经过一番查找后，怀疑是其他配置文件里有设置display-setup-script，而我的新配置文件将原来的display-setup-script替换了，所以lightdm无法启动。

再经过各种Google后，发现运行`lightdm --show-config`可以显示当前的配置项，于是我将之前的配置文件`99-start-script.conf`重命名为`99-start-script.conf.bak`，并重启lightdm服务，运行`lightdm --show-config`后显示的输出如下:

       [Seat:*]
    A  allow-guest=false
    C  greeter-wrapper=/usr/lib/lightdm/lightdm-greeter-session
    D  guest-wrapper=/usr/lib/lightdm/lightdm-guest-session
    E  xserver-command=X -core
    F  type=xlocal
    I  display-setup-script=/sbin/prime-switch
    F  display-stopped-script=/sbin/prime-switch
    G  greeter-session=slick-greeter
    H  user-session=cinnamon
    
       [LightDM]
    B  backup-logs=false
    
    Sources:
    A  /usr/share/lightdm/lightdm.conf.d/50-disable-guest.conf
    B  /usr/share/lightdm/lightdm.conf.d/50-disable-log-backup.conf
    C  /usr/share/lightdm/lightdm.conf.d/50-greeter-wrapper.conf
    D  /usr/share/lightdm/lightdm.conf.d/50-guest-wrapper.conf
    E  /usr/share/lightdm/lightdm.conf.d/50-xserver-command.conf
    F  /usr/share/lightdm/lightdm.conf.d/90-nvidia.conf
    G  /usr/share/lightdm/lightdm.conf.d/90-slick-greeter.conf
    H  /etc/lightdm/lightdm.conf.d/70-linuxmint.conf
    I  /etc/lightdm/lightdm.conf.d/99-start-script.conf.

可以看到display-setup-script项已经被nvidia的配置文件所使用，于是向`/usr/share/lightdm-start-script.sh`头部添加`/sbin/prime-switch`并保存，再将`99-start-script.conf.bak`重命名为`99-start-script.conf.bak`，再次重启lightdm服务，结果正常显示登录界面。

(封面图片来自: [wikipedia][2] 和 [Xorg官网][3])


  [1]: https://askubuntu.com/questions/115675/xhost-setting-at-boot
  [2]: https://en.wikipedia.org/wiki/X.Org_Foundation
  [3]: https://x.org/