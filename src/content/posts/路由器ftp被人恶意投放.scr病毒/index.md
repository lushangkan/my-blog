---
title: 路由器ftp被人恶意投放.scr病毒
published: "2021-06-26 23:23:00"
draft: false
description: 发现
author: 路上看见
tags:
  - 默认分类
toc: true
---


发现
--
![图片][1]
本以为是opkg用的文件,但等我进入下一级目录,又出现了这几个文件
打开,火绒直接报毒
![图片][2]
不过万幸的是,数据盘没有被影响

查看修改日期,发现是在2020年12月24日感染的
![图片][3]

好家伙,趁我在学校时投毒
不过我在当天14:05分左右在学校连接了路由器的FTP,且用户名和口令为admin,所以...锅? 
简单分析

发现了,接着就是保存样本*~~不是删除~~* ?.

[scode type="red"]样本仅供研究,请勿将其用于非法用途![/scode]
[scode type="share"]由于网安要求，样本请到微步云下载！[/scode]
丢去微步云沙盒,检出为恶意文件
![图片][4]
并且有大量的TCP和UDP连接,看起来是Exploit?
![图片][5]
[微步云链接][6]

解决
--
最后的最后,就是删除了

    find . -name "*.lnk" -type f -print -exec rm -rf {} \;
    find . -name "*.scr" -type f -print -exec rm -rf {} \;

----------


  [1]: ./2874353017.png
  [2]: ./1899996293.png
  [3]: ./4135439992.png
  [4]: ./114026523.png
  [5]: ./3437751264.png
  [6]: https://s.threatbook.cn/report/file/af94ddf7c35b9d9f016a5a4b232b43e071d59c6beb1560ba76df20df7b49ca4c/?env=win7_sp1_enx64_office2013