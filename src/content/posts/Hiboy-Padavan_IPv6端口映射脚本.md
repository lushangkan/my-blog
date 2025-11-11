---
title: Hiboy-Padavan IPv6端口映射脚本
published: "2023-06-22 23:17:00"
draft: false
description: "粘贴到 自定义设置 -> 脚本 -> 自定义脚本0"
author: 路上看见
tags:
  - 默认分类
toc: true
---


粘贴到 自定义设置 -> 脚本 -> 自定义脚本0
需打开 opt强制安装(至少mini环境)
在代码中端口转发列表输入需要映射的端口即可

    #检查包是否已经安装函数
    check_package_installed() {
        local package_name=$1
        if opkg list-installed | grep "$package_name" >/dev/null; then
            logger -t "【自定义脚本0】" "$package_name已安装"
            return 0
        else
            logger -t "【自定义脚本0】" "$package_name未安装，开始安装$package_name"
            opkg update
            opkg install "$package_name" 
            if opkg list-installed | grep "$package_name" >/dev/null; then
                logger -t "【自定义脚本0】" "$package_name安装成功"
                return 0
            else
                logger -t "【自定义脚本0】" "$package_name安装失败，请检查"
                return 1
            fi
        fi
    }
    
    function run() {
        if [ ! -f /opt/etc/opkg.conf ]; then
            logger -t "【自定义脚本0】" "opkg.conf文件不存在, 似乎opt环境未下载，稍后再试"
            return 1
        fi
        #设置端口转发
        logger -t "【自定义脚本0】" "开始设置端口转发"
        #设置opkg镜像
        sed -i 's|https\?://bin.entware.net|https://mirrors.bfsu.edu.cn/entware|g' /opt/etc/opkg.conf
        opkg update
        opkg upgrade
        check_package_installed bash
        if [ $? == 1 ]; then
            logger -t "【自定义脚本0】" "bash安装失败"
            return 1
        fi
        check_package_installed socat
        if [ $? == 1 ]; then
            logger -t "【自定义脚本0】" "socat安装失败"
            return 1
        fi
        check_package_installed libopenssl
        if [ $? == 1 ]; then
            logger -t "【自定义脚本0】" "libopenssl安装失败"
            return 1
        fi
        check_package_installed procps-ng-pkill
        if [ $? == 1 ]; then
            logger -t "【自定义脚本0】" "procps-ng-pkill安装失败"
            return 1
        fi
        #创建端口转发日志文件夹
        local socat_dir="/opt/tmp/socat"
        if [ ! -d "$socat_dir" ]; then
            mkdir -p "$socat_dir"
            logger -t "【自定义脚本0】" "创建$socat_dir目录成功"
        else
            logger -t "【自定义脚本0】" "$socat_dir目录已存在"
        fi
        #保存脚本，使用bash运行
        bash_path=$(opkg files bash | grep /bin/bash)
        script_path="/opt/tmp/port_forward.sh"
        cat >"$script_path" << EOF
    #端口转发列表
    declare -A port_map=(
        ["8889"]="192.168.123.183:3389"
        ["8123"]="192.168.123.211:8123"
        ["996"]="192.168.123.1:996"
        ["997"]="192.168.123.211:997"
    )
    #设置防火墙
    ip6tables -F
    ip6tables -X
    ip6tables -P INPUT ACCEPT
    ip6tables -P OUTPUT ACCEPT
    ip6tables -P FORWARD ACCEPT
    #如果文件不存在，就新建
    if [ ! -f /opt/tmp/port_map.txt ]; then
        touch /opt/tmp/port_map.txt
    fi
    # 遍历map，如果端口不在文件里的话，就把需要映射的端口保存在一个新的增加map，如果某个端口在文件里存在，在map不存在，就保存在新的删除map，然后就分别遍历两个新的map。
    declare -A add_port_map=()
    declare -A del_port_map=()
    #检测需要映射的端口是否在文件里
    while read line; do
        port=\$(echo \$line | awk -F ':' '{print \$1}')
        if [[ ! \${port_map[\$port]} ]]; then
            del_port_map[\$port]=\${port_map[\$port]}
        fi
    done < /opt/tmp/port_map.txt
    #检测文件里的端口是否需要映射
    for port in "\${!port_map[@]}"; do
        if ! grep -q "\$port" /opt/tmp/port_map.txt; then
            add_port_map[\$port]=\${port_map[\$port]}
        fi
    done
    #启动端口转发
    for port in "\${!add_port_map[@]}"; do
        logger -t \"【自定义脚本0】\" \"开始转发\$port端口\"
        nohup socat TCP6-LISTEN:\$port,reuseaddr,fork TCP4:\${port_map[\$port]} >>/opt/tmp/socat/port_\$port.log 2>&1 &
        #运行端口通过防火墙
        ip6tables -A INPUT -p tcp --dport \$port -j ACCEPT
    done
    #停止端口转发
    for port in "\${!del_port_map[@]}"; do
        logger -t \"【自定义脚本0】\" \"停止转发\$port端口\"
        pkill -f "socat TCP6-LISTEN:\$port"
        #停止端口通过防火墙
        ip6tables -A INPUT -p tcp --dport \$port -j REJECT
    done
    #把port_map保存到文件
    rm -f /opt/tmp/port_map.txt
    for port in "\${!port_map[@]}"; do
        echo "\$port:\${port_map[\$port]}" >>/opt/tmp/port_map.txt
    done
    EOF
        logger -t "【自定义脚本0】" "保存脚本成功"
        chmod 755 "$script_path"
        logger -t "【自定义脚本0】" "开始运行脚本"
        $bash_path "$script_path" >> /dev/null
    }
    run